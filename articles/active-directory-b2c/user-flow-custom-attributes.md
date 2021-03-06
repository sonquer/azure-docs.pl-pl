---
title: Definiowanie atrybutów niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Zdefiniuj atrybuty niestandardowe dla aplikacji w Azure Active Directory B2C, aby zbierać informacje o klientach.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7a65eb19c5694f399e806d0f6ce99717436931c6
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484353"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiowanie atrybutów niestandardowych w Azure Active Directory B2C

 Każda aplikacja dołączona do klienta ma unikatowe wymagania dotyczące informacji, które należy zebrać. Dzierżawa Azure Active Directory B2C (Azure AD B2C) zawiera wbudowany zestaw informacji przechowywanych w atrybutach, takich jak imię, nazwisko, miejscowość i kod pocztowy. Za pomocą Azure AD B2C można rozłożyć zestaw atrybutów przechowywanych na poszczególnych kontach klientów.

 Można tworzyć niestandardowe atrybuty w [Azure Portal](https://portal.azure.com/) i używać ich w przepływach użytkownika tworzenia konta, tworzeniu kont lub zalogowaniu lub przepisywaniu przepływów użytkowników. Można również odczytywać i zapisywać te atrybuty przy użyciu [interfejsu API Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutu niestandardowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**.

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wyświetlona dzierżawa B2C w katalogu i filtrze subskrypcji](./media/user-flow-custom-attributes/select-directory.PNG)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **atrybuty użytkownika**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwę** dla atrybutu niestandardowego (na przykład "ShoeSize")
6. Wybierz **Typ danych**. Dostępne są tylko **ciągi**, **wartości logiczne**i **int** .
7. Opcjonalnie wprowadź **Opis** dla celów informacyjnych.
8. Kliknij przycisk **Utwórz**.

Atrybut niestandardowy jest teraz dostępny na liście **atrybutów użytkownika** i do użycia w przepływach użytkownika. Atrybut niestandardowy jest tworzony tylko za pierwszym razem, gdy jest używany w dowolnym przepływie użytkownika, a nie po dodaniu go do listy **atrybutów użytkownika**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Używanie atrybutu niestandardowego w przepływie użytkownika

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Wybierz pozycję **atrybuty użytkownika** , a następnie wybierz atrybut niestandardowy (na przykład "ShoeSize"). Kliknij przycisk **Save** (Zapisz).
1. Wybierz pozycję **oświadczenia aplikacji** , a następnie wybierz atrybut niestandardowy.
1. Kliknij przycisk **Save** (Zapisz).

Po utworzeniu nowego użytkownika przy użyciu przepływu użytkownika, który używa nowo utworzonego atrybutu niestandardowego, obiekt może być badany w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Alternatywnie możesz użyć funkcji [Uruchom przepływ użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) w przepływie użytkownika, aby zweryfikować środowisko klienta. Na liście atrybutów zebranych podczas rejestracji można teraz zobaczyć **ShoeSize** i zobaczyć je w tokenie wysyłanym z powrotem do aplikacji.
