---
title: Eksplorowanie Azure Stream Analytics zadań w Visual Studio Code
description: W tym artykule pokazano, jak wyeksportować zadanie Azure Stream Analytics do projektu lokalnego, wyświetlić listę zadań i wyświetlić jednostki zadań.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479243"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Eksplorowanie Azure Stream Analytics z Visual Studio Code (wersja zapoznawcza)

Azure Stream Analytics rozszerzenia Visual Studio Code oferuje deweloperom lekki komfort zarządzania swoimi zadaniami Stream Analytics. Można go używać w systemach Windows, Mac i Linux. Dzięki rozszerzeniu Azure Stream Analytics można:

- [Tworzenie](quick-create-vs-code.md), uruchamianie i zatrzymywanie zadań
- Eksportowanie istniejących zadań do projektu lokalnego
- Wyświetlanie listy zadań i przeglądanie jednostek zadań

## <a name="export-a-job-to-a-local-project"></a>Eksportowanie zadania do projektu lokalnego

Aby wyeksportować zadanie do projektu lokalnego, zlokalizuj zadanie, które chcesz wyeksportować w **eksploratorze Stream Analytics** w Visual Studio Code. Następnie wybierz folder dla projektu. Projekt zostanie wyeksportowany do folderu, który wybierzesz, i można nadal zarządzać zadaniem z poziomu Visual Studio Code. Aby uzyskać więcej informacji na temat korzystania z Visual Studio Code do zarządzania zadaniami Stream Analytics, zobacz [Przewodnik Szybki Start](quick-create-vs-code.md)dotyczący usługi Visual Studio Code.

![Eksportowanie zadania ASA w Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Zadanie listy i wyświetlanie jednostek zadań

Widok zadania służy do współpracy z zadaniami Azure Stream Analytics w programie Visual Studio.


1. Kliknij ikonę **platformy Azure** na pasku działania Visual Studio Code a następnie rozwiń **węzeł Stream Analytics**. Zadania powinny być wyświetlane w ramach subskrypcji.

   ![Otwórz Eksploratora Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Rozwiń węzeł zadania, możesz otworzyć i wyświetlić zapytanie dotyczące zadania, konfiguracji, danych wejściowych, wyjść i funkcji. 

3. Kliknij prawym przyciskiem myszy węzeł zadania, a następnie wybierz **Widok Otwórz zadanie w węźle Portal** , aby otworzyć widok zadania w Azure Portal.

   ![Otwórz widok zadania w portalu](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie Azure Stream Analytics zadania w chmurze w Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)
