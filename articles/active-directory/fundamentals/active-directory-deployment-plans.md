---
title: Plany wdrożenia — Azure Active Directory | Microsoft Docs
description: Kompleksowe wskazówki dotyczące wdrażania wielu Azure Active Directory funkcji.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558529"
---
# <a name="azure-active-directory-deployment-plans"></a>Plany wdrażania usługi Azure Active Directory
Szukasz kompleksowej wskazówki dotyczącej wdrażania możliwości usług Azure Active Directory (Azure AD)? Plany wdrażania usługi Azure AD przeprowadzą Cię przez wartość biznesową, zagadnienia dotyczące planowania i procedury operacyjne, które są potrzebne do pomyślnego wdrożenia wspólnych możliwości usługi Azure AD.

Z poziomu dowolnej strony planu Użyj funkcji drukowania do pliku PDF w przeglądarce, aby utworzyć aktualną wersję offline dokumentacji.
## <a name="include-the-right-stakeholders"></a>Uwzględnij odpowiednie strony zainteresowane

Po rozpoczęciu planowania wdrożenia nowej możliwości należy uwzględnić najważniejszych uczestników w organizacji. Zalecamy określenie i udokumentowanie osoby lub osób, które spełniają każdą z następujących ról, i współpracują z nimi w celu określenia zaangażowania w projekt.  

Role mogą zawierać następujące elementy: 

|Rola |Opis |
|-|-|
|Użytkownik końcowy|Reprezentatywna Grupa użytkowników, dla których zostanie zaimplementowana funkcja. Często przegląda zmiany w programie pilotażowym.
|Menedżer pomocy technicznej IT|Przedstawiciel działu pomocy technicznej IT, który może wprowadzić dane wejściowe dotyczące tej zmiany w perspektywie pomocy technicznej.  
|Architekt tożsamości lub Administrator globalny platformy Azure|Przedstawiciel zespołu zarządzania tożsamościami odpowiedzialny za definiowanie sposobu wyrównywania tej zmiany z podstawową infrastrukturą zarządzania tożsamościami w organizacji.|
|Właściciel firmy aplikacji |Ogólny właściciel firmy, których dotyczy ta aplikacja, która może obejmować zarządzanie dostępem.  Może również dostarczyć dane wejściowe dotyczące środowiska użytkownika i użyteczności tej zmiany z perspektywy użytkownika końcowego.
|Właściciel zabezpieczeń|Przedstawiciel zespołu ds. zabezpieczeń, który może się wylogować, aby plan spełniał wymagania dotyczące bezpieczeństwa organizacji.|
|Menedżer zgodności|Osoba w organizacji odpowiedzialna za zapewnienie zgodności z wymogami firmowymi, branżowymi lub rządowymi.|

**Poziom zaangażowania może obejmować:**

- **R**esponsible do implementacji planu i wyniku projektu 

- **Pproval planu i wyniku projektu** 

- **C**ondystrybucyjny do planu i wyniku projektu 

- **Nformedm planu i wyniku**projektu


## <a name="best-practices-for-a-pilot"></a>Najlepsze rozwiązania dla pilotażu
Pilotaż umożliwia testowanie z małą grupą przed włączeniem funkcji dla wszystkich. Upewnij się, że w ramach testowania każdy przypadek użycia w organizacji zostanie dokładnie przetestowany. Najlepszym rozwiązaniem jest ukierunkowanie określonej grupy użytkowników pilotażowych przed przekazaniem jej do organizacji jako całości.

W pierwszej fazie, kierowanie do nich, użyteczność i innych odpowiednich użytkowników, którzy mogą testować i dostarczać Opinie. Ta opinia powinna być używana do dalszej analizy komunikacji i instrukcji wysyłanych do użytkowników oraz do uzyskiwania wglądu w typy problemów, które pracownicy pomocy technicznej mogą zobaczyć. 

Rozszerzanie wdrożenia do większych grup użytkowników powinno odbywać się przez zwiększenie zakresu grup przeznaczonych do użycia. Można to zrobić za pomocą [dynamicznej przynależności do grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)lub ręcznie dodając użytkowników do grup przeznaczonych do grupy.


## <a name="deploy-authentication"></a>Wdróż uwierzytelnianie

| Możliwość | Opis|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Azure Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft służące do przeprowadzania weryfikacji dwuetapowej. Korzystając z zaakceptowanych przez administratora metod uwierzytelniania, usługa Azure MFA pomaga chronić dostęp do danych i aplikacji, a jednocześnie spełnia wymagania dotyczące prostego procesu logowania. |
| [Dostęp warunkowy](https://aka.ms/deploymentplans/ca)| Za pomocą dostępu warunkowego można zaimplementować zautomatyzowane decyzje dotyczące kontroli dostępu, które mogą uzyskiwać dostęp do aplikacji w chmurze na podstawie warunków. |
| [Samoobsługowe resetowanie haseł](https://aka.ms/deploymentplans/sspr)| Funkcja samoobsługowego resetowania hasła pomaga użytkownikom resetować swoje hasła bez interwencji administratora, kiedy i gdzie potrzebują. |
| [Kończenie](https://aka.ms/deploymentplans/passwordless) | Zaimplementuj uwierzytelnianie bezhasła przy użyciu aplikacji Microsoft Authenticator lub kluczy zabezpieczeń FIDO2 w organizacji |

## <a name="deploy-application-management"></a>Wdrażanie zarządzania aplikacjami

| Możliwość | Opis|
| -| - |
| [Logowanie jednokrotne](https://aka.ms/deploymentplans/sso)| Logowanie jednokrotne ułatwia użytkownikom dostęp do aplikacji i zasobów, które są potrzebne do prowadzenia działalności, podczas logowania tylko raz. Po zalogowaniu użytkownicy mogą przechodzić z Microsoft Office do usługi SalesForce do wewnętrznych aplikacji bez konieczności wprowadzania poświadczeń po raz drugi. |
| [Panel dostępu](https://aka.ms/deploymentplans/accesspanel)| Zapewnianie użytkownikom prostego centrum w celu odnajdywania i uzyskiwania dostępu do wszystkich aplikacji. Umożliwiają im wydajniejsze korzystanie z funkcji samoobsługowych, takich jak żądanie dostępu do aplikacji i grup oraz zarządzanie dostępem do zasobów w imieniu innych użytkowników. |


## <a name="deploy-hybrid-scenarios"></a>Wdrażanie scenariuszy hybrydowych

| Możliwość | Opis|
| -| -|
| [Synchronizowanie skrótów haseł za pomocą usługi ADFS](https://aka.ms/deploymentplans/adfs2phs)| W przypadku synchronizacji skrótów haseł skróty użytkowników są synchronizowane z Active Directory lokalnych do usługi Azure AD, dzięki czemu usługa Azure AD uwierzytelnia użytkowników bez interakcji z lokalnym Active Directory |
| [Uwierzytelnianie przekazywane za pomocą usługi ADFS](https://aka.ms/deploymentplans/adfs2pta)| Uwierzytelnianie przekazywane przez usługę Azure AD ułatwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tych samych haseł. Ta funkcja zapewnia użytkownikom lepszy komfort pracy — mniej hasła do zapamiętania i zmniejsza koszty działu pomocy technicznej, ponieważ użytkownicy nie mogą zapomnieć, jak się zalogować. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory. |
| [Serwer proxy aplikacji usługi Azure AD](https://aka.ms/deploymentplans/appproxy)| Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Muszą oni uzyskiwać dostęp do aplikacji SaaS w chmurze i aplikacji firmowych w środowisku lokalnym. Serwer proxy aplikacji usługi Azure AD umożliwia ten niezawodny dostęp bez kosztownych i złożonych wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowana (stref DMZ). |
| [Bezproblemowe logowanie jednokrotne](../hybrid/how-to-connect-sso-quick-start.md)| Bezproblemowe logowanie jednokrotne w usłudze Azure Active Directory zapewnia automatyczne logowanie użytkowników, gdy ich urządzenia są połączone z siecią firmową. W przypadku tej funkcji użytkownicy nie muszą wpisywać swoich haseł, aby zalogować się do usługi Azure AD i zwykle nie musieli wprowadzać ich nazwy użytkownika. Ta funkcja zapewnia autoryzowanym użytkownikom łatwy dostęp do aplikacji opartych na chmurze bez konieczności używania dodatkowych składników lokalnych. |

## <a name="deploy-user-provisioning"></a>Wdróż Inicjowanie obsługi użytkowników

| Możliwość | Opis|
| -| -|
| [Aprowizowanie użytkowników](https://aka.ms/deploymentplans/userprovisioning)| Usługa Azure AD ułatwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze (SaaS), takich jak Dropbox, Salesforce, ServiceNow i nie tylko. |
| [Obsługa administracyjna użytkowników w chmurze](https://aka.ms/deploymentplans/cloudhr)| Zainicjowanie obsługi administracyjnej użytkowników w chmurze w celu Active Directory tworzy podstawę do ciągłego zarządzania tożsamościami i zwiększa jakość procesów biznesowych, które korzystają z autorytatywnych danych tożsamości. Korzystając z tej funkcji w przypadku produktów w chmurze, takich jak Workday lub SuccessFactors, można bezproblemowo zarządzać cyklem życia tożsamości pracowników i pracowników warunkowych przez skonfigurowanie reguł umożliwiających mapowanie łączników — w przypadku procesów opuszczających, takich jak nowe zatrudnienie, zakończenie Transfer) do akcji aprowizacji IT (takich jak Create, Enable, Disable) |

## <a name="deploy-governance-and-reporting"></a>Wdrażanie ładu i raportowania

| Możliwość | Opis|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) ułatwia zarządzanie uprzywilejowanymi rolami administracyjnymi w usłudze Azure AD, zasobach platformy Azure i innych usługach online firmy Microsoft. PIM oferuje rozwiązania, takie jak dostęp just in Time, żądania przepływów pracy zatwierdzania i w pełni zintegrowane przeglądy dostępu, dzięki czemu można identyfikować, odkrywać i zapobiegać złośliwym działaniom uprzywilejowanych ról w czasie rzeczywistym. |
| [Raportowanie i monitorowanie](https://aka.ms/deploymentplans/reporting)| Projekt rozwiązania do raportowania i monitorowania usługi Azure AD zależy od wymagań prawnych, bezpieczeństwa i działania, a także istniejącego środowiska i procesów. W tym artykule przedstawiono różne opcje projektowania i przeprowadzimy Cię do odpowiedniej strategii wdrażania. |
