---
title: Definiowanie prognoz w sklepie z bazami danych
titleSuffix: Azure Cognitive Search
description: Przykłady typowych wzorców dotyczących sposobu tworzenia projektów wzbogaconych w sklepie z bazami danych do użycia w Power BI lub platformie Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: daaedf346bed78a93e0762a37687b623d25ef753
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441973"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Projekcje magazynu wiedzy: jak kształtować i eksportować wzbogacania

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zapewnia funkcje w wersji zapoznawczej. Dostępna jest obecnie ograniczona obsługa portalu i nie ma obsługi zestawu SDK platformy .NET.

Projekcje to fizyczne wyrażenie ulepszonych dokumentów w sklepie z bazami danych. Efektywne korzystanie z ulepszonych dokumentów wymaga struktury. W tym artykule przedstawiono strukturę i relacje, uczenie się, jak tworzyć właściwości projekcji, a także sposób powiązania danych z tworzonych typów projekcji. 

Aby utworzyć projekcję, należy kształtować dane przy użyciu [umiejętności kształtu](cognitive-search-skill-shaper.md) , aby utworzyć obiekt niestandardowy lub użyć składni kształtowania wbudowanego w ramach definicji projekcji. 

Kształt danych zawiera wszystkie dane, które mają być przeznaczone do projektu, sformułowane jako hierarchia węzłów. W tym artykule przedstawiono kilka metod kształtowania danych, dzięki czemu można je projektować do struktur fizycznych, które współużytkują z raportowaniem, analizą lub przetwarzaniem podrzędnym. 

Przykłady przedstawione w tym artykule można znaleźć w tym [przykładzie interfejsu API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), który można pobrać i uruchomić w kliencie http.

## <a name="introduction-to-the-examples"></a>Wprowadzenie do przykładów

Jeśli znasz [projekcje](knowledge-store-projection-overview.md), musisz odwołać się, że istnieją trzy typy:

+ Tabele
+ Obiekty
+ Pliki

Projekcje tabeli są przechowywane w usłudze Azure Table Storage. Projekcje obiektów i plików są zapisywane w magazynie obiektów blob, gdzie projekcje obiektów są zapisywane jako pliki JSON i mogą zawierać zawartość z dokumentu źródłowego, a także wszelkie dane wyjściowe lub wzbogacania umiejętności. Potok wzbogacania umożliwia również wyodrębnienie plików binarnych, takich jak obrazy, te pliki binarne są rzutowane jako projekcje plików. Gdy obiekt binarny jest rzutowany jako projekcja obiektu, tylko metadane skojarzone z nim są zapisywane jako obiekt BLOB JSON. 

Aby zrozumieć wspólną część między kształtami i projekcjami danych, użyjemy następującej zestawu umiejętności jako podstawy do eksplorowania różnych konfiguracji. Ta zestawu umiejętności przetwarza nieprzetworzoną zawartość obrazu i tekstu. Projekcje zostaną zdefiniowane na podstawie zawartości dokumentu i danych wyjściowych umiejętności dla scenariuszy, które chcemy obsługiwać.

> [!IMPORTANT] 
> Podczas eksperymentowania z projekcjami warto [ustawić właściwość buforu indeksatora](search-howto-incremental-index.md) , aby zapewnić kontrolę kosztów. Edytowanie projekcji spowoduje, że cały dokument zostanie ponownie wzbogacony, jeśli nie zostanie ustawiona pamięć podręczna indeksatora. Gdy pamięć podręczna jest ustawiona i tylko projekcje zostały zaktualizowane, zestawu umiejętności wykonania dla wcześniej wzbogaconych dokumentów nie spowoduje żadnych nowych opłat Cognitive Services.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Korzystając z tej zestawu umiejętności, z wartością null `knowledgeStore`, z pierwszego przykładu jest wypełniany obiekt `knowledgeStore` skonfigurowany przy użyciu projekcji tworzących struktury danych tabelarycznych, których można użyć w innych scenariuszach. 

## <a name="projecting-to-tables"></a>Projekcja do tabel

Projekcja do tabel w usłudze Azure Storage jest przydatna do raportowania i analizy przy użyciu narzędzi takich jak Power BI. Power BI może odczytywać z tabel i odnajdywać relacje na podstawie kluczy generowanych podczas projekcji. W przypadku próby utworzenia pulpitu nawigacyjnego, który ma powiązane dane, upraszcza to zadanie. 

Załóżmy, że próbujemy skompilować pulpit nawigacyjny, w którym można wizualizować kluczowe frazy wyodrębnione z dokumentów jako chmurę programu Word. Aby utworzyć odpowiednią strukturę danych, można dodać umiejętność kształtu do zestawu umiejętności w celu utworzenia niestandardowego kształtu zawierającego szczegóły dotyczące określonego dokumentu i kluczowe frazy. Kształt niestandardowy będzie miał nazwę `pbiShape` w węźle głównym `document`.

> [!NOTE] 
> Projekcje tabeli są tabelami usługi Azure Storage, które podlegają limitom magazynu narzuconym przez usługę Azure Storage. Aby uzyskać więcej informacji, zobacz [limity magazynu tabel](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Warto wiedzieć, że rozmiar jednostki nie może przekroczyć 1 MB, a jedna właściwość nie może być większa niż 64 KB. Te ograniczenia sprawiają, że tabele są dobrym rozwiązaniem do przechowywania dużej liczby małych jednostek.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Tworzenie niestandardowego kształtu przy użyciu umiejętności kształtu

Utwórz niestandardowy kształt, który można projektować w usłudze Table Storage. Bez niestandardowego kształtu projekcja może odwoływać się tylko do jednego węzła (jedno rzutowanie na dane wyjściowe). Tworzenie niestandardowego kształtu pozwala agregować różne elementy w nowej logicznej całości, która może być rzutowana jako pojedyncza tabela lub przecięta i rozłożona w kolekcji tabel. 

W tym przykładzie kształt niestandardowy łączy metadane i identyfikuje jednostki i kluczowe frazy. Obiekt jest nazywany `pbiShape` i ma element nadrzędny w obszarze `/document`. 

> [!IMPORTANT] 
> Jednym z celów kształtowania jest upewnienie się, że wszystkie węzły wzbogacania są wyrażane w dobrze sformułowanym formacie JSON, który jest wymagany do projekcji w sklepie z bazami danych. Jest to szczególnie prawdziwe, gdy drzewo wzbogacania zawiera węzły, które nie są poprawnie sformułowane w formacie JSON (na przykład, gdy wzbogacanie jest nadrzędne dla elementu pierwotnego, takiego jak ciąg).
>
> Zwróć uwagę na ostatnie dwa węzły, `KeyPhrases` i `Entities`. Są one opakowane w prawidłowy obiekt JSON z `sourceContext`. Jest to wymagane jako `keyphrases` i `entities` są wzbogacane na elementy pierwotne i muszą zostać przekonwertowane na prawidłowe dane JSON, zanim będą mogły być rzutowane.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Dodaj powyższą umiejętność kształtu do zestawu umiejętności. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Teraz, gdy mamy wszystkie dane, które są konieczne do projektu w tabelach, zaktualizuj obiekt knowledgeStore przy użyciu definicji tabeli. W tym przykładzie mamy trzy tabele zdefiniowane przez ustawienie właściwości `tableName`, `source` i `generatedKeyName`.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Możesz przetwarzać swoją służbę, wykonując następujące czynności:

1. Ustaw właściwość ```storageConnectionString``` na prawidłowe parametry połączenia konta magazynu ogólnego przeznaczenia w wersji 2.  

1. Zaktualizuj zestawu umiejętności, wydając żądanie PUT.

1. Po zaktualizowaniu zestawu umiejętności Uruchom indeksator. 

Masz już projekcję działającą z trzema tabelami. Zaimportowanie tych tabel do Power BI powinno spowodować Power BI automatycznego odnajdywania relacji.

Przed przejściem do następnego przykładu program pozwala na odwiedzenie aspektów projekcji tabeli w celu zrozumienia Mechanics wycinków i odnoszących się do nich danych.

### <a name="slicing"></a>Tworzenia wycinków 

Cięcie jest techniką dzielącą cały skonsolidowany kształt na części składowe. Wyniki składają się z oddzielnych, ale powiązanych tabel, z których można korzystać osobno.

W przykładzie `pbiShape` jest skonsolidowanym kształtem (lub węzłem wzbogacania). W definicji projekcji `pbiShape` jest podzielony na dodatkowe tabele, co umożliwia ściąganie części kształtu, ```keyPhrases``` i ```Entities```. W Power BI jest to przydatne w przypadku, gdy do każdego dokumentu są skojarzone wiele jednostek i frazy kluczowe i uzyskasz więcej szczegółowych informacji, jeśli można zobaczyć jednostki i frazy kluczowe jako dane skategoryzowane.

Cięcie niejawnie generuje relację między tabelami nadrzędnymi i podrzędnymi, używając ```generatedKeyName``` w tabeli nadrzędnej w celu utworzenia kolumny o tej samej nazwie w tabeli podrzędnej. 

### <a name="naming-relationships"></a>Relacje nazw

Właściwości ```generatedKeyName``` i ```referenceKeyName``` służą do powiązania danych między tabelami, a nawet w ramach typów projekcji. Każdy wiersz w podrzędnej tabeli/projekcji ma właściwość wskazującą z powrotem do elementu nadrzędnego. Nazwa kolumny lub właściwości w elemencie podrzędnym jest ```referenceKeyName``` z elementu nadrzędnego. Gdy ```referenceKeyName``` nie zostanie podana, usługa domyślnie przyjmowana jest do ```generatedKeyName``` z elementu nadrzędnego. 

Power BI opiera się na tych generowanych kluczach w celu odnalezienia relacji w tabelach. Jeśli potrzebujesz kolumny w tabeli podrzędnej o nazwie inaczej, ustaw właściwość ```referenceKeyName``` w tabeli nadrzędnej. Przykładem może być ustawienie ```generatedKeyName``` jako identyfikatora w tabeli pbiDocument i ```referenceKeyName``` jako element DocumentID. Spowoduje to przepełnienie kolumny w tabelach pbiEntities i pbiKeyPhrases zawierających identyfikator dokumentu o nazwie element DocumentID.

## <a name="projecting-to-objects"></a>Projekcja do obiektów

Projekcje obiektów nie mają tych samych ograniczeń co projekcje tabeli i są lepiej dopasowane do projekcji dużych dokumentów. W tym przykładzie cały dokument jest rzutowany na projekcję obiektu. Projekcje obiektów są ograniczone do jednego rzutu w kontenerze i nie można go wycinkować.

Aby zdefiniować projekcję obiektu, będziemy używać tablicy ```objects``` w projekcjach. Można wygenerować nowy kształt przy użyciu umiejętności kształtu lub użyć wbudowanego kształtu projekcji obiektu. Chociaż przykładem tabeli przedstawiono podejście do tworzenia kształtu i rozdzielania, w tym przykładzie zademonstrowano użycie kształtu wbudowane. 

Kształtowanie wbudowane umożliwia tworzenie nowego kształtu w definicji wejść do projekcji. Kształtowanie wbudowane tworzy obiekt anonimowy, który jest identyczny jak umiejętność kształtu (w naszym przypadku `pbiShape`). Kształtowanie wbudowane jest przydatne w przypadku definiowania kształtu, którego nie planujesz użyć ponownie.

Właściwość projekcje jest tablicą. W tym przykładzie dodajemy nowe wystąpienie projekcji do tablicy, gdzie definicja knowledgeStore zawiera wbudowane projekcje. W przypadku korzystania z projekcji wbudowanych można pominąć umiejętność kształtu.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Projekcja do pliku

Projekcje plików to obrazy, które są wyodrębniane z dokumentu źródłowego lub dane wyjściowe wzbogacania, które mogą być rzutowane z procesu wzbogacania. Projekcje plików, podobnie jak projekcje obiektów, są implementowane jako obiekty blob w usłudze Azure Storage i zawierają obraz. 

Aby wygenerować projekcję pliku, używamy tablicy `files` w obiekcie projekcji. Ten przykład projektuje wszystkie obrazy wyodrębnione z dokumentu do kontenera o nazwie `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projekcja do wielu typów

Bardziej skomplikowany scenariusz może wymagać zaprojektowania zawartości dla różnych typów projekcji. Na przykład jeśli chcesz umieścić w projekcie pewne dane, takie jak kluczowe frazy i jednostki, Zapisz wyniki OCR tekstu i układu jako obiekty, a następnie Zaprojektuj obrazy jako pliki. 

W tym przykładzie aktualizacje zestawu umiejętności obejmują następujące zmiany:

1. Utwórz tabelę z wierszem dla każdego dokumentu.
1. Utwórz tabelę powiązaną z tabelą dokumentu z każdą frazą klucza identyfikowaną jako wiersz w tej tabeli.
1. Utwórz tabelę powiązaną z tabelą dokumentów z każdą jednostką identyfikowaną jako wiersz w tej tabeli.
1. Utwórz rzutowanie obiektu z tekstem układu dla każdego obrazu.
1. Utwórz projekcję pliku, wystając każdy wyodrębniony obraz.
1. Utwórz tabelę odsyłaczy, która zawiera odwołania do tabeli dokumentów, projekcji obiektów z tekstem układu i projekcją pliku.

Zmiany te są odzwierciedlone w definicji knowledgeStore. 

### <a name="shape-data-for-cross-projection"></a>Shape Data dla projekcji krzyżowej

Aby uzyskać potrzebne kształty dla projekcji, Zacznij od dodania nowej umiejętności kształtu, która tworzy obiekt w kształcie o nazwie `crossProjection`. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definiowanie projekcji tabeli, obiektu i pliku

W skonsolidowanym obiekcie crossProjection można wydzielić obiekt na wiele tabel, przechwycić dane wyjściowe OCR jako obiekty blob, a następnie zapisać obraz jako pliki (również w usłudze BLOB Storage).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Projekcje obiektów wymagają nazwy kontenera dla każdego rzutu, projekcji obiektów lub projekcji plików nie mogą udostępniać kontenera. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relacje między tabelami, obiektami i projekcjami plików

Ten przykład wyróżnia również kolejną funkcję projekcji. Definiując wiele typów projekcji w obrębie tego samego obiektu projekcji, istnieje relacja wyrażona w obrębie i między różnymi typami (tabelami, obiektami, plikami), co pozwala na rozpoczęcie od wiersza tabeli dla dokumentu i znalezienie całego tekstu OCR dla obrazów w tym dokumencie w projekcji obiektu. 

Jeśli nie chcesz, aby dane były powiązane, zdefiniuj projekcje w różnych obiektach projekcji. Na przykład poniższy fragment kodu spowoduje powstanie powiązanych tabel, ale bez relacji między tabelami i projekcjami obiektu (tekst OCR). 

Grupy projekcji są przydatne, gdy chcesz zaprojektować te same dane w różnych kształtach dla różnych potrzeb. Na przykład grupa projekcji dla pulpitu nawigacyjnego Power BI i inna grupa projekcji służąca do przechwytywania danych używanych do uczenia modelu uczenia maszynowego opakowanego w niestandardową umiejętność.

Podczas kompilowania projekcji różnych typów w pierwszej kolejności są generowane projekcje plików i obiektów, a ścieżki są dodawane do tabel.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Typowe problemy

Podczas definiowania projekcji istnieje kilka typowych problemów, które mogą spowodować nieoczekiwane wyniki. Sprawdź te problemy, jeśli dane wyjściowe w sklepie merytorycznym nie są oczekiwane.

+ Nie kształtowanie wzbogacania ciągów w prawidłowy kod JSON. Gdy ciągi są wzbogacane, na przykład `merged_content` wzbogacone przy użyciu kluczowych fraz, wzbogacona właściwość jest reprezentowana jako element podrzędny `merged_content` w drzewie wzbogacania. Reprezentacja domyślna nie jest poprawnie sformułowanym plikiem JSON. Dlatego w czasie projekcji należy koniecznie przekształcić wzbogacanie w prawidłowy obiekt JSON o nazwę i wartość.

+ Pomijanie ```/*``` na końcu ścieżki źródłowej. Jeśli źródło projekcji jest `/document/pbiShape/keyPhrases`, tablica fraz kluczy jest rzutowana jako pojedynczy obiekt/wiersz. Zamiast tego należy ustawić ścieżkę źródłową na `/document/pbiShape/keyPhrases/*`, aby dać pojedynczy wiersz lub obiekt dla każdej frazy kluczowej.

+ Błędy składni ścieżki. Selektory ścieżek są rozróżniane wielkości liter i mogą prowadzić do brakujących ostrzeżeń wejściowych, jeśli nie używasz dokładnej wielkości liter.

## <a name="next-steps"></a>Następne kroki

W przykładach w tym artykule przedstawiono typowe wzorce tworzenia projekcji. Teraz, gdy znasz już koncepcje, lepiej jest dowiedzieć się, jak tworzyć projekcje w konkretnym scenariuszu.

Podczas iteracji nad definicjami sklepu z bazami danych Rozważ możliwość wzbogacania przyrostowego jako kolejnego kroku. Zwiększanie przyrostowe jest oparte na pamięci podręcznej, dzięki czemu można ponownie wykorzystać wszelkie wzbogacania, w przypadku których modyfikacje zestawu umiejętności nie wpływają w inny sposób. Jest to szczególnie przydatne w przypadku potoków zawierających OCR i analizę obrazu.

> [!div class="nextstepaction"]
> [Wprowadzenie do przyrostowego wzbogacania i buforowania](cognitive-search-incremental-indexing-conceptual.md)