---
title: Jak używać Redis-CLI z pamięcią podręczną platformy Azure dla Redis
description: Dowiedz się, jak używać programu *Redis-CLI. exe* jako narzędzia wiersza polecenia do współdziałania z pamięcią podręczną platformy Azure dla Redis jako klienta.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: a48e69f19db88c7823365964c2fe9c0629a078bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412686"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Jak używać narzędzia wiersza polecenia Redis z usługą Azure cache for Redis

*Redis-CLI. exe* to popularne narzędzie wiersza polecenia służące do współdziałania z pamięcią podręczną platformy Azure dla Redis jako klient. To narzędzie jest również dostępne do użycia z usługą Azure cache for Redis.

Narzędzie jest dostępne dla platform systemu Windows przez pobranie [Redis narzędzi wiersza polecenia dla systemu Windows](https://github.com/MSOpenTech/redis/releases/). 

Jeśli chcesz uruchomić narzędzie wiersza polecenia na innej platformie, Pobierz pamięć podręczną platformy Azure dla Redis z [https://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Zbierz informacje o dostępie do pamięci podręcznej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informacje potrzebne do uzyskania dostępu do pamięci podręcznej można zebrać przy użyciu trzech metod:

1. Interfejs wiersza polecenia platformy Azure korzystający z polecenia [AZ Redis list-Keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell przy użyciu polecenia [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Za pomocą witryny Azure Portal.

W tej sekcji zostaną pobrane klucze z Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Włącz dostęp dla programu Redis-CLI. exe

W przypadku usługi Azure cache for Redis domyślnie włączony jest tylko Port SSL (6380). Narzędzie wiersza polecenia `redis-cli.exe` nie obsługuje protokołu SSL. Dostępne są dwie opcje konfiguracji:

1. [Włącz port bez protokołu SSL (6379)](cache-configure.md#access-ports) - **Ta konfiguracja nie jest zalecana** , ponieważ w tej konfiguracji klucze dostępu są wysyłane za pośrednictwem protokołu TCP w postaci zwykłego tekstu. Ta zmiana może naruszyć dostęp do pamięci podręcznej. Jedynym scenariuszem, w którym można wziąć pod uwagę tę konfigurację, jest uzyskanie dostępu do pamięci podręcznej testów.

2. Pobierz i zainstaluj [stunnel](https://www.stunnel.org/downloads.html).

    Uruchom **interfejs GUI stunnel** , aby uruchomić serwer.

    Kliknij prawym przyciskiem myszy ikonę paska zadań dla serwera stunnel, a następnie kliknij polecenie **Pokaż okno Dziennik**.

    W menu stunnel okno Dziennik, kliknij **konfiguracja** > **Edytuj konfigurację** , aby otworzyć bieżący plik konfiguracji.

    Dodaj następujący wpis dla programu *Redis-CLI. exe* w sekcji **definicje usług** . Wstaw rzeczywistą nazwę pamięci podręcznej zamiast `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Zapisz i zamknij plik konfiguracji. 
  
    W menu stunnel okno Dziennik, kliknij przycisk **konfiguracji** > **Załaduj ponownie konfigurację**.


## <a name="connect-using-the-redis-command-line-tool"></a>Połącz się za pomocą narzędzia wiersza polecenia Redis.

W przypadku korzystania z stunnel Uruchom program *Redis-CLI. exe*i przekaż tylko *port*i *klucz dostępu* (podstawowy lub pomocniczy), aby połączyć się z pamięcią podręczną.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel z Redis — interfejs wiersza polecenia](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Jeśli używasz pamięci podręcznej testów z **niezabezpieczonym** portem innym niż SSL, uruchom `redis-cli.exe` i przekaż *nazwę hosta*, *port*i *klucz dostępu* (podstawowy lub pomocniczy), aby połączyć się z pamięcią podręczną testów.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel z Redis — interfejs wiersza polecenia](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat używania [konsoli Redis](cache-configure.md#redis-console) do wydawania poleceń.

