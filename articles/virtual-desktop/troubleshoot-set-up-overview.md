---
title: Omówienie rozwiązywania problemów z pulpitem wirtualnym systemu Windows — Azure
description: Przegląd rozwiązywania problemów podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
ms.openlocfilehash: 730d12625b134f411096b4b49641b79efb1a0d26
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368746"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Rozwiązywania problemów — omówienie, opinie i obsługa techniczna

Ten artykuł zawiera omówienie problemów, które mogą wystąpić podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązania problemów.

## <a name="provide-feedback"></a>Przekaż opinię

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="escalation-tracks"></a>Śledzenie eskalacji

Skorzystaj z poniższej tabeli, aby zidentyfikować i rozwiązać problemy, które mogą wystąpić podczas konfigurowania środowiska dzierżawy przy użyciu Pulpit zdalny klienta. Po skonfigurowaniu dzierżawy możesz użyć naszej nowej [usługi diagnostycznej](diagnostics-role-service.md) , aby zidentyfikować problemy dla typowych scenariuszy.

>[!NOTE]
> Mamy forum społeczności Tech., które można odwiedzić, aby omówić Twoje problemy z zespołem produktu i aktywnymi członkami społeczności. Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby rozpocząć dyskusję.

| **Wykonaj**                                                            | **Sugerowane rozwiązanie**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Tworzenie dzierżawy pulpitu wirtualnego systemu Windows                                                    | Jeśli wystąpi awaria platformy Azure, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/). w przeciwnym razie [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **wdrożenie** dla typu problemu, a następnie wybierz pozycję **problemy z tworzeniem dzierżawy pulpitu wirtualnego systemu Windows** dla tego typu problemu.|
| Uzyskiwanie dostępu do szablonów witryny Marketplace w Azure Portal       | Jeśli wystąpi awaria platformy Azure, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Szablony pulpitów wirtualnych systemu Windows w portalu Azure Marketplace są dostępne bezpłatnie.|
| Uzyskiwanie dostępu do szablonów Azure Resource Manager z usługi GitHub                                  | Zapoznaj się z sekcją [Tworzenie maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) dla [dzierżawy i puli hostów](troubleshoot-set-up-issues.md). Jeśli problem nadal występuje, skontaktuj się z [zespołem pomocy technicznej usługi GitHub](https://github.com/contact). <br> <br> Jeśli błąd wystąpi po uzyskaniu dostępu do szablonu w usłudze GitHub, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/create-ticket/).|
| Ustawienia puli hostów sesji Azure Virtual Network (VNET) i usługi Express Route               | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), a następnie wybierz odpowiednią usługę (w obszarze Kategoria sieci). |
| Tworzenie maszyny wirtualnej puli hostów sesji, gdy Azure Resource Manager szablony udostępniane z pulpitem wirtualnym systemu Windows nie są używane | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), a następnie wybierz **maszynę wirtualną z systemem Windows** dla usługi. <br> <br> Problemy z szablonami Azure Resource Manager dostarczanymi z pulpitem wirtualnym systemu Windows można znaleźć w sekcji Tworzenie dzierżawy pulpitu wirtualnego systemu Windows w ramach [tworzenia dzierżawy i puli hostów](troubleshoot-set-up-issues.md). |
| Zarządzanie środowiskiem hosta sesji usług pulpitu wirtualnego systemu Windows z Azure Portal    | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> W przypadku problemów z zarządzaniem przy użyciu programu PowerShell Usługi pulpitu zdalnego/Windows Virtual Desktop, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) lub [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **Konfiguracja i zarządzanie** dla typu problemu, a następnie wybierz pozycję **problemy Konfigurowanie dzierżawcy przy użyciu programu PowerShell** dla typu problemu. |
| Zarządzanie konfiguracją pulpitu wirtualnego systemu Windows powiązane z pulami hostów i grupami aplikacji (grupami aplikacji)      | Zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)lub [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz odpowiedni typ problemu.|
| Wdrażanie kontenerów profilów FSLogix i zarządzanie nimi | Zobacz [Przewodnik rozwiązywania problemów dotyczących produktów FSLogix](/fslogix/fslogix-trouble-shooting-ht/) i jeśli nie rozwiąże to problemu, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **FSLogix** dla typu problemu, a następnie wybierz odpowiedni podtyp problemu. |
| Klient pulpitu zdalnego działa nieprawidłowo przy uruchomieniu                                                 | Zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md) i jeśli nie rozwiąże problemu, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję **pulpit zdalny klienci** dla typu problemu.  <br> <br> Jeśli jest to problem z siecią, użytkownicy muszą skontaktować się z administratorem sieci. |
| Połączony, ale brak kanału informacyjnego                                                                 | Rozwiązywanie problemów przy użyciu [użytkownika nawiązuje połączenie, ale nic nie jest wyświetlane (brak kanału informacyjnego)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) w obszarze [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md). <br> <br> Jeśli użytkownicy zostali przypisani do grupy aplikacji, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję **pulpit zdalny klienci** dla typu problemu. |
| Problemy z odnajdywaniem źródła danych ze względu na sieć                                            | Użytkownicy muszą skontaktować się z administratorem sieci. |
| Łączenie klientów                                                                    | Zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md) , a jeśli to nie rozwiąże problemu, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md). |
| Czas odpowiedzi aplikacji zdalnych lub pulpitu                                      | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Komunikaty lub błędy licencjonowania                                                          | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Problemy związane z korzystaniem z narzędzi pulpitu wirtualnego systemu Windows w witrynie GitHub (Azure Resource Manager szablonów, narzędzi diagnostycznych, narzędziu do zarządzania) | Aby zgłosić problemy, zobacz [usługi pulpitu zdalnego szablony Azure Resource Manager](https://github.com/Azure/RDS-Templates/blob/master/README.md) . |

## <a name="next-steps"></a>Następne kroki

- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md)
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
