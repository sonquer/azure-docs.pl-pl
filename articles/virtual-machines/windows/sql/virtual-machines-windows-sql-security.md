---
title: Zagadnienia dotyczące zabezpieczeń SQL Server na platformie Azure | Microsoft Docs
description: Ten temat zawiera ogólne wskazówki dotyczące zabezpieczania SQL Server działających na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f5ea0ddff38532b119d8d984f2dabd6d898b44a5
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031360"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Zagadnienia dotyczące zabezpieczeń SQL Server na platformie Azure Virtual Machines

Ten temat zawiera ogólne wytyczne dotyczące zabezpieczeń, które pomagają w ustanowieniu bezpiecznego dostępu do wystąpień SQL Server na maszynie wirtualnej platformy Azure.

Platforma Azure jest zgodna z kilkoma przepisami i standardami branżowymi, które umożliwiają tworzenie zgodnych rozwiązań z SQL Server uruchomionymi na maszynie wirtualnej. Aby uzyskać informacje o zgodności z przepisami z platformą Azure, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Kontrola dostępu do maszyny wirtualnej SQL

Podczas tworzenia maszyny wirtualnej SQL Server należy rozważyć, jak dokładnie kontrolować, kto ma dostęp do maszyny i SQL Server. Ogólnie rzecz biorąc, należy wykonać następujące czynności:

- Ogranicz dostęp do SQL Server tylko do aplikacji i klientów, które ich potrzebują.
- Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi zarządzania kontami i hasłami użytkowników.

Poniższe sekcje zawierają sugestie dotyczące omawiania tych punktów.

## <a name="secure-connections"></a>Bezpieczne połączenia

W przypadku tworzenia maszyny wirtualnej SQL Server z obrazem galerii opcja **SQL Server łączności** umożliwia wybór **lokalizacji lokalnej (wewnątrz maszyny wirtualnej)** , **prywatnej (w Virtual Network)** lub **publicznej (internetowej)** .

![SQL Server łączności](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Aby uzyskać najlepsze zabezpieczenia, wybierz opcję najbardziej restrykcyjną dla danego scenariusza. Na przykład, jeśli używasz aplikacji, która uzyskuje dostęp do SQL Server na tej samej maszynie wirtualnej, **to jest to** najbardziej bezpieczny wybór. W przypadku korzystania z aplikacji platformy Azure, która wymaga dostępu do SQL Server, **prywatna** komunikacja zabezpiecza komunikacji do SQL Server tylko w ramach określonego [Virtual Network platformy Azure](../../../virtual-network/virtual-networks-overview.md). Jeśli wymagany jest dostęp **publiczny** (Internet) do maszyny wirtualnej SQL Server, należy postępować zgodnie z innymi najlepszymi rozwiązaniami w tym temacie, aby zmniejszyć obszar narażony na ataki.

Wybrane opcje w portalu używają reguł zabezpieczeń dla ruchu przychodzącego w [sieciowej grupie zabezpieczeń](../../../virtual-network/security-overview.md) maszyny wirtualnej (sieciowej grupy zabezpieczeń), aby zezwalać na ruch sieciowy do maszyny wirtualnej lub go odmawiać. Można modyfikować lub tworzyć nowe reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego, aby zezwalać na ruch do portu SQL Server (domyślnie 1433). Można również określić określone adresy IP, które mogą komunikować się za pośrednictwem tego portu.

![Reguły sieciowych grup zabezpieczeń](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Oprócz reguł sieciowej grupy zabezpieczeń, aby ograniczyć ruch sieciowy, można również użyć zapory systemu Windows na maszynie wirtualnej.

Jeśli korzystasz z punktów końcowych z klasycznym modelem wdrażania, Usuń wszystkie punkty końcowe na maszynie wirtualnej, jeśli nie są używane. Aby uzyskać instrukcje dotyczące korzystania z list ACL z punktami końcowymi, zobacz [Zarządzanie listą kontroli dostępu w punkcie końcowym](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Nie jest to konieczne w przypadku maszyn wirtualnych, które używają Menedżer zasobów.

Na koniec Rozważ włączenie zaszyfrowanych połączeń dla wystąpienia aparatu bazy danych SQL Server na maszynie wirtualnej platformy Azure. Skonfiguruj wystąpienie programu SQL Server przy użyciu podpisanego certyfikatu. Aby uzyskać więcej informacji, zobacz [Włączanie szyfrowanych połączeń do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) i [składni parametrów połączenia](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="encryption"></a>Szyfrowanie

Dyski zarządzane oferują szyfrowanie po stronie serwera i Azure Disk Encryption. [Szyfrowanie po stronie serwera](/azure/virtual-machines/windows/disk-encryption) zapewnia szyfrowanie w spoczynku i zabezpiecza dane zgodnie z zobowiązaniami dotyczącymi bezpieczeństwa i zgodności w organizacji. [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) używa technologii BitLocker lub dm-crypt i integruje się z Azure Key Vault, aby szyfrować zarówno system operacyjny, jak i dyski z danymi. 

## <a name="use-a-non-default-port"></a>Użyj portu innego niż domyślny

Domyślnie SQL Server nasłuchuje na dobrze znanym porcie 1433. Aby zwiększyć poziom bezpieczeństwa, należy skonfigurować SQL Server do nasłuchiwania na porcie innym niż domyślny, na przykład 1401. Jeśli zainicjujesz SQL Server obraz galerii w Azure Portal, możesz określić ten port w bloku **ustawienia SQL Server** .

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Aby skonfigurować tę opcję po zainicjowaniu obsługi administracyjnej, dostępne są dwie opcje:

- W przypadku maszyn wirtualnych Menedżer zasobów można wybrać opcję **zabezpieczenia** z [zasobu maszyny wirtualne SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). Zapewnia to możliwość zmiany portu.

  ![Zmiana portu TCP w portalu](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- W przypadku klasycznych maszyn wirtualnych lub dla SQL Server maszyn wirtualnych, które nie zostały udostępnione w portalu, można ręcznie skonfigurować port, łącząc zdalnie z maszyną wirtualną. Aby zapoznać się z krokami konfiguracji, zobacz [Konfigurowanie serwera do nasłuchiwania na konkretnym porcie TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). W przypadku korzystania z tej techniki ręcznej należy również dodać regułę zapory systemu Windows w celu zezwolenia na ruch przychodzący na tym porcie TCP.

> [!IMPORTANT]
> Określenie portu innego niż domyślny jest dobrym pomysłem, jeśli SQL Server Port jest otwarty dla publicznych połączeń internetowych.

Gdy SQL Server nasłuchuje na porcie innym niż domyślny, należy określić port podczas nawiązywania połączenia. Rozważmy na przykład scenariusz, w którym adres IP serwera to 13.55.255.255, a SQL Server nasłuchuje na porcie 1401. Aby nawiązać połączenie z SQL Server, należy określić `13.55.255.255,1401` w parametrach połączenia.

## <a name="manage-accounts"></a>Zarządzanie kontami

Nie chcesz, aby osoby atakujące mogli łatwo odgadnąć nazwy kont lub hasła. Aby uzyskać pomoc, Skorzystaj z następujących wskazówek:

- Utwórz unikatowe konto administratora lokalnego, którego nazwa nie jest **administratorem**.

- Używaj złożonych silnych haseł dla wszystkich kont. Aby uzyskać więcej informacji na temat tworzenia silnego hasła, zobacz artykuł [Tworzenie silnego hasła](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) .

- Domyślnie platforma Azure wybiera uwierzytelnianie systemu Windows podczas SQL Server konfiguracji maszyny wirtualnej. W związku z tym Logowanie przy użyciu **skojarzenia zabezpieczeń** jest wyłączone i zostanie przypisane hasło przez Instalatora. Zalecamy, aby nie można było używać ani korzystać z logowania **administratora** systemu. Jeśli musisz mieć identyfikator logowania SQL, użyj jednej z następujących strategii:

  - Utwórz konto SQL z unikatową nazwą, która ma członkostwo **sysadmin** . Można to zrobić z poziomu portalu, włączając **uwierzytelnianie SQL** podczas aprowizacji.

    > [!TIP] 
    > Jeśli nie włączysz uwierzytelniania SQL podczas aprowizacji, należy ręcznie zmienić tryb uwierzytelniania na **SQL Server i tryb uwierzytelniania systemu Windows**. Aby uzyskać więcej informacji, zobacz [Zmienianie trybu uwierzytelniania serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Jeśli konieczne jest użycie identyfikatora logowania **administratora** systemu, należy włączyć logowanie po aprowizacji i przypisać nowe silne hasło.

## <a name="additional-best-practices"></a>Dodatkowe najlepsze rozwiązania

Oprócz praktyk opisanych w tym temacie zalecamy zapoznanie się z najlepszymi rozwiązaniami w zakresie zabezpieczeń oraz ich implementację z zastosowaniem tradycyjnych lokalnych zasad zabezpieczeń, a także najlepszych rozwiązań dotyczących zabezpieczeń maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat lokalnych zasad zabezpieczeń, zobacz [zagadnienia dotyczące zabezpieczeń w przypadku instalacji SQL Server](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) i usługi [Security Center](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Aby uzyskać więcej informacji o zabezpieczeniach maszyn wirtualnych, zobacz [Omówienie zabezpieczeń maszyn wirtualnych](/azure/security/fundamentals/virtual-machines-overview).


## <a name="next-steps"></a>Następne kroki

Jeśli interesuje Cię również najlepsze rozwiązania dotyczące wydajności, zobacz [najlepsze rozwiązania w zakresie wydajności dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

