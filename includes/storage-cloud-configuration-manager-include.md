---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038174"
---
[Biblioteka programu Microsoft Azure Configuration Manager dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji. Klasa [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizuje ustawienia konfiguracji. Analizuje on ustawienia aplikacji klienckich uruchamianych na pulpicie, na urządzeniu przenośnym, na maszynie wirtualnej platformy Azure lub w usłudze w chmurze platformy Azure.

Aby odwołać się do pakietu `CloudConfigurationManager`, Dodaj następujące dyrektywy `using`:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Oto przykład, w którym przedstawiono, jak pobrać parametry połączenia z pliku konfiguracji:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Użycie programu Azure Configuration Manager jest opcjonalne. Można również użyć interfejsu API, takiego jak [Klasa configurationmanager](/dotnet/api/system.configuration.configurationmanager).NET Framework.
