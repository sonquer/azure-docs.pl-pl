---
title: Reguły nazewnictwa Azure Data Factory jednostek
description: Opisuje reguły nazewnictwa dla jednostek Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8fa1340b586434bf98d51437d4dc6b08594f0afa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931886"
---
# <a name="azure-data-factory---naming-rules"></a>Reguły nazewnictwa Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [reguły nazewnictwa w programie Data Factory](../naming-rules.md).

W poniższej tabeli przedstawiono reguły nazewnictwa Data Factory artefaktów.

| Nazwa | Name Uniqueness | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Data Factory |Unikatowy dla Microsoft Azure. W nazwach rozróżniana jest wielkość liter, czyli `MyDF` i `mydf` odnoszą się do tej samej fabryki danych. |<ul><li>Każda Fabryka danych jest powiązana z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i mogą zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być bezpośrednio poprzedzony literą lub cyfrą. Kolejne kreski są niedozwolone w nazwach kontenerów.</li><li>Nazwa może składać się z 3-63 znaków.</li></ul> |
| Połączone usługi/tabele/potoki |Unikatowy w usłudze Fabryka danych. W nazwach nie jest rozróżniana wielkość liter. |<ul><li>Maksymalna liczba znaków w nazwie tabeli: 260.</li><li>Nazwy obiektów muszą zaczynać się literą, cyfrą lub znakiem podkreślenia (_).</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |
| Grupa zasobów |Unikatowy dla Microsoft Azure. W nazwach nie jest rozróżniana wielkość liter. |<ul><li>Maksymalna liczba znaków: 1000.</li><li>Nazwa może zawierać litery, cyfry i następujące znaki: "-", "_", "," i "."</li></ul> |

