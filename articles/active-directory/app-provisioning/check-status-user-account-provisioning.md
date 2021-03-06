---
title: Zgłoś automatyczne Inicjowanie obsługi kont użytkowników w aplikacjach SaaS
description: Dowiedz się, jak sprawdzić stan automatycznych zadań aprowizacji konta użytkownika i jak rozwiązywać problemy z obsługą poszczególnych użytkowników.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 289347474189d1fb57d95a2f424cf381e1e37875
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522666"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Samouczek: Raportowanie dotyczące automatycznego aprowizacji kont użytkowników

Usługa Azure Active Directory (Azure AD) obejmuje [usługę aprowizacji kont użytkowników](user-provisioning.md) , która pomaga zautomatyzować inicjowanie aprowizacji kont użytkowników w aplikacjach SaaS i innych systemach, na potrzeby kompleksowego zarządzania cyklem życia tożsamości. Usługa Azure AD obsługuje wstępnie zintegrowane łączniki aprowizacji użytkowników dla wszystkich aplikacji i systemów w sekcji "Polecane" w [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

W tym artykule opisano sposób sprawdzania stanu zadań aprowizacji po ich skonfigurowaniu oraz rozwiązywania problemów z inicjowaniem obsługi poszczególnych użytkowników i grup.

## <a name="overview"></a>Omówienie

Łączniki aprowizacji są konfigurowane i konfigurowane przy użyciu [Azure Portal](https://portal.azure.com), zgodnie z [podaną dokumentacją](../saas-apps/tutorial-list.md) dla obsługiwanej aplikacji. Po skonfigurowaniu i uruchomieniu zadań aprowizacji można zgłaszać przy użyciu jednej z dwóch metod:

* **Azure Portal** — w tym artykule opisano pobieranie informacji o raportach z [Azure Portal](https://portal.azure.com), które zawierają Raport z podsumowaniem udostępniania oraz szczegółowe dzienniki inspekcji dla danej aplikacji.
* **Interfejs API inspekcji** — Azure Active Directory również udostępnia interfejs API inspekcji, który umożliwia programistyczne pobieranie szczegółowych dzienników inspekcji aprowizacji. Informacje dotyczące korzystania z tego interfejsu API można znaleźć w dokumentacji dotyczącej [interfejsu API inspekcji Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) . Chociaż w tym artykule nie opisano sposobu korzystania z interfejsu API, szczegółowe informacje o typach zdarzeń aprowizacji, które są rejestrowane w dzienniku inspekcji.

### <a name="definitions"></a>Definicje

W tym artykule opisano następujące warunki:

* **System źródłowy** — repozytorium użytkowników, z których usługa Azure AD Provisioning synchronizuje. Azure Active Directory jest systemem źródłowym większości wstępnie zintegrowanych łączników aprowizacji, ale istnieją pewne wyjątki (przykład: synchronizacja ruchu w dniach Workday).
* **System docelowy** — repozytorium użytkowników, do których jest synchronizowana usługa Azure AD Provisioning. Jest to zazwyczaj aplikacja SaaS (przykłady: Salesforce, usługi ServiceNow, G Suite, Dropbox dla firm), ale w niektórych przypadkach może to być system lokalny, taki jak Active Directory (przykład: synchronizacja ruchu w dniu Workday do Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Uzyskiwanie raportów aprowizacji z Azure Portal

Aby uzyskać informacje o raportowaniu dla danej aplikacji, Zacznij od uruchomienia [Azure Portal](https://portal.azure.com) i **Azure Active Directory** &gt; **aplikacje dla przedsiębiorstw** &gt; **aprowizacji dzienników (wersja zapoznawcza)** w sekcji **działanie** . Możesz również przejść do aplikacji korporacyjnej, dla której skonfigurowano obsługę administracyjną. Na przykład w przypadku aprowizacji użytkowników do podniesienia uprawnień do serwisu LinkedIn ścieżka nawigacji do szczegółów aplikacji jest:

**Azure Active Directory > aplikacje dla przedsiębiorstw > Wszystkie aplikacje > podnoszenie uprawnień do serwisu LinkedIn**

W tym miejscu możesz uzyskać dostęp do paska postępu aprowizacji i dzienników aprowizacji opisanych poniżej.

## <a name="provisioning-progress-bar"></a>Pasek postępu aprowizacji

[Pasek postępu aprowizacji](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) jest widoczny na karcie **aprowizacji** dla danej aplikacji. Znajduje się w sekcji **Current status** poniżej **ustawień**i pokazuje stan bieżącego początkowego lub przyrostowego cyklu. Ta sekcja zawiera również następujące:

* Łączna liczba użytkowników i/grup, które zostały zsynchronizowane i są obecnie w zakresie aprowizacji między systemem źródłowym a systemem docelowym.
* Ostatnia synchronizacja została uruchomiona. Synchronizacje zwykle odbywają się co 20-40 minut po zakończeniu [cyklu początkowego](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Czy [cykl początkowy](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) został ukończony.
* Czy proces aprowizacji został umieszczony w kwarantannie, a powód stanu kwarantanny to (na przykład niepowodzenie komunikacji z systemem docelowym z powodu nieprawidłowych poświadczeń administratora).

**Bieżący stan** powinien być pierwszym miejscem, w którym administratorzy muszą sprawdzić kondycję operacyjną zadania aprowizacji.

 ![Raport podsumowujący](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Dzienniki aprowizacji (wersja zapoznawcza)

Wszystkie działania wykonywane przez usługę aprowizacji są rejestrowane w [dziennikach aprowizacji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)usługi Azure AD. Możesz uzyskać dostęp do dzienników aprowizacji w Azure Portal, wybierając pozycję **Azure Active Directory** &gt; **aplikacje korporacyjne** &gt; **dzienniki aprowizacji (wersja zapoznawcza)** w sekcji **działanie** . Możesz przeszukiwać dane aprowizacji na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub docelowym. Aby uzyskać szczegółowe informacje, zobacz [dzienniki aprowizacji (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Zarejestrowane typy zdarzeń działania obejmują:

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Raport podsumowujący aprowizacji i dzienniki aprowizacji odgrywają kluczową rolę pomagającą administratorom rozwiązywać różne problemy z inicjowaniem obsługi konta użytkownika.

Aby zapoznać się ze wskazówkami dotyczącymi sposobu rozwiązywania problemów dotyczących automatycznego aprowizacji użytkowników, zobacz [problemy dotyczące konfigurowania i aprowizacji użytkowników w aplikacji](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Dodatkowe materiały

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
