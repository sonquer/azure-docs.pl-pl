---
title: Rozwiązywanie alertów sieciowych grup zabezpieczeń na platformie Azure AD DS | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać alerty konfiguracji grup zabezpieczeń sieci dla Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258005"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Znane problemy: Alerty konfiguracji sieci w Azure Active Directory Domain Services

Aby aplikacje i usługi poprawnie komunikują się z Azure Active Directory Domain Services (Azure AD DS), należy otworzyć określone porty sieciowe, aby umożliwić przepływ ruchu. Na platformie Azure można kontrolować przepływ ruchu przy użyciu sieciowych grup zabezpieczeń. Stan kondycji domeny zarządzanej AD DS platformy Azure pokazuje alert, jeśli nie ma wymaganych reguł sieciowej grupy zabezpieczeń.

Ten artykuł ułatwia zrozumienie i rozwiązywanie typowych alertów dotyczących problemów z konfiguracją sieciowych grup zabezpieczeń.

## <a name="alert-aadds104-network-error"></a>AADDS104 alertu: Błąd sieci

### <a name="alert-message"></a>Komunikat alertu

*Firma Microsoft nie może nawiązać połączenia z kontrolerami domeny dla tej domeny zarządzanej. Może się tak zdarzyć, jeśli sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) skonfigurowana w sieci wirtualnej blokuje dostęp do domeny zarządzanej. Kolejną możliwą przyczyną jest to, że istnieje trasa zdefiniowana przez użytkownika, która blokuje ruch przychodzący z Internetu.*

Nieprawidłowe reguły sieciowej grupy zabezpieczeń są Najczęstszymi przyczynami błędów sieci w usłudze Azure AD DS. Grupa zabezpieczeń sieci dla sieci wirtualnej musi zezwalać na dostęp do określonych portów i protokołów. Jeśli te porty są zablokowane, platforma Azure nie może monitorować ani aktualizować domeny zarządzanej. Ma także wpływ na synchronizację między katalogiem usługi Azure AD i domeną zarządzaną platformy Azure AD DS. Upewnij się, że domyślne porty są otwarte, aby uniknąć przerw w działaniu usługi.

## <a name="default-security-rules"></a>Domyślne reguły zabezpieczeń

Następujące domyślne reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego są stosowane do sieciowej grupy zabezpieczeń dla domeny zarządzanej AD DS platformy Azure. Te reguły przechowują AD DS platformy Azure i umożliwiają korzystanie z platformy Azure w celu monitorowania i aktualizowania domeny zarządzanej oraz zarządzania nią. Możesz również mieć dodatkową regułę, która zezwala na ruch przychodzący w przypadku [skonfigurowania bezpiecznego protokołu LDAP][configure-ldaps].

### <a name="inbound-security-rules"></a>Reguły zabezpieczeń dla ruchu przychodzącego

| Priority | Name | Port | Protocol | Obiekt źródłowy | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Any | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 65000    | AllVnetInBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Any | Allow |
| 65500    | DenyAllInBound | Any | Any | Any | Any | Zablokuj |

### <a name="outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego

| Priority | Name | Port | Protocol | Obiekt źródłowy | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Any | Any |  Any | Internet | Allow |
| 65500    | DenyAllOutBound | Any | Any | Any | Any | Zablokuj |

>[!NOTE]
> Usługa Azure AD DS wymaga nieograniczonego dostępu wychodzącego z sieci wirtualnej. Nie zalecamy tworzenia żadnych dodatkowych reguł, które ograniczają dostęp wychodzący do sieci wirtualnej.

## <a name="verify-and-edit-existing-security-rules"></a>Weryfikowanie i edytowanie istniejących reguł zabezpieczeń

Aby sprawdzić istniejące reguły zabezpieczeń i upewnić się, że porty domyślne są otwarte, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **sieciowe grupy zabezpieczeń**.
1. Wybierz grupę zabezpieczeń sieci skojarzoną z domeną zarządzaną, taką jak *AADDS-contoso.com-sieciowej grupy zabezpieczeń*.
1. Na stronie **Przegląd** są wyświetlane istniejące reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego.

    Przejrzyj reguły ruchu przychodzącego i wychodzącego i porównaj je z listą wymaganych reguł w poprzedniej sekcji. W razie potrzeby wybierz, a następnie usuń wszystkie reguły niestandardowe, które blokują wymagany ruch. Jeśli brakuje dowolnej z wymaganych reguł, Dodaj regułę w następnej sekcji.

    Po dodaniu lub usunięciu reguł w celu zezwolenia na wymagany ruch, kondycja domeny zarządzanej przez usługę Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

### <a name="add-a-security-rule"></a>Dodawanie reguły zabezpieczeń

Aby dodać brakującą regułę zabezpieczeń, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **sieciowe grupy zabezpieczeń**.
1. Wybierz grupę zabezpieczeń sieci skojarzoną z domeną zarządzaną, taką jak *AADDS-contoso.com-sieciowej grupy zabezpieczeń*.
1. W obszarze **Ustawienia** w panelu po lewej stronie kliknij pozycję *reguły zabezpieczeń ruchu przychodzącego* lub *wychodzące reguły zabezpieczeń* w zależności od reguły, którą chcesz dodać.
1. Wybierz pozycję **Dodaj**, a następnie utwórz wymaganą regułę opartą na porcie, protokole, kierunku itd. Gdy wszystko będzie gotowe, wybierz **przycisk OK**.

Aby można było dodać regułę zabezpieczeń i wyświetlić ją na liście, potrwa kilka minut.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
