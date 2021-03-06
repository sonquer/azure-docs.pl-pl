---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Konfigurowanie programu vCenter w chmurze prywatnej o automatycznej synchronizacji na potrzeby automatyzacji vRealize
description: Zawiera opis sposobu konfigurowania serwera VMware vCenter w prywatnej chmurze automatycznej synchronizacji jako punktu końcowego dla automatyzacji vRealize Automation
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 41106498594ac05b944323e5f5e63de739aedf37
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024844"
---
# <a name="set-up-vcenter-on-your-avs-private-cloud-for-vmware-vrealize-automation"></a>Skonfiguruj program vCenter w chmurze prywatnej automatycznej synchronizacji dla programu VMware vRealize Automation

Program VMware vCenter Server można skonfigurować w chmurze prywatnej do automatycznej synchronizacji jako punkt końcowy dla automatyzacji vRealize Automation.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonaj te zadania przed skonfigurowaniem serwera vCenter:

* Skonfiguruj [połączenie sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) między środowiskiem lokalnym i chmurą prywatną.
* [Skonfiguruj przekazywanie DNS lokalnych żądań DNS](on-premises-dns-setup.md) do serwerów DNS dla chmury prywatnej automatycznej synchronizacji.
* Prześlij [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) , aby utworzyć użytkownika administracyjnego usługi VRealize Automation IaaS z zestawem uprawnień wymienionych w poniższej tabeli.

| Wartość atrybutu | Uprawnienie |
------------ | ------------- |  
| Magazyn danych |  Przydziel miejsce <br> Przeglądaj magazyn danych |
| Klaster magazynu danych | Konfigurowanie klastra magazynu danych |
| Folder | Tworzenie folderu <br>Usuń Folder |
| Cały świat |  Zarządzanie atrybutami niestandardowymi<br>Ustaw atrybut niestandardowy |
| Network (Sieć) | Przypisywanie sieci |
| Uprawnienia | Modyfikuj uprawnienia |
| Zasób | Przypisywanie maszyny wirtualnej do puli zasobów<br>Migrowanie wyłączone z maszyny wirtualnej<br>Migrowanie na maszynie wirtualnej |
| Spis maszyn wirtualnych |  Utwórz na podstawie istniejącego<br>Kliknięcie przycisku Nowe<br>Move<br>Usuń | 
| Interakcja maszyny wirtualnej |  Konfigurowanie nośnika CD<br>Interakcja z konsolą<br>Połączenie z urządzeniem<br>Zasilanie wyłączone<br>Włącz<br>Reset<br>Wstrzymywanie<br>Instalacja narzędzi | 
| Konfiguracja maszyny wirtualnej |  Dodaj istniejący dysk<br>Dodaj nowy dysk<br>Dodaj lub Usuń<br>Usuń dysk<br>Advanced<br>Zmień liczbę procesorów<br>Zmień zasób<br>Zwiększ dysk wirtualny<br>Change Tracking dysku<br>Pamięć<br>Modyfikowanie ustawień urządzenia<br>Zmiana nazwy<br>Ustawianie adnotacji (wersja 5,0 i nowsze)<br>Ustawienia<br>Swapfile |
| Inicjowanie obsługi administracyjnej |  Dostosowywanie<br>Klonuj szablon<br>Klonowanie maszyny wirtualnej<br>Wdrażanie szablonu<br>Przeczytaj specyfikacje dostosowania |
| Stan maszyny wirtualnej | Utwórz migawkę<br>Usuń migawkę<br>Przywróć migawkę |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instalowanie usługi vRealize Automation w środowisku lokalnym

1. Zaloguj się do usługi vRealize Automation IaaS Server Appliance jako administrator IaaS, który obsługuje tę AUTOMATYCZną obsługę.
2. Wdróż agenta vSphere dla punktu końcowego usługi vRealize Automation.
    1. Przejdź do https://*VRA-URL*: 5480/Installer, gdzie *VRA-URL* jest adresem URL używanym do uzyskiwania dostępu do interfejsu użytkownika administracji programu vRealize Automation.
    2. Kliknij **Instalatora IaaS** , aby pobrać Instalatora.<br>
    Konwencja nazewnictwa dla pliku Instalatora jest setup_*VRA-url*@5480.exe.
    3. Uruchom Instalatora. Na ekranie powitalnym kliknij przycisk **dalej**.
    4. Zaakceptuj umowę licencyjną i kliknij przycisk **dalej**.
    5. Podaj informacje logowania, kliknij przycisk **Zaakceptuj certyfikat**, a następnie kliknij przycisk **dalej**.
    ![poświadczenia vRA](media/configure-vra-endpoint-login.png)
    6. Wybierz opcję **Instalacja niestandardowa** i **agenci proxy** , a następnie kliknij przycisk **dalej**.
    Typ instalacji ![vRA](media/configure-vra-endpoint-install-type.png)
    7. Wprowadź informacje logowania do serwera IaaS, a następnie kliknij przycisk **dalej**. Jeśli używasz Active Directory, wprowadź nazwę użytkownika w formacie **domena \ użytkownik** . W przeciwnym razie użyj formatu **user@domain** .
    ![informacje logowania vRA](media/configure-vra-endpoint-account.png)
    8. Dla ustawień serwera proxy wprowadź wartość **vSphere** dla **typu agenta**. Wprowadź nazwę agenta.
    9. Wprowadź nazwę FQDN serwera IaaS na **hoście usługi Menedżera** i polach **hosta usługi sieci Web Menedżera modeli** . Kliknij przycisk **Testuj** , aby przetestować połączenie dla każdej wartości FQDN. Jeśli test zakończy się niepowodzeniem, zmodyfikuj ustawienia DNS tak, aby nazwa hosta IaaS serwera została rozpoznana.
    10. Wprowadź nazwę punktu końcowego programu vCenter Server dla chmury prywatnej automatycznej synchronizacji. Zapisz nazwę do użycia w dalszej części procesu konfiguracji.

        ![vRA Zainstaluj serwer proxy](media/configure-vra-endpoint-proxy.png)

    11. Kliknij przycisk **Dalej**.
    12. Kliknij pozycję **Zainstaluj**.

## <a name="configure-the-vsphere-agent"></a>Konfigurowanie agenta vSphere

1. Przejdź do https://*VRA-URL*/VCAC i zaloguj się jako **ConfigurationAdmin**.
2. Wybierz pozycję **infrastruktura** > **punkty końcowe** > **punktów końcowych**.
3. Wybierz pozycję **New** > **Virtual** > **vSphere**.
4. Wprowadź nazwę punktu końcowego vSphere, która została określona w poprzedniej procedurze.
5. W polu **adres**wprowadź adres URL vCenter Server chmury prywatnej w formacie https://*vCenter-FQDN*/SDK, gdzie *vCenter-FQDN* jest nazwą serwera vCenter.
6. Wprowadź poświadczenia dla użytkownika administracyjnego usługi vRealize Automation IaaS, który został przez Ciebie utworzony przez AUTOMATYCZną obsługę.
7. Kliknij przycisk **Testuj połączenie** , aby zweryfikować poświadczenia użytkownika. Jeśli test zakończy się niepowodzeniem, sprawdź, czy adres URL, informacje o koncie i [nazwę punktu końcowego](#verify-the-endpoint-name) zostały ponownie wykonane.
8. Po pomyślnym teście kliknij przycisk **OK** , aby utworzyć punkt końcowy vSphere.
    ![](media/configure-vra-endpoint-vra-edit.png) dostępu do konfiguracji punktu końcowego vRA

### <a name="verify-the-endpoint-name"></a>Sprawdź nazwę punktu końcowego

Aby zidentyfikować poprawną nazwę punktu końcowego serwera vCenter, wykonaj następujące czynności:

1. Otwórz wiersz polecenia na urządzeniu IaaS.
2. Zmień katalog na C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, gdzie *Agent-Name* to nazwa przypisana do punktu końcowego serwera vCenter.
3. Uruchom następujące polecenie.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Dane wyjściowe są podobne do następujących. Wartością pola `managementEndpointName` jest nazwa punktu końcowego.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
