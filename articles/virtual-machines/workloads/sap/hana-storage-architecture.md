---
title: Architektura magazynu SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Architektura magazynu wdrażania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 256aaf94175394fd737e53c6281f2d8b45e8af41
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099648"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architektura magazynu SAP HANA (duże wystąpienia)

Układ magazynu dla SAP HANA na platformie Azure (duże wystąpienia) jest konfigurowany przez SAP HANA w klasycznym modelu wdrażania na podstawie zalecanych wytycznych SAP. Wytyczne zostały udokumentowane w dokumencie [SAP HANA wymagania dotyczące magazynu](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) .

Duże wystąpienie HANA klasy typu I zawiera cztery razy wolumin pamięci jako wolumin magazynu. W przypadku klasy typu II jednostek dużego wystąpienia HANA magazyn nie jest czterokrotnie większy. Jednostki są dostarczane z woluminem przeznaczonym do przechowywania kopii zapasowych dziennika transakcji platformy HANA. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zapoznaj się z poniższą tabelą dotyczącą alokacji magazynu. W tabeli wymieniono nieznacznie pojemności dla różnych woluminów z różnymi jednostkami dużych wystąpień usługi HANA.

| Jednostka SKU dużego wystąpienia HANA | Hana/dane | Hana/log | Hana/udostępniony | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3 328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11 520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S192xm |  11 520 GB |  1 536 GB |  1,792 GB |  1 536 GB |
| S384 | 11 520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576xm | 31 744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28 000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36 000 GB | 4,100 GB | 2 050 GB | 4,100 GB |


Rzeczywiste wdrożone woluminy mogą się różnić w zależności od wdrożenia i narzędzia, które jest używane do wyświetlania rozmiarów woluminów.

W przypadku podziału jednostki SKU dużego wystąpienia platformy HANA kilka przykładów możliwych do dzielenia może wyglądać następująco:

| Partycja pamięci w GB | Hana/dane | Hana/log | Hana/udostępniony | Hana/log/kopia zapasowa |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1536 | 3 328 GB | 768 GB | 1,280 GB | 768 GB |


Te rozmiary są ilością ilościową, która może się nieco różnić w zależności od wdrażania i narzędzi używanych do przeglądania woluminów. Istnieją również inne rozmiary partycji, na przykład 2,5 TB. Te rozmiary magazynu są obliczane przy użyciu formuły podobnej do użytej w poprzednich partycjach. Termin "partycje" nie oznacza, że system operacyjny, pamięć lub zasoby procesora są podzielone na partycje. Wskazuje partycje magazynu dla różnych wystąpień platformy HANA, które warto wdrożyć w jednej jednostce dużego wystąpienia usługi HANA. 

Być może potrzebujesz więcej miejsca w magazynie. Magazyn można dodać, kupując dodatkowy magazyn w jednostkach 1 TB. Ten dodatkowy magazyn można dodać jako dodatkowy wolumin. Można go również użyć do rozszerania jednego lub kilku istniejących woluminów. Nie można zmniejszyć wielkości woluminów wdrożonych pierwotnie i w sposób opisany w poprzednich tabelach. Nie można również zmieniać nazw woluminów ani nazw instalacji. Opisane wcześniej woluminy magazynu są dołączane do jednostek dużego wystąpienia HANA jako woluminy NFS4.

Możesz użyć migawek magazynu na potrzeby tworzenia kopii zapasowych i przywracania oraz odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zapoznaj się z artykułem [Supported obsługiwane scenariusze](hana-supported-scenario.md) dotyczące szczegółowych informacji o układzie magazynu dla Twojego scenariusza.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Uruchamianie wielu wystąpień SAP HANA w jednej jednostce dużej instancji HANA

Istnieje możliwość hostowania więcej niż jednego wystąpienia aktywnego SAP HANA w jednostkach dużych wystąpień usługi HANA. Aby zapewnić możliwości migawek magazynu i odzyskiwania po awarii, taka konfiguracja wymaga zestawu woluminów na wystąpienie. Obecnie jednostki dużego wystąpienia HANA mogą zostać podzielone w następujący sposób:

- **S72, S72m, S96, S144, S192**: Z przyrostem 256 GB, z 256 GB najmniejszą jednostką początkową. Różne przyrosty, takie jak 256 GB i 512 GB, można łączyć z maksymalną ilością pamięci w jednostce.
- **S144m i S192m**: Z przyrostem 256 GB, z 512 GB najmniejszą jednostką. Różne przyrosty, takie jak 512 GB i 768 GB, można łączyć z maksymalną ilością pamięci w jednostce.
- **Klasa typu II**: Z przyrostem 512 GB, z najmniejszą jednostką początkową wynoszącą 2 TB. Różne przyrosty, takie jak 512 GB, 1 TB i 1,5 TB, można łączyć z maksymalną ilością pamięci w jednostce.

Kilka przykładów uruchamiania wielu wystąpień SAP HANA może wyglądać następująco.

| SKU | Rozmiar pamięci | Rozmiar magazynu | Rozmiary z wieloma bazami danych |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | wystąpienie 1x768-GB HANA<br /> lub wystąpienie 1x512-GB + 1x256-GB<br /> lub wystąpienia 3x256-GB | 
| S72m | 1,5 TB | 6 TB | wystąpienia 3x512GB HANA<br />lub wystąpienie 1x512-GB + wystąpienie 1x1-TB<br />lub wystąpienia 6x256-GB<br />lub wystąpienie 1x 1,5 TB | 
| S192m | 4 TB | 16 TB | wystąpienia 8x512 GB<br />lub wystąpienia 4x1-TB<br />lub wystąpienia 4x512-GB + wystąpienia 2x1-TB<br />lub wystąpienia 4x768-GB + 2x512-GB<br />lub wystąpienie 1x4-TB |
| S384xm | 8 TB | 22 TB | wystąpienia 4x2 – TB<br />lub wystąpienia 2x4-TB<br />wystąpienia 2x3 + TB + 1X2-TB<br />lub 2 wystąpienia 2,5 TB + wystąpienia 1x3 TB<br />lub wystąpienie 1x8-TB |


Istnieją również inne różnice. 

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie nieużywanych danych
Magazyn używany na potrzeby dużego wystąpienia HANA używa przezroczystego szyfrowania danych przechowywanych na dyskach od końca roku 2018. We wcześniejszych wdrożeniach można było pobrać zaszyfrowane woluminy. Jeśli postanowiono korzystać z tej opcji, możesz poprosić o zaszyfrowanie woluminów w trybie online. Przeniesienie z nieszyfrowanych na woluminy szyfrowane jest przezroczyste i nie wymaga przestojów. 

W przypadku typu I klasy jednostek SKU wolumin, na którym jest przechowywana jednostka LUN, jest szyfrowany. W programie Revisions 3 HANA duże wystąpienia sygnatur przy użyciu klasy typu II jednostek SKU dużego wystąpienia HANA należy zaszyfrować rozruchową jednostkę LUN przy użyciu metod systemu operacyjnego. W obszarze wydanie poprawki 4 HANA duże wystąpienia, przy użyciu jednostek typu II, wolumin rozruchowy LUN jest przechowywany i domyślnie jest szyfrowany. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Ustawienia wymagane dla większych wystąpień platformy HANA w dużych wystąpieniach platformy HANA
Magazyn używany w dużych wystąpieniach platformy HANA ma ograniczenie rozmiaru pliku. [Ograniczenie rozmiaru wynosi 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na plik. W przeciwieństwie do ograniczeń rozmiaru plików w systemach plików EXT3, HANA nie jest nieświadomy niejawnie ograniczenia magazynu wymuszonego przez magazyn dużych wystąpień platformy HANA. W efekcie program HANA nie utworzy automatycznie nowego pliku danych, gdy zostanie osiągnięty limit rozmiaru pliku 16TB. W miarę jak HANA próbuje zwiększyć plik poza 16 TB, program HANA zgłosi błędy i na końcu zakończy się awaria serwera indeksów.

> [!IMPORTANT]
> Aby zapobiec próbie zwiększenia rozmiaru plików danych poza 16 TB pamięci masowej magazynu platformy HANA, należy ustawić następujące parametry w pliku konfiguracyjnym Global. ini platformy HANA
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Zobacz również artykuł SAP uwagi [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Należy pamiętać, że [#2631285](https://launchpad.support.sap.com/#/notes/2631285) uwagi SAP




**Następne kroki**
- Zapoznaj się z [obsługiwanymi scenariuszami dla dużych wystąpień Hana](hana-supported-scenario.md)