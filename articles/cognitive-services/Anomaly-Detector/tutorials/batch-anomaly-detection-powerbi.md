---
title: 'Samouczek: wizualizacja anomalii przy użyciu wykrywania partii i Power BI'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wykrywania anomalii i Power BI, aby wizualizować anomalie w danych szeregów czasowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1d1f71ed56138f2c799c7410508c0bc4899e7887
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448847"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Samouczek: wizualizacja anomalii przy użyciu wykrywania partii i Power BI

Skorzystaj z tego samouczka, aby znaleźć anomalie w zestawie danych szeregów czasowych jako partię. Za pomocą Power BI Desktop będziesz korzystać z pliku programu Excel, przygotowywać dane dla interfejsu API wykrywania anomalii i wizualizować statystyczne anomalie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Używanie Power BI Desktop do importowania i przekształcania zestawu danych szeregów czasowych
> * Integruj Power BI Desktop z interfejsem API wykrywania anomalii w celu wykrycia anomalii usługi Batch
> * Wizualizuj anomalie w danych, w tym oczekiwane i widoczne wartości, a także granice wykrywania anomalii.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), dostępny bezpłatnie.
* Plik programu Excel (xlsx) zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Załaduj i sformatuj dane szeregów czasowych

Aby rozpocząć, Otwórz Power BI Desktop i Załaduj dane szeregów czasowych pobrane z wymagań wstępnych. Ten plik programu Excel zawiera serię sygnatury czasowej skoordynowanego czasu uniwersalnego (UTC) i par wartości.  

> [!NOTE]
> Power BI mogą używać danych z wielu różnych źródeł, takich jak pliki CSV, bazy danych SQL, Magazyn obiektów blob platformy Azure i nie tylko.  

W oknie głównym Power BI Desktop kliknij Wstążkę **Narzędzia główne** . W **zewnętrznej grupie danych** wstążki Otwórz menu rozwijane **Pobierz dane** i kliknij pozycję **Excel**.

![Obraz przycisku "Pobierz dane" w Power BI](../media/tutorials/power-bi-get-data-button.png)

Po wyświetleniu okna dialogowego przejdź do folderu, w którym został pobrany przykładowy plik. xlsx, i zaznacz go. Po wyświetleniu okna dialogowego **nawigatora** kliknij opcję **Arkusz1**, a następnie pozycję **Edytuj**.

![Obraz ekranu "Nawigator" źródła danych w Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI spowoduje przekonwertowanie sygnatur czasowych w pierwszej kolumnie na typ danych `Date/Time`. Te sygnatury czasowe muszą być konwertowane na tekst, aby można było je wysyłać do interfejsu API wykrywania anomalii. Jeśli Edytor Power Query nie zostanie otwarty automatycznie, kliknij przycisk **Edytuj zapytania** na karcie Narzędzia główne. 

Kliknij Wstążkę **Przekształć** w edytorze Power Query. W grupie **dowolna kolumna** Otwórz **Typ danych:** menu rozwijane i wybierz pozycję **tekst**.

![Obraz ekranu "Nawigator" źródła danych w Power BI](../media/tutorials/data-type-drop-down.png)

Po otrzymaniu powiadomienia o zmianie typu kolumny kliknij pozycję **Zamień bieżący**. Następnie kliknij przycisk **zamknij & Zastosuj** lub **Zastosuj** na Wstążce **Narzędzia główne** . 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Utwórz funkcję do wysyłania danych i sformatuj odpowiedź

Aby sformatować i wysłać plik danych do interfejsu API wykrywania anomalii, można wywołać zapytanie w utworzonej powyżej tabeli. W edytorze Power Query na Wstążce **Narzędzia główne** Otwórz menu rozwijane **nowe źródło** i kliknij polecenie **puste zapytanie**.

Upewnij się, że nowe zapytanie jest zaznaczone, a następnie kliknij przycisk **Edytor zaawansowany**. 

![Obraz przycisku "Edytor zaawansowany" w Power BI](../media/tutorials/advanced-editor-screen.png)

W Edytor zaawansowany Użyj poniższego fragmentu Power Query M, aby wyodrębnić kolumny z tabeli i wysłać je do interfejsu API. Następnie zapytanie utworzy tabelę z odpowiedzi JSON i zwróci ją. Zastąp zmienną `apiKey` prawidłowym kluczem interfejsu API wykrywania anomalii, a `endpoint` z punktem końcowym. Po wprowadzeniu zapytania do Edytor zaawansowany kliknij przycisk **gotowe**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Wywołaj zapytanie w arkuszu danych, wybierając `Sheet1` poniżej **wprowadź parametr**, a następnie kliknij pozycję **Wywołaj**. 

![Obraz przycisku "Edytor zaawansowany"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Prywatność i uwierzytelnianie źródła danych

> [!NOTE]
> Należy pamiętać o zasadach organizacji dotyczących prywatności i dostępu do danych. Aby uzyskać więcej informacji, zobacz [Power BI Desktop poziomów prywatności](https://docs.microsoft.com/power-bi/desktop-privacy-levels) .

Podczas próby uruchomienia zapytania może zostać wyświetlony komunikat ostrzegawczy, ponieważ korzysta on z zewnętrznego źródła danych. 

![Obraz przedstawiający ostrzeżenie utworzone przez Power BI](../media/tutorials/blocked-function.png)

Aby rozwiązać ten problem, kliknij menu **plik**, a następnie **Opcje i ustawienia**. Następnie kliknij pozycję **Opcje**. Poniżej **bieżącego pliku**wybierz opcję **prywatność**i **zignoruj poziomy prywatności i potencjalnie poprawić wydajność**. 

Ponadto może zostać wyświetlony komunikat z prośbą o określenie, w jaki sposób chcesz połączyć się z interfejsem API.

![Obraz przedstawiający żądanie określenia poświadczeń dostępu](../media/tutorials/edit-credentials-message.png)

Aby rozwiązać ten problem, kliknij pozycję **Edytuj poświadczenia** w komunikacie. Po wyświetleniu okna dialogowego wybierz pozycję **anonimowe** , aby połączyć się z interfejsem API anonimowo. Następnie kliknij przycisk **Connect** (Połącz). 

Następnie kliknij przycisk **zamknij & Zastosuj** na Wstążce **Narzędzia główne** , aby zastosować zmiany.

## <a name="visualize-the-anomaly-detector-api-response"></a>Wizualizuj odpowiedź interfejsu API wykrywania anomalii

Na ekranie głównym Power BI Zacznij korzystać z kwerend utworzonych powyżej, aby wizualizować dane. Najpierw wybierz **Wykres liniowy** w **wizualizacjach**. Następnie Dodaj sygnaturę czasową z wywoływanej funkcji do **osi**wykresu liniowego. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **sygnatura czasowa**. 

![Kliknij prawym przyciskiem myszy wartość sygnatury czasowej](../media/tutorials/timestamp-right-click.png)

Dodaj następujące pola z **wywoływanej funkcji** do pola **wartości** wykresu. Użyj poniższego zrzutu ekranu, aby pomóc w tworzeniu wykresu.

    * Wartość
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Obraz nowego ekranu szybkiej miary](../media/tutorials/chart-settings.png)

Po dodaniu pól kliknij wykres i zmień jego rozmiar, aby pokazać wszystkie punkty danych. Wykres będzie wyglądać podobnie do poniższego zrzutu ekranu:

![Obraz nowego ekranu szybkiej miary](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Wyświetlanie nietypowych punktów danych

Po prawej stronie okna Power BI, poniżej okienka **pola** , kliknij prawym przyciskiem myszy **wartość** w obszarze **zapytania wywoływanej funkcji**, a następnie kliknij pozycję **Nowa szybka miara**.

![Obraz nowego ekranu szybkiej miary](../media/tutorials/new-quick-measure.png)

Na wyświetlonym ekranie wybierz pozycję **przefiltrowana wartość** jako obliczenie. Ustaw **wartość podstawową** na `Sum of Value`. Następnie przeciągnij `IsAnomaly` z pól **wywołanych funkcji** , aby **przefiltrować**. Wybierz `True` z menu rozwijanego **Filtr** .

![Obraz nowego ekranu szybkiej miary](../media/tutorials/new-quick-measure-2.png)

Po kliknięciu przycisku **OK**będziesz mieć pole `Value for True` w dolnej części listy pól. Kliknij go prawym przyciskiem myszy i zmień jego nazwę na **anomalie**. Dodaj go do **wartości**wykresu. Następnie wybierz narzędzie **Format** i ustaw typ osi X na **kategorii**.

![Obraz nowego ekranu szybkiej miary](../media/tutorials/format-x-axis.png)

Zastosuj kolory do wykresu, klikając narzędzie **Format** i **kolory danych**. Wykres powinien wyglądać podobnie do poniższego:

![Obraz nowego ekranu szybkiej miary](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Wykrywanie anomalii przesyłania strumieniowego za pomocą Azure Databricks](anomaly-detection-streaming-databricks.md)
