---
title: Wiele adresów IP dla usługi Azure Virtual Machines — Portal | Microsoft Docs
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu Azure Portal | Menedżer zasobów.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: anavin
ms.openlocfilehash: 66cbb843369dee103f102c9c743da544a833ccf1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646397"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną za pomocą modelu wdrażania Azure Resource Manager przy użyciu Azure Portal. Nie można przypisać wielu adresów IP do zasobów utworzonych za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Omówienie modeli wdrażania](../resource-manager-deployment-model.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Tworzenie maszyny wirtualnej z wieloma adresami IP

Jeśli chcesz utworzyć maszynę wirtualną z wieloma adresami IP lub statycznym prywatnym adresem IP, musisz ją utworzyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, kliknij pozycję Opcje programu PowerShell lub interfejsu wiersza polecenia w górnej części tego artykułu. Można utworzyć maszynę wirtualną z jednym dynamicznym prywatnym adresem IP i (opcjonalnie) pojedynczym publicznym adresem IP. Skorzystaj z portalu, wykonując czynności opisane w artykule [Tworzenie maszyny wirtualnej z systemem Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) lub [Tworzenie maszyny wirtualnej z systemem Linux](../virtual-machines/linux/quick-create-portal.md) . Po utworzeniu maszyny wirtualnej można zmienić typ adresu IP z dynamicznego na statyczny i dodać dodatkowe adresy IP przy użyciu portalu, wykonując czynności opisane w sekcji [Dodawanie adresów IP do maszyny wirtualnej](#add) w tym artykule.

## <a name="add"></a>Dodawanie adresów IP do maszyny wirtualnej

Możesz dodać prywatne i publiczne adresy IP do interfejsu sieciowego platformy Azure, wykonując poniższe kroki. W przykładach w poniższych sekcjach założono, że masz już maszynę wirtualną z trzema konfiguracjami protokołu IP opisanymi w tym [scenariuszu](#scenario), ale nie jest to wymagane.

### <a name="coreadd"></a>Podstawowe kroki

1. Przejdź do Azure Portal w https://portal.azure.com i zaloguj się do niego, jeśli to konieczne.
2. W portalu kliknij pozycję **więcej usług** > wpisz *maszyny wirtualne* w polu Filtr, a następnie kliknij pozycję **maszyny wirtualne**.
3. W okienku **maszyny wirtualne** kliknij maszynę wirtualną, do której chcesz dodać adresy IP. Kliknij pozycję **interfejsy sieciowe** w wyświetlonym okienku maszyna wirtualna, a następnie wybierz interfejs sieciowy, do którego chcesz dodać adresy IP. W przykładzie pokazanym na poniższej ilustracji wybrano kartę sieciową o nazwie *myNIC* z maszyny wirtualnej o nazwie *myVM* :

    ![Interfejs sieciowy](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. W okienku, które pojawia się dla wybranej karty interfejsu sieciowego, kliknij pozycję **konfiguracje adresów IP**.

Wykonaj czynności opisane w jednej z poniższych sekcji, na podstawie typu adresu IP, który chcesz dodać.

### <a name="add-a-private-ip-address"></a>**Dodawanie prywatnego adresu IP**

Aby dodać nowy prywatny adres IP, wykonaj następujące kroki:

1. Wykonaj kroki opisane w sekcji [podstawowe kroki](#coreadd) tego artykułu.
2. Kliknij pozycję **Dodaj**. W wyświetlonym okienku **Dodawanie konfiguracji protokołu IP** Utwórz konfigurację adresu IP o nazwie *ipconfig-4* z *10.0.0.7* jako *statyczny* prywatny adres IP, a następnie kliknij przycisk **OK**.

    > [!NOTE]
    > Podczas dodawania statycznego adresu IP należy określić nieużywany, prawidłowy adres w podsieci, z którą jest połączona karta sieciowa. Jeśli wybrany adres nie jest dostępny, w portalu zostanie wyświetlony symbol X dla adresu IP i należy wybrać inny.

3. Po kliknięciu przycisku OK okienko zostanie zamknięte i zostanie wyświetlona nowa konfiguracja protokołu IP. Kliknij przycisk **OK** , aby zamknąć okienko **Dodawanie konfiguracji protokołu IP** .
4. Możesz kliknąć przycisk **Dodaj** , aby dodać dodatkowe konfiguracje IP lub zamknąć wszystkie otwarte bloki, aby zakończyć dodawanie adresów IP.
5. Aby dodać prywatne adresy IP do systemu operacyjnego maszyny wirtualnej, wykonaj kroki opisane w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](#os-config) w tym artykule.

### <a name="add-a-public-ip-address"></a>Dodawanie publicznego adresu IP

Publiczny adres IP jest dodawany przez skojarzenie zasobu publicznego adresu IP z nową konfiguracją IP lub istniejącą konfiguracją IP.

> [!NOTE]
> Publiczne adresy IP mają opłata nominalną. Aby dowiedzieć się więcej o cenach adresów IP, zapoznaj się z informacjami na stronie [cennika adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Utwórz zasób publicznego adresu IP

Publiczny adres IP jest jednym ustawieniem dla zasobu publicznego adresu IP. Jeśli masz zasób publicznego adresu IP, który nie jest aktualnie skojarzony z konfiguracją adresu IP, która ma zostać skojarzona z konfiguracją adresu IP, Pomiń poniższe kroki i wykonaj czynności opisane w jednej z poniższych sekcji, zgodnie z potrzebami. Jeśli nie masz dostępnego zasobu publicznego adresu IP, wykonaj następujące kroki, aby go utworzyć:

1. Przejdź do Azure Portal w https://portal.azure.com i zaloguj się do niego, jeśli to konieczne.
3. W portalu kliknij pozycję **Utwórz zasób** > **sieci** > **publiczny adres IP**.
4. W wyświetlonym okienku **Utwórz publiczny adres IP** wprowadź **nazwę**, wybierz typ **przypisania adresu IP** , **subskrypcję**, **grupę zasobów**i **lokalizację**, a następnie kliknij przycisk **Utwórz**, jak pokazano na poniższej ilustracji:

    ![Utwórz zasób publicznego adresu IP](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Wykonaj kroki opisane w jednej z poniższych sekcji, aby skojarzyć zasób publicznego adresu IP z konfiguracją adresów IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Skojarz zasób publicznego adresu IP z nową konfiguracją adresu IP

1. Wykonaj kroki opisane w sekcji [podstawowe kroki](#coreadd) tego artykułu.
2. Kliknij pozycję **Dodaj**. W wyświetlonym okienku **Dodawanie konfiguracji protokołu IP** Utwórz konfigurację adresu IP o nazwie *ipconfig-4*. W wyświetlonym okienku **Wybierz publiczny** adres IP Włącz **publiczny adres IP** i wybierz istniejący, dostępny zasób publicznego adresu IP.

    Po wybraniu zasobu publiczny adres IP kliknij przycisk **OK** , a okienko zostanie zamknięte. Jeśli nie masz istniejącego publicznego adresu IP, możesz go utworzyć, wykonując kroki opisane w sekcji [Tworzenie zasobu publicznego adresu IP](#create-public-ip) w tym artykule. 

3. Przejrzyj nową konfigurację adresów IP. Mimo że prywatny adres IP nie został jawnie przypisany, jeden został automatycznie przypisany do konfiguracji adresu IP, ponieważ wszystkie konfiguracje IP muszą mieć prywatny adres IP.
4. Możesz kliknąć przycisk **Dodaj** , aby dodać dodatkowe konfiguracje IP lub zamknąć wszystkie otwarte bloki, aby zakończyć dodawanie adresów IP.
5. Dodaj prywatny adres IP do systemu operacyjnego maszyny wirtualnej, wykonując kroki dla systemu operacyjnego w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](#os-config) w tym artykule. Nie dodawaj publicznego adresu IP do systemu operacyjnego.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Skojarz zasób publicznego adresu IP z istniejącą konfiguracją adresu IP

1. Wykonaj kroki opisane w sekcji [podstawowe kroki](#coreadd) tego artykułu.
2. Kliknij konfigurację adresu IP, do której chcesz dodać zasób publicznego adresu IP.
3. W wyświetlonym okienku IPConfig kliknij pozycję **adres IP**.
4. W wyświetlonym okienku **Wybierz publiczny adres IP** wybierz publiczny adres IP.
5. Kliknij przycisk **Zapisz** i Zamknij okienka. Jeśli nie masz istniejącego publicznego adresu IP, możesz go utworzyć, wykonując kroki opisane w sekcji [Tworzenie zasobu publicznego adresu IP](#create-public-ip) w tym artykule.
3. Przejrzyj nową konfigurację adresów IP.
4. Możesz kliknąć przycisk **Dodaj** , aby dodać dodatkowe konfiguracje IP lub zamknąć wszystkie otwarte bloki, aby zakończyć dodawanie adresów IP. Nie dodawaj publicznego adresu IP do systemu operacyjnego.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
