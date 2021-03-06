---
title: Włącz usługę profilu użytkownika programu SharePoint przy użyciu usługi Azure AD DS | Microsoft Docs
description: Dowiedz się, jak skonfigurować domenę zarządzaną Azure Active Directory Domain Services, aby obsługiwała synchronizację profilu dla programu SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: f51744d79d34b734c9cc24cf72785a076a91a4f5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509057"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Konfigurowanie Azure Active Directory Domain Services do obsługi synchronizacji profilu użytkownika dla programu SharePoint Server

Program SharePoint Server zawiera usługę umożliwiającą synchronizowanie profilów użytkowników. Ta funkcja umożliwia przechowywanie profilów użytkowników w centralnej lokalizacji i dostęp w wielu witrynach i farmach programu SharePoint. Aby skonfigurować usługę profilu użytkownika programu SharePoint Server, należy przyznać odpowiednie uprawnienia w domenie zarządzanej Azure Active Directory Domain Services (Azure AD DS). Aby uzyskać więcej informacji, zobacz [Synchronizacja profilu użytkownika w programie SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

W tym artykule opisano sposób konfigurowania AD DS platformy Azure w taki sposób, aby zezwalała na usługę synchronizacji profilu użytkownika programu SharePoint Server.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby Uzupełnij samouczek, aby [utworzyć i skonfigurować wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Maszyna wirtualna zarządzania systemem Windows Server, która jest dołączona do domeny zarządzanej AD DS platformy Azure.
    * W razie potrzeby Ukończ samouczek, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.
* Konto usługi programu SharePoint dla usługi synchronizacji profilu użytkownika.
    * W razie potrzeby zapoznaj się z tematem [Planowanie kont administracyjnych i usług w programie SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Przegląd kont usług

W domenie zarządzanej usługi Azure AD DS Grupa zabezpieczeń o nazwie **konta usługi AAD DC** istnieje jako część jednostki organizacyjnej *Użytkownicy* (OU). Członkowie tej grupy zabezpieczeń mają delegowane następujące uprawnienia:

- Uprawnienie **Replikuj zmiany katalogu** w katalogu głównym DSE.
- Uprawnienie **Replikuj zmiany katalogu** w kontekście nazewnictwa *konfiguracji* (`cn=configuration` kontener).

Grupa zabezpieczeń **konta usługi AAD DC** jest również członkiem wbudowanej grupy **systemu sprzed Windows 2000**.

Po dodaniu do tej grupy zabezpieczeń konto usługi synchronizacji profilu użytkownika programu SharePoint Server ma przyznane wymagane uprawnienia do poprawnego działania.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Włącz obsługę synchronizacji profilu użytkownika programu SharePoint Server

Konto usługi dla programu SharePoint Server musi mieć odpowiednie uprawnienia, aby replikować zmiany w katalogu i umożliwić poprawne działanie synchronizacji profilu użytkownika programu SharePoint Server. Aby zapewnić te uprawnienia, należy dodać konto usługi używane na potrzeby synchronizacji profilu użytkownika programu SharePoint z grupą **kont usługi AAD DC** .

Na maszynie wirtualnej zarządzania AD DS platformy Azure wykonaj następujące czynności:

> [!NOTE]
> Aby edytować członkostwo w grupie w domenie zarządzanej AD DS platformy Azure, musisz zalogować się na konto użytkownika, które jest członkiem grupy *administratorów kontrolera domeny usługi AAD* .

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, które zostały zainstalowane w samouczku, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
1. Aby zarządzać członkostwem w grupie, wybierz **Centrum administracyjne usługi Active Directory** z listy narzędzi administracyjnych.
1. W lewym okienku wybierz domenę zarządzaną platformy Azure AD DS, na przykład *aadds.contoso.com*. Zostanie wyświetlona lista istniejących jednostek organizacyjnych i zasobów.
1. Wybierz jednostkę organizacyjną **Użytkownicy** , a następnie wybierz grupę zabezpieczeń *konta usługi AAD DC* .
1. Wybierz pozycję **elementy członkowskie**, a następnie wybierz pozycję **Dodaj.**
1. Wprowadź nazwę konta usługi programu SharePoint, a następnie wybierz przycisk **OK**. W poniższym przykładzie konto usługi programu SharePoint ma nazwę *SPAdmin*:

    ![Dodaj konto usługi programu SharePoint do grupy zabezpieczeń konta usługi AAD DC](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Przyznawanie uprawnień Active Directory Domain Services dla synchronizacji profilu w programie SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
