---
title: Dodaj użytkownika jako twórcę laboratorium w Azure Lab Services
description: W tym artykule pokazano, jak dodać użytkownika do roli twórca laboratorium dla konta laboratorium w Azure Lab Services. Twórcy laboratorium mogą tworzyć laboratoria w ramach tego konta laboratorium.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444773"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Dodaj twórców laboratorium do konta laboratorium w Azure Lab Services
W tym artykule opisano sposób dodawania użytkowników jako twórców laboratorium do konta laboratorium w Azure Lab Services. Te zastosowania mogą następnie tworzyć laboratoria klas na koncie laboratorium. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Dodaj konto użytkownika Microsoft do roli twórcy laboratorium
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika w celu utworzenia laboratorium na potrzeby zajęć, możesz pominąć ten krok. Aby użyć innego konta użytkownika do utworzenia laboratorium na potrzeby zajęć, wykonaj następujące czynności: 

Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli **Twórca laboratorium**:

1. Na stronie **Konto laboratorium** wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** i kliknij pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Kontrola dostępu -> przycisk Dodaj przypisanie roli](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stronie **Dodawanie przypisania roli** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie twórcy laboratorium](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Jeśli dodajesz użytkownika innego niż konto Microsoft jako twórcę laboratorium, zobacz sekcję [Dodawanie użytkownika niekonto Microsoftowego jako kreatora laboratorium](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Dodawanie użytkownika innego niż konto Microsoft jako twórca laboratorium
Aby dodać użytkownika jako twórcę laboratorium, należy użyć swoich kont e-mail. Mogą być używane następujące typy kont e-mail:

- Konto e-mail, które jest dostarczane przez Azure Active Directory Office 365 (AAD). 
- Konto e-mail firmy Microsoft, takie jak `@outlook.com`, `@hotmail.com`, `@msn.com`lub `@live.com`.
- Konto e-mail inne niż firmy Microsoft, na przykład udostępniane przez firmę Yahoo lub Google. Jednak te typy kont muszą być połączone z konto Microsoft.
- Konto usługi GitHub. To konto musi być połączone z konto Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Korzystanie z konta e-mail innego niż Microsoft
Twórcy laboratorium/instruktorzy mogą korzystać z kont e-mail innych niż firmy Microsoft w celu zarejestrowania się w laboratorium zajęć i zalogowania się do niego.  Jednak zalogowanie się w portalu usług Lab Services wymaga, aby instruktorzy utworzyli konto Microsoft, które są połączone ze swoim adresem e-mail firmy innej niż Microsoft.

Wiele instruktorów może już mieć konto Microsoft połączony ze swoimi adresami e-mail spoza firmy Microsoft. Na przykład Instruktorzy mają już konto Microsoft, jeśli użyły swojego adresu e-mail od innych produktów lub usług firmy Microsoft, takich jak Office, Skype, OneDrive lub Windows.  

Gdy instruktorzy logują się do portalu usług Lab Services, są monitowani o podanie adresu e-mail i hasła. Jeśli instruktor podejmie próbę zalogowania się przy użyciu niekonto Microsoft, który nie ma powiązanego konto Microsoft, zostanie wyświetlony następujący komunikat o błędzie: 

![Komunikat o błędzie](../media/how-to-configure-student-usage/cant-find-account.png)

Aby zarejestrować się w celu konto Microsoft, Instruktorzy powinni przejść do [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Korzystanie z konta usługi GitHub
Instruktorzy mogą także użyć istniejącego konta usługi GitHub do zarejestrowania się i zalogować się do laboratorium z klasą. Jeśli instruktor ma już konto Microsoft połączony z kontem usługi GitHub, może się zalogować i podać hasło, jak pokazano w poprzedniej sekcji. Jeśli konto usługi GitHub nie zostało jeszcze połączone z konto Microsoft, należy wybrać **Opcje logowania**:

![Link opcji logowania](../media/how-to-configure-student-usage/signin-options.png)

Na stronie **Opcje logowania** wybierz pozycję **Zaloguj się za pomocą usługi GitHub**.

![Zaloguj się przy użyciu linku GitHub](../media/how-to-configure-student-usage/signin-github.png)

Na koniec są wyświetlane monity o utworzenie konto Microsoft połączonego z kontem usługi GitHub. Odbywa się to automatycznie po wybraniu **przycisku Dalej**przez instruktora.  Instruktor jest następnie natychmiast zalogowany i połączony z laboratorium klasy.


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
