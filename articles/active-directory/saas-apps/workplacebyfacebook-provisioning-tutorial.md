---
title: 'Samouczek: Konfigurowanie miejsca pracy przez serwis Facebook w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7d8a7881c00427023e5f174461b3d8b24d83444
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121449"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie miejsca pracy przez serwis Facebook w celu automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w obu miejscach pracy przez serwis Facebook i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w [miejscu pracy za](https://work.workplace.com/) pomocą usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrowanie do nowego miejsca pracy przez aplikację w serwisie Facebook
Jeśli masz istniejącą integrację z miejscem pracy w serwisie Facebook, zapoznaj się z poniższą sekcją o zmianach. Jeśli konfigurujesz miejsce pracy przez serwis Facebook po raz pierwszy, możesz pominąć tę sekcję i przejść do obszaru obsługiwane możliwości. 

#### <a name="whats-changing"></a>Co się zmieni?
* Zmiany po stronie usługi Azure AD: Metoda autoryzacji do aprowizacji użytkowników w miejscu pracy ma historycznie długi token tajny. Wkrótce zostanie wyświetlona zmiana metody autoryzacji na przyznanie autoryzacji OAuth. 
* Zmiany po stronie miejsca pracy: wcześniej aplikacja usługi Azure AD była integracją niestandardową w miejscu pracy przez serwis Facebook. Teraz zostanie wyświetlona usługa Azure AD w katalogu integracji miejsca pracy jako aplikacja innej firmy. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Co należy zrobić, aby przeprowadzić migrację istniejącej integracji niestandardowej do nowej aplikacji?
Jeśli masz istniejącą integrację z miejscem pracy z prawidłowym tokenem, **nie jest wymagana żadna akcja**. W każdym tygodniu automatycznie Migrujemy klientów do nowej aplikacji. Jest to realizowane całkowicie w tle. Jeśli nie możesz zaczekać i chcieć przenieść do nowej aplikacji ręcznie, możesz dodać nowe wystąpienie miejsca pracy z galerii i ponownie skonfigurować aprowizacji. Wszystkie nowe wystąpienia miejsca pracy będą automatycznie korzystać z nowej wersji aplikacji. 

 
Jeśli integracja z miejscem pracy odbywa się w kwarantannie, należy ponownie podać prawidłowy token, aby przeprowadzić migrację użytkownika. Sekcja poświadczenia administratora będzie wyszarzona, ale można dołączyć następujące elementy ( **? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride = true**) do adresu URL, aby ponownie zapisać poświadczenia. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Jak sprawdzić, czy moja aplikacja została zmigrowana? 
Po przeprowadzeniu migracji aplikacji transparent w sekcji autoryzacji dotyczącej zmian do przeniesień zostanie usunięty, a pole token tajne zostanie zastąpione przyciskiem "niebieska autoryzacja". 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>Sekcja poświadczeń administratora jest wyszarzona w mojej aplikacji i nie mogę jej zapisać. Dlaczego?
Sekcja poświadczeń administratora została zablokowana dla istniejących klientów w miejscu pracy. Po przeprowadzeniu migracji dzierżawy do nowej aplikacji w miejscu pracy można ponownie zaktualizować sekcję poświadczenia administratora. Jeśli nie możesz czekać, możesz użyć powyższego adresu URL, aby edytować aplikację. 

 
#### <a name="when-will-these-changes-happen"></a>Kiedy te zmiany zostaną wykonane?
Wszystkie nowe wystąpienia miejsca pracy będą już używać nowej metody integracji/autoryzacji. Istniejące integracje zostaną stopniowo migrowane w lutym. Migracja zostanie zakończona dla wszystkich dzierżawców na koniec miesiąca. 

## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w miejscu pracy przez serwis Facebook
> * Usuwanie użytkowników w miejscu pracy przez serwis Facebook, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkownika między usługą Azure AD i miejscem pracy przez serwis Facebook
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) jednokrotne do miejsca pracy przez serwis Facebook (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego)
* Miejsce pracy w ramach subskrypcji z włączoną obsługą logowania jednokrotnego w serwisie Facebook

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ dane, które mają być [mapowane między usługą Azure AD i miejscem pracy przez serwis Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie miejsca pracy przez serwis Facebook w celu obsługi aprowizacji za pomocą usługi Azure AD

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do miejsca pracy przez aplikację w serwisie Facebook. Po ustaleniu tych użytkowników możesz przypisać je do miejsca pracy przez aplikację Facebook, postępując zgodnie z poniższymi instrukcjami:

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do miejsca pracy przez serwis Facebook w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

*   Podczas przypisywania użytkownika do miejsca pracy przez serwis Facebook należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie miejsca pracy przez serwis Facebook z galerii aplikacji usługi Azure AD

Dodaj miejsce pracy przez serwis Facebook z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą w miejscu pracy przez serwis Facebook. Jeśli wcześniej skonfigurowano miejsce pracy w usłudze Facebook dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do miejsca pracy przez serwis Facebook należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **miejsce pracy w serwisie Facebook**.

    ![Link aplikacji Workplace by Facebook na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** kliknij pozycję **Autoryzuj**. Nastąpi przekierowanie do miejsca pracy przy użyciu strony autoryzacji w serwisie Facebook. Wprowadź miejsce pracy według nazwy użytkownika w usłudze Facebook i kliknij przycisk **Kontynuuj** . Kliknij pozycję **Testuj połączenie** , aby zapewnić, że usługa Azure AD może połączyć się z miejscem pracy przez serwis Facebook. Jeśli połączenie nie powiedzie się, upewnij się, że miejsce pracy przez konto w serwisie Facebook ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![aprowizacji](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autoryzować](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników w miejscu pracy przez serwis Facebook**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do miejsca pracy przez serwis Facebook, w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w miejscu pracy przez serwis Facebook dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że w miejscu pracy przez interfejs API usługi Facebook jest obsługiwany Filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |displayName|Ciąg|
   |aktywne|Wartość logiczna|
   |title|Wartość logiczna|
   |wiadomości e-mail [typ eq "Praca"] .value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |adresy [typ EQ "Work"]. sformatowane|Ciąg|
   |.streetAddress adresy [typ eq "Praca"]|Ciąg|
   |adresy [typ EQ "Work"]. locale|Ciąg|
   |addresss [Type EQ "Work"]. region|Ciąg|
   |addresss [Type EQ "Work"]. Country|Ciąg|
   |.postalCode adresy [typ eq "Praca"]|Ciąg|
   |adresy [Type EQ "Other"]. sformatowane|Ciąg|
   |wartość phoneNumbers [typ eq "Praca"]|Ciąg|
   |wartość phoneNumbers [eq wpisz "wyraz mobile"]|Ciąg|
   |wartość phoneNumbers [typ eq "faksu"]|Ciąg|
   |externalId|Ciąg|
   |preferredLanguage|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|

10. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning w miejscu pracy przez serwis Facebook, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić w miejscu pracy przez serwis Facebook, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
*  Jeśli zobaczysz, że użytkownik zostanie niepomyślnie utworzony i istnieje zdarzenie dziennika inspekcji z kodem "1789003", oznacza to, że użytkownik pochodzi z niezweryfikowanej domeny.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
