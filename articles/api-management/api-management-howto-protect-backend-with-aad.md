---
title: Ochrona interfejsu API przy użyciu protokołu OAuth 2,0 z usługą AAD i API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak chronić zaplecze interfejsu API sieci Web przy użyciu Azure Active Directory i API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 8b396b782c1254b3229aeeb8e51b61cc744d6318
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190364"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrona interfejsu API przy użyciu protokołu OAuth 2,0 z Azure Active Directory i API Management

W tym przewodniku opisano sposób konfigurowania wystąpienia API Management platformy Azure w celu ochrony interfejsu API przy użyciu protokołu OAuth 2,0 z usługą Azure Active Directory (Azure AD). 

> [!NOTE]
> Ta funkcja jest dostępna w warstwach **Developer**, **Standard** i **Premium** API Management.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, musisz mieć:
* Wystąpienie API Management
* Publikowany interfejs API korzystający z wystąpienia API Management
* Dzierżawa usługi Azure AD

## <a name="overview"></a>Omówienie

Poniżej przedstawiono krótkie omówienie kroków:

1. Zarejestrowanie aplikacji (zaplecza aplikacji) w usłudze Azure AD w celu reprezentowania interfejsu API.
2. Zarejestruj inną aplikację (klienta) w usłudze Azure AD, aby reprezentować aplikację kliencką, która musi wywołać interfejs API.
3. W usłudze Azure AD Udziel uprawnień zezwalających aplikacji klienta na wywoływanie zaplecza — aplikacji.
4. Skonfiguruj konsolę dewelopera do wywoływania interfejsu API przy użyciu autoryzacji użytkownika OAuth 2,0.
5. Dodaj zasady **walidacji-JWT** , aby sprawdzić poprawność tokenu OAuth dla każdego żądania przychodzącego.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Rejestrowanie aplikacji w usłudze Azure AD do reprezentowania interfejsu API

Aby chronić interfejs API za pomocą usługi Azure AD, pierwszy krok polega na zarejestrowaniu aplikacji w usłudze Azure AD, która reprezentuje interfejs API. 

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarejestrować aplikację. Wyszukaj i wybierz pozycję **rejestracje aplikacji**.

1. Wybierz pozycję **Nowa rejestracja**. 

1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji: 
    - W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład *zapleczu*. 
    - W sekcji **obsługiwane typy kont** wybierz opcję, która odpowiada Twojemu scenariuszowi. 

1. Pozostaw pustą sekcję **Identyfikator URI przekierowania** .

1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację. 

1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą.

1. Wybierz opcję **Uwidocznij interfejs API** i ustaw **Identyfikator URI aplikacji** z wartością domyślną. Zapisz tę wartość w późniejszym czasie.

1. Wybierz przycisk **Dodaj zakres** , aby wyświetlić stronę **Dodawanie zakresu** . Następnie utwórz nowy zakres, który jest obsługiwany przez interfejs API (na przykład `Files.Read`). Na koniec wybierz przycisk **Dodaj zakres** , aby utworzyć zakres. Powtórz ten krok, aby dodać wszystkie zakresy obsługiwane przez interfejs API.

1. Po utworzeniu zakresów należy zanotować je do użycia w kolejnym kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Zarejestruj inną aplikację w usłudze Azure AD, aby reprezentować aplikację kliencką

Każda aplikacja kliencka, która wywołuje interfejs API, musi zostać zarejestrowana jako aplikacja w usłudze Azure AD. W tym przykładzie aplikacja kliencka jest konsolą dewelopera w portalu API Management developer. Poniżej przedstawiono sposób rejestrowania innej aplikacji w usłudze Azure AD do reprezentowania konsoli dewelopera.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarejestrować aplikację. Wyszukaj i wybierz pozycję **rejestracje aplikacji**.

1. Wybierz pozycję **Nowa rejestracja**.

1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji: 
    - W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład *klient-aplikacja*. 
    - W sekcji **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacji (dowolny katalog usługi Azure AD — wielodostępny)** . 

1. W sekcji **Identyfikator URI przekierowania** wybierz pozycję `Web` i wprowadź adres URL `https://contoso5.portal.azure-api.net/signin`.

1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację. 

1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą.

Teraz Utwórz klucz tajny klienta dla tej aplikacji do użycia w kolejnym kroku.

1. Z listy stron dla aplikacji klienckiej wybierz pozycję **certyfikaty & wpisy tajne**i wybierz pozycję **nowy klucz tajny klienta**.

1. W obszarze **Dodaj wpis tajny klienta**Podaj **Opis**. Wybierz czas wygaśnięcia klucza i wybierz pozycję **Dodaj**.

Po utworzeniu wpisu tajnego należy zwrócić uwagę na wartość klucza do użycia w kolejnym kroku. 

## <a name="grant-permissions-in-azure-ad"></a>Przyznawanie uprawnień w usłudze Azure AD

Po zarejestrowaniu dwóch aplikacji do reprezentowania interfejsu API i konsoli dewelopera należy przyznać uprawnienia, aby umożliwić aplikacji klienta wywoływanie aplikacji zaplecza.  

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby udzielić uprawnień do aplikacji klienckiej. Wyszukaj i wybierz pozycję **rejestracje aplikacji**.

1. Wybierz aplikację kliencką. Następnie na liście stron dla aplikacji wybierz pozycję **uprawnienia interfejsu API**.

1. Wybierz pozycję **Dodaj uprawnienie**.

1. W obszarze **Wybierz interfejs API**wybierz pozycję **Moje interfejsy API**, a następnie Znajdź i wybierz swoją aplikację zaplecza.

1. W obszarze **delegowane uprawnienia**wybierz odpowiednie uprawnienia dla aplikacji zaplecza, a następnie wybierz pozycję **Dodaj uprawnienia**.

1. Opcjonalnie na stronie **uprawnienia interfejsu API** wybierz opcję **Przyznaj zgodę administratora na \<swoją nazwę dzierżawy >** , aby przyznać zgodę w imieniu wszystkich użytkowników w tym katalogu. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Włącz autoryzację użytkownika OAuth 2,0 w konsoli dewelopera

W tym momencie zostały utworzone aplikacje w usłudze Azure AD i przyznano im odpowiednie uprawnienia, aby umożliwić aplikacji klienta wywoływanie zaplecza — aplikacji. 

W tym przykładzie Konsola dewelopera jest aplikacją Client-App. W poniższych krokach opisano sposób włączania autoryzacji użytkownika OAuth 2,0 w konsoli dewelopera. 

1. W Azure Portal przejdź do wystąpienia API Management.

1. Wybierz pozycję **OAuth 2,0** > **Dodaj**.

1. Podaj **nazwę wyświetlaną** i **Opis**.

1. W polu **adres URL strony rejestracji klienta**wprowadź wartość symbolu zastępczego, taką jak `http://localhost`. **Adres URL strony rejestracji klienta** wskazuje stronę, za pomocą której użytkownicy mogą tworzyć i konfigurować własne konta dla dostawców OAuth 2,0 obsługujących ten program. W tym przykładzie użytkownicy nie tworzą i nie konfigurują własnych kont, więc zamiast tego należy użyć symbolu zastępczego.

1. W obszarze **typy przyzwoleń**wybierz pozycję **kod autoryzacji**.

1. Określ **adres URL punktu końcowego autoryzacji** i **adres URL punktu końcowego tokenu**. Pobierz te wartości ze strony **punkty końcowe** w dzierżawie usługi Azure AD. Przejdź ponownie do strony **rejestracje aplikacji** i wybierz **punkty końcowe**.


1. Skopiuj **punkt końcowy autoryzacji OAuth 2,0**i wklej go w polu tekstowym **adres URL punktu końcowego autoryzacji** . Wybierz pozycję **post** w obszarze Metoda żądania autoryzacji.

1. Skopiuj **punkt końcowy tokenu OAuth 2,0**i wklej go w polu tekstowym **adres URL punktu końcowego tokenu** . 

    >[!IMPORTANT]
    > Można użyć punktów końcowych **V1** lub **v2** . Jednak w zależności od wybranej wersji Poniższy krok będzie różny. Zalecamy korzystanie z punktów końcowych w wersji 2. 

1. Jeśli używasz punktów końcowych **V1** , Dodaj parametr treści o nazwie **Resource**. Dla wartości tego parametru Użyj **identyfikatora aplikacji** dla aplikacji zaplecza. 

1. Jeśli korzystasz z punktów końcowych **v2** , użyj zakresu utworzonego dla aplikacji zaplecza w polu **zakres domyślny** .

1. Następnie określ poświadczenia klienta. Są to poświadczenia dla aplikacji klient.

1. W polu **Identyfikator klienta**Użyj **identyfikatora aplikacji** klienta.

1. W przypadku **wpisu tajnego klienta**Użyj klucza utworzonego dla aplikacji klient — wcześniej. 

1. Bezpośrednio po kluczu tajnym klienta jest **redirect_url** dla typu przydzielenia kodu autoryzacji. Zanotuj ten adres URL.

1. Wybierz pozycję **Utwórz**.

1. Wróć do aplikacji klienckiej i wybierz pozycję **uwierzytelnianie**.

1. W obszarze **identyfikatory URI przekierowania**wybierz typ jako **Web**, wklej **redirect_url** w obszarze **Identyfikator URI przekierowania**, a następnie Zapisz.

Po skonfigurowaniu serwera autoryzacji uwierzytelniania OAuth 2,0 Konsola dewelopera może uzyskać tokeny dostępu z usługi Azure AD. 

Następnym krokiem jest włączenie autoryzacji użytkownika OAuth 2,0 dla interfejsu API. Dzięki temu konsola deweloper może wiedzieć, że musi uzyskać token dostępu w imieniu użytkownika, przed nawiązaniem połączenia z interfejsem API.

1. Przejdź do wystąpienia API Management i przejdź do **interfejsów API**.

2. Wybierz interfejs API, który ma być chroniony. Można na przykład użyć `Echo API`.

3. Przejdź do obszaru **Settings** (Ustawienia).

4. W obszarze **zabezpieczenia**wybierz pozycję **OAuth 2,0**i wybierz skonfigurowany wcześniej serwer OAuth 2,0. 

5. Wybierz pozycję **Zapisz**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Pomyślnie Wywołaj interfejs API z portalu dla deweloperów

> [!NOTE]
> Ta sekcja nie ma zastosowania do warstwy **zużycia** , która nie obsługuje portalu dla deweloperów.

Teraz, gdy autoryzacja użytkownika OAuth 2,0 jest włączona w interfejsie API, Konsola dewelopera uzyska token dostępu w imieniu użytkownika przed wywołaniem interfejsu API.

1. Przejdź do dowolnej operacji w obszarze interfejsu API w portalu dla deweloperów, a **następnie**wybierz pozycję Wypróbuj. Spowoduje to przeniesienie do konsoli dewelopera.

2. Zanotuj nowy element w sekcji **autoryzacja** odpowiadający serwerowi autoryzacji, który właśnie został dodany.

3. Z listy rozwijanej autoryzacja wybierz pozycję **kod autoryzacji** , a zostanie wyświetlony monit o zalogowanie się do dzierżawy usługi Azure AD. Jeśli użytkownik jest już zalogowany przy użyciu konta, może nie zostać wyświetlony monit.

4. Po pomyślnym zalogowaniu do żądania zostanie dodany nagłówek `Authorization` z tokenem dostępu z usługi Azure AD. Poniżej znajduje się przykładowy token (kodowany algorytmem Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Wybierz pozycję **Wyślij**, aby pomyślnie wywołać interfejs API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurowanie zasad sprawdzania poprawności tokenu JWT w celu wstępnego autoryzowania żądań

W tym momencie, gdy użytkownik próbuje wykonać wywołanie z poziomu konsoli dewelopera, użytkownik zostanie poproszony o zalogowanie się. Konsola dewelopera uzyskuje token dostępu w imieniu użytkownika i zawiera token w żądaniu wykonywanym w interfejsie API.

Jeśli jednak ktoś dzwoni do interfejsu API bez tokenu lub z nieprawidłowym tokenem? Na przykład spróbuj wywołać interfejs API bez nagłówka `Authorization`, a połączenie będzie nadal się powtarzać. Przyczyną jest to, że API Management nie sprawdza poprawności tokenu dostępu w tym momencie. Po prostu przekazuje nagłówek `Authorization` do interfejsu API zaplecza.

Za pomocą [walidacji zasad JWT](api-management-access-restriction-policies.md#ValidateJWT) można wstępnie autoryzować żądania w API Management, sprawdzając tokeny dostępu dla każdego żądania przychodzącego. Jeśli żądanie nie ma prawidłowego tokenu, API Management ją zablokuje. Na przykład Dodaj następujące zasady do sekcji zasady `<inbound>` w `Echo API`. Sprawdza to w tokenie dostępu i zwraca komunikat o błędzie, jeśli token jest nieprawidłowy. Aby uzyskać informacje na temat konfigurowania zasad, zobacz [Ustawianie lub edytowanie zasad](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Ten adres URL `openid-config` odnosi się do punktu końcowego v1. W przypadku punktu końcowego `openid-config`v2 Użyj `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Tworzenie aplikacji do wywoływania interfejsu API

W tym przewodniku użyto konsoli dewelopera w API Management jako Przykładowa aplikacja kliencka, która wywoła `Echo API` chroniony przez OAuth 2,0. Aby dowiedzieć się więcej na temat tworzenia aplikacji i implementowania protokołu OAuth 2,0, zobacz [Azure Active Directory przykładów kodu](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [Azure Active Directory i OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Poznaj więcej [filmów wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) dotyczących API Management.
* Aby poznać inne sposoby zabezpieczenia usługi zaplecza, zobacz [uwierzytelnianie wzajemne certyfikatów](api-management-howto-mutual-certificates.md).

* [Utwórz wystąpienie usługi API Management](get-started-create-service-instance.md).

* [Zarządzanie pierwszym interfejsem API](import-and-publish.md).
