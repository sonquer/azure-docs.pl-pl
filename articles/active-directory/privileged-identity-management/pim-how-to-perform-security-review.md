---
title: Przeglądanie dostępu do ról usługi Azure AD w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak przeglądać dostęp ról Azure Active Directory w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847086"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Przeglądanie dostępu do ról usługi Azure AD w Privileged Identity Management

Privileged Identity Management (PIM) upraszcza zarządzanie dostępem uprzywilejowanym do zasobów w usłudze Azure Active Directory (AD) i innych Usługi onlineach firmy Microsoft, takich jak Office 365 czy Microsoft Intune. Wykonaj kroki opisane w tym artykule, aby pomyślnie przejrzeć przypisane role.

Jeśli masz przypisaną rolę administracyjną, administrator ról uprzywilejowanych w organizacji może zażądać regularnego potwierdzenia, że nadal potrzebujesz tej roli dla danego zadania. Możesz otrzymać wiadomość e-mail zawierającą link lub możesz przejść bezpośrednio do [Azure Portal](https://portal.azure.com) i zacząć.

Jeśli jesteś administratorem roli uprzywilejowanej lub administratorem globalnym, który interesuje przeglądy dostępu, uzyskaj więcej informacji na [temat sposobu uruchamiania przeglądu dostępu](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Dodawanie kafelka pulpitu nawigacyjnego PIM

Jeśli nie masz usługi Azure AD Privileged Identity Management przypiętej do pulpitu nawigacyjnego w Azure Portal, wykonaj następujące kroki, aby rozpocząć pracę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz swoją nazwę użytkownika w prawym górnym rogu Azure Portal i wybierz katalog, w którym będziesz działać.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Zostanie otwarta aplikacja Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Zatwierdź lub Odmów dostępu

Po zatwierdzeniu lub odrzuceniu dostępu użytkownik otrzymuje jedynie informację o tym, czy nadal używasz tej roli, czy nie. Wybierz pozycję **Zatwierdź** , jeśli chcesz pozostać w roli, lub **Odmów** , jeśli nie potrzebujesz już dostępu. Twój stan nie zmieni się od razu, dopóki recenzent nie zastosuje wyników.
Wykonaj następujące kroki, aby znaleźć i ukończyć przegląd dostępu:

1. W usłudze Privileged Identity Management wybierz pozycję **Przejrzyj dostęp uprzywilejowany**. Jeśli masz oczekujące przeglądy dostępu, zostaną one wyświetlone na stronie **przeglądy dostępu do** usługi Azure AD.
2. Wybierz przegląd, który chcesz zakończyć.
3. Jeśli recenzja nie została utworzona, zostanie wyświetlony jako jedyny użytkownik w przeglądzie. Zaznacz znacznik wyboru obok swojej nazwy.
4. Wybierz opcję **Zatwierdź** lub **Odmów**. Może być konieczne dołączenie przyczyny decyzji w polu tekstowym **Podaj przyczynę** .  
5. Zamknij blok **Przegląd ról usługi Azure AD** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki

- [Wykonywanie przeglądu dostępu ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-perform-access-review.md)
