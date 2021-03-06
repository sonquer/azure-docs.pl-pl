---
title: Ręczne instalowanie lub aktualizowanie rozszerzeń powiązań Azure Functions
description: Dowiedz się, jak zainstalować lub zaktualizować rozszerzenia Azure Functions powiązań dla wdrożonych aplikacji funkcji.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768864"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Ręczne instalowanie lub aktualizowanie Azure Functions powiązań z poziomu portalu

Począwszy od wersji 2. x, środowisko uruchomieniowe Azure Functions używa rozszerzeń powiązań do implementowania kodu dla wyzwalaczy i powiązań. Rozszerzenia powiązań są udostępniane w pakietach NuGet. Aby zarejestrować rozszerzenie, należy zasadniczo zainstalować pakiet. Podczas opracowywania funkcji, sposób instalacji rozszerzeń powiązań zależy od środowiska programistycznego. Aby uzyskać więcej informacji, zobacz [Rejestrowanie rozszerzeń powiązań](./functions-bindings-register.md) w artykule wyzwalacze i powiązania.

Czasami trzeba ręcznie zainstalować lub zaktualizować rozszerzenia powiązań w Azure Portal. Na przykład może być konieczne zaktualizowanie zarejestrowanego powiązania do nowszej wersji. Może być również konieczne zarejestrowanie obsługiwanego powiązania, którego nie można zainstalować na karcie **integracja** w portalu.

## <a name="install-a-binding-extension"></a>Zainstaluj rozszerzenie powiązania

Wykonaj następujące kroki, aby ręcznie zainstalować lub zaktualizować rozszerzenia z portalu.

1. W [Azure Portal](https://portal.azure.com)Znajdź aplikację funkcji i wybierz ją. Wybierz kartę **Przegląd** i wybierz pozycję **Zatrzymaj**.  Zatrzymanie blokowania plików przez aplikację funkcji, aby można było wprowadzać zmiany.

1. Wybierz kartę **funkcje platformy** i w obszarze **Narzędzia deweloperskie** wybierz pozycję **Narzędzia zaawansowane (kudu)** . Punkt końcowy kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) jest otwarty w nowym oknie.

1. W oknie kudu wybierz kolejno opcje **Debuguj konsolę** > **cmd**.  

1. W oknie wiersza polecenia przejdź do `D:\home\site\wwwroot` i wybierz ikonę Usuń obok pozycji `bin`, aby usunąć folder. Wybierz **przycisk OK** , aby potwierdzić usunięcie.

1. Wybierz ikonę edycji obok pliku `extensions.csproj`, który definiuje rozszerzenia powiązań dla aplikacji funkcji. Plik projektu jest otwierany w edytorze online.

1. Wprowadź wymagane Dodatki i aktualizacje elementów **PackageReference** w **elemencie Items**, a następnie wybierz pozycję **Zapisz**. Aktualną listę obsługiwanych wersji pakietu można znaleźć w temacie [jakie pakiety są potrzebne?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) artykuł wiki. Wszystkie trzy powiązania usługi Azure Storage wymagają pakietu Microsoft. Azure. WebJobs. Extensions. Storage.

1. W folderze `wwwroot` Uruchom następujące polecenie, aby ponownie skompilować przywoływane zestawy w folderze `bin`.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Z powrotem na karcie **Przegląd** w portalu wybierz pozycję **Rozpocznij** , aby ponownie uruchomić aplikację funkcji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
