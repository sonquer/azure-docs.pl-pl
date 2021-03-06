---
title: Wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight
description: Wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887227"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenariusz: wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można utworzyć nowej tabeli Apache HBase.

## <a name="cause"></a>Przyczyna

Podczas procesu obcięcia tabeli wystąpił problem z połączeniem magazynu. Wpis tabeli został usunięty w tabeli metadanych HBase. Wszystkie pliki obiektów blob, ale zostały usunięte.

Chociaż nie znaleziono obiektu BLOB folderu o nazwie `/hbase/data/default/ThatTable` w magazynie. Sterownik WASB znalazł obecność powyżej pliku obiektu BLOB i nie zezwolił na utworzenie obiektu BLOB o nazwie `/hbase/data/default/ThatTable`, ponieważ założono, że foldery nadrzędne istnieją, w związku z czym Tworzenie tabeli zakończy się niepowodzeniem.

## <a name="resolution"></a>Rozdzielczość

1. W interfejsie użytkownika Apache Ambari Uruchom ponownie aktywne serwera hmaster. Dzięki temu jedna z dwóch serwera hmaster stanu wstrzymania stanie się aktywna, a nowa aktywna serwera hmaster będzie ponownie ładować informacje tabeli metadanych. W ten sposób nie zobaczysz tabeli `already-deleted` w interfejsie użytkownika serwera hmaster.

1. Oddzielony plik obiektów BLOB można znaleźć z narzędzi interfejsu użytkownika, takich jak Eksplorator chmury lub uruchomione polecenie, takie jak `hdfs dfs -ls /xxxxxx/yyyyy`. Uruchom `hdfs dfs -rmr /xxxxx/yyyy`, aby usunąć ten obiekt BLOB. Na przykład `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Teraz można utworzyć nową tabelę o tej samej nazwie w HBase.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
