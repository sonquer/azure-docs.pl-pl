---
title: Jak skonfigurować wielowzorcowość w usłudze Azure Cosmos DB
description: Dowiedz się, jak skonfigurować wiele wzorców dla aplikacji przy użyciu różnych zestawów SDK w Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873645"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurowanie wielu wzorców w aplikacjach korzystających z Azure Cosmos DB

Po utworzeniu konta z włączoną obsługą wielu regionów zapisu należy wprowadzić dwie zmiany w aplikacji do ConnectionPolicy dla DocumentClient, aby włączyć obsługę wielu wzorców i wielu multihostingu w Azure Cosmos DB. W ConnectionPolicy Ustaw UseMultipleWriteLocations na true i przekaż nazwę regionu, w którym aplikacja jest wdrażana na SetCurrentLocation. Spowoduje to wypełnienie właściwości PreferredLocations w oparciu o bliskość geograficzną z przekazaną lokalizacją. Jeśli nowy region zostanie później dodany do konta, aplikacja nie musi być aktualizowana ani ponownie wdrażana, a w przypadku wystąpienia zdarzenia regionalnego zostanie automatycznie wykryta bliższy region.

> [!Note]
> Konta Cosmos początkowo skonfigurowane z pojedynczym regionem zapisu można skonfigurować dla wielu regionów zapisu (tj. wielu wzorców) z zerem o wartości zero. Aby dowiedzieć się więcej, zobacz [Konfigurowanie regionów wielokrotnego zapisu](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a id="netv2">Zestaw .NET SDK w wersji 2</a>

Aby włączyć wiele wzorców w aplikacji, ustaw `UseMultipleWriteLocations` na `true`. Ponadto należy ustawić `SetCurrentLocation` do regionu, w którym aplikacja jest wdrażana i gdzie Azure Cosmos DB jest replikowana:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>Zestaw .NET SDK v3

Aby włączyć obsługę wielu wzorców w aplikacji, ustaw `ApplicationRegion` na region, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcjonalnie można użyć `CosmosClientBuilder` i `WithApplicationRegion`, aby osiągnąć ten sam wynik:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a id="java"></a>Java Async SDK

Aby włączyć wiele wzorców w aplikacji, ustaw `policy.setUsingMultipleWriteLocations(true)` i ustaw `policy.setPreferredLocations` do regionu, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Node. js, JavaScript i zestawy SDK TypeScript

Aby włączyć wiele wzorców w aplikacji, ustaw `connectionPolicy.UseMultipleWriteLocations` na `true`. Ponadto należy ustawić `connectionPolicy.PreferredLocations` do regionu, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Python SDK

Aby włączyć wiele wzorców w aplikacji, ustaw `connection_policy.UseMultipleWriteLocations` na `true`. Ponadto należy ustawić `connection_policy.PreferredLocations` do regionu, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły:

* [Używanie tokenów sesji do zarządzania spójnością w Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflict types and resolution policies in Azure Cosmos DB (Typy konfliktów i zasady ich rozwiązywania w usłudze Azure Cosmos DB)](conflict-resolution-policies.md)
* [High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)](high-availability.md)
* [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
* [Wybierz odpowiedni poziom spójności w Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy w zakresie spójności, dostępności i wydajności w usłudze Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Globalnie skalowanie przepływności aprowizacji](scaling-throughput.md)
* [Dystrybucja globalna: pod okapem](global-dist-under-the-hood.md)
