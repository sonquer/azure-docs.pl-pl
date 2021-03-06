---
title: Azure AD Connect nową konfigurację agenta aprowizacji w chmurze
description: W tym artykule opisano sposób instalowania aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853950cf441007eac0170f32f28f2d9c16a71292
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549435"
---
# <a name="azure-ad-connect-cloud-provisioning-new-agent-configuration"></a>Azure AD Connect nową konfigurację agenta aprowizacji w chmurze

Po zainstalowaniu agenta należy zalogować się do Azure Portal i skonfigurować usługę Azure Active Directory (Azure AD) łączenie się z obsługą chmury. Wykonaj następujące kroki, aby włączyć agenta.

## <a name="configure-provisioning"></a>Konfigurowanie aprowizacji
Aby skonfigurować Inicjowanie obsługi, wykonaj następujące kroki.

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **Azure AD Connect**.
1.  Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** .

    ![Zarządzanie obsługą administracyjną (wersja zapoznawcza)](media/how-to-configure/manage1.png)

1.  Wybierz pozycję **Nowa konfiguracja**.
1.  Na ekranie Konfiguracja jest wstępnie wypełniona domena lokalna.
1.  Wprowadź **wiadomość e-mail z powiadomieniem**. Ta wiadomość e-mail zostanie powiadomiona, gdy obsługa administracyjna nie jest w dobrej kondycji.
1.  Przenieś selektor, aby go **włączyć**, a następnie wybierz pozycję **Zapisz**.

    ![Inicjowanie obsługi usługi Azure AD (wersja zapoznawcza)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Inicjowanie obsługi zakresu dla określonych użytkowników i grup
Można ograniczyć agenta do synchronizowania określonych użytkowników i grup za pomocą lokalnych grup Active Directory lub jednostek organizacyjnych. W ramach konfiguracji nie można konfigurować grup i jednostek organizacyjnych. 

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **Azure AD Connect**.
1.  Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** .
1.  W obszarze **Konfiguracja**wybierz konfigurację.

    ![Sekcja konfiguracji](media/how-to-configure/scope1.png)

1.  W obszarze **Konfiguracja**wybierz pozycję **Wszyscy użytkownicy** , aby zmienić zakres reguły konfiguracji.

    ![Opcja wszyscy użytkownicy](media/how-to-configure/scope2.png)

1. Po prawej stronie można zmienić zakres, aby uwzględnić tylko grupy zabezpieczeń. Wprowadź nazwę wyróżniającą grupy, a następnie wybierz pozycję **Dodaj**.

    ![Wybrane opcje grup zabezpieczeń](media/how-to-configure/scope3.png)

1.  Lub można zmienić zakres, aby uwzględnić tylko określone jednostki organizacyjne. Wybierz pozycję **gotowe** i **Zapisz**.

    ![Opcja wybranej jednostki organizacyjnej](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Ponowne uruchamianie aprowizacji 
Jeśli nie chcesz czekać na następne zaplanowane uruchomienie, wyzwól uruchomienie aprowizacji za pomocą przycisku **ponowne Inicjowanie obsługi administracyjnej** . 
1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **Azure AD Connect**.
1.  Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** .
1.  W obszarze **Konfiguracja**wybierz konfigurację.

    ![Wybór konfiguracji w celu ponownego uruchomienia aprowizacji](media/how-to-configure/scope1.png)

1.  W górnej części wybierz pozycję **Uruchom ponownie Inicjowanie obsługi**.

## <a name="remove-a-configuration"></a>Usuń konfigurację
Aby usunąć konfigurację, wykonaj następujące kroki.

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **Azure AD Connect**.
1.  Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** .
1.  W obszarze **Konfiguracja**wybierz konfigurację.

    ![Wybór konfiguracji w celu usunięcia konfiguracji](media/how-to-configure/scope1.png)

1.  W górnej części ekranu konfiguracja wybierz pozycję **Usuń**.

    ![Przycisk Usuń](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Nie ma potwierdzenia przed usunięciem konfiguracji. Upewnij się, że jest to akcja, którą chcesz wykonać przed wybraniem opcji **Usuń**.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
