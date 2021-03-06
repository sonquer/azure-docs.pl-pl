---
title: Rozwiązywanie problemów z koligacją sesji
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z koligacją sesji na platformie Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047988"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Rozwiązywanie problemów z koligacją sesji usługi Azure Application Gateway

Dowiedz się, jak diagnozować i rozwiązywać problemy z koligacją sesji z usługą Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, usługa Application Gateway może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika.

## <a name="possible-problem-causes"></a>Możliwe przyczyny problemu

Problem związany z utrzymywaniem koligacji sesji opartej na plikach cookie może się zdarzyć z następujących najważniejszych przyczyn:

- Ustawienie "koligacja oparta na plikach cookie" nie jest włączone
- Twoja aplikacja nie może obsłużyć koligacji opartej na plikach cookie
- Aplikacja korzysta z koligacji opartej na plikach cookie, ale żądania nadal odbijają się między serwerami zaplecza

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Sprawdź, czy ustawienie "koligacja oparta na plikach cookie" jest włączone

Czasami problemy z koligacją sesji mogą wystąpić, gdy zapomnisz włączyć ustawienie "koligacja na podstawie plików cookie". Aby określić, czy na karcie Ustawienia protokołu HTTP w Azure Portal włączono ustawienie "koligacja na podstawie plików cookie", postępuj zgodnie z instrukcjami:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. W okienku **nawigacji po lewej stronie** kliknij pozycję **wszystkie zasoby**. Kliknij nazwę bramy aplikacji w bloku wszystkie zasoby. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wprowadzić nazwę bramy aplikacji w polu **Filtruj według nazwy...** aby łatwo uzyskać dostęp do bramy aplikacji.

3. Wybierz kartę **Ustawienia http** w obszarze **Ustawienia**.

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Kliknij pozycję **appGatewayBackendHttpSettings** po prawej stronie, aby sprawdzić, czy wybrano opcję **włączona** dla koligacji opartej na plikach cookie.

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Możesz również sprawdzić wartość "**CookieBasedAffinity**" *w obszarze "backendHttpSettingsCollection", używając*jednej z następującychmetod:

- Uruchamianie polecenia [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) w programie PowerShell
- Poszukaj pliku JSON przy użyciu szablonu Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Aplikacja nie może obsługiwać koligacji opartej na plikach cookie

#### <a name="cause"></a>Przyczyna

Brama aplikacji może przeprowadzić koligację opartą na sesji tylko przy użyciu pliku cookie.

#### <a name="workaround"></a>Obejście

Jeśli aplikacja nie może obsłużyć koligacji opartej na plikach cookie, należy użyć zewnętrznego lub wewnętrznego modułu równoważenia obciążenia platformy Azure lub innego rozwiązania innej firmy.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplikacja korzysta z koligacji opartej na plikach cookie, ale żądania nadal odbijają się między serwerami zaplecza

#### <a name="symptom"></a>Objaw

Ustawienie koligacji opartej na plikach cookie jest włączone, gdy uzyskujesz dostęp do Application Gateway przy użyciu adresu URL krótkiej nazwy w programie Internet Explorer, na przykład: [http://website](http://website/) , żądanie nadal jest odbijane między serwerami zaplecza.

Aby zidentyfikować ten problem, postępuj zgodnie z instrukcjami:

1. Wykonaj śledzenie debugera sieci Web na "kliencie", który nawiązuje połączenie z aplikacją za Application Gateway (w tym przykładzie korzystamy z programu Fiddler).
    **Porada** Jeśli nie wiesz, jak korzystać z programu Fiddler, zaznacz opcję "**Chcę zbierać ruch sieciowy i analizować go przy użyciu narzędzia Web Debugger**" u dołu.

2. Sprawdź i Analizuj dzienniki sesji, aby określić, czy pliki cookie dostarczone przez klienta mają ARRAffinity szczegóły. Jeśli nie znajdziesz szczegółów ARRAffinity, takich jak "**ARRAffinity =** *ARRAffinityValue*" w zestawie plików cookie, oznacza to, że klient nie odpowiada za pomocą pliku cookie Arra, który jest dostarczany przez Application Gateway.
    Na przykład:

    ![Rozwiązywanie problemów — sesja-koligacja-problemy-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![Rozwiązywanie problemów — sesja-koligacja-problemy-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Aplikacja kontynuuje próbę ustawienia pliku cookie dla każdego żądania do momentu otrzymania odpowiedzi.

#### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ program Internet Explorer i inne przeglądarki mogą nie przechowywać plików cookie lub używać ich w adresie URL krótkiej nazwy.

#### <a name="resolution"></a>Rozwiązanie

Aby unikać występowania tego problem, należy uzyskiwać dostęp do usługi Application Gateway przy użyciu nazwy FQDN. Na przykład użyj [http://website.com](https://website.com/) lub [http://appgw.website.com](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Dodatkowe dzienniki umożliwiające rozwiązywanie problemów

Można zbierać dodatkowe dzienniki i analizować je w celu rozwiązywania problemów związanych z koligacją sesji opartą na plikach cookie

### <a name="analyze-application-gateway-logs"></a>Analizowanie dzienników Application Gateway

Aby zebrać dzienniki Application Gateway, postępuj zgodnie z instrukcjami:

Włączanie rejestrowania za pośrednictwem witryny Azure Portal

1. W [Azure Portal](https://portal.azure.com/)Znajdź zasób, a następnie kliknij pozycję **dzienniki diagnostyczne**.

   W przypadku Application Gateway dostępne są trzy dzienniki: Dziennik dostępu, dziennik wydajności, Dziennik zapory

2. Aby rozpocząć zbieranie danych, kliknij pozycję **Włącz diagnostykę**.

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Blok **Ustawienia diagnostyki** zawiera ustawienia dzienników diagnostycznych. W tym przykładzie Log Analytics przechowuje dzienniki. Kliknij pozycję **Konfiguruj** w obszarze **log Analytics** , aby ustawić obszar roboczy. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Potwierdź ustawienia, a następnie kliknij przycisk **Zapisz**.

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Wyświetlanie i analizowanie dzienników dostępu Application Gateway

1. W Azure Portal w widoku zasobów Application Gateway wybierz pozycję **dzienniki diagnostyczne** w sekcji **monitorowanie** .

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Po prawej stronie wybierz pozycję "**ApplicationGatewayAccessLog**" na liście rozwijanej w obszarze **kategorie dzienników.**  

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na liście Dziennik dostępu Application Gateway kliknij dziennik, który chcesz przeanalizować i wyeksportować, a następnie wyeksportuj plik JSON.

4. Przekonwertuj plik JSON, który został wyeksportowany w kroku 3 do pliku CSV, i Wyświetl je w programie Excel, Power BI lub dowolnym innym narzędziu do wizualizacji danych.

5. Sprawdź następujące dane:

- **ClientIP**— adres IP klienta z klienta nawiązującego połączenie.
- **ClientPort** — jest to port źródłowy z klienta nawiązującego połączenie dla żądania.
- **RequestQuery** — wskazuje na to, że serwer docelowy otrzyma żądanie.
- **Serwer — rozesłane**: wystąpienie puli zaplecza, że żądanie zostało odebrane.
- **X-AzureApplicationGateway-log-ID**: identyfikator korelacji używany dla żądania. Może służyć do rozwiązywania problemów z ruchem na serwerach zaplecza. Na przykład: X-AzureApplicationGateway-CACHE-trafi = 0 & SERVER-ROUTEd = 10.0.2.4.

  - **Serwer-stan**: kod odpowiedzi HTTP otrzymany Application Gateway od zaplecza.

  ![Rozwiązywanie problemów — sesja-koligacja-problemy-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Jeśli widzisz dwa elementy pochodzące z tego samego ClientIP i portu klienta i są wysyłane na ten sam serwer zaplecza, oznacza to, że Application Gateway prawidłowo skonfigurowany.

Jeśli widzisz dwa elementy pochodzące z tego samego ClientIP i portu klienta i są one wysyłane do różnych serwerów zaplecza, oznacza to, że żądanie jest odbijane między serwerami zaplecza, wybierz pozycję "**aplikacja korzysta z koligacji opartej na plikach cookie, ale żądania nadal przechodzą między serwerami**zaplecza" w dolnej części, aby rozwiązać ten problem.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Przechwytywanie i analizowanie ruchu HTTP lub HTTPS za pomocą debugera sieci Web

Narzędzia debugowania sieci Web, takie jak programu Fiddler, mogą ułatwić debugowanie aplikacji sieci Web przez przechwytywanie ruchu sieciowego między Internetem a komputerami testowymi. Te narzędzia umożliwiają inspekcję danych przychodzących i wychodzących w miarę ich odbierania/wysyłania przez przeglądarkę. Programu Fiddler w tym przykładzie ma opcję powtarzania HTTP, która może pomóc w rozwiązywaniu problemów po stronie klienta z aplikacjami sieci Web, szczególnie w przypadku uwierzytelniania rodzaju problemu.

Użyj wybranego debugera sieci Web. W tym przykładzie będziemy używać programu Fiddler do przechwytywania i analizowania ruchu HTTP lub https, postępuj zgodnie z instrukcjami:

1. Pobierz narzędzie programu Fiddler w <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Wybierz opcję Fiddler4, jeśli na komputerze przechwytującym jest zainstalowany program .NET 4. W przeciwnym razie wybierz pozycję Fiddler2.

2. Kliknij prawym przyciskiem myszy plik wykonywalny instalatora i uruchom go jako administrator, aby zainstalować program.

    ![Rozwiązywanie problemów — sesja-koligacja — problemy — 12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Gdy otworzysz programu Fiddler, powinien on automatycznie rozpocząć przechwytywanie ruchu (Zwróć uwagę na przechwytywanie w lewym dolnym rogu). Naciśnij klawisz F12, aby uruchomić lub zatrzymać przechwytywanie ruchu.

    ![Rozwiązywanie problemów — sesja-koligacja-problemy-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Najprawdopodobniej interesuje Cię odszyfrowany ruch HTTPS i można włączyć odszyfrowywanie HTTPS, wybierając pozycję **narzędzia** > **Opcje programu Fiddler**i zaznacz pole wyboru " **Szyfruj ruch https**".

    ![Rozwiązywanie problemów — sesja-koligacja-problemy-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Przed wygenerowaniem problemu można usunąć poprzednie niepowiązane sesje, klikając przycisk **X** (ikonę) > **usunąć wszystko** jako zrzut ekranu: 

    ![Rozwiązywanie problemów — sesja-koligacja-problemy-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Po ponownym utworzeniu problemu Zapisz plik do przeglądu, wybierając pozycję **plik** > **Zapisz** > **wszystkie sesje..** . 

    ![Rozwiązywanie problemów — sesja-koligacja-problemy-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Sprawdź i Analizuj dzienniki sesji, aby określić, jaki problem występuje.

    Przykłady:

- **Przykład:** Można znaleźć dziennik sesji, z którego żądanie jest wysyłane z klienta i który przechodzi do publicznego adresu IP Application Gateway, kliknij ten dziennik, aby wyświetlić szczegóły.  Po prawej stronie dane w dolnym polu to elementy, które Application Gateway zwraca klientowi. Wybierz kartę "RAW" i określ, czy klient otrzymuje "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*". Jeśli nie ma pliku cookie, koligacja sesji nie jest ustawiona lub Application Gateway nie stosuje pliku cookie z powrotem do klienta.

   > [!NOTE]
   > Ta wartość ARRAffinity jest identyfikatorem cookie, który Application Gateway ustawiany przez klienta do określonego serwera zaplecza.

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Przykład B:** Następnym dziennikiem sesji, po którym następuje poprzedni, jest klient odpowiadający na Application Gateway, który ustawił ARRAAFFINITY. Jeśli ARRAffinity jest zgodny z identyfikatorem cookie, pakiet powinien być wysyłany na ten sam serwer zaplecza, który był wcześniej używany. Sprawdź następne kilka wierszy komunikacji http, aby sprawdzić, czy plik cookie ARRAffinity klienta jest zmieniany.

   ![Rozwiązywanie problemów — sesja-koligacja-problemy-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> W przypadku tej samej sesji komunikacji plik cookie nie powinien być zmieniany. Zaznacz górne pole po prawej stronie, wybierz kartę pliki cookie, aby sprawdzić, czy klient korzysta z pliku cookie i wysyłając go z powrotem do Application Gateway. W przeciwnym razie przeglądarka klienta nie zachowuje i nie używa pliku cookie do konwersacji. Czasami może to być klient.

 

## <a name="next-steps"></a>Następne kroki

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
