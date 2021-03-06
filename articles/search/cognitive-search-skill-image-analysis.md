---
title: Umiejętność analizy obrazów
titleSuffix: Azure Cognitive Search
description: Wyodrębnij tekst semantyczny za pomocą analizy obrazów, korzystając z umiejętności poznawczej analizy obrazów w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 106f83e4c8fdf33ac8752e5942dbb22a2df78693
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840506"
---
# <a name="image-analysis-cognitive-skill"></a>Umiejętność analizy obrazów

Umiejętność **analizy obrazów** wyodrębnia bogaty zestaw funkcji wizualnych opartych na zawartości obrazu. Na przykład można wygenerować podpis na podstawie obrazu, generować Tagi lub identyfikować osobistości i punkty orientacyjne. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) w Cognitive Services. 

> [!NOTE]
> Małe woluminy (w ramach 20 transakcji) można bezpłatnie wykonywać na platformie Azure Wyszukiwanie poznawcze, ale większe obciążenia wymagają [dołączenia zasobu Cognitive Services do rozliczenia](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| defaultLanguageCode   |  Ciąg wskazujący język, który ma zostać zwrócony. Usługa zwraca wyniki rozpoznawania w określonym języku. Jeśli ten parametr nie jest określony, wartością domyślną jest "en". <br/><br/>Obsługiwane są następujące języki: <br/>*pl* — angielski (wartość domyślna) <br/> *es* — hiszpański <br/> *ja* — japoński <br/> *pt* — portugalski <br/> *zh* — chiński uproszczony|
| visualFeatures |  Tablica ciągów wskazująca typy funkcji wizualizacji do zwrócenia. Prawidłowe typy funkcji wizualizacji to:  <ul><li>*osoba dorosła* — wykrywa, czy obraz jest pornograficznej z natury (przedstawia nagość lub akt płci), czy też jest gorii (przedstawia skrajną przemoc lub krew). Wykryto również zawartość z sugestią seksualną (alias erotycznej Content).</li><li>*marki* — wykrywa różne marki w obrazie, w tym przybliżoną lokalizację. Funkcja wizualizacji *marek* jest dostępna tylko w języku angielskim.</li><li> *Kategorie* — klasyfikuje zawartość obrazu zgodnie z taksonomią zdefiniowaną w [dokumentacji przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)Cognitive Services. </li><li> *Color* — określa kolor akcentu, kolor dominujący oraz to, czy obraz jest czarny & biały.</li><li>*Opis* — zawiera opis zawartości obrazu z kompletnymi zdaniami w obsługiwanych językach.</li><li>*twarze* — wykrywa, czy twarze są obecne. Jeśli jest obecny, program generuje współrzędne, płeć i wiek.</li><li>  *ImageType* — wykrywa, czy obraz jest obiektem clipart czy rysowaniem linii.</li><li>  *obiekty* — wykrywa różne obiekty w obrazie, w tym przybliżoną lokalizację. Funkcja wizualizacji *obiektów* jest dostępna tylko w języku angielskim.</li><li> *znaczniki* — znaczniki obrazu ze szczegółową listą wyrazów związanych z zawartością obrazu.</li></ul> Nazwy funkcji wizualnych są rozróżniane wielkości liter.|
| details informacje   | Tablica ciągów wskazująca, które szczegóły dotyczące domeny mają być zwracane. Prawidłowe typy funkcji wizualizacji to: <ul><li>*osobistości* — identyfikuje osobistości, jeśli został wykryty w obrazie.</li><li>*punkty orientacyjne* — wskazuje punkty orientacyjne, jeśli zostały wykryte na obrazie. </li></ul> |

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa      | Opis                                          |
|---------------|------------------------------------------------------|
| image         | Typ złożony. Obecnie działa tylko z polem "/Document/normalized_images" tworzonym przez indeksator usługi Azure Blob, gdy ```imageAction``` jest ustawiona na wartość inną niż ```none```. Zobacz [przykład](#sample-output) , aby uzyskać więcej informacji.|



##  <a name="sample-skill-definition"></a>Przykładowa definicja kwalifikacji

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "Tags",
                "Categories",
                "Description",
                "Faces"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Przykładowy indeks (tylko dla kategorii, opis, powierzchnie i Tagi)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Przykładowe Mapowanie pól wyjściowych (dla powyższego indeksu)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Zmiana mapowań pól wyjściowych (właściwości zagnieżdżone)

Można zdefiniować mapowania pól wyjściowych na właściwości niższego poziomu, takie jak tylko dzielnice lub osobistości. W takim przypadku upewnij się, że schemat indeksu ma pole, aby zawierało punkty orientacyjne.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "color": {
          "dominantColorForeground": "Brown",
          "dominantColorBackground": "Brown",
          "dominantColors": [
            "Brown",
            "Black"
          ],
          "accentColor": "873B59",
          "isBwImg": false
        },
        "imageType": {
          "clipArtType": 0,
          "lineDrawingType": 0
        },
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Przypadki błędów
W następujących przypadkach błędów nie są wyodrębniane żadne elementy.

| Kod błędu | Opis |
|------------|-------------|
| NotSupportedLanguage | Podany język nie jest obsługiwany. |
| InvalidImageUrl | Adres URL obrazu jest nieprawidłowo sformatowany lub nie jest dostępny.|
| InvalidImageFormat | Dane wejściowe nie są prawidłowym obrazem. |
| InvalidImageSize | Obraz wejściowy jest zbyt duży. |
| NotSupportedVisualFeature  | Określony typ funkcji jest nieprawidłowy. |
| NotSupportedImage | Nieobsługiwany obraz, na przykład pornografia podrzędna. |
| InvalidDetails | Nieobsługiwany model specyficzny dla domeny. |

Jeśli zostanie wyświetlony komunikat o błędzie podobny do `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`, sprawdź ścieżkę. Zarówno osobistości, jak i punkty orientacyjne są właściwościami w obszarze `detail`.

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Zobacz także

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz indeksator (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
