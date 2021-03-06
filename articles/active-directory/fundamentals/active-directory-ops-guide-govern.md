---
title: Przewodnik dotyczący referencyjnych operacji ładu Azure Active Directory
description: W tym przewodniku odwołuje się opis operacji sprawdzania i działań, które należy wykonać w celu zabezpieczenia zarządzania nadzorem
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535459"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Przewodnik dotyczący referencyjnych operacji ładu Azure Active Directory

W tej części [przewodnika dotyczącego odwołań usługi Azure AD](active-directory-ops-guide-intro.md) opisano testy i akcje, które należy wykonać w celu oceny i zaświadczania dostępu, które nie są uprzywilejowane i uprzywilejowanych tożsamości, inspekcji i kontroli w środowisku.

> [!NOTE]
> Te zalecenia są aktualne w dniu opublikowania, ale mogą ulec zmianie w czasie. Organizacje powinny stale sprawdzać swoje praktyki nadzoru w miarę rozwoju produktów i usług firmy Microsoft.

## <a name="key-operational-processes"></a>Najważniejsze procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu zoptymalizowania środowiska. Najważniejsze zadania i ich zalecani właściciele obejmują:

| Zadanie | Właściciel |
| :- | :- |
| Archiwizuj dzienniki inspekcji usługi Azure AD w systemie SIEM | Zespół ds. operacji InfoSec |
| Odnajdywanie aplikacji, które są zarządzane z zachowaniem zgodności | Zespół operacji IAM |
| Regularnie Przeglądaj dostęp do aplikacji | Zespół architektury InfoSec |
| Regularnie Przeglądaj dostęp do tożsamości zewnętrznych | Zespół architektury InfoSec |
| Regularnie sprawdzaj, kto ma uprzywilejowane role | Zespół architektury InfoSec |
| Zdefiniuj bramy zabezpieczeń, aby aktywować role uprzywilejowane | Zespół architektury InfoSec |
| Regularnie Przeglądaj dotacje | Zespół architektury InfoSec |
| Projektowanie katalogów i pakietów dostępu dla aplikacji i zasobów w oparciu o pracowników w organizacji | Właściciele aplikacji |
| Definiowanie zasad zabezpieczeń w celu przypisania użytkownikom dostępu do pakietów | Zespół ds. InfoSec i właściciele aplikacji |
| Jeśli zasady obejmują przepływy pracy zatwierdzania, regularnie Przeglądaj zatwierdzenia przepływu pracy | Właściciele aplikacji |
| Przeglądanie wyjątków w zasadach zabezpieczeń, takich jak zasady dostępu warunkowego, za pomocą przeglądów dostępu | Zespół ds. operacji InfoSec |

Podczas przeglądania listy może być konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosować własność do zadań z właścicielami, które nie są wyrównane do powyższych zaleceń.

#### <a name="owner-recommended-reading"></a>Odczytywanie zalecanego przez właściciela

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Nadzór na platformie Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Testowanie zmian konfiguracji

Istnieją zmiany, które wymagają specjalnych zagadnień podczas testowania, od prostych technik, takich jak przeprowadzenie docelowej podzbioru użytkowników w celu wdrożenia zmiany w równoległej dzierżawie testowej. Jeśli nie została zaimplementowana strategia testowania, należy zdefiniować podejście testowe na podstawie wytycznych w poniższej tabeli:

| Scenariusz| Zalecenie |
|-|-|
|Zmiana typu uwierzytelniania z federacyjnego na PHS/PTA lub odwrotnie| Użyj [wdrożenia etapowego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) , aby przetestować wpływ zmiany typu uwierzytelniania.|
|Wdrażanie nowych zasad dostępu warunkowego (CA) lub zasad ochrony tożsamości|Utwórz nowe zasady urzędu certyfikacji i przypisz je do użytkowników testowych.|
|Dołączanie środowiska testowego aplikacji|Dodaj aplikację do środowiska produkcyjnego, Ukryj ją z poziomu panelu Moje aplikacje i przypisz ją do użytkowników testowych w fazie oceny jakości (pytań i odpowiedzi).|
|Zmiana reguł synchronizacji|Wykonaj zmiany w Azure AD Connect testowym z tą samą konfiguracją, która jest obecnie w środowisku produkcyjnym, znana także jako tryb przejściowy, i analizuj wyniki CSExport. Jeśli są spełnione, Zamień na produkcję produkcyjną, gdy jest gotowa.|
|Zmiana marki|Przetestuj w oddzielnym dzierżawie testowej.|
|Wdrażanie nowej funkcji|Jeśli funkcja obsługuje wdrażanie do docelowego zestawu użytkowników, zidentyfikuj użytkowników pilotażowych i skompiluj. Na przykład Samoobsługowe resetowanie hasła i uwierzytelnianie wieloskładnikowe mogą dotyczyć określonych użytkowników lub grup.|
|Uruchomienie produkcyjne aplikację od lokalnego dostawcy tożsamości (dostawcy tożsamości), na przykład Active Directory, do usługi Azure AD|Jeśli aplikacja obsługuje wiele konfiguracji dostawcy tożsamości, na przykład usługi Salesforce, skonfiguruj jednocześnie i przetestuj usługę Azure AD podczas okna zmiany (Jeśli aplikacja wprowadza stronę HRD). Jeśli aplikacja nie obsługuje wielu dostawców tożsamości, Zaplanuj testowanie podczas okna kontroli zmian i przestoju programu.|
|Aktualizuj reguły grupy dynamicznej|Utwórz równoległą grupę dynamiczną z nową regułą. Porównaj z obliczonym wynikiem, na przykład Uruchom program PowerShell z tym samym warunkiem.<br>W przypadku przebiegu testu zastępowanie miejsc, w których była używana stara Grupa (jeśli to możliwe).|
|Migrowanie licencji produktu|Zapoznaj się z tematem [Zmienianie licencji dla pojedynczego użytkownika w grupie licencjonowanej w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Zmień reguły AD FS, takie jak autoryzacja, wystawianie, MFA|Użyj żądania grupy, aby docelowa podzbiór użytkowników.|
|Zmień środowisko uwierzytelniania AD FS lub podobne zmiany w całej farmie|Utwórz farmę równoległą o tej samej nazwie hosta, Implementuj zmiany konfiguracji, Testuj od klientów przy użyciu pliku HOSTs, reguł routingu NLB lub podobnego routingu.<br>Jeśli platforma docelowa nie obsługuje plików HOSTs (na przykład urządzenia przenośne), Zmień kontrolkę.|

## <a name="access-reviews"></a>Przeglądy dostępu

### <a name="access-reviews-to-applications"></a>Przeglądy dostępu do aplikacji

W miarę upływu czasu użytkownicy mogą zbierać dostęp do zasobów w miarę ich poruszania się w różnych zespołach i pozycjach. Ważne jest, aby właściciele zasobów regularnie przeglądali dostęp do aplikacji i usuwali uprawnienia, które nie są już potrzebne w całym cyklu życia użytkowników. [Przeglądy dostępu](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) w usłudze Azure AD pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Właściciele zasobów powinni regularnie przeglądać dostęp użytkowników, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp. W idealnym przypadku należy rozważyć użycie przeglądów dostępu do usługi Azure AD dla tego zadania.

![Strona początkowa przeglądów dostępu](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Każdy użytkownik, który współdziała z przeglądami dostępu, musi mieć płatną licencję Azure AD — wersja Premium P2.

### <a name="access-reviews-to-external-identities"></a>Przeglądy dostępu do tożsamości zewnętrznych

Należy zachować dostęp do tożsamości zewnętrznych ograniczonych tylko do zasobów, które są niezbędne, w czasie, gdy jest to konieczne. Ustanów zwykły zautomatyzowany proces przeglądu dostępu dla wszystkich tożsamości zewnętrznych i dostępu do aplikacji przy użyciu [przeglądów dostępu do](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)usługi Azure AD. Jeśli proces już istnieje w środowisku lokalnym, należy rozważyć użycie przeglądów dostępu do usługi Azure AD. Gdy aplikacja zostanie wycofana lub nie jest już używana, Usuń wszystkie tożsamości zewnętrzne, które miały dostęp do aplikacji.

> [!NOTE]
> Każdy użytkownik, który współdziała z przeglądami dostępu, musi mieć płatną licencję Azure AD — wersja Premium P2.

## <a name="privileged-account-management"></a>Zarządzanie kontami uprzywilejowanymi

### <a name="privileged-account-usage"></a>Użycie konta uprzywilejowanego

Hakerzy często kierują konta administratora i inne elementy uprzywilejowanego dostępu, aby szybko uzyskać dostęp do poufnych danych i systemów. Ze względu na to, że użytkownicy z rolami uprzywilejowanymi mogą postępować w miarę upływu czasu, ważne jest, aby regularnie przeglądać i zarządzać dostępem administratorów oraz zapewniać uprzywilejowany dostęp do usługi Azure AD i zasobów platformy Azure.

Jeśli w Twojej organizacji nie istnieje żaden proces zarządzania kontami uprzywilejowanymi lub obecnie Administratorzy, którzy używają zwykłych kont użytkowników do zarządzania usługami i zasobami, należy od razu zacząć używać oddzielnych kont, na przykład dla zwykłych codziennych dni. demonstracj druga dla uprzywilejowanego dostępu i skonfigurowana za pomocą usługi MFA. Jeszcze lepiej, jeśli Twoja organizacja ma Azure AD — wersja Premiumą subskrypcję P2, należy natychmiast wdrożyć [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). W tym samym tokenie należy również przejrzeć te konta uprzywilejowane i [przypisać im mniej uprzywilejowanych ról](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) , jeśli ma to zastosowanie.

Innym aspektem zarządzania kontami uprzywilejowanymi, które należy zaimplementować, jest zdefiniowanie [przeglądu dostępu](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) dla tych kont ręcznie lub [automatycznie za pomocą usługi PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review).

#### <a name="privileged-account-management-recommended-reading"></a>Zalecane odczytywanie przez zarządzanie kontami uprzywilejowanymi

- [Role w Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Konta dostępu awaryjnego

Organizacje muszą utworzyć [konta awaryjne](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) , aby przygotować się do zarządzania usługą Azure AD w taki sposób, jak w przypadku awarii uwierzytelniania:

- Składniki dotyczące przestojów infrastruktury uwierzytelniania (AD FS, lokalna usługa AD i usługi MFA)
- Obrót personelu administracyjnego

Aby zapobiec przypadkowemu zablokowaniu dzierżawy, ponieważ nie można zalogować się lub aktywować konta indywidualnego użytkownika jako administrator, należy utworzyć co najmniej dwa konta awaryjne i upewnić się, że są one zaimplementowane i zgodne z [najlepszymi rozwiązaniami firmy Microsoft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) i [procedurami awarii](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Dostęp uprzywilejowany do portalu Azure EA

[Portal Umowa Enterprise platformy Azure (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) umożliwia tworzenie subskrypcji platformy Azure na podstawie Umowa Enterprise głównych, które są zaawansowaną rolą w przedsiębiorstwie. Przed rozpoczęciem pracy z usługą Azure AD często jest wykonywana ponowna próba uruchomienia tego portalu, dlatego konieczne jest użycie tożsamości usługi Azure AD w celu jego zablokowania, usunięcia kont osobistych z portalu, upewnienia się, że właściwe Delegowanie jest stosowane i ograniczenia ryzyka blokady .

Aby wyczyścić, jeśli poziom autoryzacji portalu EA jest obecnie ustawiony na wartość "tryb mieszany", należy usunąć wszystkie [konta Microsoft](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) ze wszystkich uprzywilejowanego dostępu w portalu EA i skonfigurować portal EA do korzystania tylko z kont usługi Azure AD. Jeśli delegowane role portalu EA nie są skonfigurowane, należy również znaleźć i zaimplementować delegowane role dla działów i kont.

#### <a name="privileged-access-recommended-reading"></a>Zalecane odczytywanie uprzywilejowanego dostępu

- [Uprawnienia ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Zarządzanie upoważnieniami

[Zarządzanie prawami (em)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) umożliwia właścicielom aplikacji łączenie zasobów i przypisywanie ich do określonych osób w organizacji (zarówno wewnętrznych, jak i zewnętrznych). EM pozwala na samoobsługowe rejestrowanie i delegowanie do właścicieli firmy przy jednoczesnym zachowaniu zasad ładu w celu udzielania dostępu, ustawiania czasów trwania dostępu i zezwalania na przepływy pracy zatwierdzania. 

> [!NOTE]
> Zarządzanie prawami w usłudze Azure AD wymaga licencji na Azure AD — wersja Premium P2.

## <a name="summary"></a>Podsumowanie

Istnieje osiem aspektów bezpiecznego zarządzania tożsamościami. Ta lista pomoże zidentyfikować działania, które należy podjąć w celu oceny i zaświadczania dostępu przydzielonego do nieuprzywilejowanych i uprzywilejowanych tożsamości, inspekcji i kontroli zmian w środowisku.

- Przypisywanie właścicieli do kluczowych zadań.
- Zaimplementuj strategię testowania.
- Za pomocą przeglądów dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról.
- Ustanów zwykły, zautomatyzowany proces przeglądu dostępu dla wszystkich typów tożsamości zewnętrznych i dostępu do aplikacji.
- Ustanów proces przeglądu dostępu, aby regularnie przeglądać i zarządzać dostępem administratorów oraz zapewniać uprzywilejowany dostęp do usługi Azure AD i zasobów platformy Azure.
- Zainicjuj obsługę kont awaryjnych, aby przygotować się do zarządzania usługą Azure AD w nieoczekiwany sposób.
- Zablokuj dostęp do portalu Azure EA.
- Zaimplementuj Zarządzanie prawami, aby zapewnić dostęp do kolekcji zasobów.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [testami operacyjnymi i akcjami usługi Azure AD](active-directory-ops-guide-ops.md).
