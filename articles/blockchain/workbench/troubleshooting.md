---
title: Rozwiązywanie problemów z usługą Azure łańcucha bloków Workbench
description: Jak rozwiązywać problemy z aplikacją Azure łańcucha bloków Workbench w wersji zapoznawczej.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324296"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure łańcucha bloków Workbench Preview

Skrypt programu PowerShell jest dostępny, aby pomóc w debugowaniu i obsłudze deweloperów. Skrypt generuje podsumowanie i zbiera szczegółowe dzienniki dotyczące rozwiązywania problemów. Zebrane dzienniki obejmują:

* Sieć łańcucha bloków, taka jak Ethereum
* Mikrousługi łańcucha bloków Workbench
* Application Insights
* Monitorowanie platformy Azure (dzienniki Azure Monitor)

Możesz użyć tych informacji do określenia następnych kroków i ustalenia głównej przyczyny problemów.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Skrypt rozwiązywania problemów

Skrypt rozwiązywania problemów z programem PowerShell jest dostępny w witrynie GitHub. [Pobierz plik ZIP](https://github.com/Azure-Samples/blockchain/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Uruchamianie skryptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Uruchom skrypt `collectBlockchainWorkbenchTroubleshooting.ps1`, aby zebrać dzienniki i utworzyć plik ZIP zawierający folder informacji o rozwiązywaniu problemów. Na przykład:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skrypt akceptuje następujące parametry:

| Parametr  | Opis | Wymagany |
|---------|---------|----|
| Identyfikator | Utwórz identyfikator subskrypcji, aby utworzyć lub zlokalizować wszystkie zasoby. | Yes |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, w której wdrożono łańcucha bloków Workbench. | Yes |
| OutputDirectory | Ścieżka do tworzenia danych wyjściowych. Plik ZIP. Jeśli nie zostanie określony, wartością domyślną jest bieżący katalog. | Nie |
| LookbackHours | Liczba godzin do użycia podczas ściągania danych telemetrycznych. Wartość domyślna to 24 godziny. Wartość maksymalna to 90 godzin | Nie |
| OmsSubscriptionId | Identyfikator subskrypcji, w której wdrożone są dzienniki Azure Monitor. Ten parametr należy przekazać tylko wtedy, gdy dzienniki Azure Monitor dla sieci łańcucha bloków są wdrożone poza grupą zasobów łańcucha bloków Workbench.| Nie |
| OmsResourceGroup |Grupa zasobów, w której wdrożono dzienniki Azure Monitor. Ten parametr należy przekazać tylko wtedy, gdy dzienniki Azure Monitor dla sieci łańcucha bloków są wdrożone poza grupą zasobów łańcucha bloków Workbench.| Nie |
| OmsWorkspaceName | Nazwa obszaru roboczego Log Analytics. Ten parametr należy przekazać tylko wtedy, gdy dzienniki Azure Monitor dla sieci łańcucha bloków są wdrożone poza grupą zasobów łańcucha bloków Workbench | Nie |

## <a name="what-is-collected"></a>Co jest zbierane?

Wyjściowy plik ZIP zawiera następującą strukturę folderów:

| Folder lub plik | Opis  |
|---------|---------|
| \Summary.txt | Podsumowanie systemu |
| \Metrics\blockchain | Metryki dotyczące łańcucha bloków |
| \Metrics\Workbench | Metryki dotyczące Workbench |
| \Details\Blockchain | Szczegółowe dzienniki dotyczące łańcucha bloków |
| \Details\Workbench | Szczegółowe dzienniki dotyczące Workbench |

Plik podsumowania zawiera migawkę ogólnego stanu aplikacji i kondycji aplikacji. Podsumowanie zawiera zalecane akcje, wyróżnia najważniejsze błędy i metadane dotyczące uruchomionych usług.

Folder **metryk** zawiera metryki różnych składników systemu w miarę upływu czasu. Na przykład plik wyjściowy `\Details\Workbench\apiMetrics.txt` zawiera podsumowanie różnych kodów odpowiedzi i czasów odpowiedzi w ciągu okresu zbierania. Folder **szczegóły** zawiera szczegółowe dzienniki umożliwiające rozwiązywanie określonych problemów z usługą Workbench lub podstawową siecią łańcucha bloków. Na przykład `\Details\Workbench\Exceptions.csv` zawiera listę najnowszych wyjątków, które wystąpiły w systemie, co jest przydatne do rozwiązywania problemów z inteligentnymi kontraktami lub interakcjami z łańcucha bloków. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przewodnik rozwiązywania problemów z usługą Azure łańcucha bloków Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
