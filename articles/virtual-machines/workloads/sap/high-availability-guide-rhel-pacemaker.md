---
title: Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure | Microsoft Docs
description: Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: 9ccbd67348a8dae7391471ccd1dcc1ba9b135ea2
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941824"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Przeczytaj najpierw następujące informacje i dokumenty SAP:

* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane programy SAP i system operacyjny oraz kombinacje baz danych.
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure.
* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2002167] zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2009879] SAP HANA wytycznych dotyczących Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux (ten artykuł)][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Replikacja systemu SAP HANA w klastrze Pacemaker](https://access.redhat.com/articles/3004101)
* Ogólna dokumentacja RHEL
  * [Omówienie dodatku wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatkiem wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dodatkowe informacje o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — SBD i fence_sbd](https://access.redhat.com/articles/2800691)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) klastra o wysokiej dostępności na Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Konfigurowanie protokołu SAP S/4HANA ASCS/wykres WYWOŁUJĄCYCH przy użyciu autonomicznej kolejki serwer 2 (ENSA2) w Pacemaker na RHEL 7,6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Instalacja klastra

![Pacemaker na RHEL — Omówienie](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat nie obsługuje emulowanego przez oprogramowanie licznika alarmowego. Red Hat nie obsługuje SBD na platformach w chmurze. Aby uzyskać szczegółowe informacje, zobacz [zasady pomocy technicznej dotyczące klastrów RHEL o wysokiej dostępności — SBD i fence_sbd](https://access.redhat.com/articles/2800691).
> Jedynym obsługiwanym mechanizmem ogrodzenia dla klastrów Pacemaker Red Hat Enterprise Linux na platformie Azure jest Agent usługi Azure ogrodzeni.  

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. Rejestr **[A]**

   Zarejestruj maszyny wirtualne i Dołącz je do puli zawierającej repozytoria dla RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Należy pamiętać, że przez dołączenie puli do obrazu programu RHEL z witryny Azure Marketplace w witrynie PAYG, opłaty są naliczane dwukrotnie za użycie RHEL: jeden dla obrazu PAYG i jeden raz dla uprawnienia RHEL w puli, którą dołączysz. Aby rozwiązać ten problem, platforma Azure udostępnia teraz obrazy BYOS RHEL. Więcej informacji można znaleźć [tutaj](../redhat/byos.md).

1. **[A]** Włącz RHEL dla REPOZYTORIów SAP

   Aby zainstalować wymagane pakiety, należy włączyć następujące repozytoria.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Zainstaluj dodatek RHEL ha

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Firma Microsoft zaleca, aby klienci korzystali z szybszych czasów pracy awaryjnej (lub nowszych), jeśli zakończy się niepowodzeniem, lub węzły klastra nie mogą komunikować się, które są już takie:  
   > RHEL 7,6: ogrodzeni-Agents-4.2.1-11. el7_6.8  
   > RHEL 7,5: ogrodzeni-Agents-4.0.11-86. el7_5.8  
   > RHEL 7,4: ogrodzeni-Agents-4.0.11-66. el7_4.12  
   > Aby uzyskać więcej informacji, zobacz [maszynę wirtualną platformy Azure działającą jako SKŁADOWA RHEL o wysokiej dostępności trwa bardzo długo lub nie można przekroczyć limitu czasu przed zamknięciem maszyny wirtualnej](https://access.redhat.com/solutions/3408711).

   Sprawdź wersję agenta usługi Azure ogrodzenia. W razie potrzeby zaktualizuj ją do wersji równej lub nowszej niż podane powyżej.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Jeśli trzeba zaktualizować agenta usługi Azure ogrodzenia, a jeśli jest używana rola niestandardowa, należy zaktualizować rolę niestandardową w celu uwzględnienia akcji **wyłączenie**. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie roli niestandardowej dla agenta ogranicznika](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach. Zaletą używania/etc/hosts będzie niezależnie od systemu DNS, co może być zbyt pojedynczy punkt awarii klastra.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Zmień hacluster hasło do tego samego hasła

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** dodawanie reguł zapory dla Pacemaker

   Dodaj następujące reguły zapory do całej komunikacji między węzłami klastra.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Włącz podstawowe usługi klastra

   Uruchom następujące polecenia, aby włączyć usługę Pacemaker i ją uruchomić.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Utwórz klaster Pacemaker

   Uruchom następujące polecenia, aby uwierzytelnić węzły i utworzyć klaster. Ustaw token na 30000, aby zezwalać na konserwację pamięci. Aby uzyskać więcej informacji, zobacz [ten artykuł dla systemu Linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Ustaw oczekiwane głosy

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Utwórz urządzenie STONITH

Urządzenie pomocą metody STONITH używa nazwy głównej usługi, do autoryzacji dla Microsoft Azure. Wykonaj następujące kroki, aby utworzyć jednostkę usługi.

1. Przejdź do usługi <https://portal.azure.com>
1. Otwórz blok usługi Azure Active Directory  
   Przejdź do właściwości i zanotuj nazwę katalogu. Jest to **identyfikator dzierżawy**.
1. Kliknij przycisk rejestracje aplikacji
1. Kliknij pozycję Nowa rejestracja
1. Wprowadź nazwę, wybierz pozycję "konta tylko w tym katalogu organizacji". 
2. Wybierz pozycję typ aplikacji "sieć Web", wprowadź adres URL logowania (na przykład http:\//localhost), a następnie kliknij przycisk Dodaj.  
   Adres URL logowania nie jest używany i może być dowolny prawidłowy adres URL
1. Wybierz pozycję Certyfikaty i wpisy tajne, a następnie kliknij pozycję Nowy wpis tajny klienta.
1. Wprowadź opis nowego klucza, wybierz pozycję "nigdy nie wygasa" i kliknij przycisk Dodaj.
1. Zanotuj wartość. Jest ona używana jako **hasło** jednostki usługi
1. Wybierz pozycję przegląd. Zanotuj identyfikator aplikacji. Jest ona używana jako nazwa użytkownika (**Identyfikatora logowania** w poniższych krokach) jednostki usługi

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Utworzyć rolę niestandardową dla agenta odgradzania

Nazwa główna usługi nie ma uprawnień do dostępu do zasobów platformy Azure, domyślnie. Należy nadać uprawnienia główne usługi, aby uruchomić i zatrzymać (wyłączyć) wszystkie maszyny wirtualne w klastrze. Jeśli nie utworzono jeszcze niestandardowej roli, można utworzyć za pomocą [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Użyj zawartości dla pliku wejściowego. Należy dostosować zawartość dla Twojej subskrypcji, Zastąp c276fc76-9cd4-44c9-99a7-4fd71546436e i e91d47c4-76f3-4271-a796-21b4ecfe3624 identyfikatory subskrypcji. Jeśli masz tylko jedną subskrypcję, należy usunąć drugi wpis w AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Przypisz rolę niestandardową do jednostki usługi

Przypisz rolę niestandardową "Linux horyzont agenta rolę" utworzonego w rozdziale ostatniego jednostki usługi. Nie używaj roli właściciel już!

1. Przejdź do usługi https://portal.azure.com
1. Otwieranie bloku wszystkie zasoby
1. Wybierz maszynę wirtualną, w pierwszym węźle klastra
1. Kliknij przycisk kontroli dostępu (IAM)
1. Kliknij przycisk Dodaj przypisanie roli
1. Wybierz rolę "Rolę agenta Odgradzania Linux"
1. Wprowadź nazwę aplikacji, które zostały utworzone powyżej
1. Kliknij pozycję Zapisz

Powtórz powyższe kroki dla drugiego węzła klastra.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Tworzenie urządzeń pomocą metody STONITH

Po edycji uprawnień dla maszyn wirtualnych można skonfigurować urządzenia pomocą metody STONITH w klastrze.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Użyj następującego polecenia, aby skonfigurować urządzenie ogrodzenia.

> [!NOTE]
> Opcja "pcmk_host_map" jest wymagana tylko w poleceniu, jeśli nazwy hosta RHEL i nazwy węzłów platformy Azure nie są identyczne. Zapoznaj się z sekcją pogrubienie w poleceniu.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Włącz korzystanie z urządzenia STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
