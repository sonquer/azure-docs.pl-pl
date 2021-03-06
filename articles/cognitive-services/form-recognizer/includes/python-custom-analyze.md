---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446444"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analizowanie formularzy par klucz-wartość i tabel

Następnie będziesz używać nowo przeszkolonego modelu do analizowania dokumentu i wyodrębniania par klucz-wartość i tabel z tej usługi. Wywołaj interfejs API **[analizy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** za pomocą następującego kodu w nowym skrypcie języka Python. Przed uruchomieniem skryptu wprowadź następujące zmiany:

1. Zastąp `<file path>` ścieżką pliku formularza (na przykład C:\temp\file.PDF). Może to być również adres URL pliku zdalnego. W tym przewodniku szybki start można użyć plików w folderze **testowym** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Zastąp `<model_id>` IDENTYFIKATORem modelu otrzymanym w poprzedniej sekcji.
1. Zamień `<endpoint>` na punkt końcowy uzyskany przy użyciu klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zastąp `<file type>` typem pliku. Obsługiwane typy: `application/pdf`, `image/jpeg`, `image/png``image/tiff`.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Zapisz kod w pliku z rozszerzeniem. pr. Na przykład *form-Recognizer-analyze.py*.
1. Otwórz okno wiersza polecenia.
1. W monicie użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-analyze.py`.

Po wywołaniu interfejsu API **analizowania formularzy** otrzymasz odpowiedź `201 (Success)` z nagłówkiem **lokalizacji operacji** . Wartość tego nagłówka jest IDENTYFIKATORem, który będzie używany do śledzenia wyników operacji analizy. Powyższy skrypt drukuje wartość tego nagłówka w konsoli programu.

## <a name="get-the-analyze-results"></a>Pobierz wyniki analizy

Dodaj następujący kod w dolnej części skryptu języka Python. Spowoduje to użycie wartości identyfikatora z poprzedniego wywołania w nowym wywołaniu interfejsu API w celu pobrania wyników analizy. Operacja **analizy formularza** jest asynchroniczna, dlatego ten skrypt wywołuje interfejs API w regularnych odstępach czasu, dopóki wyniki nie będą dostępne. Zalecamy interwał co najmniej jednej sekundy.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
