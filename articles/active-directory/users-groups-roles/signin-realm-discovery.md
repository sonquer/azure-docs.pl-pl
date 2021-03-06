---
title: Wyszukiwanie nazwy użytkownika podczas logowania — Azure Active Directory | Microsoft Docs
description: Jak komunikaty na ekranie odzwierciedlają wyszukiwanie nazw użytkowników podczas logowania w Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024250"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Odnajdywanie obszaru macierzystego dla stron logowania Azure Active Directory

Zmieniamy zachowanie logowania usługi Azure Active Directory (Azure AD), aby zrobić miejsce na nowe metody uwierzytelniania i poprawić użyteczność. Podczas logowania usługa Azure AD określa, gdzie użytkownik musi się uwierzytelnić. Usługa Azure AD podejmuje inteligentne decyzje, odczytując ustawienia użytkownika i organizacji dla nazwy użytkownika wprowadzonej na stronie logowania. Jest to krok w kierunku przyszłości bez haseł, w której będzie można używać dodatkowych poświadczeń, takich jak FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Zachowanie odnajdywania obszaru głównego

W przeszłości odnajdywanie obszaru macierzystego było regulowane przez domenę dostarczoną podczas logowania lub przez zasady odnajdywania obszaru macierzystego dla niektórych starszych aplikacji. Na przykład w naszym zachowaniu odnajdywania Azure Active Directory użytkownik może wpisać swoją nazwę użytkownika, ale nadal dotrze do ekranu zbierania poświadczeń w organizacji. Dzieje się tak, gdy użytkownik prawidłowo poda nazwę domeny organizacji "contoso.com". Takie zachowanie nie pozwala na szczegółowe dostosowanie środowiska dla poszczególnych użytkowników.

Aby zapewnić obsługę szerszego zakresu poświadczeń i zwiększyć użyteczność, Azure Active Directory zachowanie wyszukiwania nazwy użytkownika w trakcie procesu logowania zostanie zaktualizowane. Nowe zachowanie podejmuje inteligentne decyzje poprzez odczytywanie ustawień dzierżawy i poziomu użytkownika na podstawie nazwy użytkownika wprowadzonej na stronie logowania. Aby to umożliwić, Azure Active Directory sprawdzi, czy nazwa użytkownika wprowadzona na stronie logowania istnieje w podanej domenie lub przekierowuje użytkownika w celu podania poświadczeń.

Dodatkową zaletą tej pracy jest ulepszona obsługa komunikatów o błędach. Poniżej przedstawiono kilka przykładów ulepszonych komunikatów o błędach podczas logowania do aplikacji, która obsługuje tylko Azure Active Directory użytkowników.

- Nazwa użytkownika jest nieprawidłowa lub nazwa użytkownika nie została jeszcze zsynchronizowana z usługą Azure AD:
  
    ![Nazwa użytkownika jest nieprawidłowa lub nie została znaleziona](./media/signin-realm-discovery/typo-username.png)
  
- Nazwa domeny jest w niewpisanej postaci:
  
    ![Nazwa domeny jest w nieprawidłowym typie lub nie została znaleziona](./media/signin-realm-discovery/typo-domain.png)
  
- Użytkownik próbuje zalogować się przy użyciu znanej domeny konsumenta:
  
    ![Logowanie się ze znaną domeną klienta](./media/signin-realm-discovery/consumer-domain.png)
  
- Hasło jest wpisane niepoprawnie, ale nazwa użytkownika jest dokładna:  
  
    ![hasło jest wpisane z dobrą nazwą użytkownika](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Ta funkcja może mieć wpływ na domeny federacyjne polegające na Starym odnajdowaniu obszaru macierzystego na poziomie domeny w celu wymuszenia Federacji. Aby uzyskać aktualizacje w przypadku dodania obsługi domeny federacyjnej, zobacz [odnajdywanie obszaru macierzystego podczas logowania do Microsoft 365 usług](https://azure.microsoft.com/updates/signin-hrd/). W międzyczasie niektóre organizacje przeszkolone swoich pracowników w celu zalogowania się przy użyciu nazwy użytkownika, która nie istnieje w Azure Active Directory ale zawiera poprawną nazwę domeny, ponieważ nazwy domen kierują obecnie użytkowników do punktu końcowego domeny swojej organizacji. Nowe zachowanie logowania nie zezwala na to. Użytkownik zostanie powiadomiony o poprawce nazwy użytkownika i nie może zalogować się przy użyciu nazwy użytkownika, która nie istnieje w Azure Active Directory.
>
> Jeśli ty lub Twoja organizacja ma praktyki, które są zależne od starego zachowania, ważne jest, aby administratorzy organizacji mogli zaktualizować dokumentację logowania i uwierzytelniania pracownika oraz poszkolić pracowników do korzystania z Azure Active Directory nazwy użytkownika do logowania.
  
Jeśli masz problemy z nowym zachowaniem, w sekcji **Opinie** w tym artykule pozostaw Twoje uwagi.  

## <a name="next-steps"></a>Następne kroki

[Dostosuj znakowanie logowania](../fundamentals/add-custom-domain.md)
