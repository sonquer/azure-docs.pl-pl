---
title: Niejawny przepływ uwierzytelniania OAuth 2,0 — platforma tożsamości firmy Microsoft | Azure
description: Zabezpiecz aplikacje jednostronicowe przy użyciu niejawnego przepływu platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7f27ad2adc5d4abf2b5ec993b3398ebf1370f52
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159679"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform i niejawny przepływ dotacji

Za pomocą punktu końcowego platformy tożsamości firmy Microsoft możesz zalogować użytkowników do aplikacji jednostronicowych zarówno z kontami osobistymi, jak i służbowymi firmy Microsoft. Jednostronicowe i inne aplikacje JavaScript, które są uruchamiane głównie w przeglądarce, mają kilka interesujących wyzwań podczas uwierzytelniania:

* Charakterystyki zabezpieczeń tych aplikacji różnią się znacznie od tradycyjnych aplikacji sieci Web opartych na serwerze.
* Wiele serwerów autoryzacji i dostawców tożsamości nie obsługuje żądań CORS.
* Przeglądarka pełnej strony przekieruje się do środowiska użytkownika.

W przypadku tych aplikacji (AngularJS, wpływ. js, reaguje. js itd.) platforma Microsoft Identity platform obsługuje niejawny przepływ OAuth 2,0. Niejawny przepływ jest opisany w [specyfikacji OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Główną korzyścią jest umożliwienie aplikacji uzyskiwania tokenów z platformy tożsamości firmy Microsoft bez konieczności wymiany poświadczeń serwera wewnętrznej bazy danych. Dzięki temu aplikacja może logować się do użytkownika, obsługiwać sesję i uzyskiwać tokeny do innych interfejsów API sieci Web w kodzie JavaScript klienta. Istnieje kilka ważnych zagadnień związanych z zabezpieczeniami, które należy wziąć pod uwagę podczas korzystania z niejawnego przepływu w odróżnieniu od [personifikacji](https://tools.ietf.org/html/rfc6749#section-10.3) [klientów](https://tools.ietf.org/html/rfc6749#section-10.3) i użytkowników.

W tym artykule opisano, jak programować bezpośrednio w odniesieniu do protokołu w aplikacji.  Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast [uzyskiwać tokeny i wywoływać zabezpieczone interfejsy API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z MSAL](sample-v2-code.md).

Jeśli jednak wolisz zrezygnować z używania biblioteki w aplikacji jednostronicowej i wysyłać komunikaty protokołu samodzielnie, wykonaj poniższe czynności.

> [!NOTE]
> Nie wszystkie scenariusze i funkcje Azure Active Directory (Azure AD) są obsługiwane przez punkt końcowy platformy tożsamości firmy Microsoft. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Odpowiednie scenariusze dla niejawnego udzielenia OAuth2

Specyfikacja OAuth2 deklaruje, że niejawny przydział został opracowany w celu włączenia aplikacji agenta użytkownika — to znaczy, że aplikacje JavaScript działają w przeglądarce. Definiowanie cech takich aplikacji polega na tym, że kod JavaScript jest używany do uzyskiwania dostępu do zasobów serwera (zazwyczaj internetowego interfejsu API) i odpowiednio do aktualizowania środowiska użytkownika aplikacji. Pomyśl o aplikacjach, takich jak Gmail lub Outlook Dostęp w sieci Web: po wybraniu wiadomości ze skrzynki odbiorczej tylko panel wizualizacji komunikatów zmieni się, aby wyświetlić nowy wybór, podczas gdy reszta strony pozostanie niezmodyfikowana. Ta cecha różni się od tradycyjnych aplikacji sieci Web opartych na przekierowaniu, w przypadku których każda interakcja użytkownika powoduje pełne ogłaszanie zwrotne i pełne strony nowej odpowiedzi serwera.

Aplikacje korzystające z podejścia opartego na języku JavaScript do jego skrajnych metod są nazywane jednostronicowymi aplikacjami lub aplikacji jednostronicowych. Pomysłem jest to, że te aplikacje obsługują tylko początkową stronę HTML i powiązane z nią JavaScript, a wszystkie kolejne interakcje są realizowane przez wywołania interfejsu API sieci Web wykonywane za pośrednictwem języka JavaScript. Jednak podejście hybrydowe, gdzie aplikacja jest oparta na odświeżeniu, ale wykonuje sporadyczne wywołania JS, nie są nietypowe — dyskusja dotycząca niejawnego użycia przepływu jest również istotna dla tych.

Aplikacje oparte na przekierowaniu zazwyczaj zabezpieczają swoje żądania za pośrednictwem plików cookie, jednak takie podejście nie działa prawidłowo w przypadku aplikacji JavaScript. Pliki cookie działają tylko względem domeny, dla której zostały wygenerowane, podczas gdy wywołania JavaScript mogą być kierowane do innych domen. W rzeczywistości często będzie to miało miejsce: należy wziąć pod uwagę aplikacje wywołujące Microsoft Graph interfejs API, interfejs API pakietu Office, interfejs API platformy Azure — wszystkie znajdujące się poza domeną, w której jest obsługiwana aplikacja. Rosnąca tendencja dla aplikacji języka JavaScript nie ma żadnego zaplecza, co polega na 100% od interfejsów API sieci Web innych firm w celu zaimplementowania funkcji firmy.

Obecnie preferowaną metodą ochrony wywołań interfejsu API sieci Web jest użycie metody tokena okaziciela OAuth2, gdzie każde wywołanie towarzyszy token dostępu OAuth2. Internetowy interfejs API bada token dostępu przychodzącego i, jeśli znajdzie w nim wymagane zakresy, udziela dostępu do wymaganej operacji. Niejawny przepływ zapewnia wygodny mechanizm dla aplikacji języka JavaScript do uzyskiwania tokenów dostępu dla internetowego interfejsu API, oferując wiele korzyści w odniesieniu do plików cookie:

* Tokeny mogą być niezawodnie uzyskiwane bez konieczności wykonywania wywołań między źródłami — obowiązkowa Rejestracja identyfikatora URI przekierowania, do którego tokeny zwracają gwarancje, że tokeny nie są umieszczane
* Aplikacje JavaScript mogą uzyskać dowolną liczbę tokenów dostępu dla tylu interfejsów API sieci Web, które są docelowe — bez ograniczeń dla domen
* Funkcje HTML5, takie jak sesja lub magazyn lokalny, zapewniają pełną kontrolę nad buforowaniem tokenów i zarządzaniem okresem istnienia, natomiast zarządzanie plikami cookie jest nieprzezroczyste dla aplikacji
* Tokeny dostępu nie są narażone na ataki podrabiania żądań między lokacjami (CSRF)

Niejawny przepływ wydawania nie wydaje tokenów odświeżania, głównie ze względów bezpieczeństwa. Token odświeżania nie jest tak wąski, jak tokeny dostępu, umożliwiając znacznie większą moc, co oznacza znacznie więcej uszkodzeń w przypadku, gdy jest on wycieka. W niejawnym przepływie tokeny są dostarczane w adresie URL, dlatego ryzyko przechwycenia jest większe niż w przypadku przyznania kodu autoryzacji.

Jednak aplikacja JavaScript ma inny mechanizm do usuwania tokenów dostępu bez wielokrotnego monitowania użytkownika o poświadczenia. Aplikacja może używać ukrytego elementu IFRAME do wykonywania nowych żądań tokenów względem punktu końcowego autoryzacji usługi Azure AD: o ile przeglądarka nadal ma aktywną sesję (odczyt: zawiera plik cookie sesji) względem domeny usługi Azure AD, żądanie uwierzytelnienia może pomyślnie występuje bez konieczności interakcji z użytkownikiem.

Ten model przyznaje aplikacji JavaScript możliwość niezależnego odnawiania tokenów dostępu, a nawet pozyskiwania nowych dla nowego interfejsu API (pod warunkiem, że użytkownik wcześniej wyraził zgodę na ich). Pozwala to uniknąć dodawania, utrzymywania i ochrony artefaktu o wysokiej wartości, takiego jak token odświeżania. Artefakt, który sprawia, że możliwe jest odnowienie dyskretne, plik cookie sesji usługi Azure AD jest zarządzany poza aplikacją. Inną zaletą tego podejścia jest to, że użytkownik może wylogować się z usługi Azure AD przy użyciu dowolnych aplikacji zalogowanych do usługi Azure AD uruchomionych na dowolnej z kart przeglądarki. Spowoduje to usunięcie pliku cookie sesji usługi Azure AD, a aplikacja JavaScript automatycznie utraci możliwość odnowienia tokenów dla zalogowanego użytkownika.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Czy niejawne przyznanie jest odpowiednie dla mojej aplikacji?

Niejawne przyznanie przedstawia większe ryzyko niż inne dotacje, a obszary, do których należy zwrócić uwagę, są dobrze udokumentowane (na przykład [nieprawidłowe użycie tokenu dostępu do personifikacji właściciela zasobu w przepływie niejawnym] [OAuth2-spec-niejawne użycie] i [model zagrożeń OAuth 2,0) Zagadnienia dotyczące zabezpieczeń] [OAuth2-Threat-model-and-Security-implikacje]). Jednak profil wyższego ryzyka jest w dużym stopniu spowodowany tym, że ma to na celu umożliwienie aplikacjom wykonującym kod aktywny, obsługiwanym przez zasób zdalny w przeglądarce. Jeśli planujesz architekturę SPA, nie ma składników zaplecza ani nie ma potrzeby wywoływania internetowego interfejsu API za pośrednictwem języka JavaScript, zaleca się użycie niejawnego przepływu do pozyskiwania tokenów.

Jeśli aplikacja jest klientem macierzystym, niejawny przepływ nie jest doskonałym rozwiązaniem. Brak pliku cookie sesji usługi Azure AD w kontekście natywnego klienta pozbawia aplikację od środków w celu utrzymania długotrwałych sesji. Oznacza to, że aplikacja będzie kilkukrotnie monitować użytkownika o uzyskanie tokenów dostępu dla nowych zasobów.

Jeśli tworzysz aplikację sieci Web, która zawiera zaplecze i zużywa interfejs API z kodu wewnętrznej bazy danych, niejawny przepływ nie jest również dobrym dopasowaniem. Inne dotacje zapewniają znacznie większą moc. Na przykład przyznanie poświadczeń klienta OAuth2 umożliwia uzyskanie tokenów, które odzwierciedlają uprawnienia przypisane do samej aplikacji, w przeciwieństwie do delegowania użytkowników. Oznacza to, że klient ma możliwość utrzymania dostępu programowego do zasobów nawet wtedy, gdy użytkownik nie jest aktywnie zaangażowany w sesję i tak dalej. Nie tylko, ale takie dotacje dają wyższe gwarancje bezpieczeństwa. Na przykład tokeny dostępu nigdy nie są przesyłane przez przeglądarkę użytkownika, nie są one ryzykowne w historii przeglądarki i tak dalej. Aplikacja kliencka może także przeprowadzić silne uwierzytelnianie podczas żądania tokenu.

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono, jak wygląda cały niejawny przepływ logowania, a kolejne sekcje opisują każdy krok bardziej szczegółowo.

![Diagram przedstawiający niejawny przepływ logowania](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Aby najpierw podpisać użytkownika w aplikacji, możesz wysłać żądanie uwierzytelniania [OpenID Connect Connect](v2-protocols-oidc.md) i uzyskać `id_token` z punktu końcowego platformy tożsamości firmy Microsoft.

> [!IMPORTANT]
> Aby pomyślnie zażądać tokenu identyfikatora i/lub tokenu dostępu, Rejestracja aplikacji na stronie [Azure Portal-rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) musi mieć odpowiedni niejawny przepływ, który jest włączony, wybierając **tokeny identyfikatora** i **tokeny dostępu** lub w sekcji **niejawne udzielenie** . Jeśli nie jest włączona, zostanie zwrócony błąd `unsupported_response`: **podana wartość parametru wejściowego "response_type" nie jest dozwolona dla tego klienta. Oczekiwana wartość to "Code"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Aby przetestować Logowanie przy użyciu niejawnego przepływu, kliknij przycisk <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Po zalogowaniu przeglądarka powinna zostać przekierowana do `https://localhost/myapp/` przy użyciu `id_token` na pasku adresu.
>

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` | wymagane |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje o protokole](active-directory-v2-protocols.md#endpoints). |
| `client_id` | wymagane | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `response_type` | wymagane |Musi zawierać `id_token` do logowania za OpenID Connect Connect. Może również zawierać `token`response_type. Użycie `token` w tym miejscu umożliwi aplikacji otrzymywanie tokenu dostępu od razu od autoryzowanego punktu końcowego bez konieczności podawania drugiego żądania do autoryzowanego punktu końcowego. Jeśli używasz response_type `token`, parametr `scope` musi zawierać zakres wskazujący zasób, dla którego ma zostać wystawiony token (na przykład user. Read on Microsoft Graph).  |
| `redirect_uri` | Rekomendowane |Redirect_uri aplikacji, w której odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z redirect_uris zarejestrowanego w portalu, z wyjątkiem tego, że musi on być zakodowany w adresie URL. |
| `scope` | wymagane |Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md). W przypadku OpenID Connect Connect (id_tokens) musi zawierać zakres `openid`, który tłumaczy na uprawnienie "Logowanie do Ciebie" w interfejsie użytkownika wyrażania zgody. Opcjonalnie możesz również uwzględnić zakresy `email` i `profile`, aby uzyskać dostęp do dodatkowych danych użytkownika. W przypadku żądania zgody na różne zasoby można również uwzględnić inne zakresy w żądaniu. |
| `response_mode` | obowiązkowe |Określa metodę, która ma zostać użyta do wysłania zwróconego tokenu z powrotem do aplikacji. Domyślnie kwerenda dotyczy tylko tokenu dostępu, ale fragmentu, jeśli żądanie zawiera id_token. |
| `state` | Rekomendowane |Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu [zapobiegania atakom na fałszerstwo żądań między witrynami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |
| `nonce` | wymagane |Wartość uwzględniona w żądaniu wygenerowanym przez aplikację, która zostanie uwzględniona w uzyskanym id_token jako jako żądanie. Następnie aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Wartość jest zazwyczaj losowo losowym, unikatowym ciągiem, który może służyć do identyfikowania pochodzenia żądania. Wymagane tylko wtedy, gdy zażądano id_token. |
| `prompt` | obowiązkowe |Wskazuje typ interakcji użytkownika, która jest wymagana. Jedyne prawidłowe wartości w tym momencie to "login'", "none", "select_account" i "zgody". `prompt=login` wymusić użytkownikowi wprowadzanie poświadczeń dla tego żądania, negację logowania jednokrotnego. `prompt=none` jest przeciwieństwem, dzięki czemu użytkownik nie będzie wyświetlany z żadnym interaktywnym monitem. Jeśli żądanie nie może zostać zakończone dyskretnie przy użyciu logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwróci błąd. `prompt=select_account` wysyła użytkownika do selektora konta, w którym zostaną wyświetlone wszystkie konta zapamiętane w sesji. `prompt=consent` wyzwolenie okna dialogowego zgody OAuth po zalogowaniu się użytkownika, poproszenie użytkownika o przyznanie uprawnień do aplikacji. |
| `login_hint`  |obowiązkowe |Może służyć do wstępnego wypełniania pola Nazwa użytkownika/adres e-mail na stronie logowania dla użytkownika, jeśli znana jest jego nazwa użytkownika przed czasem. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania, ponieważ już wyodrębnili nazwę użytkownika z poprzedniego logowania przy użyciu `preferred_username`.|
| `domain_hint` | obowiązkowe |W przypadku pominięcia zostanie pominięty proces odnajdywania na podstawie poczty e-mail, który użytkownik przejdzie na stronie logowania, co prowadzi do nieco bardziej usprawnionego środowiska użytkownika. Jest to często używane w przypadku aplikacji biznesowych, które działają w ramach jednej dzierżawy, gdzie będą podawać nazwę domeny w ramach danej dzierżawy.  Spowoduje to przekazanie tego użytkownika do dostawcy federacyjnego dla tej dzierżawy.  Należy zauważyć, że uniemożliwi to Gościom logowanie do tej aplikacji.  |

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft sprawdzi również, czy użytkownik wyraził zgodę na uprawnienia wskazane w `scope` parametr zapytania. Jeśli użytkownik wyraził zgodę na **żadne** z tych uprawnień, skontaktuje się z użytkownikiem, aby zalecić wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [uprawnienia, zgoda i aplikacje z wieloma dzierżawcami](v2-permissions-and-consent.md).

Gdy użytkownik uwierzytelni się i udzieli zgody, punkt końcowy platformy tożsamości firmy Microsoft zwróci odpowiedź do aplikacji we wskazanym `redirect_uri`przy użyciu metody określonej w parametrze `response_mode`.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź przy użyciu `response_mode=fragment` i `response_type=id_token+token` wygląda następująco (z podziałami wierszy na potrzeby czytelności):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Opis |
| --- | --- |
| `access_token` |Uwzględnione, jeśli `response_type` zawiera `token`. Token dostępu, którego żądała aplikacja. Token dostępu nie powinien być zdekodowany lub w inny sposób sprawdzany, powinien być traktowany jako ciąg nieprzezroczysty. |
| `token_type` |Uwzględnione, jeśli `response_type` zawiera `token`. Będzie zawsze `Bearer`. |
| `expires_in`|Uwzględnione, jeśli `response_type` zawiera `token`. Wskazuje liczbę sekund ważności tokenu na potrzeby buforowania. |
| `scope` |Uwzględnione, jeśli `response_type` zawiera `token`. Wskazuje zakresy, dla których access_token będzie prawidłowy. Nie może zawierać wszystkich żądanych zakresów, jeśli nie mają zastosowania do użytkownika (w przypadku zakresów tylko w usłudze Azure AD, które są wymagane w przypadku logowania za pomocą konta osobistego). |
| `id_token` | Podpisany token sieci Web JSON (JWT). Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, zobacz [`id_token reference`](id-tokens.md). <br> **Uwaga:** Dostępne tylko wtedy, gdy zażądano zakresu `openid`. |
| `state` |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri`, aby aplikacja mogła je odpowiednio obsłużyć:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Uzyskiwanie tokenów dostępu w trybie dyskretnym w tle

Teraz, gdy użytkownik został zarejestrowany w aplikacji jednostronicowej, można w trybie dyskretnym uzyskać tokeny dostępu do wywoływania interfejsów API sieci Web zabezpieczonych przez platformę tożsamości firmy Microsoft, takich jak [Microsoft Graph](https://developer.microsoft.com/graph). Nawet jeśli token został już odebrany przy użyciu response_type `token`, można użyć tej metody, aby uzyskać tokeny dla dodatkowych zasobów bez konieczności przekierowania użytkownika w celu ponownego zalogowania.

W normalnym przepływie OpenID Connect Connect/OAuth należy to zrobić, wysyłając żądanie do punktu końcowego platformy tożsamości firmy Microsoft `/token`. Jednak punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje żądań CORS, dlatego w celu uzyskania i odświeżenia tokenów nie można wykonać wywołań AJAX. Zamiast tego można użyć niejawnego przepływu w ukrytym elemencie iframe, aby uzyskać nowe tokeny dla innych interfejsów API sieci Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Aby uzyskać szczegółowe informacje na temat parametrów zapytania w adresie URL, zobacz [Wysyłanie żądania logowania](#send-the-sign-in-request).

> [!TIP]
> Spróbuj skopiować & wklejając poniższe żądanie do karty przeglądarki! (Nie zapomnij zastąpić wartości `login_hint` poprawną wartością dla użytkownika)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Za pomocą `prompt=none` parametru to żądanie zakończy się pomyślnie lub zakończy się niepowodzeniem, a następnie wróć do aplikacji. Pomyślna odpowiedź zostanie wysłana do aplikacji we wskazanym `redirect_uri`przy użyciu metody określonej w parametrze `response_mode`.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Pomyślna odpowiedź przy użyciu `response_mode=fragment` wygląda następująco:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametr | Opis |
| --- | --- |
| `access_token` |Uwzględnione, jeśli `response_type` zawiera `token`. Token dostępu, którego żądał aplikacja, w tym przypadku Microsoft Graph. Token dostępu nie powinien być zdekodowany lub w inny sposób sprawdzany, powinien być traktowany jako ciąg nieprzezroczysty. |
| `token_type` | Będzie zawsze `Bearer`. |
| `expires_in` | Wskazuje liczbę sekund ważności tokenu na potrzeby buforowania. |
| `scope` | Wskazuje zakresy, dla których access_token będzie prawidłowy. Nie może zawierać wszystkich żądanych zakresów, jeśli nie mają zastosowania do użytkownika (w przypadku zakresów tylko w usłudze Azure AD, które są wymagane w przypadku logowania za pomocą konta osobistego). |
| `id_token` | Podpisany token sieci Web JSON (JWT). Uwzględnione, jeśli `response_type` zawiera `id_token`. Aplikacja może zdekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich dla żadnych ograniczeń autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, zobacz [informacje dotyczące`id_token`](id-tokens.md). <br> **Uwaga:** Dostępne tylko wtedy, gdy zażądano zakresu `openid`. |
| `state` |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri`, aby aplikacja mogła je odpowiednio obsłużyć. W przypadku `prompt=none`oczekiwany błąd to:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |

Jeśli ten błąd wystąpi w żądaniu iframe, użytkownik musi interaktywnie zalogować się ponownie, aby pobrać nowy token. Możesz obsłużyć ten przypadek w dowolny sposób, który ma sens dla aplikacji.

## <a name="refreshing-tokens"></a>Odświeżanie tokenów

Niejawne przyznanie nie zapewnia tokenów odświeżania. Zarówno `id_token`s, jak i `access_token`s wygaśnie po krótkim czasie, więc aplikacja musi zostać przygotowana do okresowego odświeżania tych tokenów. Aby odświeżyć każdy typ tokenu, można wykonać te same ukryte żądanie iframe z powyżej przy użyciu parametru `prompt=none`, aby kontrolować zachowanie platformy tożsamości. Jeśli chcesz otrzymać nowy `id_token`, pamiętaj, aby użyć `id_token` w `response_type` i `scope=openid`, a także jako parametr `nonce`.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

OpenID Connect Connect `end_session_endpoint` umożliwia aplikacji wysyłanie żądania do punktu końcowego platformy tożsamości firmy Microsoft w celu zakończenia sesji użytkownika i czyszczenie plików cookie ustawionych przez punkt końcowy platformy tożsamości firmy Microsoft. Aby w pełni podpisać użytkownika z aplikacji sieci Web, aplikacja powinna zakończyć własną sesję użytkownika (zazwyczaj przez wyczyszczenie pamięci podręcznej tokenów lub usunięcie plików cookie), a następnie przekierować przeglądarkę do:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` |wymagane |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common`, `organizations`, `consumers`i identyfikator dzierżawy. Aby uzyskać więcej informacji, zobacz temat [podstawowe informacje o protokole](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Rekomendowane | Adres URL, do którego użytkownik powinien zostać zwrócony po zakończeniu wylogowywania. Ta wartość musi być zgodna z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji. Jeśli ta wartość nie jest uwzględniona, użytkownik zostanie pokazany jako komunikat ogólny przez punkt końcowy platformy tożsamości firmy Microsoft. |

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć kodowanie, przejdź do [przykładów MSAL js](sample-v2-code.md) .
