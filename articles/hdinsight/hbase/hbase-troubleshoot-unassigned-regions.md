---
title: Problemy z serwerami regionów w usłudze Azure HDInsight
description: Problemy z serwerami regionów w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887142"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemy z serwerami regionów w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Scenariusz: nieprzypisane regiony

### <a name="issue"></a>Problem

Podczas uruchamiania `hbase hbck` polecenia zostanie wyświetlony komunikat o błędzie podobny do:

```
multiple regions being unassigned or holes in the chain of regions
```

W interfejsie użytkownika Apache HBase Master można zobaczyć liczbę regionów, które są niezrównoważone na wszystkich serwerach regionów. Następnie można uruchomić polecenie `hbase hbck`, aby zobaczyć otwory w łańcuchu regionów.

### <a name="cause"></a>Przyczyna

Dziury mogą być wynikiem regionów trybu offline.

### <a name="resolution"></a>Rozdzielczość

Napraw przydziały. Wykonaj poniższe kroki, aby przywrócić stan Normalny nieprzypisanych regionów:

1. Zaloguj się do klastra HBase usługi HDInsight przy użyciu protokołu SSH.

1. Uruchom `hbase zkcli` polecenie, aby nawiązać połączenie z powłoką dozorcy.

1. Uruchom polecenie `rmr /hbase/regions-in-transition` lub `rmr /hbase-unsecure/regions-in-transition`.

1. Zamknij powłokę dozorcy za pomocą polecenia `exit`.

1. Otwórz interfejs użytkownika Apache Ambari, a następnie uruchom ponownie usługę Active HBase Master.

1. Uruchom ponownie polecenie `hbase hbck` (bez żadnych dalszych opcji). Sprawdź dane wyjściowe i upewnij się, że wszystkie regiony są przypisane.

---

## <a name="scenario-dead-region-servers"></a>Scenariusz: serwery regionów martwych

### <a name="issue"></a>Problem

Nie można uruchomić serwerów regionów.

### <a name="cause"></a>Przyczyna

Dzielenie wielu katalogów WAL.

1. Pobierz listę bieżących WALs: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Sprawdź plik `wals.out`. W przypadku zbyt wielu katalogów dzielenia (począwszy od znaku *-dzielenia) serwer regionu prawdopodobnie kończy się niepowodzeniem z powodu tych katalogów.

### <a name="resolution"></a>Rozdzielczość

1. Zatrzymaj HBase z portalu Ambari.

1. Wykonaj `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`, aby uzyskać świeżą listę WALs.

1. Przenieś katalogi *-dzielenie katalogów do folderu tymczasowego, `splitWAL`i usuń katalogi *-dzielenie.

1. Wykonaj `hbase zkcli` polecenie, aby nawiązać połączenie z powłoką dozorcy.

1. Wykonaj `rmr /hbase-unsecure/splitWAL`.

1. Uruchom ponownie usługę HBase.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
