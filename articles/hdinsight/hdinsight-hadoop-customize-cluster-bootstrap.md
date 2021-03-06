---
title: Dostosowywanie konfiguracji klastrów usługi Azure HDInsight przy użyciu narzędzia Bootstrap
description: Dowiedz się, jak dostosować konfigurację klastra usługi HDInsight programowo przy użyciu szablonów programu .NET, programu PowerShell i Menedżer zasobów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: e641340ac04415ee4a20cda2bc09bbdbef9802a6
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931402"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap

Skrypty Bootstrap umożliwiają programowe Instalowanie i Konfigurowanie składników w usłudze Azure HDInsight.

Istnieją trzy podejścia do ustawiania ustawień pliku konfiguracji podczas tworzenia klastra usługi HDInsight:

* Korzystanie z programu Azure PowerShell
* Korzystanie z zestawu SDK dla platformy .NET
* Korzystanie z szablonu usługi Azure Resource Manager

Na przykład przy użyciu tych metod programistycznych można skonfigurować opcje w następujących plikach:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred — lokacja
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server. Properties (Konfiguracja Kafka-Broker)

Aby uzyskać informacje na temat instalowania dodatkowych składników w klastrze usługi HDInsight podczas tworzenia, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

* W przypadku korzystania z programu PowerShell należy użyć polecenia [AZ module](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Poniższy kod programu PowerShell dostosowuje konfigurację [Apache Hive](https://hive.apache.org/) :

> [!IMPORTANT]  
> Może być konieczne użycie parametru `Spark2Defaults` z [dodatkiem Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Można przekazać wartości puste do parametru, jak pokazano w poniższym przykładzie kodu.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Pełny działający skrypt programu PowerShell znajduje się w [dodatku](#appendix-powershell-sample).

**Aby sprawdzić zmianę:**

1. Przejdź do `https://CLUSTERNAME.azurehdinsight.net/`, gdzie `CLUSTERNAME` jest nazwą klastra.
1. W menu po lewej stronie przejdź do > **Konfiguracja** usługi **Hive** > **Zaawansowane**.
1. Rozwiń węzeł **zaawansowana gałąź — lokacja**.
1. Zlokalizuj **gałąź Hive. metadanych. Client. Socket. Timeout** i Potwierdź, że wartość to **latach 90**.

Więcej przykładów na temat dostosowywania innych plików konfiguracji:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Zobacz [zestaw SDK usługi Azure HDInsight dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

Narzędzia Bootstrap można użyć w szablonie Menedżer zasobów:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Usługa Hadoop dostosowuje szablon Azure Resource Manager ładowania początkowego klastra](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Zobacz także

* [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md) zawiera instrukcje dotyczące tworzenia klastra usługi HDInsight przy użyciu innych opcji niestandardowych.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalowanie i używanie Apache Spark w klastrach usługi HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Instalowanie i używanie oprogramowania Apache Giraph w klastrach usługi HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Dodatek: Przykładowy program PowerShell

Ten skrypt programu PowerShell tworzy klaster usługi HDInsight i dostosowuje ustawienie Hive. Wprowadź wartości dla `$nameToken`, `$httpPassword`i `$sshPassword`.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
