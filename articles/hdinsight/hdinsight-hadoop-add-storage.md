---
title: Dodawanie dodatkowych kont magazynu platformy Azure do usługi HDInsight
description: Dowiedz się, jak dodać dodatkowe konta magazynu platformy Azure do istniejącego klastra usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 6ad583fdb880e36e6ac9c2dfda56bb68378ea598
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314009"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Dodawanie dodatkowych kont magazynu do usługi HDInsight

Dowiedz się, jak za pomocą akcji skryptu dodać dodatkowe *konta* magazynu platformy Azure do usługi HDInsight. Kroki opisane w tym dokumencie umożliwiają dodanie *konta* magazynu do istniejącego klastra usługi HDInsight. Ten artykuł dotyczy *kont* magazynu (nie domyślnego konta magazynu klastra), a nie dodatkowego magazynu, takiego jak [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) i [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie dotyczą dodawania dodatkowych kont magazynu do klastra po jego utworzeniu. Aby uzyskać informacje na temat dodawania kont magazynu podczas tworzenia klastra, zobacz [Konfigurowanie klastrów w usłudze HDInsight przy użyciu Apache Hadoop, Apache Spark, Apache Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nazwa i klucz konta magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).
* W przypadku korzystania z programu PowerShell należy użyć polecenia AZ module.  Zobacz [omówienie Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Zasady działania

Podczas przetwarzania skrypt wykonuje następujące czynności:

* Jeśli konto magazynu istnieje już w konfiguracji pliku Core-site. XML klastra, skrypt kończy działanie i nie są wykonywane żadne dalsze działania.

* Sprawdza, czy konto magazynu istnieje i czy można uzyskać do niego dostęp przy użyciu klucza.

* Szyfruje klucz przy użyciu poświadczeń klastra.

* Dodaje konto magazynu do pliku pliku Core-site. XML.

* Powoduje zatrzymanie i ponowne uruchomienie usługi [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)i [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) . Zatrzymywanie i uruchamianie tych usług pozwala im na korzystanie z nowego konta magazynu.

> [!WARNING]  
> Użycie konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="add-storage-account"></a>Dodaj konto magazynu

Użyj [akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) , aby zastosować zmiany z uwzględnieniem następujących zagadnień:

|Właściwość | Wartość |
|---|---|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Typy węzłów|Główny|
|Parametry|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (opcjonalnie)|

* `ACCOUNTNAME` to nazwa konta magazynu, które ma zostać dodane do klastra usługi HDInsight.
* `ACCOUNTKEY` jest kluczem dostępu dla `ACCOUNTNAME`.
* Parametr `-p` jest opcjonalny. Jeśli ta wartość jest określona, klucz nie jest szyfrowany i jest przechowywany w pliku pliku Core-site. XML jako zwykły tekst.

## <a name="verification"></a>Weryfikacja

Podczas wyświetlania klastra usługi HDInsight w Azure Portal wybranie pozycji __konta magazynu__ w obszarze __Właściwości__ nie powoduje wyświetlenia kont magazynu dodanych za pomocą tej akcji skryptu. W przypadku Azure PowerShell i interfejsu wiersza polecenia platformy Azure nie jest wyświetlane dodatkowe konto magazynu. Informacje o magazynie nie są wyświetlane, ponieważ skrypt modyfikuje `core-site.xml` konfigurację klastra. Te informacje nie są używane podczas pobierania informacji o klastrze przy użyciu interfejsów API zarządzania platformy Azure.

Aby sprawdzić, czy magazyn dodatkowy wykorzystuje jedną z metod przedstawionych poniżej:

### <a name="powershell"></a>PowerShell

Skrypt zwróci nazwy kont magazynu skojarzone z danym klastrem. Zastąp `CLUSTERNAME` rzeczywistą nazwą klastra, a następnie uruchom skrypt.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net`, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Przejdź do lokalizacji **HDFS** > **konfiguracjami** > **Zaawansowane** > **niestandardowe podstawowe Lokacje**.

1. Obserwuj klucze zaczynające się od `fs.azure.account.key`. Nazwa konta będzie częścią klucza, jak pokazano w tym przykładowym obrazie:

   ![Weryfikacja za poorednictwem oprogramowania Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Usuń konto magazynu

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net`, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Przejdź do lokalizacji **HDFS** > **konfiguracjami** > **Zaawansowane** > **niestandardowe podstawowe Lokacje**.

1. Usuń następujące klucze:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Po usunięciu tych kluczy i zapisaniu konfiguracji należy ponownie uruchomić Oozie, przędzę, MapReduce2, HDFS i Hive jedną.

## <a name="known-issues"></a>Znane problemy

### <a name="storage-firewall"></a>Zapora magazynu

Jeśli wybierzesz opcję zabezpieczenia konta magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach**, należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...** , aby Usługa HDInsight mogła uzyskać dostęp do konta magazynu.

### <a name="unable-to-access-storage-after-changing-key"></a>Nie można uzyskać dostępu do magazynu po zmianie klucza

Jeśli zmienisz klucz dla konta magazynu, Usługa HDInsight nie będzie już uzyskiwać dostępu do konta magazynu. Usługa HDInsight używa zapisanej w pamięci podręcznej kopii klucza w pliku Core-site. XML dla klastra. Tę kopię w pamięci podręcznej należy zaktualizować, aby odpowiadała nowemu kluczowi.

Wykonanie akcji skryptu ponownie __nie aktualizuje klucza__ , ponieważ skrypt sprawdza, czy wpis dla konta magazynu już istnieje. Jeśli wpis już istnieje, nie wprowadza żadnych zmian.

Aby obejść ten problem:  
1. Usuń konto magazynu.
1. Dodaj konto magazynu.

> [!IMPORTANT]  
> Obracanie klucza magazynu dla konta magazynu podstawowego dołączonego do klastra nie jest obsługiwane.

### <a name="poor-performance"></a>Niska wydajność

Jeśli konto magazynu znajduje się w innym regionie niż klaster usługi HDInsight, może wystąpić niska wydajność. Dostęp do danych w innym regionie wysyła ruch sieciowy poza regionalnymi centrami danych platformy Azure i przez publiczny Internet, co może spowodować opóźnienia.

### <a name="additional-charges"></a>Dodatkowe opłaty

Jeśli konto magazynu znajduje się w innym regionie niż klaster usługi HDInsight, możesz zauważyć dodatkowe opłaty za ruch wychodzący w ramach rozliczeń na platformie Azure. Opłata za ruch wychodzący jest stosowana, gdy dane opuszczają regionalne centrum danych. Ta opłata jest stosowana, nawet jeśli ruch jest przeznaczony dla innego centrum danych platformy Azure w innym regionie.

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak dodać dodatkowe konta magazynu do istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
