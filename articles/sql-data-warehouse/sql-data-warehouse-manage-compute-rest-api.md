---
title: Wstrzymywanie, wznawianie i skalowanie przy użyciu interfejsów API REST
description: Zarządzanie mocą obliczeniową w Azure SQL Data Warehouse za poorednictwem interfejsów API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f72b3fd1024a68a6f48d2e9e676fc7ca23bf2a4f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686042"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Interfejsy API REST dla Azure SQL Data Warehouse
Interfejsy API REST do zarządzania obliczeniami w Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych
Aby zmienić jednostki magazynu danych, należy użyć interfejsu API REST [tworzenia lub aktualizacji bazy danych](/rest/api/sql/databases/createorupdate) . Poniższy przykład ustawia jednostki magazynu danych wartości DW1000 dla bazy danych MySQLDW, która jest hostowana na serwerze Server. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby wstrzymać bazę danych, należy użyć interfejsu API REST usługi [Pause Database](/rest/api/sql/databases/pause) . Poniższy przykład wstrzymuje bazę danych o nazwie Database02 hostowaną na serwerze o nazwie Serwer01. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Wznów Obliczanie

Aby uruchomić bazę danych, należy użyć interfejsu API REST [wznawiania bazy danych](/rest/api/sql/databases/resume) . W poniższym przykładzie jest uruchamiana baza danych o nazwie Database02 hostowana na serwerze o nazwie Serwer01. Serwer należy do grupy zasobów platformy Azure o nazwie ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Sprawdź stan bazy danych

> [!NOTE]
> Obecnie sprawdzanie stanu bazy danych może zwrócić do trybu ONLINE, gdy baza danych kończy przepływ pracy w trybie online, co powoduje błędy połączeń. W przypadku korzystania z tego wywołania interfejsu API w celu wyzwalania prób połączenia może być konieczne dodanie 2 do 3 minut opóźnienia w kodzie aplikacji.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Pobierz harmonogram konserwacji
Sprawdź harmonogram konserwacji, który został ustawiony dla hurtowni danych. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Ustaw harmonogram konserwacji
Aby ustawić i zaktualizować harmonogram maintnenance w istniejącym magazynie danych.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md).

