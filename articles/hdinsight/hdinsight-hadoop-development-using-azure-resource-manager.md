---
title: Migrowanie do narzędzi Azure Resource Manager dla usługi HDInsight
description: Jak przeprowadzić migrację do Azure Resource Manager narzędzi programistycznych dla klastrów usługi HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934581"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrowanie do narzędzi programistycznych opartych na Azure Resource Manager dla klastrów usługi HDInsight

Usługa HDInsight jest przestarzała narzędzia oparte na platformie Azure Service Manager (ASM) dla usługi HDInsight. Jeśli używasz Azure PowerShell, klasycznego interfejsu wiersza polecenia platformy Azure lub zestawu .NET SDK usługi HDInsight do pracy z klastrami HDInsight, zaleca się użycie Azure Resource Manager wersji programu PowerShell, interfejsu wiersza polecenia i zestawu .NET SDK. Ten artykuł zawiera informacje o sposobach migracji do nowej metody opartej na Menedżer zasobów. O ile ma to zastosowanie, ten dokument przedstawia różnice między metodami ASM i Menedżer zasobów w usłudze HDInsight.

> [!IMPORTANT]  
> Obsługa programu PowerShell, interfejsu wiersza polecenia opartego na ASM i zestawu .NET SDK będzie kontynuowana **1 stycznia 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrowanie klasycznego interfejsu wiersza polecenia platformy Azure do Azure Resource Manager

> [!IMPORTANT]  
> Interfejs wiersza polecenia platformy Azure nie zapewnia obsługi pracy z klastrami usługi HDInsight. Nadal możesz używać klasycznego interfejsu wiersza polecenia platformy Azure z usługą HDInsight, ale klasyczny interfejs wiersza polecenia platformy Azure jest przestarzały.

Poniżej przedstawiono podstawowe polecenia do pracy z usługą HDInsight za pomocą klasycznego interfejsu wiersza polecenia platformy Azure:

* `azure hdinsight cluster create` — tworzy nowy klaster usługi HDInsight
* `azure hdinsight cluster delete` — usuwa istniejący klaster usługi HDInsight
* `azure hdinsight cluster show` — wyświetlanie informacji o istniejącym klastrze
* `azure hdinsight cluster list` — zawiera listę klastrów usługi HDInsight dla Twojej subskrypcji platformy Azure

Użyj przełącznika `-h`, aby sprawdzić parametry i przełączniki dostępne dla każdego polecenia.

### <a name="new-commands"></a>Nowe polecenia
Nowe polecenia dostępne w Azure Resource Manager są następujące:

* `azure hdinsight cluster resize` — dynamicznie zmienia liczbę węzłów procesu roboczego w klastrze.
* `azure hdinsight cluster enable-http-access` — Włącza dostęp HTTPs do klastra (domyślnie włączone)
* `azure hdinsight cluster disable-http-access` — wyłącza dostęp HTTPs do klastra
* `azure hdinsight script-action` — udostępnia polecenia do tworzenia akcji skryptu i zarządzania nimi w klastrze
* `azure hdinsight config` — zawiera polecenia służące do tworzenia pliku konfiguracji, który może być używany z `hdinsight cluster create` polecenie w celu zapewnienia informacji o konfiguracji.

### <a name="deprecated-commands"></a>Przestarzałe polecenia
W przypadku używania poleceń `azure hdinsight job` do przesyłania zadań do klastra usługi HDInsight te polecenia nie są dostępne za pomocą poleceń Menedżer zasobów. Aby programowo przesłać zadania do usługi HDInsight ze skryptów, należy zamiast tego użyć interfejsów API REST dostarczonych przez usługi HDInsight. Aby uzyskać więcej informacji na temat przesyłania zadań przy użyciu interfejsów API REST, zobacz następujące dokumenty.

* [Uruchamianie zadań MapReduce z usługą Hadoop w usłudze HDInsight przy użyciu programu zwinięcie](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Uruchamianie zapytań Apache Hive z Apache Hadoop w usłudze HDInsight przy użyciu programu zwinięcie](hadoop/apache-hadoop-use-hive-curl.md)


Aby uzyskać informacje na temat innych sposobów uruchamiania Apache Hadoop MapReduce, Apache Hive i Apache świń interaktywnie, zobacz [Korzystanie z usługi MapReduce z usługą Hadoop w usłudze HDInsight](hadoop/hdinsight-use-mapreduce.md), [używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hadoop/hdinsight-use-hive.md)i [Korzystanie z programu Apache świni z Apache Hadoop w usłudze HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Przykłady
**Tworzenie klastra**

* Stare polecenie (ASM) — `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nowe polecenie — `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Usuwanie klastra**

* Stare polecenie (ASM) — `azure hdinsight cluster delete myhdicluster`
* Nowe polecenie — `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Wyświetl listę klastrów**

* Stare polecenie (ASM) — `azure hdinsight cluster list`
* Nowe polecenie — `azure hdinsight cluster list`

> [!NOTE]  
> Dla polecenia list, określenie grupy zasobów przy użyciu `-g` zwróci tylko klastry należące do określonej grupy zasobów.

**Pokaż informacje o klastrze**

* Stare polecenie (ASM) — `azure hdinsight cluster show myhdicluster`
* Nowe polecenie — `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrowanie Azure PowerShell do Azure Resource Manager
Ogólne informacje dotyczące Azure PowerShell w trybie Azure Resource Manager można znaleźć w temacie [Korzystanie z Azure PowerShell z Azure Resource Manager](../powershell-azure-resource-manager.md).

Polecenia cmdlet Menedżer zasobów Azure PowerShell można zainstalować obok poleceń cmdlet programu ASM. Polecenia cmdlet z dwóch trybów można rozróżnić według ich nazw.  Tryb Menedżer zasobów ma *AzHDInsight* w nazwach poleceń cmdlet, które są porównywane z *AZUREHDINSIGHT* w trybie ASM.  Na przykład *New-AzHDInsightCluster* a *New-AzureHDInsightCluster*. Parametry i przełączniki mogą mieć nazwy wiadomości i dostępnych jest wiele nowych parametrów podczas korzystania z Menedżer zasobów.  Na przykład kilka poleceń cmdlet wymaga nowego przełącznika o nazwie *-ResourceGroupName*. 

Aby można było używać poleceń cmdlet usługi HDInsight, należy nawiązać połączenie z kontem platformy Azure i utworzyć nową grupę zasobów:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Zmieniono nazwy poleceń cmdlet
Aby wyświetlić listę poleceń cmdlet usługi HDInsight ASM w konsoli programu Windows PowerShell:

    help *azurehdinsight*

Poniższa tabela zawiera listę poleceń cmdlet programu ASM i ich nazwy w trybie Menedżer zasobów:

| Polecenia cmdlet programu ASM | Polecenia cmdlet Menedżer zasobów |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nowe polecenia cmdlet
Poniżej znajdują się nowe polecenia cmdlet, które są dostępne tylko w trybie Menedżer zasobów. 

**Polecenia cmdlet powiązane z akcją skryptu:**

* **Get-AzHDInsightPersistedScriptAction**: Pobiera akcje utrwalonego skryptu dla klastra i wyświetla je w kolejności chronologicznej lub pobiera szczegóły dla określonej akcji utrwalonego skryptu. 
* **Get-AzHDInsightScriptActionHistory**: Pobiera historię akcji skryptu dla klastra i wyświetla listę w odwrotnej kolejności chronologicznej lub pobiera szczegóły wykonanej wcześniej akcji skryptu. 
* **Remove-AzHDInsightPersistedScriptAction**: usuwa akcję utrwalonego skryptu z klastra usługi HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: ustawia wcześniej wykonaną akcję skryptu jako utrwaloną akcję skryptu.
* **Submit-AzHDInsightScriptAction**: przesyła nową akcję skryptu do klastra usługi Azure HDInsight. 

Aby uzyskać dodatkowe informacje dotyczące użycia, zobacz [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

**Polecenia cmdlet związane z tożsamościami klastra:**

* **Add-AzHDInsightClusterIdentity**: dodaje tożsamość klastra do obiektu konfiguracji klastra, aby klaster usługi HDInsight mógł uzyskać dostęp do Azure Data Lake Storage. Zobacz [Tworzenie klastra usługi HDInsight z Data Lake Storage przy użyciu Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Przykłady
**Tworzenie klastra**

Stare polecenie (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nowe polecenie:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Usuwanie klastra**

Stare polecenie (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nowe polecenie:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Wyświetl listę klastrów**

Stare polecenie (ASM):

    Get-AzureHDInsightCluster

Nowe polecenie:

    Get-AzHDInsightCluster 

**Pokaż klaster**

Stare polecenie (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nowe polecenie:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Inne przykłady
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Prześlij zadania Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Przesyłanie zadań Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrowanie do nowego zestawu .NET SDK usługi HDInsight
[Zestaw .NET SDK usługi HDInsight](https://msdn.microsoft.com/library/azure/mt416619.aspx) oparty na usłudze Azure Service Management (ASM) jest obecnie przestarzały. Zaleca się użycie [zestawu .NET SDK usługi HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)opartego na Menedżer zasobów opartej na usłudze Azure Resource Management. Następujące pakiety usługi HDInsight oparte na ASM są przestarzałe.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ta sekcja zawiera wskaźniki do dodatkowych informacji na temat wykonywania określonych zadań przy użyciu zestawu SDK opartego na Menedżer zasobów.

| Jak... Korzystanie z zestawu SDK usługi HDInsight opartego na Menedżer zasobów | Linki |
| --- | --- |
| Zestaw SDK usługi Azure HDInsight dla platformy .NET|Zobacz [zestaw SDK usługi Azure HDInsight dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Interaktywna uwierzytelnianie aplikacji przy użyciu Azure Active Directory z zestawem SDK platformy .NET |Zobacz [uruchamianie Apache Hive zapytań przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kodu w tym artykule używa podejścia do uwierzytelniania interaktywnego. |
| Uwierzytelnianie aplikacji nieinteraktywnie przy użyciu Azure Active Directory z zestawem SDK .NET |Zobacz [Tworzenie aplikacji nieinterakcyjnych dla usługi HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Prześlij zadanie Apache Hive przy użyciu zestawu .NET SDK |Zobacz [przesyłanie zadań Apache Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Prześlij zadanie Apache Sqoop przy użyciu zestawu SDK platformy .NET |Zobacz [przesyłanie zadań Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Wyświetlanie listy klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [listę klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skalowanie klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [skalowanie klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Przyznawanie/odwoływanie dostępu do klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [przyznawanie/odwoływanie dostępu do klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aktualizowanie poświadczeń użytkownika HTTP dla klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [Aktualizowanie poświadczeń użytkownika http dla klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Znajdowanie domyślnego konta magazynu dla klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [Znajdowanie domyślnego konta magazynu dla klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Usuwanie klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [usuwanie klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Przykłady
Poniżej przedstawiono kilka przykładów dotyczących sposobu wykonywania operacji przy użyciu zestawu SDK opartego na ASM i odpowiedni fragment kodu dla zestawu SDK opartego na Menedżer zasobów.

**Tworzenie klienta CRUD klastra**

* Stare polecenie (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nowe polecenie (autoryzacja główna usługi)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nowe polecenie (Autoryzacja użytkownika)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Tworzenie klastra**

* Stare polecenie (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nowe polecenie
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Włączanie dostępu HTTP**

* Stare polecenie (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nowe polecenie
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Usuwanie klastra**

* Stare polecenie (ASM)
  
        client.DeleteCluster(dnsName);
* Nowe polecenie
  
        client.Clusters.Delete(resourceGroup, dnsname);

