---
title: Rozwiązywanie problemów z blokadą konta w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać typowe problemy, które powodują, że konta użytkowników mają być blokowane w Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 29789f299f266c86d719d56cfbf8e262907f7264
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827080"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Rozwiązywanie problemów z blokadą konta przy użyciu domeny zarządzanej Azure AD Domain Services

Aby zapobiec powtarzaniu złośliwych prób logowania, usługa Azure AD DS blokuje konta po określonej wartości progowej. Ta blokada konta może również być spowodowana awarią bez zdarzenia ataku logowania. Na przykład jeśli użytkownik wielokrotnie wprowadzi nieprawidłowe hasło lub usługa próbuje użyć starego hasła, konto zostanie zablokowane.

Ten artykuł rozwiązywania problemów zawiera informacje o tym, dlaczego blokady kont są wykonywane i jak można skonfigurować zachowanie oraz jak przeglądać inspekcje zabezpieczeń w celu rozwiązywania problemów z blokadami zdarzeń.

## <a name="what-is-an-account-lockout"></a>Co to jest blokada konta?

Konto użytkownika w usłudze Azure AD DS jest blokowane, gdy osiągnięto określony próg nieudanych prób logowania. To zachowanie blokady konta zostało zaprojektowane z myślą o ochronie przed powtarzanymi próbami logowania z wykorzystaniem pełnego wymuszania, które mogą wskazywać na zautomatyzowany atak cyfrowy.

**Domyślnie jeśli w ciągu 2 minut występuje 5 nieudanych prób wprowadzenia hasła, konto jest zablokowane przez 30 minut.**

Domyślne progi blokady konta są konfigurowane przy użyciu szczegółowych zasad haseł. Jeśli masz określony zestaw wymagań, możesz zastąpić te domyślne progi blokady konta. Nie zaleca się jednak zwiększania limitów progu w celu zmniejszenia liczby blokad konta. Najpierw Rozwiąż problem z zachowaniem blokady konta.

### <a name="fine-grained-password-policy"></a>Szczegółowe zasady haseł

Szczegółowe zasady haseł (FGPPs) pozwalają stosować określone ograniczenia dotyczące zasad blokowania haseł i kont dla różnych użytkowników w domenie. SZCZEGÓŁOWYCH zasad haseł ma wpływ tylko na użytkowników utworzonych w usłudze Azure AD DS. Zasady haseł nie mają wpływu na użytkowników w chmurze i użytkowników domeny, które zostały zsynchronizowane z domeną zarządzaną AD DS platformy Azure z usługi Azure AD.

Zasady są dystrybuowane za pomocą skojarzenia grupy w domenie zarządzanej platformy Azure AD DS i wszelkie wprowadzone zmiany są stosowane podczas następnego logowania użytkownika. Zmiana zasad nie powoduje odblokowania konta użytkownika, które jest już zablokowane.

Aby uzyskać więcej informacji na temat szczegółowych zasad haseł, zobacz [Konfigurowanie zasad blokowania haseł i kont][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Najczęstsze przyczyny blokady konta

Najczęstsze przyczyny zablokowania konta, bez żadnego złośliwego zamiaru lub czynników, obejmują następujące scenariusze:

* **Użytkownik zablokował się.**
    * Czy po zmianie hasła użytkownik będzie kontynuował korzystanie z poprzedniego hasła? Domyślne zasady blokady konta 5 nieudanych prób w ciągu 2 minut mogą być przyczyną przypadkowego ponawiania próby starego hasła przez użytkownika.
* **Istnieje aplikacja lub usługa, która ma stare hasło.**
    * Jeśli konto jest używane przez aplikacje lub usługi, te zasoby mogą wielokrotnie próbować zalogować się przy użyciu starego hasła. To zachowanie powoduje, że konto jest zablokowane.
    * Spróbuj zminimalizować użycie konta w wielu różnych aplikacjach lub usługach, a następnie zarejestruj, gdzie są używane poświadczenia. Jeśli hasło konta zostanie zmienione, należy odpowiednio zaktualizować powiązane aplikacje lub usługi.
* **Hasło zostało zmienione w innym środowisku, a nowe hasło nie zostało jeszcze zsynchronizowane.**
    * Jeśli hasło do konta zostanie zmienione poza usługą Azure AD DS, na przykład w środowisku Premium AD DS, może upłynąć kilka minut, zanim zmieni się hasło za pomocą usługi Azure AD i usługi Azure AD DS.
    * Użytkownik próbujący zalogować się do zasobu za pomocą usługi Azure AD DS przed ukończeniem procesu synchronizacji haseł spowoduje jego zablokowanie.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Rozwiązywanie problemów z blokadami kont przy użyciu inspekcji zabezpieczeń

Aby rozwiązać problemy w przypadku wystąpienia zdarzeń blokady konta i lokalizacji, z których pochodzą, [Włącz inspekcje zabezpieczeń dla usługi Azure AD DS (obecnie w wersji zapoznawczej)][security-audit-events]. Zdarzenia inspekcji są przechwytywane tylko po włączeniu tej funkcji. W idealnym przypadku należy włączyć inspekcje zabezpieczeń *przed* wystawieniem problemu z blokadą konta. Jeśli konto użytkownika wielokrotnie ma problemy z blokadą, można włączyć inspekcje zabezpieczeń gotowe do następnego momentu.

Po włączeniu inspekcji zabezpieczeń następujące przykładowe zapytania pokazują, jak przejrzeć *zdarzenia blokady konta*, kod *4740*.

Wyświetl wszystkie zdarzenia blokady konta w ciągu ostatnich siedmiu dni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Wyświetl wszystkie zdarzenia blokady konta dla ostatnich siedmiu dni dla konta o nazwie *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Wyświetl wszystkie zdarzenia blokady konta w zakresie od 26 czerwca do 2019 o godzinie 9 i 1 lipca 2019 północy, posortowane rosnąco według daty i godziny:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat szczegółowych zasad haseł w celu dostosowania progów blokady konta, zobacz [Konfigurowanie zasad blokowania haseł i kont][configure-fgpp].

Jeśli nadal masz problemy z przyłączaniem maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure, [Znajdź pomoc i Otwórz bilet pomocy technicznej dla Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
