---
title: Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu programu PowerShell
description: Dowiedz się, jak utworzyć klaster Eksplorator danych i bazę danych platformy Azure przy użyciu programu PowerShell
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: d4561d49c37298a2b1a7f6c6542d78c3e19a145c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978336"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Program PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon usługi ARM](create-cluster-database-resource-manager.md)  

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. W tym artykule opisano tworzenie klastra i bazy danych przy użyciu programu PowerShell. Polecenia cmdlet i skrypty programu PowerShell w systemie Windows, Linux lub [Azure Cloud Shell](../cloud-shell/overview.md) można uruchamiać w programie [AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) , aby tworzyć i konfigurować klastry i bazy danych platformy Azure Eksplorator danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby sprawdzić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurowanie parametrów

Poniższe kroki nie są wymagane, jeśli uruchamiasz polecenia w usłudze Azure Cloud Shell. Jeśli używasz interfejsu wiersza polecenia lokalnie, wykonaj kroki 1 & 2, aby zalogować się do platformy Azure i ustawić bieżącą subskrypcję:

1. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Ustaw subskrypcję, w której ma zostać utworzony klaster:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Podczas lokalnego uruchamiania interfejsu wiersza polecenia platformy Azure lub w Azure Cloud Shell należy zainstalować na urządzeniu moduł AZ. Kusto:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Data Explorer

1. Utwórz klaster przy użyciu następującego polecenia:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | Nazwa | *mykustocluster* | Wybrana nazwa klastra.|
   | Jednostka SKU | *D13_v2* | Jednostka SKU, która będzie używana na potrzeby klastra. |
   | ResourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    Istnieją dodatkowe parametry opcjonalne, których można używać, takie jak pojemność klastra.

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Jeśli wynik zawiera element `provisioningState` o wartości `Succeeded`, klaster został utworzony pomyślnie.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Data Explorer

1. Utwórz bazę danych przy użyciu następującego polecenia:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | NazwaKlastra | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | Nazwa | *mykustodatabase* | Nazwa bazy danych.|
   | ResourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | softDeletePeriod | *3650:00:00:00* | Okres przechowywania danych na potrzeby zapytań. |
   | hotCachePeriod | *3650:00:00:00* | Okres przechowywania danych w pamięci podręcznej. |

1. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

* Jeśli planujesz postępować zgodnie z innymi artykułami, Zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, usuń klaster. Usunięcie klastra powoduje również usunięcie znajdujących się w nim baz danych. Użyj następującego polecenia, aby usunąć klaster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Następne kroki

* [Dodatkowe polecenia AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Pozyskiwanie danych przy użyciu usługi Azure Eksplorator danych .NET Standard SDK (wersja zapoznawcza)](net-standard-ingest-data.md)
