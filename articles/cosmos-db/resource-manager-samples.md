---
title: Szablony Azure Resource Manager dla Azure Cosmos DB
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961851"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony Azure Resource Manager dla Azure Cosmos DB

W poniższych tabelach uwzględniono linki do Azure Resource Manager szablonów dla Azure Cosmos DB:

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

|**Szablon**|**Opis**|
|---|---|
|[Tworzenie konta, bazy danych, kontenera usługi Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejsu API programu SQL (rdzeń) w dwóch regionach z dwoma kontenerami z przepływem danych udostępnionej usługi Database i kontenerem o dedykowanej przepływności. Przepływność można zaktualizować przez ponowne przesłanie szablonu ze zaktualizowaną wartością właściwości przepływności. |
|[Utwórz konto usługi Azure Cosmos, bazę danych i kontener przy użyciu procedury składowanej, wyzwalacza i UDF](manage-sql-with-resource-manager.md#create-sproc) | Ten szablon służy do tworzenia konta interfejsu API SQL (rdzeń) w dwóch regionach z procedurą składowaną, wyzwalaczem i formatem UDF dla kontenera. |

## <a name="mongodb-api"></a>Interfejs API bazy danych MongoDB

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych, kolekcję](manage-mongodb-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta przy użyciu interfejsu API Azure Cosmos DB dla MongoDB w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwa kontenery, które współużytkują przepływność na poziomie bazy danych. |

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy, tabeli](manage-cassandra-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejs API Cassandra w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwie tabele, które współdzielą przepływność na poziomie przestrzeni kluczy. |

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych, wykres](manage-gremlin-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejsu API Gremlin w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwa wykresy, które współdzielą przepływność na poziomie bazy danych. |

## <a name="table-api"></a>Interfejs API tabel

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejs API tabel w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało jedną tabelę. |

> [!TIP]
> Aby włączyć współdzieloną przepływność przy korzystaniu z interfejs API tabel, należy włączyć przepływność na poziomie konta w witrynie Azure Portal.

Aby uzyskać dokumentację referencyjną, zobacz sekcję [Azure Resource Manager Reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .
