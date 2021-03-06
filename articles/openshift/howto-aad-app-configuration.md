---
title: Azure Active Directory integrację z usługą Azure Red Hat OpenShift
description: Dowiedz się, jak utworzyć grupę zabezpieczeń i użytkownika usługi Azure AD na potrzeby testowania aplikacji na Microsoft Azure klastrze Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 6e2437fadb743706d4f4215bbcbab8616817de5f
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545623"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory integrację z usługą Azure Red Hat OpenShift

Jeśli nie utworzono jeszcze dzierżawy usługi Azure Active Directory (Azure AD), postępuj zgodnie z instrukcjami w temacie [Tworzenie dzierżawy usługi Azure AD dla systemu Azure Red Hat OpenShift](howto-create-tenant.md) przed kontynuowaniem tych instrukcji.

Microsoft Azure Red Hat OpenShift potrzebuje uprawnień do wykonywania zadań w imieniu klastra. Jeśli Twoja organizacja nie ma jeszcze użytkownika usługi Azure AD, grupy zabezpieczeń usługi Azure AD lub rejestracji aplikacji usługi Azure AD, która ma być używana jako nazwa główna usługi, wykonaj te instrukcje, aby je utworzyć.

## <a name="create-a-new-azure-active-directory-user"></a>Tworzenie nowego użytkownika usługi Azure Active Directory

W [Azure Portal](https://portal.azure.com)upewnij się, że dzierżawa jest wyświetlana pod nazwą użytkownika w prawym górnym rogu portalu:

![zrzut ekranu portalu z dzierżawcą wymieniony w prawym górnym rogu](./media/howto-create-tenant/tenant-callout.png) Jeśli zostanie wyświetlona niewłaściwa dzierżawa, kliknij nazwę użytkownika w prawym górnym rogu, a następnie kliknij pozycję **Przełącz katalog**i wybierz poprawną dzierżawę z listy **wszystkie katalogi** .

Utwórz nowego użytkownika Azure Active Directory administratora globalnego, aby zalogować się do klastra Red Hat OpenShift platformy Azure.

1. Przejdź do bloku [Użytkownicy — wszyscy użytkownicy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) .
2. Kliknij pozycję **+ nowy użytkownik** , aby otworzyć okienko **użytkownika** .
3. Wprowadź **nazwę** dla tego użytkownika.
4. Utwórz **nazwę użytkownika** na podstawie nazwy utworzonej dzierżawy, a `.onmicrosoft.com` dołączona na końcu. Na przykład `yourUserName@yourTenantName.onmicrosoft.com`. Zapisz tę nazwę użytkownika. Będzie on potrzebny do zalogowania się do klastra.
5. Kliknij pozycję **rola katalogu** , aby otworzyć okienko rola katalogu, a następnie wybierz pozycję **administrator globalny** , a następnie kliknij przycisk **OK** w dolnej części okienka.
6. W okienku **użytkownika** kliknij pozycję **Pokaż hasło** i Zarejestruj hasło tymczasowe. Po pierwszym zalogowaniu zostanie wyświetlony monit o zresetowanie go.
7. W dolnej części okienka kliknij pozycję **Utwórz** , aby utworzyć użytkownika.

## <a name="create-an-azure-ad-security-group"></a>Tworzenie grupy zabezpieczeń usługi Azure AD

Aby udzielić dostępu administratora klastra, członkostwa w grupie zabezpieczeń usługi Azure AD są synchronizowane z grupą OpenShift "OSA-Customer-admins". Jeśli nie zostanie określony, nie zostanie udzielony żaden dostęp administratora klastra.

1. Otwórz blok [grupy Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) .
2. Kliknij pozycję **+ Nowa grupa**.
3. Podaj nazwę i opis grupy.
4. Ustaw **Typ grupy** na **zabezpieczenia**.
5. Ustaw **Typ członkostwa** na **przypisane**.

    Dodaj użytkownika usługi Azure AD, który został utworzony we wcześniejszym kroku, do tej grupy zabezpieczeń.

6. Kliknij pozycję **elementy członkowskie** , aby otworzyć okienko **Wybierz członków** .
7. Z listy członków wybierz utworzonego powyżej użytkownika usługi Azure AD.
8. W dolnej części portalu kliknij pozycję **Wybierz** , a następnie **Utwórz** , aby utworzyć grupę zabezpieczeń.

    Zapisz wartość identyfikatora grupy.

9. Po utworzeniu grupy zobaczysz ją na liście wszystkich grup. Kliknij nową grupę.
10. Na wyświetlonej stronie Skopiuj **Identyfikator obiektu**. Ta wartość zostanie odwołująca się do `GROUPID` w samouczku [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

> [!IMPORTANT]
> Aby zsynchronizować tę grupę z grupą OSA-OpenShift-admins, Utwórz klaster przy użyciu interfejsu wiersza polecenia platformy Azure. Azure Portal aktualnie nie ma pola, aby ustawić tę grupę.

## <a name="create-an-azure-ad-app-registration"></a>Tworzenie rejestracji aplikacji usługi Azure AD

Można automatycznie utworzyć klienta rejestracji aplikacji Azure Active Directory (Azure AD) w ramach tworzenia klastra, pomijając flagę `--aad-client-app-id` do polecenia `az openshift create`. W tym samouczku pokazano, jak utworzyć rejestrację aplikacji usługi Azure AD pod kątem kompletności.

Jeśli Twoja organizacja nie ma jeszcze rejestracji aplikacji Azure Active Directory (Azure AD) do użycia jako jednostki usługi, postępuj zgodnie z poniższymi instrukcjami, aby ją utworzyć.

1. Otwórz [blok rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) i kliknij pozycję **+ Nowa rejestracja**.
2. W okienku **zarejestruj aplikację** wprowadź nazwę rejestracji aplikacji.
3. Upewnij się, że w obszarze **obsługiwane typy kont** są zaznaczone **tylko konta w tym katalogu organizacyjnym** . Jest to najbardziej bezpieczny wybór.
4. Po poznaniu identyfikatora URI klastra zostanie dodany identyfikator URI przekierowania. Kliknij przycisk **zarejestruj** , aby utworzyć rejestrację aplikacji usługi Azure AD.
5. Na wyświetlonej stronie Skopiuj **Identyfikator aplikacji (klienta)** . Ta wartość zostanie odwołująca się do `APPID` w samouczku [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Zrzut ekranu przedstawiający stronę obiektu aplikacji](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Wygeneruj klucz tajny klienta na potrzeby uwierzytelniania aplikacji w Azure Active Directory.

1. W sekcji **Zarządzanie** na stronie rejestracje aplikacji kliknij pozycję **Certyfikaty & wpisy tajne**.
2. W okienku **certyfikaty & wpisy tajne** kliknij pozycję **+ nowy klucz tajny klienta**.  Zostanie wyświetlone okienko **Dodaj wpis tajny klienta** .
3. Podaj **Opis**.
4. Ustawienie **wygasa** na preferowany czas, na przykład przez **2 lata**.
5. Kliknij przycisk **Dodaj** . wartość klucza zostanie wyświetlona w sekcji wpisy **tajne klienta** strony.
6. Skopiuj wartość klucza. Ta wartość zostanie odwołująca się do `SECRET` w samouczku [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Zrzut ekranu przedstawiający okienko certyfikaty i wpisy tajne](./media/howto-create-tenant/create-key.png)

Aby uzyskać więcej informacji na temat obiektów aplikacji platformy Azure, zobacz temat [obiekty główne aplikacji i usługi w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Aby uzyskać szczegółowe informacje na temat tworzenia nowej aplikacji usługi Azure AD, zobacz [Rejestrowanie aplikacji za pomocą punktu końcowego Azure Active Directory v 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Dodawanie uprawnień interfejsu API

1. W sekcji **Zarządzanie** kliknij pozycję **uprawnienia interfejsu API**.
2. Kliknij pozycję **Dodaj uprawnienie** , a następnie wybierz pozycję **Azure Active Directory Graph** następnie **uprawnienia delegowane**. 
3. Rozwiń pozycję **użytkownik** na poniższej liście i Włącz uprawnienie **User. Read** . Jeśli funkcja **User. Read** jest włączona domyślnie, upewnij się, że jest to element uprawnień **Azure Active Directory Graph** **. odczyt**, *nie* **Microsoft Graph** uprawnień **użytkownika. odczyt**.
4. Przewiń w górę i wybierz pozycję **uprawnienia aplikacji**.
5. Rozwiń **katalog** znajdujący się na poniższej liście i Włącz **katalog.**
6. Kliknij przycisk **Dodaj uprawnienia** , aby zaakceptować zmiany.
7. Panel uprawnień interfejsu API powinien teraz wyświetlać zarówno *użytkownika. Read* i *Directory. readal*. Zwróć uwagę na ostrzeżenie w kolumnie **wymagana zgoda administratora** obok *katalogu Directory. readal*.
8. Jeśli jesteś *administratorem subskrypcji platformy Azure*, kliknij przycisk **Udziel zgody administratora na *nazwę subskrypcji***  poniżej. Jeśli nie jesteś *administratorem subskrypcji platformy Azure*, zażądaj zgody od administratora.
![zrzut ekranu przedstawiający panel uprawnień interfejsu API. Uprawnienia User. Read i Directory. readal dodane, zgoda administratora wymagana dla katalogu](./media/howto-aad-app-configuration/permissions-required.png).

> [!IMPORTANT]
> Synchronizacja grupy Administratorzy klastra będzie działała dopiero po udzieleniu zgody. Zobaczysz zielony okrąg z zaznaczeniem i komunikatem "udzielono dla *nazwy subskrypcji*" w kolumnie *wymagana zgoda administratora* .

Aby uzyskać szczegółowe informacje na temat zarządzania administratorami i innymi rolami, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Zasoby

* [Aplikacje i obiekty główne usługi w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Szybki Start: rejestrowanie aplikacji za pomocą punktu końcowego Azure Active Directory v 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Następne kroki

Jeśli spełniono wszystkie [wymagania wstępne dotyczące usługi Azure Red Hat OpenShift](howto-setup-environment.md), możesz utworzyć pierwszy klaster.

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
