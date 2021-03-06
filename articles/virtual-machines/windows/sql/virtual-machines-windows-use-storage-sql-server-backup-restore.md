---
title: Jak używać usługi Azure Storage do SQL Server kopii zapasowych i przywracania | Microsoft Docs
description: Dowiedz się, jak utworzyć kopię zapasową SQL Server w usłudze Azure Storage. W tym artykule wyjaśniono zalety tworzenia kopii zapasowych baz danych SQL w usłudze Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101891"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Tworzenie kopii zapasowych i przywracanie SQL Server przy użyciu usługi Azure Storage
## <a name="overview"></a>Omówienie
Począwszy od SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2, można teraz pisać SQL Server kopie zapasowe bezpośrednio do usługi Azure Blob Storage. Za pomocą tej funkcji można tworzyć kopie zapasowe i przywracać z Blob service platformy Azure przy użyciu lokalnej bazy danych SQL Server lub bazy danych SQL Server na maszynie wirtualnej platformy Azure. Tworzenie kopii zapasowych w chmurze zapewnia korzyści z dostępności, nieograniczonego magazynu poza lokacją i ułatwia migrację danych do i z chmury. Instrukcje tworzenia kopii zapasowej lub przywracania można wydać przy użyciu języka Transact-SQL lub SMO.

SQL Server 2016 wprowadza nowe możliwości; za pomocą [kopii zapasowej migawek plików](https://msdn.microsoft.com/library/mt169363.aspx) można wykonywać niemal chwilowo kopie zapasowe i niezwykle szybkie przywracanie.

W tym temacie wyjaśniono, dlaczego można wybrać użycie usługi Azure Storage do tworzenia kopii zapasowych SQL, a następnie opisano składniki, których dotyczą. Możesz użyć zasobów podanych na końcu artykułu, aby uzyskać dostęp do przewodników i dodatkowych informacji umożliwiających rozpoczęcie korzystania z tej usługi z kopiami zapasowymi SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Zalety korzystania z usługi Azure Blob Service do tworzenia kopii zapasowych SQL Server
Istnieje kilka wyzwań, które należy uwzględnić podczas tworzenia kopii zapasowych SQL Server. Te wyzwania obejmują zarządzanie magazynem, ryzyko awarii magazynu, dostęp do magazynu poza lokacją i konfigurację sprzętu. Wiele z tych wyzwań zostało rozkierowanych za pomocą usługi Azure Blob Storage do SQL Server kopii zapasowych. Weź pod uwagę następujące korzyści:

* **Łatwość użycia**: Przechowywanie kopii zapasowych w obiektach Blob platformy Azure może być wygodnym, elastycznym i łatwym w obsłudze opcją poza lokacją. Tworzenie magazynu poza lokacją dla SQL Server kopii zapasowych może być łatwe do zmodyfikowania istniejących skryptów/zadań, aby użyć składni **kopii zapasowej do adresu URL** . Magazyn poza lokacją powinien być zazwyczaj wystarczająco mały od lokalizacji produkcyjnej bazy danych, aby zapobiec pojedynczej awarii, która może mieć wpływ na Lokacje i produkcyjne bazy danych w trybie offline. Dzięki rozdzieleniu [geograficznie obiektów blob platformy Azure](../../../storage/common/storage-redundancy.md)masz dodatkową warstwę ochrony w przypadku awarii, która może wpływać na cały region.
* **Archiwum kopii zapasowych**: Usługa Azure Blob Storage oferuje lepszą alternatywę dla często używanej opcji taśmy do archiwizowania kopii zapasowych. Magazyn taśm może wymagać fizycznego transportu do funkcji poza lokacją i środków ochrony multimediów. Przechowywanie kopii zapasowych w usłudze Azure Blob Storage zapewnia natychmiastową, wysoką dostępność i trwałą opcję archiwizowania.
* **Zarządzany sprzęt**: Nie ma żadnych nakładów związanych z zarządzaniem sprzętem w ramach usług platformy Azure. Usługi platformy Azure zarządzają sprzętem i zapewniają replikację geograficzną w celu zapewnienia nadmiarowości i ochrony przed awariami sprzętu.
* **Nieograniczony magazyn**: Dzięki włączeniu bezpośredniej kopii zapasowej obiektów blob platformy Azure masz dostęp do praktycznie nieograniczonego magazynu. Alternatywnie tworzenie kopii zapasowej na dysku maszyny wirtualnej platformy Azure ma limity na podstawie rozmiaru maszyny. Istnieje ograniczenie liczby dysków, które można dołączyć do maszyny wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowych. Ten limit wynosi 16 dysków dla bardzo dużego wystąpienia i mniej dla mniejszych wystąpień.
* **Dostępność kopii zapasowej**: Kopie zapasowe przechowywane w obiektach Blob platformy Azure są dostępne z dowolnego miejsca i w dowolnym czasie i mogą być łatwo dostępne dla przywrócenia do lokalnego SQL Server lub innego SQL Server uruchomionego na maszynie wirtualnej platformy Azure, bez konieczności dołączania/odłączania ani pobierania bazy danych i dołączanie dysku VHD.
* **Koszt**: Płatność tylko za usługę, która jest używana. Może być opłacalne jako opcja archiwum poza lokacją i kopiami zapasowymi. Aby uzyskać więcej informacji, zobacz(https://go.microsoft.com/fwlink/?LinkId=277060 "Kalkulator")cen kalkulatora cen [platformy Azure]i Cennik [platformy Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "") .
* **Migawki magazynu**: Gdy pliki bazy danych są przechowywane w obiekcie blob platformy Azure i używasz SQL Server 2016, możesz użyć [kopii zapasowej migawek plików](https://msdn.microsoft.com/library/mt169363.aspx) , aby wykonywać niemal chwilowe kopie zapasowe i niezwykle szybkie przywracanie.

Aby uzyskać więcej informacji, zobacz [SQL Server wykonywanie kopii zapasowych i przywracanie za pomocą usługi Azure Blob Storage](https://go.microsoft.com/fwlink/?LinkId=271617).

Poniższe dwie sekcje zawierają wprowadzenie do usługi Azure Blob Storage, w tym wymaganych składników SQL Server. Ważne jest, aby zrozumieć składniki i ich interakcje w celu pomyślnego użycia kopii zapasowych i przywracania z usługi Azure Blob Storage.

## <a name="azure-blob-storage-service-components"></a>Składniki usługi Azure Blob Storage
Poniższe składniki platformy Azure są używane podczas tworzenia kopii zapasowych w usłudze Azure Blob Storage.

| Składnik | Opis |
| --- | --- |
| **Konto magazynu** |Konto magazynu jest punktem początkowym dla wszystkich usług magazynu. Aby uzyskać dostęp do usługi Azure Blob Storage, należy najpierw utworzyć konto usługi Azure Storage. Aby uzyskać więcej informacji na temat usługi Azure Blob Storage, zobacz [jak korzystać z usługi azure BLOB Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Kontener** |Kontener zawiera grupowanie zestawu obiektów blob i może przechowywać nieograniczoną liczbę obiektów BLOB. Aby napisać SQL Server kopię zapasową do Blob service platformy Azure, musisz mieć co najmniej utworzony kontener główny. |
| **Obiekt blob** |Plik o dowolnym typie i rozmiarze. Adresy obiektów BLOB są adresowane przy użyciu następującego formatu adresu URL: **https://[konto magazynu]. blob. Core. Windows. NET/[Container]/[BLOB]** . Aby uzyskać więcej informacji na temat stronicowych obiektów blob, zobacz [Opis bloków i stronicowych obiektów BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Składniki SQL Server
Poniższe składniki SQL Server są używane podczas tworzenia kopii zapasowej w usłudze Azure Blob Storage.

| Składnik | Opis |
| --- | --- |
| **Adres URL** |Adres URL określa Uniform Resource Identifier (URI) do unikatowego pliku kopii zapasowej. Adres URL służy do dostarczania lokalizacji i nazwy pliku kopii zapasowej SQL Server. Adres URL musi wskazywać na rzeczywisty obiekt BLOB, a nie tylko kontener. Jeśli obiekt BLOB nie istnieje, zostanie utworzony. W przypadku określenia istniejącego obiektu BLOB kopia ZAPASowa kończy się niepowodzeniem, chyba że zostanie określona > z FORMATOWANIEm. Poniżej znajduje się przykładowy adres URL określony w poleceniu BACKUP: **http [s]://[storageaccount]. blob. Core. Windows. NET/[Container]/[filename. bak]** . Protokół HTTPS jest zalecany, ale nie jest wymagany. |
| **Poświadczenie** |Informacje wymagane do nawiązania połączenia i uwierzytelnienia w usłudze Azure Blob Storage są przechowywane jako poświadczenia.  Aby SQL Server zapisywania kopii zapasowych do obiektu blob platformy Azure lub przywracania z niego, należy utworzyć SQL Server poświadczenia. Aby uzyskać więcej informacji, zobacz [SQL Server Credential](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Program SQL Server 2016 został zaktualizowany do obsługi blokowych obiektów BLOB. Zapoznaj [się z samouczkiem: Aby uzyskać więcej informacji, Użyj usługi Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/dn466438.aspx) z bazami danych SQL Server 2016.
> 
> 

## <a name="next-steps"></a>Następne kroki
1. Utwórz konto platformy Azure, jeśli jeszcze go nie masz. Jeśli oceniasz platformę Azure, weź pod uwagę [bezpłatną wersję próbną](https://azure.microsoft.com/free/).
2. Następnie przejdź do jednego z następujących samouczków, które przeprowadzą Cię przez proces tworzenia konta magazynu i przywracania.
   
   * **SQL Server 2014**: [Ręczny SQL Server 2014 kopia zapasowa i przywracanie do usługi](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)Microsoft Azure Blob Storage.
   * **SQL Server 2016**: [Ręczny Korzystanie z usługi Microsoft Azure Blob Storage z bazami danych SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Zapoznaj się z dodatkową dokumentacją, rozpoczynając od [SQL Server tworzenia kopii zapasowych i przywracania za pomocą usługi Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx).

Jeśli masz jakieś problemy, zapoznaj się z tematem [SQL Server wykonywania kopii zapasowych w ramach najlepszych rozwiązań dotyczących adresów URL i rozwiązywania problemów](https://msdn.microsoft.com/library/jj919149.aspx).

Inne SQL Server opcje tworzenia kopii zapasowej i przywracania znajdują się [w temacie Tworzenie kopii zapasowych i przywracanie SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

