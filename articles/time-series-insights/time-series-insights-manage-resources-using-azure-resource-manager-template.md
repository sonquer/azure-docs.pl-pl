---
title: Zarządzanie środowiskiem za pomocą szablonów Azure Resource Manager — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak programowo zarządzać środowiskiem Azure Time Series Insights przy użyciu Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1caa80469504d52d3103fb2776fb3e7210971690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024402"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Tworzenie zasobów Time Series Insights przy użyciu szablonów Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów Time Series Insights przy użyciu [szablonów Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), programu PowerShell i dostawcy zasobów Time Series Insights.

Time Series Insights obsługuje następujące zasoby:

   | Zasób | Opis |
   | --- | --- |
   | Środowisko | Środowisko Time Series Insights jest logicznym grupą zdarzeń odczytywanych z brokerów zdarzeń, przechowywanych i udostępnionych dla zapytań. Aby uzyskać więcej informacji, przeczytaj temat [Planowanie środowiska Azure Time Series Insightsowego](time-series-insights-environment-planning.md) |
   | Źródło zdarzeń | Źródłem zdarzenia jest połączenie z brokerem zdarzeń, z którego Time Series Insights odczytuje i pobiera zdarzenia do środowiska. Obecnie obsługiwane źródła zdarzeń to IoT Hub i centrum zdarzeń. |
   | Zestaw danych referencyjnych | Zestawy danych referencyjnych zapewniają metadane dotyczące zdarzeń w środowisku. Metadane w zestawach danych referencyjnych zostaną dołączone do zdarzeń w czasie wykonywania operacji we/wychodzącym. Zestawy danych referencyjnych są definiowane jako zasoby według ich właściwości klucza zdarzenia. Rzeczywiste metadane, które tworzą zestaw danych referencyjnych, są przekazywane lub modyfikowane za pomocą interfejsów API płaszczyzny danych. |
   | Zasady dostępu | Zasady dostępu udzielają uprawnień do wydawania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem. Aby uzyskać więcej informacji, przeczytaj temat [udzielanie dostępu do danych w środowisku Time Series Insights przy użyciu Azure Portal](time-series-insights-data-access.md) |

Szablon Menedżer zasobów to plik JSON, który definiuje infrastrukturę i konfigurację zasobów w grupie zasobów. Poniższe dokumenty opisują pliki szablonów bardziej szczegółowo:

- [Wdrożenie szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md)
- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/templates/deploy-powershell.md)
- [Typy zasobów Microsoft. TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

Szablon [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) jest publikowany w serwisie GitHub. Ten szablon tworzy środowisko Time Series Insights, podrzędne źródło zdarzeń skonfigurowane do korzystania z zdarzeń z centrum zdarzeń oraz dostęp do danych środowiska. Jeśli nie określono istniejącego centrum zdarzeń, zostanie ono utworzone przy użyciu wdrożenia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Określ szablon i parametry wdrożenia

Poniższa procedura opisuje sposób użycia programu PowerShell do wdrożenia szablonu Azure Resource Manager, który tworzy środowisko Time Series Insights, podrzędne źródło zdarzeń skonfigurowane do korzystania z zdarzeń z centrum zdarzeń i dostępu do zasad, które udzielają dostępu do dane środowiska. Jeśli nie określono istniejącego centrum zdarzeń, zostanie ono utworzone przy użyciu wdrożenia.

1. Zainstaluj Azure PowerShell, postępując zgodnie z instrukcjami w temacie Rozpoczynanie [pracy z Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Sklonuj lub Skopiuj szablon [201-timeseriesinsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) z usługi GitHub.

   * Utwórz plik parametrów

     Aby utworzyć plik parametrów, skopiuj plik [201-timeseriesinsights-Environment-z-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) .

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Wymagane parametry

     | Parametr | Opis |
     | --- | --- |
     | eventHubNamespaceName | Przestrzeń nazw źródłowego centrum zdarzeń. |
     | eventHubName | Nazwa źródłowego centrum zdarzeń. |
     | consumerGroupName | Nazwa grupy odbiorców, która będzie używana przez usługę Time Series Insights do odczytywania danych z centrum zdarzeń. **Uwaga:** Aby uniknąć rywalizacji o zasoby, ta grupa odbiorców musi być przeznaczona dla usługi Time Series Insights i nie może być udostępniana innym czytelnikom. |
     | EnvironmentName | Nazwa środowiska. Nazwa nie może zawierać takich znaków: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`i żadnych kontrolek. Wszystkie inne znaki są dozwolone.|
     | eventSourceName | Nazwa zasobu podrzędnego źródła zdarzeń. Nazwa nie może zawierać takich znaków: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`i żadnych kontrolek. Wszystkie inne znaki są dozwolone. |

    <div id="optional-parameters"></div>

   * Parametry opcjonalne

     | Parametr | Opis |
     | --- | --- |
     | existingEventHubResourceId | Opcjonalny identyfikator zasobu istniejącego centrum zdarzeń, który zostanie połączony ze środowiskiem Time Series Insights za pomocą źródła zdarzenia. **Uwaga:** Użytkownik wdrażający szablon musi mieć uprawnienia do wykonywania operacji ListKeys w centrum zdarzeń. Jeśli żadna wartość nie zostanie przeniesiona, do szablonu zostanie utworzony nowy centrum zdarzeń. |
     | environmentDisplayName | Opcjonalna przyjazna nazwa wyświetlana w narzędziach lub interfejsach użytkownika zamiast nazwy środowiska. |
     | environmentSkuName | Nazwa jednostki SKU. Aby uzyskać więcej informacji, zapoznaj się ze [stroną cennika Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Pojemność jednostki SKU. Aby uzyskać więcej informacji, zapoznaj się ze [stroną cennika Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | Minimalny przedział czasu, w którym zdarzenia środowiska będą dostępne dla zapytania. Wartość musi być określona w formacie ISO 8601, na przykład `P30D` dla zasad przechowywania wynoszących 30 dni. |
     | eventSourceDisplayName | Opcjonalna przyjazna nazwa wyświetlana w narzędziach lub interfejsach użytkownika zamiast nazwy źródła zdarzenia. |
     | eventSourceTimestampPropertyName | Właściwość zdarzenia, która będzie używana jako sygnatura czasowa źródła zdarzeń. Jeśli wartość nie jest określona dla timestampPropertyName lub określono wartość null lub pusty ciąg, zostanie użyty czas tworzenia zdarzenia. |
     | eventSourceKeyName | Nazwa klucza dostępu współdzielonego, który będzie używany przez usługę Time Series Insights do łączenia się z centrum zdarzeń. |
     | accessPolicyReaderObjectIds | Lista identyfikatorów obiektów użytkowników lub aplikacji w usłudze Azure AD, które powinny mieć dostęp czytelnika do środowiska. Identyfikator objectId jednostki usługi można uzyskać, wywołując polecenie cmdlet **Get-AzADUser** lub **Get-AzADServicePrincipal** . Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |
     | accessPolicyContributorObjectIds | Lista identyfikatorów obiektów użytkowników lub aplikacji w usłudze Azure AD, które powinny mieć dostęp współautora do środowiska. Identyfikator objectId jednostki usługi można uzyskać, wywołując polecenie cmdlet **Get-AzADUser** lub **Get-AzADServicePrincipal** . Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |

   * Na przykład następujący plik parametrów służy do tworzenia środowiska i źródła zdarzeń, które odczytuje zdarzenia z istniejącego centrum zdarzeń. Tworzy również dwie zasady dostępu, które przyznają dostęp współautora do środowiska.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```

    * Aby uzyskać więcej informacji, przeczytaj artykuł dotyczący [parametrów](../azure-resource-manager/templates/parameter-files.md) .

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Wdrażanie szablonu szybkiego startu przy użyciu programu PowerShell

> [!IMPORTANT]
> Poniższe operacje wiersza polecenia opisują [AZ PowerShell module](https://docs.microsoft.com/powershell/azure/overview).

1. W programie PowerShell Zaloguj się do konta platformy Azure.

    * W wierszu polecenia programu PowerShell uruchom następujące polecenie:

      ```powershell
      Connect-AzAccount
      ```

    * Zostanie wyświetlony monit o zalogowanie się do konta platformy Azure. Po zalogowaniu Uruchom następujące polecenie, aby wyświetlić dostępne subskrypcje:

      ```powershell
      Get-AzSubscription
      ```

    * To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję bieżącej sesji, uruchamiając następujące polecenie. Zastąp `<YourSubscriptionId>` identyfikatorem GUID subskrypcji platformy Azure, której chcesz użyć:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Utwórz nową grupę zasobów, jeśli taka nie istnieje.

   * Jeśli nie masz istniejącej grupy zasobów, Utwórz nową grupę zasobów za pomocą polecenia **New-AzResourceGroup** . Podaj nazwę grupy zasobów i lokalizacji, której chcesz użyć. Przykład:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Jeśli to się powiedzie, zostanie wyświetlona Podsumowanie nowej grupy zasobów.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Przetestuj wdrożenie.

   * Sprawdź poprawność wdrożenia, uruchamiając polecenie cmdlet `Test-AzResourceGroupDeployment`. Podczas testowania wdrożenia podaj parametry dokładnie tak, jak podczas wykonywania wdrożenia.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Tworzenie wdrożenia

    * Aby utworzyć nowe wdrożenie, uruchom polecenie cmdlet `New-AzResourceGroupDeployment` i podaj wymagane parametry po wyświetleniu monitu. Parametry obejmują nazwę wdrożenia, nazwę grupy zasobów oraz ścieżkę lub adres URL pliku szablonu. Jeśli parametr **mode** nie zostanie określony, zostanie użyta wartość domyślna **przyrostu** . Aby uzyskać więcej informacji, zobacz [wdrożenia przyrostowe i kompletne](../azure-resource-manager/templates/deployment-modes.md).

    * Następujące polecenie powoduje wybranie pięciu wymaganych parametrów w oknie programu PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    * Aby zamiast tego określić plik parametrów, użyj następującego polecenia:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Można również użyć parametrów wbudowanych podczas uruchamiania polecenia cmdlet wdrażania. Polecenie jest następujące:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Aby uruchomić [kompletne](../azure-resource-manager/templates/deployment-modes.md) wdrożenie **, należy ustawić**parametr **mode** na:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Weryfikowanie wdrożenia

    * Jeśli zasoby zostały wdrożone pomyślnie, w oknie programu PowerShell zostanie wyświetlone podsumowanie wdrożenia:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Wdróż szablon szybkiego startu za pomocą Azure Portal

   * Strona główna szablonu szybkiego startu w witrynie GitHub zawiera również przycisk **Wdróż na platformie Azure** . Kliknięcie tej opcji spowoduje otwarcie niestandardowej strony wdrożenia w Azure Portal. Na tej stronie można wprowadzać lub wybierać wartości dla każdego z parametrów z [wymaganych parametrów](#required-parameters) lub w tabelach [parametrów opcjonalnych](#optional-parameters) . Po wypełnieniu ustawień kliknięcie przycisku **Kup** spowoduje zainicjowanie wdrożenia szablonu.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat programistycznego zarządzania zasobami Time Series Insights przy użyciu interfejsów API REST, Odczytaj [Time Series Insights zarządzania](https://docs.microsoft.com/rest/api/time-series-insights-management/).
