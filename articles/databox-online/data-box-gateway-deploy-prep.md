---
title: Samouczek dotyczący przygotowywania witryny Azure Portal do wdrożenia usługi Data Box Gateway | Microsoft Docs
description: Pierwszy samouczek dotyczący wdrażania usługi Azure Data Box Gateway obejmuje przygotowywanie witryny Azure Portal.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fdb4b7c370d5edadf3e0e5bc0d1ae548da6bcaa9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438661"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Samouczek: przygotowanie do wdrożenia Azure Data Box Gateway


To jest pierwszy samouczek z serii samouczków dotyczących wdrażania opisujących pełne wdrożenie usługi Azure Data Box Gateway. W tym samouczku opisano sposób przygotowania witryny Azure Portal do wdrażania zasobu usługi Data Box Gateway. 

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przygotowanie portalu zajmuje mniej niż 10 minut.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

## <a name="get-started"></a>Rozpocznij

Proces wdrażania usługi Data Box Gateway opisano w następujących samouczkach w zalecanej kolejności.

| **#** | **W tym kroku** | **Skorzystaj z tych dokumentów** |
| --- | --- | --- | 
| 1. |**[Przygotowywanie witryny Azure Portal na potrzeby usługi Data Box Gateway](data-box-gateway-deploy-prep.md)** |Utwórz i skonfiguruj zasób usługi Data Box Gateway przed aprowizowaniem urządzenia wirtualnego usługi Data Box Gateway. |
| 2. |**[Aprowizowanie usługi Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprowizowanie usługi Data Box Gateway w oprogramowaniu VMware](data-box-gateway-deploy-provision-vmware.md)**|W przypadku funkcji Hyper-V przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta funkcji Hyper-V z systemem Windows Server 2016 lub Windows Server 2012 R2. <br><br><br> W przypadku oprogramowania VMware przeprowadź aprowizację i nawiąż połączenie z urządzeniem wirtualnym usługi Data Box Gateway w systemie hosta z oprogramowaniem VMware ESXi 6.0, 6.5 lub 6.7.<br></br> |
| 3. |**[Łączenie, konfigurowanie i aktywowanie usługi Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika, przeprowadź konfigurację urządzenia i je aktywuj. Następnie można aprowizować udziały SMB.  |
| 4. |**[Przesyłanie danych za pomocą usługi Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Dodaj udziały i nawiąż z nimi połączenie za pomocą protokołu SMB lub NFS. |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu opisano wymagania wstępne dotyczące konfiguracji zasobu usługi Data Box Gateway, urządzenia usługi Data Box Gateway i sieci centrum danych.

### <a name="for-the-data-box-gateway-resource"></a>Zasób usługi Data Box Gateway

Przed rozpoczęciem upewnij się, że:

- Subskrypcja Microsoft Azure powinna być obsługiwana dla zasobu Data Box Gateway. Obsługiwane są również subskrypcje z opcją płatność zgodnie z rzeczywistym użyciem.
- Masz uprawnienia właściciela lub współautora na poziomie grupy zasobów dla zasobów Data Box Edge/Data Box Gateway, IoT Hub i magazynu platformy Azure.

    - Aby utworzyć dowolny zasób Data Box Edge/Data Box Gateway, należy mieć uprawnienia jako współautor (lub wyższy) w zakresie na poziomie grupy zasobów. Należy również upewnić się, że dostawca `Microsoft.DataBoxEdge` jest zarejestrowany. Aby uzyskać informacje na temat rejestrowania, przejdź do pozycji [zarejestruj dostawcę zasobów](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Aby utworzyć zasób konta magazynu, należy ponownie uzyskać wartość współautor lub wyższy dostęp do zakresu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
- Masz uprawnienia administratora lub użytkownika do Azure Active Directory interfejs API programu Graph. Aby uzyskać więcej informacji, zobacz [Azure Active Directory interfejs API programu Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

### <a name="for-the-data-box-gateway-device"></a>Urządzenie usługi Data Box Gateway

Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

- Masz dostęp do hosta funkcji Hyper-V z systemem Windows Server 2012 R2 lub nowszym bądź z oprogramowaniem VMware (ESXi 6.0, 6.5 lub 6.7), którego można użyć do aprowizacji urządzenia.
- System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego usługi Data Box:
  
  - Co najmniej 4 procesory wirtualne.
  - Co najmniej 8 GB pamięci RAM.
  - Jeden interfejs sieciowy.
  - Dysk systemu operacyjnego o rozmiarze 250 GB.
  - Dysk wirtualny o rozmiarze 2 TB do przechowywania danych systemu.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

- Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia usługi Data Box Gateway. Aby uzyskać więcej informacji, zobacz [wymagania systemowe Data Box Gateway](data-box-gateway-system-requirements.md).

- W normalnych warunkach operacyjnych Data Box Gateway należy:

    - Co najmniej 10 MB/s, aby upewnić się, że urządzenie pozostaje zaktualizowane.
    - Co najmniej 20 MB/s dedykowane i pobiera przepustowość do przesyłania plików.

## <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Jeśli masz istniejący zasób usługi Data Box Gateway, którego możesz użyć do zarządzania urządzeniami wirtualnymi, pomiń ten krok i przejdź do sekcji [Uzyskiwanie klucza aktywacji](#get-the-activation-key).

Aby utworzyć zasób Data Box Gateway, w Azure Portal wykonaj następujące czynności.

1. Użyj poświadczeń Microsoft Azure, aby zalogować się do:

    - Azure Portal pod tym adresem URL: [https://portal.azure.com](https://portal.azure.com).
    - Lub Portal Azure Government pod tym adresem URL: [https://portal.azure.us](https://portal.azure.us). Aby uzyskać więcej informacji, przejdź do [obszaru łączenie z Azure Government przy użyciu portalu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. W okienku po lewej stronie wybierz pozycję **+ Utwórz zasób**. Wyszukaj **Data Box Edge/Data Box Gateway**. Wybierz pozycję Data Box Edge/Data Box Gateway. Wybierz pozycję **Utwórz**.
3. Wybierz subskrypcję, która ma być używana dla Data Box Gateway urządzenia. Wybierz region, w którym chcesz wdrożyć zasób Data Box Gateway. W tym wydaniu dostępne są Wschodnie stany USA, Azja Wschodnia Południowe i Europa Zachodnia. Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. W opcji **Data Box Gateway** wybierz pozycję **Utwórz**.

    ![Wyszukiwanie usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Na karcie **podstawowe** wprowadź lub wybierz poniższe **szczegóły projektu**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja    |Jest to wypełniane automatycznie w oparciu o wcześniejszy wybór. Subskrypcja jest połączona z kontem rozliczeniowym. |
    |Grupa zasobów  |Wybierz istniejącą grupę lub utwórz nową.<br>Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).     |

5. Wprowadź lub wybierz następujące **szczegóły wystąpienia**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa   | Przyjazna nazwa identyfikująca zasób.<br>Nazwa może zawierać od 2 do 50 znaków, w tym litery, cyfry i łączniki.<br> Nazwa rozpoczyna się i kończy literą lub cyfrą.        |   
    |Region     |W tej wersji Wschodnie stany USA, Południowe Azja Wschodnia i Europa Zachodnia są dostępne do wdrożenia zasobu. W przypadku Azure Government dostępne są wszystkie regiony rządowe wymienione w [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/) . <br> Wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie.|
    
    ![Tworzenie zasobu usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Wybierz pozycję **Przegląd + utwórz**.
 
7. Na karcie **Recenzja + tworzenie** Przejrzyj **szczegóły cennika**, **warunki użytkowania**i szczegóły dotyczące zasobu. Wybierz pozycję **Utwórz**.

    ![Przejrzyj szczegóły zasobów Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Tworzenie zasobu trwa kilka minut. Po pomyślnym utworzeniu i wdrożeniu zasobu zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu**.

![Przejrzyj szczegóły zasobów Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Pobieranie obrazu urządzenia wirtualnego

Po utworzeniu zasobu usługi Data Box Gateway pobierz odpowiedni obraz urządzenia wirtualnego do aprowizowania urządzenia wirtualnego w systemie hosta. Obrazy urządzeń wirtualnych są specyficzne dla systemu operacyjnego.

> [!IMPORTANT]
> Oprogramowania uruchomionego w usłudze Data Box Gateway można używać tylko z zasobem usługi Data Box Gateway.

Wykonaj następujące kroki w [Azure Portal](https://portal.azure.com/) , aby pobrać obraz urządzenia wirtualnego.

1. W utworzonym zasobie, a następnie wybierz pozycję **Przegląd**. Jeśli masz istniejący zasób Azure Data Box Gateway, wybierz zasób i przejdź do **omówienia**. Wybierz pozycję **Konfiguracja urządzenia**.

    ![Nowy zasób usługi Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Na kafelku **obraz do pobrania** wybierz obraz urządzenia wirtualnego odpowiadający systemowi operacyjnemu na serwerze hosta używanym do aprowizacji maszyny wirtualnej. Pliki obrazów są około 5,6 GB.
   
   * [Dysk VHDX dla funkcji Hyper-V w systemie Windows Server 2012 R2 i nowszych](https://aka.ms/dbe-vhdx-2012).
   * [Dysk VMDK dla oprogramowania VMWare ESXi 6.0, 6.5 lub 6.7](https://aka.ms/dbe-vmdk).

    ![Pobierz obraz Data Box Gateway urządzenia wirtualnego](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.


## <a name="get-the-activation-key"></a>Uzyskiwanie klucza aktywacji

Po rozpoczęciu i uruchomieniu zasobu Data Box Gateway należy uzyskać klucz aktywacji. Ten klucz jest używany do aktywowania urządzenia usługi Data Box Gateway i nawiązaniu połączenia z zasobem. Ten klucz można uzyskać już teraz za pośrednictwem witryny Azure Portal.

1. Wybierz utworzony zasób, a następnie wybierz pozycję **Przegląd**. W obszarze **Konfiguracja urządzenia**przejdź do kafelka **Konfigurowanie i aktywowanie** .

    ![Konfigurowanie i aktywowanie kafelka](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Wybierz pozycję **Generuj klucz**, aby utworzyć klucz aktywacji. Wybierz ikonę kopiowania, aby skopiować klucz i zapisać go do użytku w przyszłości.

    ![Pobieranie klucza aktywacji](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Klucz aktywacji wygasa po trzech dniach od jego wygenerowania.
> - Jeśli klucz wygaśnie, wygeneruj nowy klucz. Starszy klucz nie jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Tworzenie nowego zasobu
> * Pobieranie obrazu urządzenia wirtualnego
> * Uzyskiwanie klucza aktywacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować maszynę wirtualną na potrzeby usługi Data Box Gateway. W zależności od systemu operacyjnego hosta szczegółowe instrukcje znajdują się w temacie:

> [!div class="nextstepaction"]
> [Aprowizowanie usługi Data Box Gateway w funkcji Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

LUB

> [!div class="nextstepaction"]
> [Aprowizowanie usługi Data Box Gateway w oprogramowaniu VMware](./data-box-gateway-deploy-provision-vmware.md)


