---
title: Połącz Configuration Manager Azure Monitor | Microsoft Docs
description: W tym artykule przedstawiono procedurę łączenia Configuration Manager z obszarem roboczym w programie Azure Monitor i rozpoczynania analizowania danych.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 5b5af034b116ec1cdcefc811630683c9f560c840
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513669"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Połącz Configuration Manager z Azure Monitor
Możesz połączyć środowisko Configuration Manager punktu końcowego firmy Microsoft w celu Azure Monitor synchronizacji danych kolekcji urządzeń i odwoływać się do tych kolekcji w Azure Monitor i Azure Automation.  

## <a name="prerequisites"></a>Wymagania wstępne

Azure Monitor obsługuje Configuration Manager Current Branch, wersja 1606 i nowsze.

>[!NOTE]
>Funkcja łączenia Configuration Manager z obszarem roboczym Log Analytics jest opcjonalna i nie jest domyślnie włączona. Tę funkcję należy włączyć przed jej użyciem. Aby uzyskać więcej informacji, zobacz [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Przegląd konfiguracji

Poniższe kroki podsumowują procedurę konfigurowania integracji Configuration Manager z Azure Monitor.  

1. W Azure Active Directory Zarejestruj Configuration Manager jako aplikację sieci Web i/lub aplikację internetowego interfejsu API, a następnie upewnij się, że identyfikator klienta i klucz tajny klienta pochodzą z rejestracji w Azure Active Directory. Zobacz [w obsłudze portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md) Aby uzyskać szczegółowe informacje na temat wykonywania tego kroku.

2. W Azure Active Directory [udziel Configuration Manager (zarejestrowanej aplikacji sieci Web) uprawnienia dostępu do Azure monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. W Configuration Manager Dodaj połączenie przy użyciu kreatora **usług platformy Azure** .

4. [Pobierz i Zainstaluj agenta log Analytics dla systemu Windows](#download-and-install-the-agent) na komputerze z uruchomioną rolą systemu lokacji punktu połączenia usługi Configuration Manager. Agent wysyła Configuration Manager dane do obszaru roboczego Log Analytics w Azure Monitor.

5. W Azure Monitor [Importuj kolekcje z Configuration Manager](#import-collections) jako grupy komputerów.

6. W Azure Monitor Wyświetl dane z Configuration Manager jako [grupy komputerów](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Udziel programu Configuration Manager z uprawnieniami do usługi Log Analytics

W poniższej procedurze, można przyznać *Współautor* roli w obszarze roboczym usługi Log Analytics do aplikacji usługi AD i jednostki usługi utworzonej wcześniej w programie Configuration Manager. Jeśli jeszcze nie masz obszaru roboczego, zobacz temat [Tworzenie obszaru roboczego w Azure monitor](../../azure-monitor/learn/quick-create-workspace.md) przed kontynuowaniem. Dzięki temu programu Configuration Manager do uwierzytelnienia i nawiązania połączenia z obszarem roboczym usługi Log Analytics.  

> [!NOTE]
> Należy określić uprawnienia w obszarze roboczym Log Analytics Configuration Manager. W przeciwnym razie otrzymasz komunikat o błędzie podczas korzystania z Kreatora konfiguracji w programie Configuration Manager.
>

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.

2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, aby zmodyfikować.

3. W okienku po lewej stronie wybierz **kontrola dostępu (IAM)** .

4. Strony kontrola dostępu (IAM), kliknij **Dodaj przypisanie roli** i **Dodaj przypisanie roli** zostanie wyświetlone okienko.

5. W **Dodaj przypisanie roli** okienku w obszarze **roli** listy rozwijanej wybierz **Współautor** roli.  

6. W obszarze **Przypisz dostęp do** listy rozwijanej wybierz aplikacji programu Configuration Manager wcześniej utworzone w AD, a następnie kliknij przycisk **OK**.  

## <a name="download-and-install-the-agent"></a>Pobierz i zainstaluj agenta

Zapoznaj się z artykułem [łączenie komputerów z systemem Windows w celu Azure monitor na platformie Azure](agent-windows.md) , aby poznać metody instalacji agenta log Analytics dla systemu Windows na komputerze hostującym rolę systemu lokacji punktu połączenia usługi Configuration Manager.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Łączenie Configuration Manager z Log Analytics obszarem roboczym

>[!NOTE]
> Aby można było dodać połączenie Log Analytics, środowisko Configuration Manager musi mieć skonfigurowany [punkt połączenia z usługą](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) dla trybu online.

> [!NOTE]
> Lokację najwyższego poziomu należy połączyć w hierarchii, aby Azure Monitor. Jeśli podłączysz autonomiczną lokację główną do Azure Monitor a następnie dodasz centralną lokację administracyjną do środowiska, musisz usunąć i ponownie utworzyć połączenie w ramach nowej hierarchii.

1. W obszarze roboczym **Administracja** w obszarze Configuration Manager wybierz pozycję **usługi chmurowe** , a następnie wybierz pozycję **usługi platformy Azure**. 

2. Kliknij prawym przyciskiem myszy pozycję **usługi platformy Azure** , a następnie wybierz pozycję **Konfiguruj usługi platformy Azure**. Zostanie wyświetlona strona **Konfigurowanie usług platformy Azure** . 
   
3. Na **ogólne** ekranu, upewnij się, że zostały wykonane następujące akcje i że możesz mieć szczegółów dla każdego elementu, a następnie wybierz **dalej**.

4. Na stronie usługi platformy Azure w Kreatorze usług platformy Azure:

    1. Określ **nazwę** dla obiektu w Configuration Manager.
    2. Podaj opcjonalny **Opis** , aby ułatwić identyfikację usługi.
    3. Wybierz łącznik usługi Azure Service **OMS**.

    >[!NOTE]
    >Pakiet OMS jest teraz nazywany Log Analytics, który jest funkcją Azure Monitor.

5. Wybierz pozycję **dalej** , aby przejść do strony właściwości aplikacji platformy Azure w Kreatorze usług platformy Azure.

6. Na stronie **aplikacja** w Kreatorze usług platformy Azure najpierw wybierz środowisko platformy Azure z listy, a następnie kliknij przycisk **Importuj**.

7. Na stronie **Importuj aplikacje** określ następujące informacje:

    1. Określ **nazwę dzierżawy usługi Azure AD** dla aplikacji.

    2. Określ **Identyfikator dzierżawy** usługi Azure AD dla dzierżawy usługi Azure AD. Te informacje można znaleźć na stronie **właściwości** Azure Active Directory. 

    3. Określ nazwę aplikacji w polu **Nazwa** aplikacji.

    4. Określ **Identyfikator klienta**, identyfikator aplikacji utworzonej wcześniej aplikacji usługi Azure AD.

    5. Podaj klucz **tajny klienta dla utworzonej**aplikacji usługi Azure AD.

    6. Określ jako **wygaśnięcie klucza tajnego**, datę wygaśnięcia klucza.

    7. Określ identyfikator **URI identyfikatora aplikacji**utworzonej wcześniej aplikacji usługi Azure AD.

    8. Wybierz pozycję **Weryfikuj** i po prawej stronie wyniki powinny być wyświetlane **pomyślnie**.

8. Na stronie **Konfiguracja** zapoznaj się z informacjami, aby sprawdzić, czy pola obszaru roboczego **platformy Azure**, **grupy zasobów platformy Azure**i **pakietu Operations Management Suite** zostały wstępnie wypełnione, wskazując, że aplikacja usługi Azure AD ma wystarczające uprawnienia w grupie zasobów. Jeśli pola są puste, oznacza to, że aplikacja nie ma wymaganych praw. Wybierz Kolekcje urządzeń do zebrania i przekierowania do obszaru roboczego, a następnie wybierz pozycję **Dodaj**.

9. Przejrzyj opcje na stronie **Potwierdź ustawienia** , a następnie wybierz pozycję **dalej** , aby rozpocząć tworzenie i Konfigurowanie połączenia.

10. Po zakończeniu konfiguracji zostanie wyświetlona strona **ukończenie** . Wybierz polecenie **Zamknij**. 

Po połączeniu Configuration Manager z Azure Monitor, można dodawać lub usuwać kolekcje oraz wyświetlać właściwości połączenia.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualizowanie właściwości połączenia z obszarem roboczym Log Analytics

Jeśli hasło lub klucz tajny klienta wygaśnie lub zostanie utracony, należy ręcznie zaktualizować właściwości połączenia Log Analytics.

1. W obszarze roboczym **administracja** Configuration Manager wybierz pozycję **Cloud Services** , a następnie wybierz pozycję **Łącznik pakietu OMS** , aby otworzyć stronę **Właściwości połączenia pakietu OMS** .
2. Na tej stronie, kliknij przycisk **usługi Azure Active Directory** kartę, aby wyświetlić swoje **dzierżawy**, **identyfikator klienta**, **Data wygaśnięcia klucza tajnego klienta**. **Sprawdź** swoje **klucz tajny klienta** Jeśli wygasł.

## <a name="import-collections"></a>Importuj kolekcje

Po dodaniu połączenia Log Analytics, aby Configuration Manager i zainstalować agenta na komputerze z uruchomioną rolą systemu lokacji punktu połączenia usługi Configuration Manager, następnym krokiem jest zaimportowanie kolekcji z Configuration Manager na platformie Azure Monitoruj jako grupy komputerów.

Po zakończeniu początkowej konfiguracji w celu zaimportowania kolekcji urządzeń z hierarchii informacje o kolekcji są pobierane co 3 godziny, aby zachować bieżące członkostwo. Można to wyłączyć w dowolnym momencie.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, którego programu Configuration Manager jest zarejestrowane w usłudze.  
3. Wybierz pozycję **Ustawienia zaawansowane**.
4. Wybierz **grup komputerów** , a następnie wybierz **SCCM**.  
5. Wybierz **członkostwa w kolekcjach programu Configuration Manager importu** a następnie kliknij przycisk **Zapisz**.  
   
    ![Grupy komputerów - kartę programu SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Wyświetlanie danych z programu Configuration Manager

Po dodaniu Log Analytics połączenia w celu Configuration Manager i zainstalowania agenta na komputerze z uruchomioną rolą systemu lokacji punktu połączenia usługi Configuration Manager dane z agenta są wysyłane do obszaru roboczego Log Analytics w Azure Monitor. W Azure Monitor kolekcje Configuration Manager są wyświetlane jako [grupy komputerów](../../azure-monitor/platform/computer-groups.md). Wyświetlanie grup z **programu Configuration Manager** strony w obszarze **grup Settings\Computer**.

Po zaimportowaniu kolekcje, możesz zobaczyć, na ilu komputerach za pomocą członkostwa w kolekcjach zostały wykryte. Widać również wielu kolekcji, które zostały zaimportowane.

![Grupy komputerów - kartę programu SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Po kliknięciu jednego z nich zostanie otwarty Edytor zapytań dzienników zawierający wszystkie zaimportowane grupy lub wszystkie komputery należące do poszczególnych grup. Za pomocą funkcji [wyszukiwania w dzienniku](../../azure-monitor/log-query/log-query-overview.md)można wykonać dokładniejszą analizę danych członkostwa w kolekcji.

## <a name="next-steps"></a>Następne kroki

Użyj [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje dotyczące danych programu Configuration Manager.
