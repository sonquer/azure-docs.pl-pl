---
title: Zalecenia dotyczące zabezpieczeń usługi queue storage
titleSuffix: Azure Storage
description: Dowiedz się więcej na temat zaleceń dotyczących zabezpieczeń w usłudze queue storage. Wdrożenie tych wskazówek pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 1315e1f81ee32a544b623b7f3ffad61a7e7275d8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486289"
---
# <a name="security-recommendations-for-queue-storage"></a>Zalecenia dotyczące zabezpieczeń usługi queue storage

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń usługi queue storage. Wdrożenie tych zaleceń pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności. Aby uzyskać więcej informacji na temat tego, co firma Microsoft może spełnić obowiązki dostawcy usług, Przeczytaj [udostępnione obowiązki w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Niektóre zalecenia zawarte w tym artykule mogą być automatycznie monitorowane przez Azure Security Center. Azure Security Center to pierwszy wiersz obrony w ochronie zasobów na platformie Azure. Aby uzyskać informacje na temat Azure Security Center, zobacz [co to jest Azure Security Center?](../../security-center/security-center-intro.md).

Azure Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich rozwiązywania. Aby uzyskać więcej informacji o Azure Security Center zaleceniach, zapoznaj się [z zaleceniami dotyczącymi zabezpieczeń w programie Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Korzystanie z modelu wdrażania Azure Resource Manager | Utwórz nowe konta magazynu przy użyciu Azure Resource Manager model wdrażania, aby uzyskać ważne ulepszenia zabezpieczeń, w tym najwyższą kontrolę dostępu (RBAC) i inspekcję, wdrażanie i zarządzanie oparte na Menedżer zasobówach, dostęp do zarządzanych tożsamości, dostęp Aby Azure Key Vault dla wpisów tajnych i uwierzytelniania i autoryzacji opartej na usłudze Azure AD w celu uzyskania dostępu do danych i zasobów usługi Azure Storage. Jeśli to możliwe, Przeprowadź migrację istniejących kont magazynu, które używają klasycznego modelu wdrażania, aby użyć Azure Resource Manager. Aby uzyskać więcej informacji na temat Azure Resource Manager, zobacz [Azure Resource Manager omówienie](/azure/azure-resource-manager/resource-group-overview). | - |
| Włącz opcję **wymagany bezpieczny transfer** na wszystkich kontach magazynu | Po włączeniu opcji **wymagany bezpieczny transfer** wszystkie żądania dotyczące konta magazynu muszą odbywać się za pośrednictwem bezpiecznych połączeń. Wszystkie żądania wysyłane za pośrednictwem protokołu HTTP zakończą się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Wymagaj bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md). | [Tak](../../security-center/security-center-sql-service-recommendations.md) |
| Włącz zaawansowaną ochronę przed zagrożeniami dla wszystkich kont magazynu | Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Alerty zabezpieczeń są wyzwalane w Azure Security Center, gdy wystąpią anomalie działania i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zagrożeń. Aby uzyskać więcej informacji, zobacz [Advanced Threat Protection for Azure Storage](../common/storage-advanced-threat-protection.md). | [Tak](../../security-center/security-center-sql-service-recommendations.md) |
| Ogranicz tokeny sygnatury dostępu współdzielonego (SAS) tylko do połączeń HTTPS | Wymaganie protokołu HTTPS, gdy klient korzysta z tokenu sygnatury dostępu współdzielonego, aby uzyskać dostęp do danych w kolejce, aby zminimalizować ryzyko podsłuchiwania. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Użyj Azure Active Directory (Azure AD), aby autoryzować dostęp do danych kolejki | Usługa Azure AD zapewnia znakomite zabezpieczenia i łatwość użycia w porównaniu z kluczami udostępnionymi do autoryzowania żądań do usługi queue storage. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md). | - |
| Należy pamiętać o najniższych uprawnieniach podczas przypisywania uprawnień do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem RBAC | Podczas przypisywania roli do użytkownika, grupy lub aplikacji Przyznaj podmiotowi zabezpieczeń tylko te uprawnienia, które są niezbędne do wykonywania swoich zadań. Ograniczanie dostępu do zasobów pomaga zapobiegać przypadkowemu i złośliwemu wykorzystaniu danych. | - |
| Zabezpiecz klucze dostępu do konta za pomocą Azure Key Vault | Firma Microsoft zaleca używanie usługi Azure AD do autoryzowania żądań do usługi Azure Storage. Jeśli jednak musisz użyć autoryzacji klucza współużytkowanego, Zabezpiecz klucze konta za pomocą Azure Key Vault. Możesz pobrać klucze z magazynu kluczy w czasie wykonywania, zamiast zapisywać je w aplikacji. | - |
| Okresowe ponowne generowanie kluczy konta | Okresowe obracanie kluczy konta zmniejsza ryzyko ujawnienia danych do złośliwych aktorów. | - |
| Należy pamiętać o najniższych uprawnieniach podczas przypisywania uprawnień do sygnatury dostępu współdzielonego | Podczas tworzenia sygnatury dostępu współdzielonego należy określić tylko te uprawnienia, które są wymagane przez klienta do wykonywania funkcji. Ograniczanie dostępu do zasobów pomaga zapobiegać przypadkowemu i złośliwemu wykorzystaniu danych. | - |
| Zaplanuj odwołanie dla dowolnego sygnatury dostępu współdzielonego, które wystawiasz klientom | W przypadku naruszenia zabezpieczeń sygnatury dostępu współdzielonego należy odwołać te sygnatury dostępu współdzielonego tak szybko, jak to możliwe. Aby odwołać sygnaturę dostępu współdzielonego delegowania użytkownika, odwołaj klucz delegowania użytkownika, aby szybko unieważniać wszystkie podpisy skojarzone z tym kluczem. Aby odwołać sygnaturę dostępu współdzielonego usługi, która jest skojarzona z przechowywanymi zasadami dostępu, można usunąć zachowane zasady, zmienić nazwę zasad lub zmienić jej czas wygaśnięcia na czas w przeszłości. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).  | - |
| Jeśli sygnatura dostępu współdzielonego usługi nie jest skojarzona z przechowywanymi zasadami, ustaw czas wygaśnięcia na jedną godzinę lub mniejszą | Nie można odwołać skojarzenia zabezpieczeń usługi, która nie jest skojarzona z przechowywanymi zasadami dostępu. Z tego powodu ograniczenie czasu wygaśnięcia w taki sposób, aby sygnatura dostępu współdzielonego była ważna przez jedną godzinę lub mniej. | - |

## <a name="networking"></a>Networking

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Włącz reguły zapory | Skonfiguruj reguły zapory, aby ograniczyć dostęp do konta magazynu do żądań pochodzących z określonych adresów IP lub zakresów lub z listy podsieci w usłudze Azure Virtual Network (VNet). Więcej informacji o konfigurowaniu reguł zapory znajduje się w temacie [Azure File Sync ustawienia serwera proxy i zapory](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Zezwalaj zaufanym usługom firmy Microsoft na dostęp do konta magazynu | Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w ramach platformy Azure Virtual Network (VNet) lub z dozwolonych publicznych adresów IP. Żądania, które są blokowane obejmują z innymi usługami platformy Azure w witrynie Azure portal, rejestrowania i metryk usług i tak dalej. Możesz zezwolić na żądania z innych usług platformy Azure, dodając wyjątek, aby zezwolić zaufanym usługom firmy Microsoft na dostęp do konta magazynu. Aby uzyskać więcej informacji na temat dodawania wyjątku dla zaufanych usług firmy Microsoft, zobacz [Azure File Sync serwera proxy i ustawień zapory](../files/storage-sync-files-firewall-and-proxy.md).| - |
| Używanie prywatnych punktów końcowych | Prywatny punkt końcowy przypisuje prywatny adres IP z usługi Azure Virtual Network (VNet) do konta magazynu. Zabezpiecza cały ruch między siecią wirtualną a kontem magazynu za pośrednictwem prywatnego linku. Aby uzyskać więcej informacji o prywatnych punktach końcowych, zobacz [nawiązywanie połączenia prywatnego z kontem magazynu przy użyciu prywatnego punktu końcowego platformy Azure](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Ograniczanie dostępu sieciowego do określonych sieci | Ograniczanie dostępu sieciowego do sieci obsługujących klientów wymagających dostępu zmniejsza narażenie zasobów na ataki sieciowe. | [Tak](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Rejestrowanie/monitorowanie

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Śledzenie sposobu autoryzacji żądań | Włącz rejestrowanie usługi Azure Storage, aby śledzić, w jaki sposób każde żądanie skierowane do usługi Azure Storage zostało autoryzowane. Dzienniki wskazują, czy żądanie zostało wykonane anonimowo, przy użyciu tokenu OAuth 2,0 przy użyciu klucza współużytkowanego lub przy użyciu sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji, zobacz [Rejestrowanie analizy usługi Azure Storage](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja zabezpieczeń platformy Azure](https://docs.microsoft.com//azure/security/)
- [Bezpieczna dokumentacja dotycząca opracowywania](https://docs.microsoft.com/azure/security/develop/)oprogramowania.
