---
title: Tworzenie zintegrowanych rozwiązań
description: Narzędzia i partnerzy rozwiązania integrujące się z magazynem danych obsługiwanym przy użyciu usługi SQL Analytics.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8b38b50401c50ecfb9cd37c53ad013ca05e126c0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153302"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integrowanie innych usług z magazynem danych usługi SQL Analytics 
Funkcja analizy SQL w ramach usługi Azure Synapse Analytics umożliwia użytkownikom integrację z wieloma innymi usługami na platformie Azure. Za pomocą usługi SQL Analytics można utworzyć magazyn danych za pośrednictwem zasobu puli SQL, który może następnie korzystać z kilku dodatkowych usług, z których niektóre obejmują:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Usługa Azure Stream Analytics

Aby uzyskać więcej informacji na temat usług integracji na platformie Azure, zapoznaj się z artykułem [partnerzy integracji](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Integracja Power BI umożliwia łączenie mocy obliczeniowej hurtowni danych z dynamicznym raportowaniem i wizualizacją Power BI. Obecnie integracja Power BI obejmuje:

* **Połączenie bezpośrednie**: bardziej zaawansowane połączenie z przekazywaniem logicznym względem magazynu danych obsługiwanego przy użyciu puli SQL. Przekazywanie zapewnia szybszą analizę w większej skali.
* **Otwórz w Power BI**: przycisk "otwórz w Power BI" przekazuje informacje o wystąpieniu do Power BI w uproszczony sposób nawiązywania połączenia.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)lub [Dokumentacja Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory zapewnia użytkownikom zarządzaną platformę do tworzenia złożonej wyodrębniania i potoków ładowania. Integracja puli SQL z Azure Data Factory obejmuje:

* **Procedury składowane**: organizuje wykonywanie procedur składowanych.
* **Kopiowanie**: Użyj podajnika APD do przenoszenia danych do puli SQL. Ta operacja może korzystać ze standardowego mechanizmu przenoszenia danych ADF lub bazy w ramach okładek. 

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning to w pełni zarządzana usługa analizy, która umożliwia tworzenie modeli Intricate przy użyciu dużego zestawu narzędzi predykcyjnych. Pula SQL jest obsługiwana jako źródło i miejsce docelowe dla tych modeli i ma następujące funkcje:

* **Odczytaj dane:** Modele dysków na dużą skalę przy użyciu języka T-SQL względem puli SQL.
* **Zapisz dane:** Zatwierdź zmiany z dowolnego modelu z powrotem do puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Azure Stream Analytics to złożona, w pełni zarządzana infrastruktura służąca do przetwarzania i zużywania danych zdarzeń generowanych z usługi Azure Event Hub.  Integracja z pulą SQL umożliwia wydajne przetwarzanie danych przesyłanych strumieniowo i przechowywanie ich wraz z danymi relacyjnymi, co pozwala na dokładniejszą i bardziej zaawansowaną analizę.  

* **Dane wyjściowe zadania:** Wyślij dane wyjściowe z zadań Stream Analytics bezpośrednio do puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


