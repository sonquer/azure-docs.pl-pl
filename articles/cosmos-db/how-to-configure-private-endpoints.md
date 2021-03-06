---
title: Konfigurowanie prywatnego linku platformy Azure dla konta usługi Azure Cosmos
description: Dowiedz się, jak skonfigurować link prywatny platformy Azure, aby uzyskać dostęp do konta usługi Azure Cosmos przy użyciu prywatnego adresu IP w sieci wirtualnej.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: fde8829da3e523ced44143db0dee6b93cf9152bd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147770"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Konfigurowanie prywatnego linku platformy Azure dla konta usługi Azure Cosmos (wersja zapoznawcza)

Za pomocą prywatnego linku platformy Azure można nawiązać połączenie z kontem usługi Azure Cosmos za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w podsieci w sieci wirtualnej. Następnie możesz ograniczyć dostęp do konta usługi Azure Cosmos za pośrednictwem prywatnych adresów IP. Jeśli łącze prywatne jest połączone z ograniczonymi zasadami sieciowej grupy zabezpieczeń, pomaga zmniejszyć ryzyko związane z eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [prywatny link do platformy Azure](../private-link/private-link-overview.md) .

Link prywatny umożliwia użytkownikom dostęp do konta usługi Azure Cosmos z poziomu sieci wirtualnej lub z dowolnej równorzędnej sieci wirtualnej. Zasoby mapowane do linku prywatnego są również dostępne lokalnie przez prywatną komunikację równorzędną za pośrednictwem sieci VPN lub Azure ExpressRoute. 

Można nawiązać połączenie z kontem usługi Azure Cosmos skonfigurowanym za pomocą linku prywatnego przy użyciu metody zatwierdzania automatyczne lub ręczne. Aby dowiedzieć się więcej, zapoznaj się z sekcją [przepływ pracy zatwierdzania](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) w dokumentacji łącza prywatnego. 

W tym artykule opisano kroki tworzenia prywatnego punktu końcowego. Przyjęto założenie, że używasz metody automatycznego zatwierdzania.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Tworzenie prywatnego punktu końcowego za pomocą Azure Portal

Wykonaj następujące kroki, aby utworzyć prywatny punkt końcowy dla istniejącego konta usługi Azure Cosmos przy użyciu Azure Portal:

1. W okienku **wszystkie zasoby** wybierz konto usługi Azure Cosmos.

1. Wybierz pozycję **połączenia prywatne punktów końcowych** z listy ustawień, a następnie wybierz pozycję **prywatny punkt końcowy**:

   ![Wybory dla tworzenia prywatnego punktu końcowego w Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. W okienku **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza)** wprowadź lub wybierz następujące szczegóły:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz grupę zasobów.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź dowolną nazwę prywatnego punktu końcowego. Jeśli ta nazwa jest wykonywana, utwórz unikatową. |
    |Region| Wybierz region, w którym chcesz wdrożyć link prywatny. Utwórz prywatny punkt końcowy w tej samej lokalizacji, w której znajduje się Twoja sieć wirtualna.|
    |||
1. Wybierz pozycję **Dalej: zasób**.
1. W obszarze **Utwórz prywatny punkt końcowy zasobu**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**. <br/><br/> Następnie możesz wybrać jeden z zasobów, aby skonfigurować link prywatny. Możesz też nawiązać połączenie z zasobem innej osoby, używając identyfikatora zasobu lub aliasu, który zostały Ci udostępnione.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Zasób |Wybierz swoje konto usługi Azure Cosmos. |
    |Docelowy zasób podrzędny |Wybierz typ interfejsu API Azure Cosmos DB, który chcesz zmapować. Ta wartość domyślna to tylko jeden wybór dla interfejsów API SQL, MongoDB i Cassandra. W przypadku interfejsów API Gremlin i tabel można również wybrać **SQL** , ponieważ te interfejsy API współdziałają z interfejsem API SQL. |
    |||

1. Wybierz pozycję **Dalej: Konfiguracja**.
1. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — Konfiguracja**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieć**| |
    | Sieć wirtualna| Wybierz sieć wirtualną. |
    | Subnet | Wybierz podsieć. |
    |**Integracja Prywatna strefa DNS**||
    |Integracja z prywatną strefą DNS |Wybierz pozycję **Tak**. <br><br/> Aby połączyć się prywatnie z prywatnym punktem końcowym, potrzebny jest rekord DNS. Zalecamy integrację prywatnego punktu końcowego z prywatną strefą DNS. Możesz również użyć własnych serwerów DNS lub utworzyć rekordy DNS przy użyciu plików hosta na maszynach wirtualnych. |
    |Strefa Prywatna strefa DNS |Wybierz pozycję **privatelink.Documents.Azure.com**. <br><br/> Prywatna strefa DNS jest określana automatycznie. Nie można go zmienić za pomocą Azure Portal.|
    |||

1. Wybierz pozycję **Przegląd + utwórz**. Na stronie **Recenzja i tworzenie** zweryfikuje swoją konfigurację.
1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

Po zaakceptowaniu prywatnego linku dla konta usługi Azure Cosmos w obszarze Azure Portal opcja **wszystkie sieci** w okienku **Zapora i sieci wirtualne** jest niedostępna.

W poniższej tabeli przedstawiono mapowanie między różnymi typami interfejsów API konta usługi Azure Cosmos, obsługiwanymi podrzędnymi zasobami i odpowiednimi nazwami stref prywatnych. Dostęp do kont Gremlin i interfejs API tabel można także uzyskać za pomocą interfejsu API SQL, dlatego dla tych interfejsów API są dostępne dwa wpisy.

|Typ interfejsu API konta usługi Azure Cosmos  |Obsługiwane zasoby podrzędne (lub identyfikatory grup) |Nazwa strefy prywatnej  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Pobierz prywatne adresy IP

Po aprowizacji prywatnego punktu końcowego możesz wysyłać zapytania dotyczące adresów IP. Aby wyświetlić adresy IP z Azure Portal:

1. Wybierz pozycję **Wszystkie zasoby**.
1. Wyszukaj utworzony wcześniej prywatny punkt końcowy. W tym przypadku jest to **cdbPrivateEndpoint3**.
1. Wybierz kartę **Przegląd** , aby wyświetlić ustawienia DNS i adresy IP.

![Prywatne adresy IP w Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Utworzono wiele adresów IP dla prywatnego punktu końcowego:

* Jeden dla globalnego punktu końcowego (region-niezależny od) konta usługi Azure Cosmos
* Jeden dla każdego regionu, w którym wdrożono konto usługi Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Tworzenie prywatnego punktu końcowego za pomocą Azure PowerShell

Uruchom następujący skrypt programu PowerShell, aby utworzyć prywatny punkt końcowy o nazwie "MyPrivateEndpoint" dla istniejącego konta usługi Azure Cosmos. Zastąp wartości zmiennej szczegółowymi informacjami dla danego środowiska.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrowanie prywatnego punktu końcowego z prywatną strefą DNS

Po utworzeniu prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS przy użyciu następującego skryptu programu PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Pobierz prywatne adresy IP

Po aprowizacji prywatnego punktu końcowego można wysyłać zapytania dotyczące adresów IP i mapowania nazw FQDN przy użyciu następującego skryptu programu PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Tworzenie prywatnego punktu końcowego przy użyciu szablonu Menedżer zasobów

Połączenie prywatne można skonfigurować przez utworzenie prywatnego punktu końcowego w podsieci sieci wirtualnej. Można to osiągnąć przy użyciu szablonu Azure Resource Manager.

Użyj poniższego kodu, aby utworzyć szablon Menedżer zasobów o nazwie "PrivateEndpoint_template. JSON". Ten szablon służy do tworzenia prywatnego punktu końcowego dla istniejącego konta interfejsu API usługi Azure Cosmos w istniejącej sieci wirtualnej.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Zdefiniuj plik parametrów dla szablonu**

Utwórz plik parametrów dla szablonu i nadaj mu nazwę "PrivateEndpoint_parameters. JSON". Dodaj następujący kod do pliku parametrów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Wdrażanie szablonu przy użyciu skryptu programu PowerShell**

Utwórz skrypt programu PowerShell przy użyciu następującego kodu. Przed uruchomieniem skryptu Zastąp Identyfikator subskrypcji, nazwę grupy zasobów i inne wartości zmiennych informacjami o danym środowisku.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

W skrypcie programu PowerShell zmienna `GroupId` może zawierać tylko jedną wartość. Ta wartość jest typem interfejsu API konta. Dozwolone wartości to: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`i `Table`. Niektóre typy kont usługi Azure Cosmos są dostępne za pomocą wielu interfejsów API. Na przykład:

* Do konta interfejsu API Gremlin można uzyskać dostęp zarówno z kont usługi Gremlin, jak i interfejsu API SQL.
* Do konta interfejs API tabel można uzyskać dostęp z poziomu kont interfejsu API zarówno w tabeli, jak i SQL.

Dla tych kont należy utworzyć jeden prywatny punkt końcowy dla każdego typu interfejsu API. Odpowiedni typ interfejsu API jest określony w tablicy `GroupId`.

Po pomyślnym wdrożeniu szablonu można zobaczyć dane wyjściowe podobne do poniższej ilustracji. Wartość `provisioningState` jest `Succeeded`, jeśli prywatne punkty końcowe są poprawnie skonfigurowane.

![Dane wyjściowe wdrożenia dla szablonu Menedżer zasobów](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Po wdrożeniu szablonu prywatne adresy IP są zastrzeżone w podsieci. Reguła zapory dla konta usługi Azure Cosmos jest skonfigurowana do akceptowania połączeń tylko z prywatnego punktu końcowego.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrowanie prywatnego punktu końcowego ze strefą Prywatna strefa DNS

Użyj poniższego kodu, aby utworzyć szablon Menedżer zasobów o nazwie "PrivateZone_template. JSON". Ten szablon tworzy prywatną strefę DNS dla istniejącego konta interfejsu API SQL Cosmos platformy Azure w istniejącej sieci wirtualnej.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }       
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }       
    ]
}
```

Użyj poniższego kodu, aby utworzyć szablon Menedżer zasobów o nazwie "PrivateZoneRecords_template. JSON".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }       
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }   
    ]
}
```

**Zdefiniuj plik parametrów dla szablonu**

Utwórz następujący dwa pliki parametrów dla szablonu. Utwórz plik "PrivateZone_parameters. JSON". przy użyciu następującego kodu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Utwórz plik "PrivateZoneRecords_parameters. JSON". przy użyciu następującego kodu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Wdrażanie szablonu przy użyciu skryptu programu PowerShell**

Utwórz skrypt programu PowerShell przy użyciu następującego kodu. Przed uruchomieniem skryptu Zastąp Identyfikator subskrypcji, nazwę grupy zasobów i inne wartości zmiennych informacjami o danym środowisku.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Konfigurowanie niestandardowego systemu DNS

Należy używać prywatnej strefy DNS w podsieci, w której utworzono prywatny punkt końcowy. Skonfiguruj punkty końcowe tak, aby każdy prywatny adres IP był mapowany na wpis DNS. (Zobacz Właściwość `fqdns` w odpowiedzi pokazanej wcześniej).

Podczas tworzenia prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS na platformie Azure. Jeśli zdecydujesz się użyć niestandardową strefę DNS, musisz ją skonfigurować, aby dodać rekordy DNS dla wszystkich prywatnych adresów IP zarezerwowanych dla prywatnego punktu końcowego.

## <a name="private-link-combined-with-firewall-rules"></a>Link prywatny połączony z regułami zapory

W przypadku korzystania z prywatnego linku w połączeniu z regułami zapory są możliwe następujące sytuacje i wyniki:

* Jeśli nie skonfigurujesz żadnych reguł zapory, domyślnie cały ruch będzie miał dostęp do konta usługi Azure Cosmos.

* W przypadku skonfigurowania ruchu publicznego lub punktu końcowego usługi i utworzenia prywatnych punktów końcowych różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory.

* Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi i utworzysz prywatne punkty końcowe, konto usługi Azure Cosmos będzie dostępne tylko za pomocą prywatnych punktów końcowych. Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych konto zostanie otwarte w całej sieci.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizowanie prywatnego punktu końcowego po dodaniu lub usunięciu regionu

Dodawanie lub usuwanie regionów na koncie usługi Azure Cosmos wymaga dodania lub usunięcia wpisów DNS dla tego konta. Zaktualizuj te zmiany odpowiednio w prywatnym punkcie końcowym, wykonując następujące czynności:

1. Gdy administrator Azure Cosmos DB dodaje lub usuwa regiony, administrator sieci otrzymuje powiadomienie o oczekujących zmianach. Dla prywatnego punktu końcowego zamapowanego na konto usługi Azure Cosmos wartość właściwości `ActionsRequired` zmienia się z `None` na `Recreate`. Następnie administrator sieci aktualizuje prywatny punkt końcowy, wysyłając żądanie PUT z tym samym ładunkiem Menedżer zasobów, który został użyty do jego utworzenia.

1. Po zaktualizowaniu prywatnego punktu końcowego można zaktualizować prywatną strefę DNS podsieci w celu odzwierciedlenia dodanych lub usuniętych wpisów DNS oraz odpowiednich prywatnych adresów IP.

Załóżmy na przykład, że wdrożono konto usługi Azure Cosmos w trzech regionach: "zachodnie stany USA", "środkowe stany USA" i "Europa Zachodnia". W przypadku tworzenia prywatnego punktu końcowego dla konta cztery prywatne adresy IP są zastrzeżone w podsieci. Istnieje jeden adres IP dla każdego z trzech regionów i istnieje jeden adres IP dla punktu końcowego Global/region-niezależny od.

Później możesz dodać nowy region (na przykład "Wschodnie stany USA") do konta usługi Azure Cosmos. Domyślnie nowy region nie jest dostępny z istniejącego prywatnego punktu końcowego. Administrator konta usługi Azure Cosmos powinien odświeżyć połączenie prywatnego punktu końcowego przed uzyskaniem dostępu do niego z nowego regionu. 

Po uruchomieniu polecenia ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` dane wyjściowe polecenia zawierają parametr `actionsRequired`. Ten parametr jest ustawiony na `Recreate`. Ta wartość wskazuje, że prywatny punkt końcowy powinien być odświeżony. Następnie administrator konta usługi Azure Cosmos uruchamia polecenie `Set-AzPrivateEndpoint`, aby wyzwolić odświeżanie prywatnego punktu końcowego.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Nowy prywatny adres IP jest automatycznie zarezerwowany w podsieci w tym prywatnym punkcie końcowym. Wartość `actionsRequired` jest `None`. Jeśli nie masz prywatnej integracji strefy DNZ (innymi słowy, jeśli używasz niestandardowej prywatnej strefy DNS), musisz skonfigurować prywatną strefę DNS, aby dodać nowy rekord DNS dla prywatnego adresu IP, który odnosi się do nowego regionu.

Po usunięciu regionu można użyć tych samych kroków. Prywatny adres IP usuniętego regionu jest automatycznie odzyskiwany, a flaga `actionsRequired` zostanie `None`. Jeśli nie masz żadnej integracji prywatnej strefy DNZ, musisz skonfigurować prywatną strefę DNS, aby usunąć rekord DNS dla usuniętego regionu.

Rekordy DNS w prywatnej strefie DNS nie są usuwane automatycznie po usunięciu prywatnego punktu końcowego lub usunięciu regionu z konta usługi Azure Cosmos. Należy ręcznie usunąć rekordy DNS.

## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania z prywatnego linku do konta usługi Azure Cosmos stosowane są następujące ograniczenia:

* Obsługa linków prywatnych dla kont usługi Azure Cosmos i sieci wirtualnych jest dostępna tylko w określonych regionach. Aby zapoznać się z listą obsługiwanych regionów, zobacz sekcję [dostępne regiony](../private-link/private-link-overview.md#availability) w artykule dotyczącym łącza prywatnego. 

  > [!NOTE]
  > Aby utworzyć prywatny punkt końcowy, upewnij się, że zarówno sieć wirtualna, jak i konto usługi Azure Cosmos są obsługiwane w regionach.

* Jeśli używasz prywatnego linku z kontem usługi Azure Cosmos przy użyciu połączenia trybu bezpośredniego, możesz użyć tylko protokołu TCP. Protokół HTTP nie jest jeszcze obsługiwany.

* W przypadku korzystania z interfejsu API Azure Cosmos DB dla kont MongoDB prywatny punkt końcowy jest obsługiwany tylko dla kont na serwerze w wersji 3,6 (to oznacza, że konta używają punktu końcowego w formacie `*.mongo.cosmos.azure.com`). Link prywatny nie jest obsługiwany dla kont na serwerze w wersji 3,2 (oznacza to, że konta używają punktu końcowego w formacie `*.documents.azure.com`). Aby użyć linku prywatnego, należy przeprowadzić migrację starych kont do nowej wersji.

* W przypadku korzystania z interfejsu API Azure Cosmos DB dla kont MongoDB z linkiem prywatnym nie można używać narzędzi takich jak Robo 3T, Studio 3T i Mongoose. Punkt końcowy może mieć obsługę linku prywatnego tylko wtedy, gdy określono parametr `appName=<account name>`. Może to być na przykład `replicaSet=globaldb&appName=mydbaccountname`. Ponieważ te narzędzia nie przekazują nazwy aplikacji w parametrach połączenia do usługi, nie można użyć prywatnego linku. Jednak nadal możesz uzyskać dostęp do tych kont przy użyciu sterowników zestawu SDK z wersją 3,6.

* Nie można przenieść ani usunąć sieci wirtualnej, jeśli zawiera link prywatny.

* Nie można usunąć konta usługi Azure Cosmos, jeśli jest ono dołączone do prywatnego punktu końcowego.

* Nie można przełączyć konta usługi Azure Cosmos do trybu failover w regionie, który nie jest zamapowany na wszystkie prywatne punkty końcowe dołączone do konta.

* Administratorowi sieci należy udzielić co najmniej uprawnienia "*/PrivateEndpointConnectionsApproval" w zakresie konta usługi Azure Cosmos w celu utworzenia automatycznie zatwierdzonych prywatnych punktów końcowych.

### <a name="limitations-to-private-dns-zone-integration"></a>Ograniczenia związane z integracją prywatnej strefy DNS

Rekordy DNS w prywatnej strefie DNS nie są usuwane automatycznie po usunięciu prywatnego punktu końcowego lub usunięciu regionu z konta usługi Azure Cosmos. Rekordy DNS należy usunąć ręcznie przed:

* Dodawanie nowego prywatnego punktu końcowego połączonego z tą prywatną strefą DNS.
* Dodawanie nowego regionu do dowolnego konta bazy danych z prywatnymi punktami końcowymi połączonymi z tą prywatną strefą DNS.

Jeśli rekordy DNS nie są czyszczone, mogą wystąpić nieoczekiwane problemy z płaszczyzną danych. Te problemy obejmują awarię danych do regionów dodanych po usunięciu prywatnego punktu końcowego lub usunięciu regionu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat funkcji zabezpieczeń Azure Cosmos DB, zobacz następujące artykuły:

* Aby skonfigurować zaporę dla Azure Cosmos DB, zobacz [Obsługa zapory](firewall-support.md).

* Aby dowiedzieć się, jak skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md).

* Aby dowiedzieć się więcej na temat linku prywatnego, zapoznaj się z dokumentacją [prywatnego linku do platformy Azure](../private-link/private-link-overview.md) .
