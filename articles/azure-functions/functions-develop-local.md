---
title: Lokalne opracowywanie i uruchamianie usługi Azure Functions
description: Dowiedz się, jak kodować i testować usługi Azure Functions na komputerze lokalnym przed uruchomieniem ich na Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230646"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions kod i test lokalnie

Chociaż można opracowywać i testować Azure Functions w [Azure Portal], wielu deweloperów preferuje lokalne środowisko programistyczne. Funkcje ułatwiają Używanie ulubionych edytorów kodu i narzędzi programistycznych do tworzenia i testowania funkcji na komputerze lokalnym. Funkcje lokalne mogą łączyć się z aktywnymi usługami platformy Azure i debugować je na komputerze lokalnym za pomocą środowiska uruchomieniowego Full Functions.

## <a name="local-development-environments"></a>Lokalne środowiska programistyczne

Sposób tworzenia funkcji na komputerze lokalnym zależy od preferencji [języka](supported-languages.md) i narzędzi. Środowiska w poniższej tabeli obsługują programowanie lokalne:

|Środowisko                              |Języki         |Opis|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-create-first-function-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(Biblioteka klas), skrypt (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Rozszerzenie Azure Functions dla vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dodaje obsługę funkcji do vs Code. Wymaga podstawowych narzędzi. Program obsługuje programowanie w systemach Linux, MacOS i Windows w przypadku korzystania z wersji 2. x podstawowych narzędzi. Aby dowiedzieć się więcej, zobacz [Tworzenie pierwszej funkcji przy użyciu Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Wiersz polecenia lub terminal](functions-run-local.md) | [](functions-reference-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(Biblioteka klas), skrypt (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Azure Functions Core Tools] udostępnia podstawowe środowisko uruchomieniowe i szablony służące do tworzenia funkcji, które umożliwiają programowanie lokalne. Wersja 2. x obsługuje programowanie w systemach Linux, MacOS i Windows. Wszystkie środowiska są zależne od podstawowych narzędzi do środowiska uruchomieniowego funkcji lokalnych. |
| [Program Visual Studio 2019](functions-develop-vs.md) | [C#(Biblioteka klas)](functions-dotnet-class-library.md) | Narzędzia Azure Functions są dołączone do obciążeń **programistycznych platformy Azure** dla programu [Visual Studio 2019](https://www.visualstudio.com/vs/) i jego nowszych wersji. Umożliwia kompilowanie funkcji w bibliotece klas i publikowanie biblioteki. dll na platformie Azure. Obejmuje podstawowe narzędzia do testowania lokalnego. Aby dowiedzieć się więcej, zobacz temat [programowanie Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (różne) | [Java](functions-reference-java.md) | Integruje się z podstawowymi narzędziami, aby umożliwić programowanie funkcji języka Java. Wersja 2. x obsługuje programowanie w systemach Linux, MacOS i Windows. Aby dowiedzieć się więcej, zobacz [Tworzenie pierwszej funkcji przy użyciu języka Java i Maven](functions-create-first-java-maven.md). Obsługuje także Programowanie przy użyciu narzędzia [zaćmienie](functions-create-maven-eclipse.md) i [IntelliJ](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Każdy z tych lokalnych środowisk programistycznych umożliwia tworzenie projektów aplikacji funkcji i używanie wstępnie zdefiniowanych szablonów funkcji do tworzenia nowych funkcji. Każdy z nich korzysta z podstawowych narzędzi, dzięki czemu można testować i debugować funkcje w środowisku uruchomieniowym Real Functions na własnym komputerze tak samo jak w przypadku każdej innej aplikacji. Możesz również opublikować projekt aplikacji funkcji z dowolnego z tych środowisk na platformie Azure.  

## <a name="next-steps"></a>Następne kroki

+ Aby dowiedzieć się więcej na temat lokalnego C# opracowywania skompilowanych funkcji przy użyciu programu visual Studio 2019, zobacz [programowanie Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md).
+ Aby dowiedzieć się więcej na temat lokalnego opracowywania funkcji przy użyciu VS Code na komputerze Mac, Linux lub Windows, zobacz [Deploy Azure Functions from vs Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Aby dowiedzieć się więcej na temat opracowywania funkcji z poziomu wiersza polecenia lub terminalu, zobacz [Work with Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
