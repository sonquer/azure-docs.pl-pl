---
title: Konfigurowanie parametrów-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania parametrów Postgres na pojedynczym serwerze Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763627"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera dla Azure Database for PostgreSQL-pojedynczego serwera przy użyciu interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera usługi Azure PostgreSQL za pomocą interfejsu wiersza polecenia (CLI). Podzestaw konfiguracji aparatu jest narażony na poziomie serwera i można go modyfikować. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- Utwórz serwer Azure Database for PostgreSQL i bazę danych, wykonując następujące czynności: [utwórz Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Zainstaluj interfejs wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) na maszynie lub Użyj [Azure Cloud Shell](../cloud-shell/overview.md) w Azure Portal za pomocą przeglądarki.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for PostgreSQL Server
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [AZ Postgres Server Configuration list](/cli/azure/postgres/server/configuration) .

Dla serwera **mydemoserver.Postgres.Database.Azure.com** można wyświetlić listę parametrów konfiguracji serwera w **obszarze Grupa zasobów**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom polecenie [AZ Postgres Server Configuration show](/cli/azure/postgres/server/configuration) .

W tym przykładzie przedstawiono szczegółowe informacje o **dzienniku\_minimalny** parametr konfiguracji serwera\_komunikatów dla serwera **mydemoserver.Postgres.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modyfikuj wartość parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera PostgreSQL. Aby zaktualizować konfigurację, użyj polecenia [AZ Postgres Server Configuration Set](/cli/azure/postgres/server/configuration) . 

Aby zaktualizować **dziennik\_minimum\_komunikatów** parametry konfiguracji serwera **mydemoserver.Postgres.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Jeśli chcesz zresetować wartość parametru konfiguracji, po prostu wybierz opcję pozostawienia opcjonalnego parametru `--value`, a usługa zastosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
To polecenie resetuje **dziennik\_minimalną konfigurację\_komunikatów** **do wartości domyślnej**. Aby uzyskać więcej informacji na temat konfiguracji serwera i dozwolonych wartości, zobacz dokumentację PostgreSQL na [serwerze konfiguracji](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak ponownie uruchomić serwer](howto-restart-server-cli.md)
- Aby skonfigurować i uzyskać dostęp do dzienników serwera, zobacz [Dzienniki serwera w Azure Database for PostgreSQL](concepts-server-logs.md)
