---
title: Konfiguracja aplikacji platformy Azure — często zadawane pytania
description: Często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 60ba0a7723861d6e642a23418dda6a1daa57f14e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523496"
---
# <a name="azure-app-configuration-faq"></a>Konfiguracja aplikacji platformy Azure — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Czym różni się konfiguracja aplikacji od Azure Key Vault?

Konfiguracja aplikacji ułatwia deweloperom zarządzanie ustawieniami aplikacji i dostępnością funkcji kontroli. Celem jest uproszczenie wielu zadań związanych z pracą ze złożonymi danymi konfiguracyjnymi.

Konfiguracja aplikacji obsługuje:

- Hierarchiczne przestrzenie nazw
- etykietowania
- Obszerne zapytania
- Pobieranie wsadowe
- Wyspecjalizowane operacje zarządzania
- Interfejs użytkownika zarządzania funkcjami

Konfiguracja aplikacji uzupełnia Key Vault, a dwa powinny być używane obok siebie w większości wdrożeń aplikacji.

## <a name="should-i-store-secrets-in-app-configuration"></a>Czy należy przechowywać wpisy tajne w konfiguracji aplikacji?

Mimo że konfiguracja aplikacji zapewnia zabezpieczenia z ograniczeniami, Key Vault jest nadal najlepszym miejscem do przechowywania wpisów tajnych aplikacji. Key Vault zapewnia szyfrowanie na poziomie sprzętu, szczegółowe zasady dostępu oraz operacje zarządzania, takie jak rotacja certyfikatów.

Można utworzyć wartości konfiguracji aplikacji, które odwołują się do wpisów tajnych przechowywanych w Key Vault. Aby uzyskać więcej informacji, zobacz [Korzystanie z odwołań Key Vault w aplikacji ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Czy konfiguracja aplikacji szyfruje moje dane?

Tak. Konfiguracja aplikacji szyfruje wszystkie przechowywane wartości klucza i szyfruje komunikację sieciową. Nazwy kluczy i etykiety są używane jako indeksy do pobierania danych konfiguracji i nie są szyfrowane.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Czym różni się konfiguracja aplikacji od ustawień Azure App Service?

Azure App Service umożliwia zdefiniowanie ustawień aplikacji dla każdego wystąpienia App Service. Te ustawienia są przesyłane jako zmienne środowiskowe do kodu aplikacji. Jeśli chcesz, możesz skojarzyć ustawienie z określonym miejscem wdrożenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień aplikacji](/azure/app-service/configure-common#configure-app-settings).

W przeciwieństwie do konfiguracji aplikacji platformy Azure można definiować ustawienia, które mogą być współużytkowane przez wiele aplikacji. Obejmuje to aplikacje działające w App Service, a także inne platformy. Kod aplikacji uzyskuje dostęp do tych ustawień za pośrednictwem dostawców konfiguracji dla platform .NET i Java, za pomocą zestawu Azure SDK lub bezpośrednio za pośrednictwem interfejsów API REST.

Można również importować i eksportować ustawienia między App Service i konfiguracji aplikacji. Ta funkcja umożliwia szybkie skonfigurowanie nowego magazynu konfiguracji aplikacji na podstawie istniejących ustawień App Service. Możesz również udostępnić konfigurację z istniejącą aplikacją, która opiera się na App Service ustawieniach.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Czy istnieją jakieś ograniczenia rozmiaru kluczy i wartości przechowywanych w konfiguracji aplikacji?

Dla pojedynczego elementu klucz-wartość istnieje limit 10 KB.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak należy przechowywać konfiguracje dla wielu środowisk (testów, przemieszczania, produkcji itp.)?

Użytkownik kontroluje, kto może uzyskać dostęp do konfiguracji aplikacji na poziomie magazynu. Użyj oddzielnego magazynu dla każdego środowiska, które wymaga innych uprawnień. Takie podejście zapewnia najlepszą izolację zabezpieczeń.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jakie są zalecane metody korzystania z konfiguracji aplikacji?

Zobacz [najlepsze rozwiązania](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Ile jest koszt konfiguracji aplikacji?

Istnieją dwie warstwy cenowe: 

- Warstwa Bezpłatna
- Warstwa standardowa.

Jeśli sklep został utworzony przed wprowadzeniem warstwy Standardowa, jest on automatycznie przenoszony do warstwy Bezpłatna po ogólnej dostępności. Możesz wybrać opcję uaktualnienia do warstwy Standardowa lub pozostać w warstwie Bezpłatna.

Nie można obniżyć poziomu sklepu z warstwy Standardowa do warstwy Bezpłatna. W warstwie Bezpłatna można utworzyć nowy magazyn, a następnie zaimportować dane konfiguracji do tego magazynu.

## <a name="which-app-configuration-tier-should-i-use"></a>Której warstwy konfiguracji aplikacji należy użyć?

Obie warstwy konfiguracji aplikacji oferują podstawowe funkcje, w tym ustawienia konfiguracji, flagi funkcji, odwołania Key Vault, podstawowe operacje zarządzania, metryki i dzienniki.

Poniżej przedstawiono zagadnienia dotyczące wybierania warstwy.

- **Zasoby na subskrypcję**: zasób składa się z pojedynczego magazynu konfiguracji. Każda subskrypcja jest ograniczona do jednego magazynu konfiguracji w warstwie Bezpłatna. Subskrypcje mogą mieć nieograniczoną liczbę magazynów konfiguracji w warstwie Standardowa.
- **Magazyn na zasób**: w warstwie Bezpłatna każdy magazyn konfiguracji jest ograniczony do 10 MB pamięci masowej. W warstwie Standardowa każdy magazyn konfiguracji może korzystać z maksymalnie 1 GB miejsca w magazynie.
- **Historia klucza**: Konfiguracja aplikacji przechowuje historię wszystkich zmian wprowadzonych do kluczy. W warstwie Bezpłatna ta historia jest przechowywana przez siedem dni. W warstwie Standardowa ta historia jest przechowywana przez 30 dni.
- **Liczba żądań**dziennie: magazyny w warstwie Bezpłatna są ograniczone do 1 000 żądań dziennie. Gdy sklep osiągnie 1 000 żądań, będzie zwracał kod stanu HTTP 429 dla wszystkich żądań do północy czasu UTC.

    W przypadku magazynów w warstwie Standardowa pierwsze 200 000 żądań każdego dnia są uwzględniane w dziennej opłacie. Dodatkowe żądania są rozliczane jako nadwyżkowe.

- **Umowa dotycząca poziomu usług**: warstwa standardowa ma umowę SLA na 99,9% czasu dostępności. Warstwa Bezpłatna nie ma umowy SLA.
- **Funkcje zabezpieczeń**: w obu warstwach są dostępne podstawowe funkcje zabezpieczeń, w tym szyfrowanie za pomocą kluczy zarządzanych przez firmę Microsoft, uwierzytelnianie za pośrednictwem algorytmu HMAC lub Azure Active Directory, obsługa RBAC i tożsamość zarządzana. Warstwa standardowa oferuje bardziej zaawansowane funkcje zabezpieczeń, w tym obsługę linków prywatnych i szyfrowanie za pomocą kluczy zarządzanych przez klienta.
- **Koszt**: magazyny w warstwie Standardowa mają dzienną opłatą za użycie. Jest również opłata za nadwyżkowe żądania w porównaniu do codziennej alokacji. Nie ma kosztu korzystania z magazynu warstwy Bezpłatna.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Czy mogę uaktualnić sklep z warstwy Bezpłatna do warstwy Standardowa? Czy mogę obniżyć poziom sklepu z warstwy Standardowa do warstwy Bezpłatna?

W dowolnym momencie możesz przeprowadzić uaktualnienie z warstwy Bezpłatna do warstwy Standardowa.

Nie można obniżyć poziomu sklepu z warstwy Standardowa do warstwy Bezpłatna. W warstwie Bezpłatna można utworzyć nowy magazyn, a następnie [zaimportować dane konfiguracji do tego magazynu](howto-import-export-data.md).

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Jak mogę otrzymywać anonse dotyczące nowych wersji i innych informacji związanych z konfiguracją aplikacji?

Subskrybowanie [repozytorium anonsów usługi GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak zgłosić problem lub przekazać sugestię?

Możesz skontaktować się bezpośrednio z usługą [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Następne kroki

* [Informacje o konfiguracji aplikacji platformy Azure](./overview.md)
