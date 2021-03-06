---
title: Migrowanie pakietów usług SSIS do wystąpienia zarządzanego SQL
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak migrować pakiety SQL Server Integration Services (SSIS) i projekty do wystąpienia zarządzanego Azure SQL Database przy użyciu Azure Database Migration Service lub Data Migration Assistant.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 22f3e6a0e4c041024e826a7ed724d788ce77da62
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751225"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrowanie pakietów SQL Server Integration Services do wystąpienia zarządzanego Azure SQL Database
Jeśli używasz SQL Server Integration Services (SSIS) i chcesz migrować projekty SSIS/pakiety z SSISDB źródłowego hostowanego przez SQL Server do SSISDB docelowego hostowanego przez wystąpienie zarządzane Azure SQL Database, możesz użyć Azure Database Migration Service.

Jeśli używana wersja usług SSIS jest wcześniejsza niż 2012 lub używasz typów magazynu pakietów innych niż SSISDB, przed migracją projektów i pakietów SSIS należy je przekonwertować przy użyciu Kreatora konwersji projektów usług Integration Services, który można także uruchomić z programu SSMS. Aby uzyskać więcej informacji, zobacz artykuł [konwertowanie projektów na model wdrażania projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) obecnie nie obsługuje Azure SQL Database jako docelowej docelowej migracji. Aby ponownie wdrożyć projekty usług SSIS/pakiety do Azure SQL Database, zapoznaj się z artykułem [ponowne wdrażanie pakietów SQL Server Integration Services do Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Oceń źródłowe projekty SSIS/pakiety.
> * Migruj projekty SSIS/pakiety na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* Aby utworzyć Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji, zapoznaj się z artykułem [topologie sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.
* Aby upewnić się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Aby skonfigurować [zaporę systemu Windows na potrzeby dostępu do aparatu dla źródłowej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Aby otworzyć Zaporę systemu Windows w celu umożliwienia Azure Database Migration Service dostępu do SQL Server źródłowej, domyślnie jest to port TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowych baz danych podczas migracji oraz do plików za pośrednictwem portu SMB 445.
* Azure SQL Database wystąpienie zarządzane do hostowania SSISDB. Jeśli trzeba ją utworzyć, postępuj zgodnie ze szczegółowymi informacjami w artykule [tworzenie Azure SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Aby upewnić się, że nazwy logowania używane do łączenia źródłowego SQL Server i docelowego wystąpienia zarządzanego są członkami roli serwera sysadmin.
* Aby sprawdzić, czy program SSIS został zainicjowany w Azure Data Factory (ADF) zawierający Azure-SSIS Integration Runtime (IR) z miejscem docelowym SSISDB hostowanym przez wystąpienie zarządzane Azure SQL Database (zgodnie z opisem w artykule [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Oceń źródłowe projekty SSIS/pakiety

Chociaż Ocena źródła SSISDB nie jest jeszcze zintegrowana z bazą danych Asystent migracji (DMA), projekty i pakiety usług SSIS zostaną ocenione/zweryfikowane w miarę ich ponownego wdrożenia w miejscu docelowym SSISDB hostowanym w wystąpieniu zarządzanym Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę **Azure Database Migration Service**, a następnie wybierz usługę **Azure Database Migration Service** z listy rozwijanej.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz ją.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do SQL Server źródłowych i docelowym Azure SQL Database wystąpieniem zarządzanym.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](https://aka.ms/DMSVnet).

    Aby uzyskać więcej informacji zobacz artykuł [Topologie sieci na potrzeby migracji wystąpienia zarządzanego usługi Azure SQL DB przy użyciu usługi Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** Określ nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz opcję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database wystąpienie zarządzane**, a następnie wybierz **Typ działania**wybierz pozycję **migracja pakietu usług SSIS**.

   ![Tworzenie projektu usługi DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **szczegóły lokalizacji docelowej migracji** Określ szczegóły połączenia dla elementu docelowego.

     ![Szczegóły elementu docelowego](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Wybierz pozycję **Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. W przypadku **projektów SSIS i opcji zastępowania środowisk**należy określić, czy mają być zastępowane lub ignorowane istniejące projekty i środowiska usług SSIS.

    ![Podsumowanie projektu migracji](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze wskazówkami dotyczącymi migracji w [przewodniku migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.
