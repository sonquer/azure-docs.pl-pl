---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Konfigurowanie źródeł tożsamości programu vCenter w chmurze prywatnej automatycznej synchronizacji
description: Opisuje sposób konfigurowania programu vCenter w chmurze prywatnej chmury w celu uwierzytelniania za pomocą Active Directory dla administratorów oprogramowania VMware w celu uzyskania dostępu do programu vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad4a7b2bc67b7d50d9e9a5f8337a09dbe77366ea
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014219"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Skonfiguruj źródła tożsamości vCenter do użycia Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Informacje o źródłach tożsamości VMware vCenter

Program VMware vCenter obsługuje różne źródła tożsamości do uwierzytelniania użytkowników, którzy uzyskują dostęp do programu vCenter. Twoja funkcja automatycznej synchronizacji w chmurze prywatnej można skonfigurować do uwierzytelniania za pomocą Active Directory dla administratorów oprogramowania VMware w celu uzyskania dostępu do programu vCenter. Po zakończeniu instalacji użytkownik **cloudowner** może dodawać użytkowników ze źródła tożsamości do programu vCenter. 

Domenę Active Directory i kontrolery domeny można skonfigurować w dowolny z następujących sposobów:

* Active Directory domen i kontrolerów domeny działających lokalnie
* Active Directory kontrolery domeny i domeny działające na platformie Azure jako maszyny wirtualne w ramach subskrypcji platformy Azure
* Nowe Active Directory domeny i kontrolery domeny uruchomione w chmurze prywatnej automatycznej synchronizacji
* Usługa Azure Active Directory

W tym przewodniku opisano zadania służące do konfigurowania Active Directory domen i kontrolerów domeny z systemem lokalnym lub jako maszyn wirtualnych w ramach subskrypcji. Jeśli chcesz używać usługi Azure AD jako źródła tożsamości, zapoznaj się z [tematem korzystanie z usługi Azure AD jako dostawcy tożsamości dla programu vCenter w ramach automatycznej synchronizacji chmury prywatnej](azure-ad.md) , aby uzyskać szczegółowe instrukcje dotyczące konfigurowania źródła tożsamości.

Przed [dodaniem źródła tożsamości](#add-an-identity-source-on-vcenter)tymczasowo [Eskalacja uprawnień programu vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group  Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorzy* , a konta usług nie mogą być używane do logowania się do interfejsu użytkownika sieci Web vSphere.   


## <a name="identity-source-options"></a>Opcje źródła tożsamości

* [Dodawanie Active Directory lokalnego jako źródła tożsamości logowania jednokrotnego](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Skonfiguruj nowe Active Directory w chmurze prywatnej automatycznej synchronizacji](#set-up-new-active-directory-on-an-avs-private-cloud)
* [Konfigurowanie Active Directory na platformie Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Dodawanie Active Directory lokalnego jako źródła tożsamości logowania jednokrotnego

Aby skonfigurować Active Directory lokalne jako źródło tożsamości logowania jednokrotnego, potrzebne są:

* [Połączenie sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) z lokalnego centrum danych z chmurą prywatną.
* Adres IP lokalnego serwera DNS dodany do programu vCenter i kontrolera usług platformy (PSC).

Podczas konfigurowania domeny Active Directory należy skorzystać z informacji podanych w poniższej tabeli.

| **Opcja** | **Opis** |
|------------|-----------------|
| **Nazwa** | Nazwa źródła tożsamości. |
| **Podstawowa nazwa wyróżniająca dla użytkowników** | Podstawowa nazwa wyróżniająca dla użytkowników. |
| **Nazwa domeny** | NAZWY FDQN domeny, na przykład example.com. W tym polu tekstowym nie należy podawać adresu IP. |
| **Alias domeny** | Nazwa NetBIOS domeny. Dodaj nazwę NetBIOS domeny Active Directory jako alias źródła tożsamości, jeśli używasz uwierzytelniania SSPI. |
| **Podstawowa nazwa wyróżniająca dla grup** | Podstawowa nazwa wyróżniająca dla grup. |
| **Podstawowy adres URL serwera** | Serwer LDAP podstawowego kontrolera domeny dla domeny.<br><br>Użyj formatu `ldap://hostname:port` lub `ldaps://hostname:port`. Port jest zazwyczaj 389 dla połączeń LDAP i 636 dla połączeń LDAPs. W przypadku Active Directory wdrożeń wielodomenowych, port jest zwykle 3268 dla LDAP i 3269 dla LDAPs.<br><br>Certyfikat, który ustanawia zaufanie dla punktu końcowego LDAPs serwera Active Directory, jest wymagany w przypadku używania `ldaps://` w podstawowym lub pomocniczym adresie URL LDAP. |
| **Adres URL serwera pomocniczego** | Adres serwera LDAP pomocniczego kontrolera domeny, który jest używany do pracy w trybie failover. |
| **Wybieranie certyfikatu** | Jeśli chcesz używać LDAPs z serwerem LDAP Active Directory lub źródłem tożsamości serwera OpenLDAP, po wpisaniu `ldaps://` w polu tekstowym adresu URL zostanie wyświetlony przycisk Wybierz certyfikat. Pomocniczy adres URL nie jest wymagany. |
| **Nazwa użytkownika** | Identyfikator użytkownika w domenie, który ma minimalny dostęp tylko do odczytu do podstawowej nazwy wyróżniającej dla użytkowników i grup. |
| **Hasło** | Hasło użytkownika, który jest określony przez nazwę użytkownika. |

Jeśli masz informacje w poprzedniej tabeli, możesz dodać swoje Active Directory lokalne jako źródło tożsamości logowania jednokrotnego w programie vCenter.

> [!TIP]
> Więcej informacji o źródłach tożsamości logowania jednokrotnego znajdziesz na [stronie dokumentacji programu VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-an-avs-private-cloud"></a>Skonfiguruj nowe Active Directory w chmurze prywatnej automatycznej synchronizacji

Możesz skonfigurować nową domenę Active Directory w chmurze prywatnej automatycznej synchronizacji i użyć jej jako źródła tożsamości do logowania jednokrotnego. Domena Active Directory może być częścią istniejącego lasu Active Directory lub można ją skonfigurować jako Las niezależny.

### <a name="new-active-directory-forest-and-domain"></a>Nowy las i domena Active Directory

Aby skonfigurować nowy las Active Directory i domenę, potrzebne są:

* Co najmniej jedna maszyna wirtualna z systemem Microsoft Windows Server do użycia jako kontrolery domeny dla nowego lasu i domeny Active Directory.
* Co najmniej jedna maszyna wirtualna, na której działa usługa DNS w celu rozpoznawania nazw.

Aby uzyskać szczegółowe instrukcje, zobacz temat [Instalowanie nowego lasu systemu Windows Server 2012 Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> Aby zapewnić wysoką dostępność usług, zalecamy skonfigurowanie wielu kontrolerów domeny i serwerów DNS.

Po skonfigurowaniu lasu Active Directory i domeny możesz [dodać Źródło tożsamości w programie vCenter](#add-an-identity-source-on-vcenter) dla nowego Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nowa domena Active Directory w istniejącym lesie Active Directory

Aby skonfigurować nową domenę Active Directory w istniejącym lesie Active Directory, potrzebne są:

* Połączenie sieci VPN typu lokacja-lokacja z lokalizacją lasu Active Directory.
* Serwer DNS, aby rozpoznać nazwę istniejącego lasu Active Directory.

Szczegółowe kroki można znaleźć w temacie [Install a New Windows Server 2012 Active Directory Child lub Tree Domain](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) .

Po skonfigurowaniu domeny Active Directory można [dodać Źródło tożsamości w programie vCenter](#add-an-identity-source-on-vcenter) dla nowego Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Konfigurowanie Active Directory na platformie Azure

Active Directory uruchomiony na platformie Azure jest podobny do Active Directory działających lokalnie. Aby skonfigurować Active Directory uruchomiony na platformie Azure jako źródło tożsamości logowania jednokrotnego w programie vCenter, serwer vCenter i kontroler PSC muszą mieć łączność sieciową z usługą Azure Virtual Network, w której działają usługi Active Directory. Możesz nawiązać tę łączność przy użyciu usługi [azure Virtual Network Connection przy użyciu usługi ExpressRoute](azure-expressroute-connection.md) z sieci wirtualnej platformy Azure, w której usługa Active Directory Services jest uruchomiona w celu automatycznej synchronizacji chmury prywatnej.

Po nawiązaniu połączenia sieciowego wykonaj kroki opisane w sekcji [Dodawanie lokalnego Active Directory jako źródła tożsamości logowania jednokrotnego](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) , aby dodać je jako źródło tożsamości. 

## <a name="add-an-identity-source-on-vcenter"></a>Dodawanie źródła tożsamości w programie vCenter

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md) w chmurze prywatnej automatycznej synchronizacji.

2. Zaloguj się do programu vCenter w chmurze prywatnej do automatycznej synchronizacji.

3. Wybierz pozycję **Strona główna administracja >** .

    ![Administracja](media/OnPremAD01.png)

4. Wybierz pozycję Logowanie jednokrotne **> konfiguracja**.

    ![Logowanie jednokrotne](media/OnPremAD02.png)

5. Otwórz kartę **źródła tożsamości** i kliknij przycisk **+** , aby dodać nowe źródło tożsamości.

    ![Źródła tożsamości](media/OnPremAD03.png)

6. Wybierz **Active Directory jako serwer LDAP** , a następnie kliknij przycisk **dalej**.

    ![Usługa Active Directory](media/OnPremAD04.png)

7. Określ parametry źródła tożsamości dla danego środowiska, a następnie kliknij przycisk **dalej**.

    ![Usługa Active Directory](media/OnPremAD05.png)

8. Przejrzyj ustawienia i kliknij przycisk **Zakończ**.
