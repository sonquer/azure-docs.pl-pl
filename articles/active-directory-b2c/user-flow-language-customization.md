---
title: Dostosowanie języka w Azure Active Directory B2C
description: Dowiedz się więcej o dostosowywaniu środowiska w przepływach użytkownika.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ab34c1c34c2d2a67b5e3fdd6a5e84f0663455e20
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847916"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Dostosowanie języka w Azure Active Directory B2C

Dostosowanie języka w programie Azure Active Directory B2C (Azure AD B2C) umożliwia przepływom użytkownika dostosowywać się do potrzeb klientów. Firma Microsoft udostępnia tłumaczenia dla [języków 36](#supported-languages), ale można również udostępniać własne tłumaczenia w dowolnym języku. Nawet jeśli środowisko użytkownika jest dostarczane tylko dla jednego języka, można dostosować dowolny tekst na stronach.

## <a name="how-language-customization-works"></a>Jak działa dostosowanie języka

Za pomocą dostosowywania języka można wybrać Języki, w których przepływ użytkownika jest dostępny. Po włączeniu funkcji można podać parametr ciągu zapytania, `ui_locales`z aplikacji. Gdy wywołasz się do Azure AD B2C, Strona zostanie przetłumaczona na wskazane ustawienia regionalne. Ten typ konfiguracji zapewnia pełną kontrolę nad językami w przepływie użytkownika i ignoruje ustawienia języka w przeglądarce klienta.

Użytkownik może nie potrzebować tego poziomu kontroli nad językami, które widzi klient. Jeśli nie podano parametru `ui_locales`, środowisko klienta zależy od ustawień przeglądarki. Można nadal kontrolować Języki, do których przepływ użytkownika jest tłumaczony przez dodanie go jako obsługiwanego języka. Jeśli w przeglądarce klienta ustawiono język, który nie ma być obsługiwany, w zamian zostanie wyświetlony język wybrany jako domyślny w obsługiwanych kulturach.

* **interfejs użytkownika — określony język**: po włączeniu dostosowywania języka przepływ użytkownika zostanie przetłumaczony na język, który jest określony w tym miejscu.
* **Język żądany w przeglądarce**: Jeśli nie określono `ui_locales` parametru, przepływ użytkownika zostanie przetłumaczony na język żądany w przeglądarce, *Jeśli język jest obsługiwany*.
* **Język domyślny dla zasad**: Jeśli w przeglądarce nie określono języka lub określono taki, który nie jest obsługiwany, przepływ użytkownika zostanie przetłumaczony na domyślny język przepływu użytkownika.

> [!NOTE]
> Jeśli używasz niestandardowych atrybutów użytkownika, musisz podać własne tłumaczenia. Aby uzyskać więcej informacji, zobacz [Dostosowywanie ciągów](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Obsługa żądanych języków dla ui_locales

Zasady, które zostały utworzone przed ogólnym udostępnieniem dostosowania języka, muszą najpierw włączyć tę funkcję. Zasady i przepływy użytkowników utworzone po włączeniu dostosowywania języka są domyślnie włączone.

Po włączeniu dostosowywania języka w przepływie użytkownika można kontrolować język przepływu użytkownika, dodając parametr `ui_locales`.

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz pozycję **Włącz Dostosowywanie języka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Wybierz Języki, w których przepływ użytkownika jest włączony

Włączenie zestawu języków dla przepływu użytkownika do tłumaczenia na żądanie przeglądarki bez parametru `ui_locales`.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który ma być obsługiwany.
1. W okienku właściwości Zmień wartość **włączone** na **tak**.
1. Wybierz pozycję **Zapisz** w górnej części okienka właściwości.

>[!NOTE]
>Jeśli parametr `ui_locales` nie zostanie podany, Strona zostanie przetłumaczona na język przeglądarki klienta tylko wtedy, gdy jest włączona.
>

## <a name="customize-your-strings"></a>Dostosowywanie ciągów

Dostosowanie języka umożliwia dostosowanie dowolnego ciągu w przepływie użytkownika.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który chcesz dostosować.
1. W obszarze **pliki zasobów na poziomie strony**wybierz stronę, którą chcesz edytować.
1. Wybierz pozycję **Pobierz domyślne** (lub **Pobierz zastąpienia** , jeśli wcześniej edytowano ten język).

Te kroki zapewniają plik JSON, którego można użyć do rozpoczęcia edytowania ciągów.

### <a name="change-any-string-on-the-page"></a>Zmień dowolny ciąg na stronie

1. Otwórz plik JSON pobrany z poprzednich instrukcji w edytorze JSON.
1. Znajdź element, który chcesz zmienić. Możesz znaleźć `StringId` ciągu, którego szukasz, lub poszukać atrybutu `Value`, który chcesz zmienić.
1. Zaktualizuj atrybut `Value` za pomocą tego, co ma być wyświetlane.
1. Dla każdego ciągu, który chcesz zmienić, Zmień `Override` na `true`.
1. Zapisz plik i przekaż zmiany. (Formant przekazywania można znaleźć w tym samym miejscu, w którym został pobrany plik JSON).

> [!IMPORTANT]
> Jeśli zachodzi potrzeba przesłonięcia ciągu, należy ustawić wartość `Override` na `true`. Jeśli wartość nie zostanie zmieniona, wpis zostanie zignorowany.

### <a name="change-extension-attributes"></a>Zmień atrybuty rozszerzenia

Jeśli chcesz zmienić ciąg dla niestandardowego atrybutu użytkownika lub chcesz dodać go do pliku JSON, jest w następującym formacie:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Zastąp `<ExtensionAttribute>` nazwą niestandardowego atrybutu użytkownika.

Zastąp `<ExtensionAttributeValue>` nowym ciągiem, który ma być wyświetlany.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Podaj listę wartości za pomocą LocalizedCollections

Jeśli chcesz podać listę wartości odpowiedzi, musisz utworzyć atrybut `LocalizedCollections`. `LocalizedCollections` jest tablicą par `Name` i `Value`. Kolejność dla elementów będzie kolejność, w jakiej są wyświetlane. Aby dodać `LocalizedCollections`, użyj następującego formatu:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` jest atrybutem użytkownika, do którego odnosi się ten atrybut `LocalizedCollections`.
* `Name` jest wartością pokazywaną użytkownikowi.
* `Value` jest zwracana w ramach żądania, gdy ta opcja jest zaznaczona.

### <a name="upload-your-changes"></a>Przekaż zmiany

1. Po wprowadzeniu zmian w pliku JSON Wróć do swojej dzierżawy B2C.
1. Wybierz pozycję **przepływy użytkownika** i kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz język, w którym chcesz przeprowadzić tłumaczenie.
1. Wybierz stronę, na której chcesz przekazać tłumaczenia.
1. Wybierz ikonę folderu i wybierz plik JSON do przekazania.

Zmiany są zapisywane automatycznie w przepływie użytkownika.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dostosowywanie interfejsu użytkownika strony przy użyciu dostosowywania języka

Istnieją dwa sposoby lokalizowania zawartości HTML. Jednym ze sposobów jest włączenie [dostosowywania języka](user-flow-language-customization.md). Włączenie tej funkcji umożliwia Azure AD B2C do przesyłania dalej parametru Connect OpenID Connect `ui-locales`do punktu końcowego. Na serwerze zawartości można użyć tego parametru, aby zapewnić dostosowane strony HTML, które są specyficzne dla języka.

Alternatywnie można ściągnąć zawartość z różnych miejsc na podstawie ustawień regionalnych, które są używane. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości wieloznacznej `{Culture:RFC5646}`, nastąpi wywołanie tego samego. Załóżmy na przykład, że jest to identyfikator URI strony niestandardowej:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Stronę można załadować w `fr`. Gdy Strona pobiera zawartość HTML i CSS, pobiera z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Dodawanie języków niestandardowych

Możesz również dodać Języki, dla których firma Microsoft obecnie nie udostępnia tłumaczeń. Musisz podać tłumaczenia dla wszystkich ciągów w przepływie użytkownika. Kody języka i ustawień regionalnych są ograniczone do tych w standardzie ISO 639-1.

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
2. Kliknij przepływ użytkownika, w którym chcesz dodać Języki niestandardowe, a następnie kliknij pozycję **Języki**.
3. Wybierz pozycję **Dodaj język niestandardowy** w górnej części strony.
4. W otwartym okienku kontekstu Zidentyfikuj język, w którym są udostępniane tłumaczenia, wprowadzając prawidłowy kod ustawień regionalnych.
5. Dla każdej strony można pobrać zestaw zastąpień dla języka angielskiego i rozpocząć korzystanie z tłumaczeń.
6. Po zakończeniu pracy z plikami JSON można przekazać je dla każdej strony.
7. Wybierz pozycję **Włącz**, a przepływ użytkownika może teraz pokazać ten język dla użytkowników.
8. Zapisz język.

>[!IMPORTANT]
>Przed zapisaniem należy włączyć Języki niestandardowe lub przekazywać do nich zastąpienia.

## <a name="additional-information"></a>Dodatkowe informacje

### <a name="page-ui-customization-labels-as-overrides"></a>Etykiety dostosowywania interfejsu użytkownika strony jako zastąpienia

Po włączeniu dostosowywania języka poprzednie zmiany w etykietach korzystających z dostosowywania interfejsu użytkownika są utrwalane w pliku JSON dla języka angielskiego (pl). Można nadal zmieniać etykiety i inne ciągi przez przekazywanie zasobów języka w celu dostosowania języka.

### <a name="up-to-date-translations"></a>Aktualne tłumaczenia

Firma Microsoft dokłada starań, aby zapewnić najbardziej aktualne tłumaczenia do użycia. Firma Microsoft stale ulepsza tłumaczenia i zapewnia ich zgodność. Firma Microsoft zidentyfikuje usterki i zmiany w globalnej terminologii oraz wprowadzi aktualizacje, które bezproblemowo pracują w przepływie użytkownika.

### <a name="support-for-right-to-left-languages"></a>Obsługa języków pisanych od prawej do lewej

Firma Microsoft obecnie nie obsługuje języków pisanych od prawej do lewej. Można to zrobić za pomocą niestandardowych ustawień regionalnych i CSS, aby zmienić sposób wyświetlania ciągów. Jeśli potrzebujesz tej funkcji, zagłosuj na opinię na temat [platformy Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Tłumaczenia dostawcy tożsamości społecznościowej

Firma Microsoft udostępnia parametr `ui_locales` OIDC do logowania do sieci społecznościowych. Jednak niektórzy dostawcy tożsamości społecznościowych, w tym Facebook i Google, nie honorli.

### <a name="browser-behavior"></a>Zachowanie przeglądarki

Programy Chrome i Firefox żądają swojego języka zestawu. Jeśli jest to obsługiwany język, jest on wyświetlany przed ustawieniem domyślnym. Przeglądarka Microsoft Edge obecnie nie żąda języka i przechodzi bezpośrednio do języka domyślnego.

## <a name="supported-languages"></a>Obsługiwane języki

Azure AD B2C obejmuje obsługę następujących języków. Języki przepływu użytkownika są udostępniane przez Azure AD B2C. Języki powiadomień usługi uwierzytelniania wieloskładnikowego (MFA) są udostępniane przez [usługę Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Język              | Kod języka | Przepływy użytkowników         | Powiadomienia usługi MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabski                | ty            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Bułgarski             | BG            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Bengalski                | bn            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Kataloński               | urzędu certyfikacji            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Czeski                 | cs            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Duński                | da            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Niemiecki                | de            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Grecki                 | el            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Polski               | en            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Hiszpański               | es            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Estoński              | staw            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Baskijski                | Europejska            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Fiński               | fi            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Francuski                | fr            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Galicyjski              | GL            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Hebrajski                | Przewodniczący            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Chorwacki              | wysoki            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| węgierski             | hu            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Indonezyjski            | id            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Włoski               | it            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Japoński              | ja            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Kazachski                | kk            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Kannada               | kn            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Koreański                | ko            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Litewski            | przelew            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Łotewski               | lv            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Malajalam             | ml            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Marathi               | mr            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Malajski                 | ms            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Norweski (Bokmal)      | nb            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Holenderski                 | nl            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Norweski             | nie            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Polski                | pl            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Portugalski (Brazylia)   | pt-br         | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Portugalski (Portugalia) | pt-pt         | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Rumuński              | ro            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Rosyjski               | ru            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Słowacki                | SK            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Słoweński             | sl            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Serbski — Cyrylica    | Wirtualizacja SR-cryl-CS    | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Serbski (łaciński)       | Wirtualizacja sr-latn-cs    | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Szwedzki               | sv            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Tamilski                 | ta            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Telugu                | Usuń            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Tajlandzki                  | th            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Turecki               | zdawczy            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Ukraiński             | Zjednoczone Królestwo            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Wietnamski            | vi            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Chiński (uproszczony)  | zh-Hans       | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Chiński — tradycyjny | zh-Hant       | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
