---
title: Nie można uruchomić oprogramowania Apache HBase Master w usłudze Azure HDInsight
description: Nie można uruchomić oprogramowania Apache HBase Master (serwera hmaster) w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887210"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Nie można uruchomić oprogramowania Apache HBase Master (serwera hmaster) w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Scenariusz: niepowodzenie zmiany nazwy Atomowej

### <a name="issue"></a>Problem

Podczas procesu uruchamiania zidentyfikowano nieoczekiwane pliki.

### <a name="cause"></a>Przyczyna

W trakcie procesu uruchamiania serwera hmaster wykonuje wiele kroków inicjowania, w tym przeniesienie danych z folderu Scratch (tmp) do folderu danych. Serwera hmaster również przegląda folder dzienników zapisu (WAL), aby sprawdzić, czy istnieją nieodpowiadające serwery regionów.

Serwera hmaster wykonuje podstawowe polecenie list w folderach WAL. Jeśli w dowolnym momencie serwera hmaster widzi nieoczekiwany plik w żadnym z tych folderów, zgłosi wyjątek i nie zostanie uruchomiony.

### <a name="resolution"></a>Rozdzielczość

Sprawdź stos wywołań i spróbuj ustalić, który folder może powodować problem (na przykład może być folderem WAL lub folderem. tmp). Następnie w Eksploratorze chmury lub przy użyciu poleceń systemu plików HDFS Spróbuj zlokalizować plik problemu. Zwykle jest to plik `*-renamePending.json`. (Plik `*-renamePending.json` jest plikiem dziennika, który służy do implementowania operacji niepodzielnej zmiany nazwy w sterowniku WASB. Ze względu na błędy w tej implementacji te pliki mogą pozostać po awarii procesu i tak dalej.) Wymuś usunięcie tego pliku w programie Cloud Explorer lub przy użyciu systemu plików HDFS.

Czasami może być również plik tymczasowy o nazwie, taki jak `$$$.$$$` w tej lokalizacji. Aby wyświetlić ten plik, należy użyć polecenia HDFS `ls`. nie można wyświetlić pliku w programie Cloud Explorer. Aby usunąć ten plik, użyj polecenia HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.

Po uruchomieniu tych poleceń serwera hmaster powinny zacząć działać od razu.

---

## <a name="scenario-no-server-address-listed"></a>Scenariusz: brak adresu serwera na liście

### <a name="issue"></a>Problem

Może zostać wyświetlony komunikat z informacją, że tabela `hbase: meta` nie jest w trybie online. Uruchomienie `hbck` może zgłosić, że `hbase: meta table replicaId 0 is not found on any region.` w dziennikach serwera hmaster może zostać wyświetlony komunikat: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Przyczyna

Nie można zainicjować serwera hmaster po ponownym uruchomieniu HBase.

### <a name="resolution"></a>Rozdzielczość

1. W HBase Shell wprowadź następujące polecenia (Zmień wartości rzeczywiste stosownie do potrzeb):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Usuń wpis `hbase: namespace`. Ten wpis może być tym samym błędem, który jest raportowany podczas skanowania tabeli `hbase: namespace`.

1. Uruchom ponownie aktywne serwera hmaster z interfejsu użytkownika Ambari, aby wyświetlić stan HBase w stanie uruchomienia.

1. W powłoce HBase, aby wyświetlić wszystkie tabele w trybie offline, uruchom następujące polecenie:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenariusz: Java. IO. IOException: TimedOut

### <a name="issue"></a>Problem

Serwera hmaster limit czasu dla wyjątku krytycznego podobnego do: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele tabel i regionów, które nie zostały opróżnione po ponownym uruchomieniu usług serwera hmaster. Limit czasu jest znaną wadą z serwera hmaster. Ogólne zadania uruchamiania klastra mogą zająć dużo czasu. Serwera hmaster zamyka się, jeśli tabela przestrzeni nazw nie jest jeszcze przypisana. Długotrwałe zadania uruchamiania są wykonywane w przypadku, gdy istnieje duża ilość nieopróżnionych danych i przekroczenie limitu czasu wynoszącego pięć minut.

### <a name="resolution"></a>Rozdzielczość

1. W interfejsie użytkownika Apache Ambari przejdź do pozycji **HBase** > **configs**. W pliku `hbase-site.xml` niestandardowego Dodaj następujące ustawienie:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Uruchom ponownie wymagane usługi (serwera hmaster, a także inne usługi HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenariusz: częste ponowne uruchamianie serwera regionów

### <a name="issue"></a>Problem

Okresowe ponowne uruchamianie węzłów. W dziennikach serwera regionów mogą pojawić się wpisy podobne do:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Przyczyna

Długi `regionserver` JVM GC. Wstrzymanie spowoduje, że `regionserver` nie odpowiada i nie będzie mogła wysłać pulsu do serwera hmaster w ciągu limitu czasu sesji ZK 40s. Serwera hmaster uważa, że `regionserver` jest martwa i spowoduje przerwanie `regionserver` i ponowne uruchomienie.

### <a name="resolution"></a>Rozdzielczość

Zmień limit czasu sesji dozorcy, a nie tylko `hbase-site` ustawienie `zookeeper.session.timeout` ale również ustawienia `zoo.cfg` `maxSessionTimeout` należy zmienić.

1. Dostęp do interfejsu użytkownika Ambari, przejdź do **HBase-> configs-> ustawienia**, w sekcji Limity czasu, Zmień wartość limitu czasu sesji dozorcy.

1. Dostęp do interfejsu użytkownika Ambari, przejdź do **dozorcy-> configs-> Custom** `zoo.cfg`, Dodaj/Zmień następujące ustawienie. Upewnij się, że wartość jest taka sama jak HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Uruchom ponownie wymagane usługi.

---

## <a name="scenario-log-splitting-failure"></a>Scenariusz: niepowodzenie dzielenia dziennika

### <a name="issue"></a>Problem

HMasters nie powiodło się w klastrze HBase.

### <a name="cause"></a>Przyczyna

Nieprawidłowo skonfigurowane ustawienia systemu plików HDFS i HBase dla konta magazynu pomocniczego.

### <a name="resolution"></a>Rozdzielczość

Ustaw HBase. ROOTDIR: wasb://@.blob.core.windows.net/hbase i ponownie uruchom usługi na Ambari.

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
