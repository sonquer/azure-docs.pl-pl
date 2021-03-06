---
title: Obsługiwane systemy operacyjne, aparaty kontenerów — Azure IoT Edge
description: Dowiedz się, które systemy operacyjne można uruchomić demona usługi Azure IoT Edge i środowisko uruchomieniowe i aparatów obsługiwane kontenerów urządzeń z systemem produkcyjnym
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f1f4efc13e2d11e7111264564a227a484d611d5
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760100"
---
# <a name="azure-iot-edge-supported-systems"></a>Usługa Azure IoT Edge obsługiwanych systemów

Ten artykuł zawiera szczegółowe informacje o systemach i składnikach obsługiwanych przez IoT Edge, niezależnie od tego, czy są one oficjalne czy w wersji zapoznawczej.

Jeśli wystąpią problemy podczas korzystania z usługi Azure IoT Edge, istnieje kilka sposobów poszukiwania pomocy technicznej. Wypróbuj jeden z następujących kanałów, aby uzyskać pomoc techniczną:

**Raportowania błędów** — większość programowania, który przechodzi do produktów Azure IoT Edge odbywa się w projekt open source usługi IoT Edge. Błędy mogą być zgłaszane na [strony problemy](https://github.com/azure/iotedge/issues) projektu. Poprawki należy szybko z projektu w drodze do aktualizacji produktów.

**Zespół obsługi klienta firmy Microsoft** — użytkowników, którzy mają [plan pomocy technicznej](https://azure.microsoft.com/support/plans/) zaangażować zespół obsługi klienta firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z [witryny Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Żądania funkcji** — produktu usługi Azure IoT Edge śledzi żądania funkcji za pomocą produktu [stronę z opiniami użytkowników](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Aparaty kontenera

Moduły Azure IoT Edge są implementowane jako kontenery, dlatego IoT Edge potrzebuje aparatu kontenera, aby je uruchomić. Firma Microsoft udostępnia z kontenera aparatu moby aparatu, aby spełnić to wymaganie. Ten aparat kontenera jest oparty na projekcie Moby Open Source. Docker CE i Docker EE są innymi aparatami popularnych kontenera. Są one również oparte na projekcie Moby Open Source i są zgodne z Azure IoT Edge. Firma Microsoft zapewnia najlepszą pomoc techniczną dla systemów korzystających z tych aparatów kontenerów; Jednak firma Microsoft nie może wydać w nich poprawek. Z tego powodu firma Microsoft zaleca używanie aparatu moby w systemach produkcyjnych.

<br>
<center>

![aparatu Moby jako](./media/support/only-moby-for-production.png)
środowiska uruchomieniowego kontenera </center>

## <a name="operating-systems"></a>Systemy operacyjne

Azure IoT Edge działa w większości systemów operacyjnych, które mogą uruchamiać kontenery; jednak nie wszystkie te systemy są równie obsługiwane. Systemy operacyjne są podzielone na warstwy, które reprezentują poziom pomocy technicznej, które użytkownicy mogą oczekiwać.

* Obsługiwane są systemy warstwy 1. W przypadku systemów warstwy 1 Firma Microsoft:
  * Czy ten system operacyjny jest w zautomatyzowanych testach
  * udostępnia pakiety instalacyjne dla nich
* Systemy warstwy 2 są zgodne z Azure IoT Edge i mogą być używane stosunkowo łatwo. W przypadku systemów warstwy 2:
  * Firma Microsoft zakończyła nieformalne testowanie na platformach lub wie, że partner pomyślnie uruchomił Azure IoT Edge na platformie
  * Pakiety instalacyjne dla innych platform może działać na tych platformach

Rodzina systemu operacyjnego hosta musi być zawsze zgodna z rodziną systemu operacyjnego gościa używanego wewnątrz kontenera modułu. Innymi słowy, można używać tylko kontenerów systemu Linux w kontenerach systemów Linux i Windows w systemie Windows. W przypadku korzystania z systemu Windows obsługiwane są tylko przetwórz izolowane kontenery, a nie kontenery izolowane funkcji Hyper-V.  

<br>
<center>

system operacyjny hosta ![jest zgodny z systemem operacyjnym gościa](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Warstwa 1

Systemy wymienione w poniższej tabeli są obsługiwane przez firmę Microsoft, ogólnie dostępne lub w publicznej wersji zapoznawczej, i są testowane z każdą nową wersją. 

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Rozciągaj i ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Serwer Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza  |
| [Serwer Ubuntu 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), kompilacja 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), kompilacja 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), kompilacja 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), kompilacja 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Wymienione powyżej systemy operacyjne Windows są wymaganiami dotyczącymi urządzeń z kontenerami systemu Windows w systemie Windows, które są jedyną obsługiwaną konfiguracją dla środowiska produkcyjnego. Pakiety instalacyjne Azure IoT Edge dla systemu Windows umożliwiają używanie kontenerów z systemem Linux w systemie Windows; Jednak ta konfiguracja służy tylko do tworzenia i testowania. Aby uzyskać więcej informacji, zobacz [używanie IoT Edge w systemie Windows do uruchamiania kontenerów systemu Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Warstwa 2

Systemy wymienione w poniższej tabeli są uważane za zgodne z Azure IoT Edge, ale nie są aktywnie przetestowane ani obsługiwane przez firmę Microsoft.

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7,5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Opiekun — wbudowany system operacyjny Linux](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Opiekun — wbudowany system operacyjny Linux w systemie operacyjnym + AMD64](./media/tutorial-c-module/green-check.png) | ![Opiekun — wbudowany system operacyjny Linux dla systemu operacyjnego i ARM32v7](./media/tutorial-c-module/green-check.png) | ![Opiekun — wbudowany system operacyjny Linux dla systemu operacyjnego i ARM64](./media/tutorial-c-module/green-check.png) |
| [Opiekun systemu operacyjnego w wersji Embedded Linux](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Opiekun — wbudowany system operacyjny Linux w wersji + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Opiekun — osadzony system operacyjny Linux w systemie operacyjnym i ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7,5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Rzeka wiatr 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rzeka wiatr 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 systemów, w tym Raspian Buster, użyj wersji OpenSSL, która nie jest obsługiwana przez IoT Edge. Przed zainstalowaniem IoT Edge Użyj następującego polecenia:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge można uruchamiać na maszynach wirtualnych. Użycie maszyny wirtualnej jako urządzenia IoT Edge jest powszechne, gdy klienci chcą rozszerzyć istniejącą infrastrukturę za pomocą funkcji analizy brzegowej. Rodzina systemu operacyjnego hosta maszyny wirtualnej musi być zgodna z rodziną systemu operacyjnego gościa używanego wewnątrz kontenera modułu. To wymaganie jest takie samo, jak w przypadku uruchamiania Azure IoT Edge bezpośrednio na urządzeniu. Azure IoT Edge jest niezależny od podstawowej technologii wirtualizacji i działa na maszynach wirtualnych opartych na platformach takich jak Hyper-V i vSphere.

<br>
<center>

![Azure IoT Edge](./media/support/edge-on-vm.png)
maszyny wirtualnej </center>

## <a name="minimum-system-requirements"></a>Minimalne wymagania systemowe

Azure IoT Edge działa doskonale na urządzeniach jako Raspberry PI3 na sprzęcie klasy serwerów. Wybór odpowiedniego sprzętu dla danego scenariusza zależy od obciążeń, które chcesz uruchomić. Podejmowanie ostatecznej decyzji o urządzeniu może być skomplikowane; można jednak łatwo zacząć tworzyć prototypy rozwiązania na tradycyjnych laptopach lub komputerach stacjonarnych.

Środowisko pracy podczas tworzenia prototypów ułatwi wybór ostatecznego urządzenia. Należy wziąć pod uwagę następujące pytania:

* Ile modułów znajduje się w obciążeniu?
* Ile warstw ma udział w kontenerach modułów?
* W jakim języku są zapisywane Twoje moduły?
* Ile danych będzie przetwarzanych przez moduły?
* Czy moduły wymagają dowolnego wyspecjalizowanego sprzętu w celu przyspieszenia obciążeń?
* Jakie są odpowiednie charakterystyki wydajności rozwiązania?
* Jaki jest Twój budżet sprzętu?
