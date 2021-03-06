---
title: Omówienie obsługi funkcji Cloud-init dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Omówienie możliwości usługi Cloud-init w celu skonfigurowania maszyny wirtualnej w czasie aprowizacji na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: e3a09a0d8412af711bfb6c539dc9d2829b1f0898
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964587"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Obsługa usługi Cloud-init dla maszyn wirtualnych w systemie Azure
W tym artykule opisano obsługę funkcji [Cloud-init](https://cloudinit.readthedocs.io) w celu skonfigurowania maszyny wirtualnej lub zestawów skalowania maszyn wirtualnych w czasie aprowizacji na platformie Azure. Te konfiguracje usługi Cloud-init są uruchamiane podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure.  

Inicjowanie obsługi maszyn wirtualnych to proces, w którym platforma Azure przejdzie do lokalizacji maszyny wirtualnej Tworzenie wartości parametrów, takich jak nazwa hosta, nazwa użytkownika, hasło itp., oraz udostępnienie ich dla maszyny wirtualnej w trakcie jej uruchamiania. "Agent aprowizacji" użyje tych wartości, skonfiguruje maszynę wirtualną i zwróci zwrot z powrotem po zakończeniu. 

Platforma Azure obsługuje dwa agenci aprowizacji [Cloud-init](https://cloudinit.readthedocs.io)oraz [agenta systemu Azure Linux (wala)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>Cloud-init — Omówienie
[Cloud-init](https://cloudinit.readthedocs.io) to szeroko stosowane podejście do dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego uruchomienia. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest wywoływana podczas początkowego rozruchu, dlatego są żadne dodatkowe kroki ani agenci wymagane do zastosowania konfiguracji.  Aby uzyskać więcej informacji na temat poprawnego formatowania plików `#cloud-config` lub innych danych wejściowych, zobacz [witrynę dokumentacji Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` są to pliki tekstowe zakodowane w formacie base64.

Usługa Cloud-init działa również między dystrybucjami. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Usługa Cloud-init automatycznie korzysta z natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie współpracujemy z partnerami zalecanych dystrybucji systemu Linux, aby mogła mieć pakietu cloud-init, włączone obrazów dostępnych w witrynie Azure marketplace. Te obrazy spowodują, że wdrożenia i konfiguracje usługi Cloud-init pracują bezproblemowo z maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych. Początkowo firma Microsoft współpracuje z wystawionymi partnerami dystrybucji i nadrzędnymi systemu Linux w celu zapewnienia funkcji Cloud-init z systemem operacyjnym na platformie Azure, a następnie pakiety są aktualizowane i udostępniane publicznie w repozytoriach pakietów dystrybucji. 

Istnieją dwa etapy umożliwiające udostępnienie usługi Cloud-init dla zatwierdzonego systemu operacyjnego Linux dystrybucji na platformie Azure, obsługę pakietów, a następnie obsługę obrazów:
* "Obsługa pakietu Cloud-init w przypadku dokumentów platformy Azure", które pakiety inicjowania usługi Cloud-init są obsługiwane lub w wersji zapoznawczej, dzięki czemu można używać tych pakietów z systemem operacyjnym w obrazie niestandardowym.
* "Image Cloud-init Read", jeśli obraz jest już skonfigurowany do korzystania z funkcji Cloud-init.


### <a name="canonical"></a>Canonical
| Wydawca/wersja| Oferta | JSZ | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanoniczny 18,04 |UbuntuServer |18.04-LTS |najnowsza |tak | tak |
|Kanoniczny 16,04|UbuntuServer |16.04-LTS |najnowsza |tak | tak |
|Kanoniczny 14,04|UbuntuServer |14.04.5-LTS |najnowsza |tak | tak |

### <a name="rhel"></a>RHEL
| Wydawca/wersja | Oferta | JSZ | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |tak | tak — obsługa z wersji pakietu: *18.2-1. el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | tak (Zwróć uwagę na to, że jest to obraz w wersji zapoznawczej, a wszystkie obrazy z systemem RHEL 7,7 obsługują funkcję Cloud-init, zostanie to 2020 usunięte. | tak — obsługa z wersji pakietu: *18.5 -3. el7*|
|RedHat 7,7 |RHEL |7 — NIEPRZETWORZONY | nd.| aktualizacje bez obrazu do uruchomienia lutego 2020| tak — obsługa z wersji pakietu: *18.5 -3. el7*|
|RedHat 7,7 |RHEL |7 — LVM | nd.| aktualizacje bez obrazu do uruchomienia lutego 2020| tak — obsługa z wersji pakietu: *18.5 -3. el7*|
|RedHat 7,7 |RHEL |7,7 | nd.| aktualizacje bez obrazu do uruchomienia lutego 2020 | tak — obsługa z wersji pakietu: *18.5 -3. el7*|
|RedHat 7,7 |RHEL — BYOS | RHEL — lvm77 | nd.|aktualizacje bez obrazu do uruchomienia lutego 2020  | tak — obsługa z wersji pakietu: *18.5 -3. el7*|

### <a name="centos"></a>CentOS

| Wydawca/wersja | Oferta | JSZ | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |tak (Zwróć uwagę na to, że jest to obraz w wersji zapoznawczej, a wszystkie obrazy z systemem CentOS 7,7 obsługują funkcję Cloud-init, zostanie to 2020 usunięte. | tak — obsługa z wersji pakietu: *18.5 -3. el7. CentOS*|

* 2020 w tym miejscu można zaktualizować obrazy z systemem CentOS 7,7, które będą miały włączoną funkcję Cloud-init. 

### <a name="oracle"></a>Oracle

| Wydawca/wersja | Oferta | JSZ | Wersja | obraz Cloud-init gotowy | Obsługa pakietu Cloud-init na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77-Ci |7.7.01| Podgląd obrazu (Zwróć uwagę na to, że jest to obraz w wersji zapoznawczej, a gdy wszystkie obrazy z systemem Oracle 7,7 obsługują funkcję Cloud-init, zostanie ono 2020 usunięte. | nie, w wersji zapoznawczej pakiet jest: *18.5-3.0.1. el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
Obecnie pracujemy nad obsługą wersji zapoznawczej, oczekują aktualizacji w lutym i marzec 2020.

Obecnie Azure Stack obsługuje Inicjowanie obsługi obrazów z obsługą funkcji Cloud-init.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Czym różni się usługa Cloud-init i Agent systemu Linux (WALA)?
WALA to Agent specyficzny dla platformy Azure służący do aprowizacji i konfigurowania maszyn wirtualnych oraz obsługi [rozszerzeń platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Ulepszamy zadanie konfigurowania maszyn wirtualnych, aby korzystało z usługi Cloud-init zamiast agenta systemu Linux, aby umożliwić istniejącym klientom korzystającym z usługi Cloud-init używanie ich bieżących skryptów w chmurze lub nowych klientów w celu skorzystania z zaawansowanych funkcji konfiguracji usługi Cloud-init. Jeśli masz istniejące inwestycje w skrypty inicjowania usługi Cloud-init do konfigurowania systemów z systemem Linux, **nie ma dodatkowych ustawień wymaganych** do przetworzenia ich przez program Cloud-init. 

Usługa Cloud-init nie może przetwarzać rozszerzeń platformy Azure, więc WALA jest nadal wymagana w obrazie do przetwarzania rozszerzeń, ale musi mieć wyłączony kod aprowizacji, w przypadku zatwierdzonych obrazów systemu Linux dystrybucje, które są konwertowane na potrzeby inicjowania obsługi przez funkcję Cloud-init, będą mieć WALA zainstalowana i poprawnie skonfigurowana.

Jeśli podczas tworzenia maszyny wirtualnej nie zostanie uwzględniony przełącznik `--custom-data` interfejsu wiersza polecenia platformy Azure w czasie aprowizacji, Usługa Cloud-init lub WALA przyjmuje parametry minimalnej aprowizacji maszyny wirtualnej wymagane do zainicjowania obsługi maszyny wirtualnej i Ukończ wdrożenie przy użyciu ustawień domyślnych.  Jeśli odwołujesz się do konfiguracji Cloud-init z przełącznikiem `--custom-data`, niezależnie od tego, jakie dane są zawarte w danych niestandardowych, będą dostępne w chmurze-init podczas uruchamiania maszyny wirtualnej.

konfiguracje inicjowania usługi Cloud-init stosowane do maszyn wirtualnych nie mają ograniczeń czasowych i nie spowodują, że wdrożenie zakończy się niepowodzeniem. Ta wartość nie jest prawdziwa w przypadku WALA, jeśli zmienisz domyślne ustawienia WALA na przetwarzanie danych niestandardowych, nie można przekroczyć łącznego czasu udostępniania maszyny wirtualnej o wartości 40mins, jeśli tak, Tworzenie maszyny wirtualnej zakończy się niepowodzeniem.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Wdrażanie maszyny wirtualnej z obsługą usługi Cloud-init
Wdrażanie maszyny wirtualnej z włączoną obsługą chmury jest proste, ponieważ odwołuje się do dystrybucji z włączoną obsługą chmury podczas wdrażania.  Obsłudze dystrybucji systemu Linux muszą włączyć i zintegrować usługę Cloud-init z podstawowymi opublikowanymi obrazami platformy Azure. Po potwierdzeniu obrazu, który chcesz wdrożyć, jest włączona funkcja Cloud-init, można użyć interfejsu wiersza polecenia platformy Azure do wdrożenia obrazu. 

Pierwszym krokiem wdrożenia tego obrazu jest utworzenie grupy zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Następnym krokiem jest utworzenie pliku w bieżącej powłoce o nazwie *Cloud-init. txt* i wklejenie poniższej konfiguracji. Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Naciśnij `ctrl-X`, aby zamknąć plik, wpisz `y`, aby zapisać plik, a następnie naciśnij klawisz `enter`, aby potwierdzić nazwę pliku przy zamykaniu.

Ostatnim krokiem jest utworzenie maszyny wirtualnej za pomocą polecenia [AZ VM Create](/cli/azure/vm) . 

Poniższy przykład tworzy maszynę wirtualną o nazwie *centos74* i tworzy klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy maszynę wirtualną o nazwie *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetli informacje specyficzne dla Twojego wdrożenia. Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.  Tworzenie maszyny wirtualnej trwa trochę czasu, pakiety do zainstalowania i aplikacja do uruchomienia. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Do maszyny wirtualnej można użyć protokołu SSH i wykonać kroki opisane w sekcji Rozwiązywanie problemów w celu wyświetlenia dzienników usługi Cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Rozwiązywanie problemów z usługą Cloud-init
Po zainicjowaniu obsługi maszyny wirtualnej Usługa Cloud-init będzie działać za pomocą wszystkich modułów i skryptów zdefiniowanych w `--custom-data` w celu skonfigurowania maszyny wirtualnej.  Jeśli konieczne jest rozwiązanie błędów lub pominięć z konfiguracji, należy wyszukać nazwę modułu (na przykład`disk_setup` lub `runcmd`) w dzienniku inicjowania usługi Cloud-init w **/var/log/Cloud-init.log**.

> [!NOTE]
> Nie każdy błąd modułu powoduje krytyczny błąd konfiguracji w chmurze. Na przykład przy użyciu modułu `runcmd`, jeśli skrypt nie powiedzie się, Usługa Cloud-init nadal zgłosi zgłoszenie, ponieważ moduł runcmd został uruchomiony.

Więcej szczegółów dotyczących rejestrowania w usłudze Cloud-init można znaleźć w [dokumentacji usługi Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Następne kroki
Przykłady zmian konfiguracji w usłudze Cloud-init można znaleźć w następujących dokumentach:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
 
