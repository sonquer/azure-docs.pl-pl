---
title: Konfigurowanie aplikacji w celu uzyskiwania dostępu do interfejsów API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak skonfigurować aplikację zarejestrowaną za pomocą platformy tożsamości firmy Microsoft tak, aby uwzględnić identyfikatory URI przekierowania, poświadczenia lub uprawnienia na potrzeby dostępu do internetowych interfejsów API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/07/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 32691892ccae31541855f47bd8274aa28b6dc185
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704293"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web

Aby aplikacje klienckie przeznaczone dla Internetu lub aplikacje poufne mogły uczestniczyć w przepływie przydziałów autoryzacji, który wymaga uwierzytelniania (i uzyskania tokenu dostępu), muszą ustanowić bezpieczne poświadczenia. Domyślną metodą uwierzytelniania obsługiwaną w witrynie Azure Portal jest użycie identyfikatora klienta i klucza tajnego.

Dodatkowo, zanim klient będzie mógł uzyskać dostęp do internetowego interfejsu API uwidocznionego przez aplikację zasobów (np. interfejsu API Microsoft Graph), platforma udzielania zgody zapewnia, że klient uzyska wymagane udzielenie uprawnień na podstawie żądanych uprawnień. Domyślnie wszystkie aplikacje mogą wybierać uprawnienia z interfejsu API Microsoft Graph. [Uprawnienie „Loguj się i odczytuj profil użytkownika” interfejsu API Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) jest domyślnie wybrane. Dla każdego żądanego internetowego interfejsu API możesz wybrać spośród [dwóch rodzajów uprawnień](developer-glossary.md#permissions):

* **Uprawnienia aplikacji** — Twoja aplikacja kliencka musi mieć bezpośredni dostęp do internetowego interfejsu API w swoim imieniu (bez kontekstu użytkownika). Tego rodzaju uprawnienia wymagają zgody administratora i nie są dostępne dla publicznych (mobilnych i klasycznych) aplikacji klienckich.
* **Uprawnienia delegowane** — Twoja aplikacja kliencka musi mieć dostęp do internetowego interfejsu API w imieniu zalogowanego użytkownika, ale z dostępem ograniczonym przez wybrane uprawnienie. Tego rodzaju uprawnienie może być udzielane przez użytkownika, chyba że wymaga ono zgody administratora.

  > [!NOTE]
  > Dodanie uprawnienia delegowanego do aplikacji nie powoduje automatycznego udzielenia zgody dla użytkowników w dzierżawie. Użytkownicy muszą nadal ręcznie wyrażać zgodę na dodane uprawnienia delegowane w czasie wykonywania, chyba że administrator wyrazi zgodę w imieniu wszystkich użytkowników.

W tym przewodniku Szybki start przedstawimy, jak skonfigurować aplikację pod kątem wykonywania następujących działań:

* [Dodawanie identyfikatorów URI przekierowania do aplikacji](#add-redirect-uris-to-your-application)
* [Skonfiguruj ustawienia zaawansowane dla aplikacji](#configure-advanced-settings-for-your-application)
* [Modyfikuj obsługiwane typy kont](#modify-supported-account-types)
* [Dodawanie poświadczeń do aplikacji sieci Web](#add-credentials-to-your-web-application)
* [Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy upewnij się, że są spełnione następujące wymagania wstępne:

* Znasz obsługiwane [uprawnienia i zgody](v2-permissions-and-consent.md), ponieważ ich znajomość jest istotna podczas tworzenia aplikacji, które mają być używane przez innych użytkowników lub aplikacje.
* Masz dzierżawę z zarejestrowanymi w niej aplikacjami.
  * Jeśli nie masz zarejestrowanych aplikacji, [dowiedz się, jak zarejestrować aplikacje za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logowanie do witryny Azure Portal i wybranie aplikacji

Przed skonfigurowaniem aplikacji wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**. 
1. W lewym okienku wybierz pozycję **rejestracje aplikacji**.
1. Znajdź i wybierz aplikację do skonfigurowania. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji lub główną stronę rejestracji.
1. Wykonaj kroki konfigurowania aplikacji pod kątem dostępu do internetowych interfejsów API:
    * [Dodawanie identyfikatorów URI przekierowania do aplikacji](#add-redirect-uris-to-your-application)
    * [Skonfiguruj ustawienia zaawansowane dla aplikacji](#configure-advanced-settings-for-your-application)
    * [Modyfikuj obsługiwane typy kont](#modify-supported-account-types)
    * [Dodawanie poświadczeń do aplikacji sieci Web](#add-credentials-to-your-web-application)
    * [Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Dodawanie identyfikatorów URI przekierowania do aplikacji

Aby dodać identyfikator URI przekierowania do aplikacji:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uwierzytelnianie**.
1. Aby dodać niestandardowy identyfikator URI dla internetowej i publicznej aplikacji klienckiej, wykonaj następujące kroki:
   1. Znajdź sekcję **Identyfikator URI przekierowania**.
   1. Wybierz typ tworzonej aplikacji: **Internetowa** lub **Klient publiczny (mobilny i klasyczny)** .
   1. Podaj identyfikator URI przekierowania dla aplikacji.
      * Dla aplikacji internetowych podaj podstawowy adres URL aplikacji. Na przykład ciąg `http://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą używać tego adresu URL, aby zalogować się do internetowej aplikacji klienckiej.
      * W przypadku aplikacji publicznych podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla swojej aplikacji, na przykład: `https://MyFirstApp`.

1. Aby wybrać z sugerowanych identyfikatorów URI przekierowania dla klientów publicznych (mobilnych, klasycznych), wykonaj następujące kroki:
    1. Znajdź sekcję **Identyfikatory URI przekierowania dla klientów publicznych (mobilnych, klasycznych)** z sugerowanymi identyfikatorami.
    1. Wybierz odpowiednie identyfikatory URI przekierowania dla aplikacji za pomocą pól wyboru. Możesz również wprowadzić niestandardowy identyfikator URI przekierowania. Jeśli nie masz pewności, co należy zrobić, zapoznaj się z dokumentacją biblioteki.

Istnieją pewne ograniczenia dotyczące identyfikatorów URI przekierowania. Dowiedz się więcej o [ograniczeniach i ograniczeniach identyfikatorów URI przekierowania](https://docs.microsoft.com/azure/active-directory/develop/reply-url).
> [!NOTE]
> Wypróbuj nowe ustawienia **uwierzytelniania** , w którym można skonfigurować ustawienia dla aplikacji na podstawie platformy lub urządzenia, które mają być docelowe.
>
> Aby wyświetlić ten widok, wybierz opcję **Wypróbuj nowe środowisko** z domyślnego widoku strony **uwierzytelniania** .
>
> ![Kliknij pozycję "Wypróbuj nowe środowisko", aby wyświetlić widok konfiguracji platformy](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Spowoduje to przejście do [strony nowej **konfiguracji platformy** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Skonfiguruj ustawienia zaawansowane dla aplikacji

W zależności od używanej aplikacji istnieją pewne dodatkowe ustawienia, które mogą być potrzebne do skonfigurowania, takie jak:

* **Adres URL wylogowywania**
* W przypadku aplikacji jednostronicowych można włączyć **niejawną zgodę** i wybrać tokeny, które mają zostać wystawione przez punkt końcowy autoryzacji.
* W przypadku aplikacji klasycznych, które korzystają z tokenów z zintegrowanego uwierzytelniania systemu Windows, przepływu kodu urządzenia lub nazwy użytkownika/hasła w sekcji **domyślny typ klienta** , skonfiguruj wartość **tak**dla ustawienia **Traktuj aplikację jako klienta publicznego** .
* Aby przeprowadzić integrację z usługą konto Microsoft przy użyciu zestawu Live SDK, skonfiguruj **obsługę zestawu SDK na żywo**. Nowe aplikacje nie wymagają tego ustawienia.
* **Domyślny typ klienta**

### <a name="modify-supported-account-types"></a>Modyfikuj obsługiwane typy kont

**Obsługiwane typy kont** określają, kto może korzystać z aplikacji lub uzyskać dostęp do interfejsu API.

Po [skonfigurowaniu obsługiwanych typów kont](quickstart-register-app.md) podczas pierwszej rejestracji aplikacji można zmienić to ustawienie tylko przy użyciu edytora manifestu aplikacji, jeśli:

* Można zmienić typy kont z **AzureADMyOrg** lub **AzureADMultipleOrgs** na **AzureADandPersonalMicrosoftAccount**lub na odwrót.
* Można zmienić typy kont z **AzureADMyOrg** na **AzureADMultipleOrgs**lub odwrotnie.

Aby zmienić obsługiwane typy kont dla istniejącej rejestracji aplikacji:

* Zobacz [Konfigurowanie manifestu aplikacji](reference-app-manifest.md) i aktualizowanie klucza `signInAudience`.

## <a name="configure-platform-settings-for-your-application"></a>Konfigurowanie ustawień platformy dla aplikacji

[![skonfigurować ustawienia dla aplikacji w oparciu o platformę lub urządzenie](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-small.png#lightbox)

Aby skonfigurować ustawienia aplikacji zależnie od platformy lub urządzenia, jesteś celem:

1. Na stronie **konfiguracje platformy** wybierz opcję **Dodaj platformę** , a następnie wybierz jedną z dostępnych opcji.

   ![Pokazuje stronę Konfigurowanie platform](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Wprowadź informacje o ustawieniach w oparciu o wybraną platformę.

   | Platforma                | Choices              | Ustawienia konfiguracji            |
   |-------------------------|----------------------|-----------------------------------|
   | **Aplikacje internetowe**    | **Sieć Web**              | Wprowadź **Identyfikator URI przekierowania** dla aplikacji. |
   | **Aplikacje mobilne** | **iOS**              | Wprowadź **Identyfikator pakietu**aplikacji, który można znaleźć w Xcode w oknie info. plist lub ustawienia kompilacji. Dodanie identyfikatora pakietu powoduje automatyczne utworzenie identyfikatora URI przekierowania dla aplikacji. |
   |                         | **Android**          | * Podaj **nazwę pakietu**aplikacji, którą można znaleźć w pliku pliku AndroidManifest. XML.<br/>* Wygeneruj i wprowadź **skrót sygnatury**. Dodanie skrótu podpisu powoduje automatyczne utworzenie identyfikatora URI przekierowania dla aplikacji.  |
   | **Komputery stacjonarne i urządzenia**   | **Komputery stacjonarne i urządzenia** | Obowiązkowe. Wybierz jeden z zalecanych **sugerowanych identyfikatorów URI przekierowania** , jeśli tworzysz aplikacje dla komputerów stacjonarnych i urządzeń.<br/>Obowiązkowe. Wprowadź **niestandardowy identyfikator URI przekierowania**, który jest używany jako lokalizacja, w której usługa Azure AD będzie przekierowywać użytkowników w odpowiedzi na żądania uwierzytelniania. Na przykład w przypadku aplikacji .NET Core, w których chcesz obsłużyć interakcję, użyj `https://localhost`. |

   > [!IMPORTANT]
   > W przypadku aplikacji mobilnych, które nie używają najnowszej biblioteki MSAL lub nie używają brokera, należy skonfigurować identyfikatory URI przekierowania dla tych aplikacji na **komputerach stacjonarnych i urządzeniach**.

1. W zależności od wybranej platformy mogą istnieć dodatkowe ustawienia, które można skonfigurować. W przypadku usługi **Web** Apps można:
    * Dodaj więcej identyfikatorów URI przekierowania
    * Skonfiguruj **niejawną zgodę** na wybór tokenów, które mają być wystawione przez punkt końcowy autoryzacji:
        * W przypadku aplikacji jednostronicowych wybierz zarówno **tokeny dostępu** , jak i **tokeny identyfikatora**
        * W przypadku aplikacji sieci Web wybierz pozycję **identyfikatory tokenów**

## <a name="add-credentials-to-your-web-application"></a>Dodawanie poświadczeń do aplikacji internetowej

Aby dodać poświadczenia do aplikacji internetowej:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Certyfikaty i wpisy tajne**.

1. Aby dodać certyfikat, wykonaj następujące kroki:

    1. Wybierz pozycję **Przekaż certyfikat**.
    1. Wybierz plik, który chcesz przekazać. Plik musi być plikiem typu cer, pem lub crt.
    1. Wybierz pozycję **Dodaj**.

1. Aby dodać wpis tajny klienta, wykonaj następujące kroki:

    1. Wybierz pozycję **Nowy wpis tajny klienta**.
    1. Dodaj opis wpisu tajnego klienta.
    1. Wybierz czas trwania.
    1. Wybierz pozycję **Dodaj**.

> [!NOTE]
> Po zapisaniu zmian w konfiguracji kolumna najdalej z prawej strony będzie zawierać wartość wpisu tajnego klienta. **Pamiętaj o skopiowaniu wartości** do użycia w kodzie aplikacji klienckiej, ponieważ nie będą one dostępne po opuszczeniu tej strony.

## <a name="add-permissions-to-access-web-apis"></a>Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API

Aby dodać uprawnienia na potrzeby uzyskiwania dostępu do interfejsów API zasobu z poziomu klienta:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uprawnienia interfejsu API**.
1. W sekcji **skonfigurowane uprawnienia** wybierz przycisk **Dodaj uprawnienie** .
1. Domyślnie widok umożliwia wybranie **interfejsów API firmy Microsoft**. Wybierz sekcję interfejsów API, która Cię interesuje:
    * **Interfejsy API firmy Microsoft** — umożliwia wybranie uprawnień dla interfejsów API firmy Microsoft, takich jak Microsoft Graph.
    * **Interfejsy API używane przez moją organizację** — umożliwia wybranie uprawnień dla interfejsów API, które zostały uwidocznione przez organizację, lub interfejsów API, z którymi jest zintegrowana Twoja organizacja.
    * **Moje interfejsy API** — umożliwia wybranie uprawnień dla interfejsów API, które zostały uwidocznione przez Ciebie.
1. Po wybraniu interfejsów API zobaczysz stronę **Żądanie uprawnień interfejsu API**. Jeśli interfejs API udostępnia uprawnienia delegowane i aplikacji, wybierz typ uprawnień, którego potrzebuje Twoja aplikacja.
1. Po zakończeniu wybierz pozycję **Dodaj uprawnienia**. Wrócisz do strony **Uprawnienia interfejsu API**, gdzie uprawnienia zostały zapisane i dodane do tabeli.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Informacje o uprawnieniach interfejsu API i interfejsie użytkownika zgody administratora

### <a name="configured-permissions"></a>Skonfigurowane uprawnienia

W tej sekcji przedstawiono uprawnienia, które zostały jawnie skonfigurowane w obiekcie aplikacji (uprawnienia \The, które są częścią wymaganej listy dostępu do zasobów aplikacji). Możesz dodawać lub usuwać uprawnienia z tej tabeli. Jako administrator możesz również udzielić/odwołać zgodę administratora na zestaw uprawnień interfejsu API lub poszczególnych uprawnień w tej sekcji.

### <a name="other-permissions-granted"></a>Inne uprawnienia przyznane

Jeśli aplikacja jest zarejestrowana w dzierżawie, może zostać wyświetlona dodatkowa sekcja zatytułowana **inne uprawnienia udzielone dzierżawcy**. W tej sekcji przedstawiono uprawnienia, które zostały przyznane dla dzierżawy, ale nie zostały jawnie skonfigurowane w obiekcie aplikacji (np. uprawnienia, które były dynamicznie wymagane i wysłane). Ta sekcja pojawia się tylko wtedy, gdy istnieje co najmniej jedno uprawnienie, które ma zastosowanie.

Można dodać zestaw uprawnień interfejsu API lub poszczególnych uprawnień, które są wyświetlane w tej sekcji, do sekcji **skonfigurowane uprawnienia** . Jako administrator możesz również odwołać zgodę administratora dla poszczególnych interfejsów API lub uprawnień w tej sekcji.

### <a name="admin-consent-button"></a>Przycisk zgody administratora

Jeśli aplikacja jest zarejestrowana w dzierżawie, zostanie wyświetlona przycisk **Udziel zgody administratora dla dzierżawy** . Ta wartość zostanie wyłączona, jeśli nie jesteś administratorem lub nie skonfigurowano żadnych uprawnień dla aplikacji.
Ten przycisk pozwala administratorowi łatwo udzielić zgody administratora na uprawnienia skonfigurowane dla aplikacji. Kliknięcie przycisku zgody administratora powoduje uruchomienie nowego okna z monitem o wyrażenie zgody, na którym są wyświetlane wszystkie skonfigurowane uprawnienia.

> [!NOTE]
> Istnieje opóźnienie między skonfigurowanymi uprawnieniami aplikacji i wyświetlanymi w monicie o zgodę. Jeśli nie widzisz wszystkich skonfigurowanych uprawnień w monicie o zgodę, zamknij ją i uruchom ponownie.

Jeśli masz uprawnienia, które zostały przyznane, ale nie zostały skonfigurowane, po kliknięciu przycisku zgody administratora zostanie wyświetlony monit o podjęcie decyzji o sposobie obsługi tych uprawnień. Można je dodać do skonfigurowanych uprawnień lub można je usunąć.

Monit o zgodę zapewnia opcję **akceptacji** lub **anulowania**. Jeśli wybierzesz pozycję **Akceptuj**, przyznano zgodę na administrowanie. W przypadku wybrania opcji **Anuluj**zgoda administratora nie zostanie udzielona i zostanie wyświetlony komunikat o błędzie informujący o odrzuceniu zgody.

> [!NOTE]
> Istnieje opóźnienie między udzieleniem zgody administratora (wybranie opcji **Akceptuj** w monicie o zgodę) i statusem zgody administratora w interfejsie użytkownika.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych powiązanych przewodników Szybki start dotyczących zarządzania aplikacjami:

* [Rejestrowanie aplikacji za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md)
* [Konfigurowanie aplikacji w celu uwidocznienia internetowych interfejsów API](quickstart-configure-app-expose-web-apis.md)
* [Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)
* [Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft](quickstart-remove-app.md)

Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

Aby dowiedzieć się więcej o wytycznych dotyczących oznaczania marką, które należy stosować podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).
