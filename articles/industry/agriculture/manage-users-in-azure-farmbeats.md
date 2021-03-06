---
title: Zarządzanie użytkownikami w usłudze Azure FarmBeats
description: W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 208a302a0702a5c4de4d194c9e72f562aaf758c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482298"
---
# <a name="manage-users"></a>Zarządzanie użytkownikami

Usługa Azure FarmBeats obejmuje zarządzanie użytkownikami dla osób, które są częścią wystąpienia usługi Azure Active Directory (Azure AD). Możesz dodać użytkowników do wystąpienia usługi Azure FarmBeats, aby uzyskać dostęp do interfejsów API, wyświetlić wygenerowane mapy oraz dane telemetryczne czujnika dostępu z farmy.

## <a name="prerequisites"></a>Wymagania wstępne

- Wymagana jest instalacja usługi Azure FarmBeats. Aby uzyskać więcej informacji, zobacz [Instalowanie usługi Azure FarmBeats](install-azure-farmbeats.md).
- Identyfikatory poczty e-mail użytkowników, których chcesz dodać lub usunąć z wystąpienia usługi Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Zarządzanie użytkownikami usługi Azure FarmBeats

Usługa Azure FarmBeats używa usługi Azure AD do uwierzytelniania, kontroli dostępu i ról. Użytkowników w dzierżawie usługi Azure AD możesz dodać jako użytkowników w usłudze Azure FarmBeats.

> [!NOTE]
> Jeśli użytkownik, który próbujesz dodać jako użytkownika platformy Azure FarmBeats, nie znajduje się w dzierżawie usługi Azure AD, wykonaj czynności opisane w sekcji "Dodawanie użytkowników usługi Azure AD".

Usługa Azure FarmBeats obsługuje dwa typy ról użytkownika:

 - **Administrator**: pełny dostęp do interfejsów API usługi Azure FarmBeats Datahub. Użytkownicy w tej roli mogą wysyłać zapytania do wszystkich obiektów usługi Azure FarmBeats Datahub i wykonywać wszystkie operacje z akceleratora FarmBeats.
 - **Tylko do odczytu**: dostęp tylko do odczytu do interfejsów API FarmBeats Datahub. Użytkownicy mogą wyświetlać interfejsy API Datahub, pulpity nawigacyjne akceleratora i mapy. Użytkownicy z dostępem tylko do odczytu nie mogą wykonywać operacji, takich jak generowanie map, kojarzenie urządzeń lub tworzenie Farm.

## <a name="add-users-to-azure-farmbeats"></a>Dodawanie użytkowników do usługi Azure FarmBeats

Aby dodać użytkowników do usługi Azure FarmBeats:

1. Zaloguj się do akceleratora, a następnie wybierz ikonę **Ustawienia** .
2. Wybierz **Access Control**.

    ![Okienko ustawień farmy](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Wprowadź identyfikator poczty e-mail użytkownika, do którego chcesz udzielić dostępu.
4. Wybierz żądaną rolę, **administratora** lub **tylko do odczytu**.
5. Wybierz pozycję **Dodaj rolę**.

Dodany użytkownik może teraz uzyskiwać dostęp do usługi Azure FarmBeats (zarówno Datahub, jak i Akcelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Usuwanie użytkowników z usługi Azure FarmBeats

Aby usunąć użytkowników z systemu Azure FarmBeats:

1. Zaloguj się do akceleratora, a następnie wybierz ikonę **Ustawienia** .
2. Wybierz **Access Control**.
3. Wybierz pozycję **Usuń**.

   Użytkownik zostanie usunięty z systemu. Zostanie wyświetlony następujący komunikat potwierdzający:

   ![Komunikat z potwierdzeniem usługi Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Dodawanie użytkowników usługi Azure AD

> [!NOTE]
> Aby można było przypisywać aplikacje i role, użytkownicy usługi Azure FarmBeats muszą znajdować się w dzierżawie usługi Azure AD. Jeśli użytkownik, który ma zostać dodany do usługi Azure FarmBeats, nie istnieje już w dzierżawie usługi Azure AD, postępuj zgodnie z instrukcjami w tej sekcji. Jeśli użytkownik istnieje w dzierżawie usługi Azure AD, możesz pominąć te instrukcje.

Aby dodać użytkowników do usługi Azure AD, wykonaj następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. W prawym górnym rogu wybierz swoje konto, a następnie przejdź do dzierżawy usługi Azure AD, która jest skojarzona z FarmBeats.
3. Wybierz **Azure Active Directory** > **użytkowników**.

    Zostanie wyświetlona lista użytkowników usługi Azure AD.

4. Aby dodać użytkownika do katalogu, wybierz pozycję **nowy użytkownik**. Aby dodać użytkownika zewnętrznego, wybierz pozycję **nowy użytkownik-Gość**.

    ![Okienko "Wszyscy użytkownicy"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Wybierz nazwę nowego użytkownika, a następnie wypełnij pola wymagane dla tego użytkownika.
6. Wybierz pozycję **Utwórz**.

Aby uzyskać informacje na temat zarządzania użytkownikami usługi Azure AD, zobacz [Dodawanie lub usuwanie użytkowników w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Następne kroki

Pomyślnie dodano użytkowników do wystąpienia usługi Azure FarmBeats. Teraz Dowiedz się, jak [tworzyć farmy i zarządzać nimi](manage-farms-in-azure-farmbeats.md#create-farms).
