---
title: Pobieranie par klucz-wartość z punktu w czasie
titleSuffix: Azure App Configuration
description: Pobieranie starych par klucz-wartość przy użyciu migawek do momentu w konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523664"
---
# <a name="point-in-time-snapshot"></a>Migawka punktu w czasie

Konfiguracja aplikacji platformy Azure przechowuje rekord zmian wprowadzonych w parach klucz-wartość. Ten rekord zawiera oś czasu zmian klucza i wartości. Można odtworzyć historię dowolnej wartości klucza i w dowolnym momencie podać jej wartość przeszłą w ciągu ostatnich siedmiu dni. Korzystając z tej funkcji, możesz "podróż czasowa" do tyłu i pobrać starą wartość klucza. Można na przykład odzyskać ustawienia konfiguracji używane przed ostatnim wdrożeniem, aby przywrócić poprzednią konfigurację aplikacji.

## <a name="key-value-retrieval"></a>Pobieranie wartości klucza

Za pomocą Azure PowerShell można pobrać przeszłe wartości klucza.  Użyj `az appconfig revision list`, dodając odpowiednie parametry w celu pobrania wymaganych wartości.  Określ wystąpienie konfiguracji aplikacji platformy Azure, podając nazwę magazynu (`--name {app-config-store-name}`) lub korzystając z parametrów połączenia (`--connection-string {your-connection-string}`). Ogranicz dane wyjściowe, określając określony punkt w czasie (`--datetime`) i określając maksymalną liczbę elementów do zwrócenia (`--top`).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pobierz wszystkie zapisane zmiany wartości kluczy.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Pobierz wszystkie zarejestrowane zmiany klucza `environment` i etykiet `test` i `prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Pobierz wszystkie zarejestrowane zmiany w obszarze klucza hierarchicznego `environment:prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Pobierz wszystkie zapisane zmiany klucza `color` w określonym punkcie w czasie.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Pobierz ostatnie 10 zarejestrowanych zmian do wartości kluczy i zwróć tylko wartości `key`, `label`i `last-modified` sygnatury czasowej.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji sieci Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
