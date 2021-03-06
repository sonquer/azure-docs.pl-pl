---
title: 'Szybki Start: Content Moderatora Biblioteka kliencka dla języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę z biblioteką kliencką Content Moderator Cognitive Services platformy Azure dla języka Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772359"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Szybki Start: Content Moderatora Biblioteka kliencka dla języka Python

Rozpocznij pracę z biblioteką klienta Content Moderator dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Content Moderator to usługa poznawczej, która sprawdza zawartość tekstu, obrazu i wideo dla materiałów, które są potencjalnie obraźliwe, ryzykowne lub w inny sposób niepożądane. W przypadku znalezienia takich treści usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsłużyć oflagowaną zawartość w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników.

Użyj biblioteki klienta Content Moderator dla języka Python, aby:

* [Tekst umiarkowany](#moderate-text)
* [Używanie listy warunków niestandardowych](#use-a-custom-terms-list)
* [Obrazy umiarkowane](#moderate-images)
* [Używanie niestandardowej listy obrazów](#use-a-custom-image-list)
* [Utwórz recenzję](#create-a-review)

[Dokumentacja referencyjna](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) [ | ](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-content-moderator-azure-resource"></a>Tworzenie zasobu Content Moderator platformy Azure

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla Content Moderator przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni bezpłatnie. Po utworzeniu konta będzie ono dostępne w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/)

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla adresu URL klucza i punktu końcowego o nazwie `CONTENT_MODERATOR_SUBSCRIPTION_KEY` i `CONTENT_MODERATOR_ENDPOINT`.
 
### <a name="create-a-python-script"></a>Tworzenie skryptu w języku Python

Utwórz nowy skrypt w języku Python i otwórz go za pomocą preferowanego edytora lub środowiska IDE. Następnie Dodaj następujące instrukcje `import` na początku pliku.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Następnie utwórz zmienne dla lokalizacji i klucza punktu końcowego zasobu jako zmienne środowiskowe. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennych.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Bibliotekę klienta Content Moderator można zainstalować przy użyciu następującego polecenia:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje zestawu SDK języka Python Content Moderator.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Ta klasa jest wymagana dla wszystkich funkcji Content Moderator. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Ta klasa udostępnia funkcje do analizowania obrazów na potrzeby zawartości dla dorosłych, danych osobowych lub osób ludzkich.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Ta klasa udostępnia funkcje do analizowania tekstu dla języka, niewulgarności, błędów i informacji osobistych.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Ta klasa udostępnia funkcje interfejsów API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i przeglądów ludzkich.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Content Moderator dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tekst umiarkowany](#moderate-text)
* [Używanie listy warunków niestandardowych](#use-a-custom-terms-list)
* [Obrazy umiarkowane](#moderate-images)
* [Używanie niestandardowej listy obrazów](#use-a-custom-image-list)
* [Utwórz recenzję](#create-a-review)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienne środowiskowe](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza Content moderator i punktu końcowego.

Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Tekst umiarkowany

Poniższy kod używa klienta Content Moderator, aby analizować treść tekstu i drukować wyniki w konsoli programu. Najpierw Utwórz **text_files/** folder w katalogu głównym projektu i dodaj plik *content_moderator_text_moderation. txt* . Dodaj własny tekst do tego pliku lub użyj następującego przykładowego tekstu:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Dodaj odwołanie do nowego folderu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Następnie Dodaj następujący kod do skryptu w języku Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Używanie listy warunków niestandardowych

Poniższy kod pokazuje, jak zarządzać listą niestandardowych terminów na potrzeby moderowania tekstu. Możesz użyć klasy [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) , aby utworzyć listę warunków, zarządzać indywidualnymi postanowieniami i na ekranie inne treści tekstu.

### <a name="get-sample-text"></a>Pobierz przykładowy tekst

Aby użyć tego przykładu, należy utworzyć **text_files/** folder w katalogu głównym projektu i dodać plik *content_moderator_term_list. txt* . Ten plik powinien zawierać tekst organiczny, który zostanie sprawdzony względem listy warunków. Możesz użyć następującego przykładowego tekstu:

```
This text contains the terms "term1" and "term2".
```

Dodaj odwołanie do folderu, jeśli jeszcze nie został zdefiniowany.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Utwórz listę

Dodaj następujący kod do skryptu języka Python, aby utworzyć niestandardową listę warunków i zapisać jej wartość identyfikatora.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definiuj szczegóły listy

Aby edytować nazwę i opis, można użyć identyfikatora listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Dodawanie terminu do listy

Poniższy kod dodaje terminy `"term1"` i `"term2"` do listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Pobierz wszystkie warunki na liście

Możesz użyć identyfikatora listy, aby zwrócić wszystkie warunki z listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Odśwież indeks listy

Po każdym dodaniu lub usunięciu warunków z listy należy odświeżyć indeks, aby można było użyć zaktualizowanej listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Tekst ekranu na liście

Główną funkcją listy warunków niestandardowych jest porównanie treści tekstu z listą i Dowiedz się, czy istnieją odpowiednie warunki. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Usuwanie terminu z listy

Poniższy kod usuwa termin `"term1"` z listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Usuń wszystkie warunki z listy

Użyj poniższego kodu, aby wyczyścić listę wszystkich jej warunków.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Usuń listę

Użyj poniższego kodu, aby usunąć listę niestandardowych warunków.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Obrazy umiarkowane

Poniższy kod używa klienta Content Moderator, wraz z obiektem [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) , do analizowania obrazów pod kątem treści dla dorosłych i erotycznej.

### <a name="get-images"></a>Pobierz obrazy

Zdefiniuj odwołanie do niektórych obrazów do przeanalizowania.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Następnie Dodaj następujący kod, aby wykonać iterację obrazów. Reszta kodu w tej sekcji zostanie przestawiona w tej pętli.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sprawdź zawartość dla dorosłych/erotycznej

Poniższy kod sprawdza obraz pod podanym adresem URL dla zawartości dla dorosłych lub erotycznej, a następnie drukuje wyniki do konsoli. Aby uzyskać informacje na temat tego, co oznaczają te warunki, zobacz Przewodnik dotyczący [pojęć związanych z moderowaniem obrazu](./image-moderation-api.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Wyszukaj widoczny tekst

Poniższy kod sprawdza obraz pod kątem widocznej zawartości tekstowej i drukuje wyniki do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sprawdź dostępność twarzy

Poniższy kod sprawdza obraz dla ludzkich twarzy i drukuje wyniki w konsoli programu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Używanie niestandardowej listy obrazów

Poniższy kod przedstawia sposób zarządzania niestandardową listą obrazów na potrzeby moderowania obrazu. Ta funkcja jest przydatna, jeśli platforma często odbiera wystąpienia tego samego zestawu obrazów, które chcesz wypróbować. Dzięki utrzymywaniu listy tych konkretnych obrazów można poprawić wydajność. Klasa [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) umożliwia tworzenie listy obrazów, zarządzanie poszczególnymi obrazami na liście i porównywanie innych obrazów.

Utwórz następujące zmienne tekstowe do przechowywania adresów URL obrazów, które będą używane w tym scenariuszu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Nie jest to właściwa lista, ale nieformalna lista obrazów, które zostaną dodane do `add images` sekcji kodu.


### <a name="create-an-image-list"></a>Tworzenie listy obrazów

Dodaj następujący kod, aby utworzyć listę obrazów i zapisać odwołanie do jego identyfikatora.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Dodawanie obrazów do listy

Poniższy kod dodaje wszystkie obrazy do listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Zdefiniuj funkcję pomocnika **add_images** w innym miejscu w skrypcie.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Pobierz obrazy z listy

Poniższy kod drukuje nazwy wszystkich obrazów na liście.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Aktualizuj szczegóły listy

Aby zaktualizować nazwę i opis listy, można użyć identyfikatora listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Pobierz szczegóły listy

Użyj poniższego kodu, aby wydrukować bieżące szczegóły listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Odśwież indeks listy

Po dodaniu lub usunięciu obrazów należy odświeżyć indeks listy, aby można było użyć go do wyświetlania innych obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Dopasuj obrazy do listy

Główną funkcją list obrazów jest porównanie nowych obrazów i sprawdzenie, czy istnieją jakieś dopasowania.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Usuwanie obrazu z listy

Poniższy kod usuwa element z listy. W tym przypadku jest to obraz, który nie jest zgodny z kategorią listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Usuń wszystkie obrazy z listy

Użyj poniższego kodu, aby wyczyścić listę obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Usuń listę obrazów

Użyj poniższego kodu, aby usunąć daną listę obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Utwórz recenzję

Za pomocą zestawu SDK języka Python Content Moderator można pokazywać zawartość do [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com) , aby można było je sprawdzić. Aby dowiedzieć się więcej na temat narzędzia do przeglądu, zobacz [Przewodnik dotyczący koncepcyjnego narzędzia do przeglądu](./review-tool-user-guide/human-in-the-loop.md).

Poniższy kod używa klasy [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) w celu utworzenia przeglądu, pobrania jego identyfikatora i sprawdzenia jego szczegółów po odebraniu danych ludzkich za pośrednictwem portalu internetowego narzędzia do przeglądu.

### <a name="get-review-credentials"></a>Pobierz poświadczenia przeglądu

Najpierw Zaloguj się do narzędzia przeglądu i Pobierz swoją nazwę zespołu. Następnie przypisz go do odpowiedniej zmiennej w kodzie. Opcjonalnie można skonfigurować punkt końcowy wywołania zwrotnego, aby otrzymywać aktualizacje dotyczące działania przeglądu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Utwórz przegląd obrazu

Dodaj następujący kod, aby utworzyć i opublikować przegląd dla danego adresu URL obrazu. Kod zapisuje odwołanie do identyfikatora przeglądu. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Pobierz szczegóły przeglądu

Użyj poniższego kodu, aby sprawdzić szczegóły danego przeglądu. Po utworzeniu recenzji możesz samodzielnie przejść do narzędzia do przeglądu i korzystać z zawartości. Aby dowiedzieć się, jak to zrobić, zobacz [Przewodnik dotyczący przeglądów](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Po zakończeniu możesz ponownie uruchomić ten kod i pobrać wyniki procesu recenzowania.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Jeśli w tym scenariuszu użyto punktu końcowego wywołania zwrotnego, powinien on otrzymać zdarzenie w tym formacie:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia `python` w pliku szybkiego startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z biblioteki języka Python Content Moderator w celu wykonywania zadań moderowania. Następnie Dowiedz się więcej o moderowaniu obrazów lub innych nośnikach, odczytując Przewodnik koncepcyjny.

> [!div class="nextstepaction"]
>[Pojęcia związane z moderowaniem obrazu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co to jest platforma Azure Content Moderator?](./overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).