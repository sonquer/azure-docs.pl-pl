---
title: Wyzwalacz usługi Azure queue storage i powiązania dla Azure Functions — Omówienie
description: Zapoznaj się z tematem jak używać wyzwalacza usługi Azure queue storage i powiązania danych wyjściowych w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 16c325f2d6ea78ba980406f56839dd4d0b64bee1
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482262"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Wyzwalacz usługi Azure queue storage i powiązania dla Azure Functions — Omówienie

Azure Functions mogą działać jako nowe wiadomości usługi Azure queue storage są tworzone i mogą zapisywać komunikaty w kolejce w ramach funkcji.

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji jako kolejki zmian danych | [Wyzwalacz](./functions-bindings-storage-queue-trigger.md) |
| Komunikaty magazynu kolejki zapisu |[Powiązanie danych wyjściowych](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje 2. x i nowsze

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [Pakiet NuGet], wersja 3. x | |
| C#Skrypt, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) jest zalecane do użycia z Visual Studio Code. |
| C#Skrypt (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[core tools]: ./functions-run-local.md
[pakietu rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji jako kolejki zmian danych magazynu (wyzwalacz)](./functions-bindings-storage-queue-trigger.md)
- [Komunikaty magazynu kolejki zapisu (powiązanie danych wyjściowych)](./functions-bindings-storage-blob-output.md)