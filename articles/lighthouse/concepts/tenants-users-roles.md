---
title: Dzierżawy, role i użytkownicy w scenariuszach usługi Azure Lighthouse
description: Zapoznaj się z pojęciami Azure Active Directory dzierżawców, użytkowników i ról, a także, jak mogą one być używane w scenariuszach usługi Azure Lighthouse.
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 344e104201a83b3589dae6dbd3b02e49e4575e00
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156339"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Dzierżawy, role i użytkownicy w scenariuszach usługi Azure Lighthouse

Przed dołączeniem klientów do funkcji [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md)ważne jest, aby zrozumieć, jak działają dzierżawy, użytkownicy i role Azure Active Directory (Azure AD), a także jak mogą one być używane w scenariuszach usługi Azure Lighthouse.

*Dzierżawca* jest dedykowanym i zaufanym wystąpieniem usługi Azure AD. Zazwyczaj każda dzierżawa reprezentuje jedną organizację. Zarządzanie zasobami delegowanymi przez platformę Azure umożliwia logiczne projekcję zasobów z jednej dzierżawy do innej dzierżawy. Dzięki temu użytkownicy w dzierżawie zarządzającej (na przykład należącej do dostawcy usługi) mogą uzyskiwać dostęp do delegowanych zasobów w dzierżawie klienta lub umożliwiają [przedsiębiorstwom z wieloma dzierżawcami scentralizowanych operacji zarządzania](enterprise.md).

Aby można było zrealizować tę logiczną projekcję, należy *dołączyć* subskrypcję (lub co najmniej jedną grupę zasobów w ramach subskrypcji) w dzierżawie klienta w celu zarządzania zasobami delegowanymi przez platformę Azure. Ten proces dołączania można przeprowadzić [za pomocą szablonów Azure Resource Manager](../how-to/onboard-customer.md) lub przez [opublikowanie publicznej lub prywatnej oferty w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md).

Niezależnie od wybranej metody dołączania należy zdefiniować *autoryzacje*. Każda autoryzacja określa konto użytkownika w dzierżawie zarządzającej, które będzie miało dostęp do zasobów delegowanych, i wbudowaną rolę, która ustawia uprawnienia dla każdego z tych użytkowników dla tych zasobów.

## <a name="role-support-for-azure-delegated-resource-management"></a>Obsługa ról dla delegowanego zarządzania zasobami platformy Azure

Podczas definiowania autoryzacji każde konto użytkownika musi mieć przypisaną jedną z [wbudowanych ról kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/built-in-roles.md). Role niestandardowe i [role administratora klasycznej subskrypcji](../../role-based-access-control/classic-administrators.md) nie są obsługiwane.

Wszystkie [wbudowane role](../../role-based-access-control/built-in-roles.md) są obecnie obsługiwane przez delegowane zarządzanie zasobami platformy Azure z następującymi wyjątkami:

- Rola [właściciela](../../role-based-access-control/built-in-roles.md#owner) nie jest obsługiwana.
- Wszystkie wbudowane role z uprawnieniami [Dataactions](../../role-based-access-control/role-definitions.md#dataactions) nie są obsługiwane.
- Wbudowana rola [administratora dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) jest obsługiwana, ale tylko w ograniczonym celu [przypisywania ról do tożsamości zarządzanej w dzierżawie klienta](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Ta rola nie ma żadnych innych uprawnień zwykle przyznanych przez tę rolę. W przypadku zdefiniowania użytkownika z tą rolą należy również określić wbudowane role, które ten użytkownik może przypisać do zarządzanych tożsamości.

> [!NOTE]
> Po dodaniu nowej wbudowanej roli do platformy Azure można ją przypisać podczas dołączania [klienta przy użyciu szablonów Azure Resource Manager](../how-to/onboard-customer.md). Po [opublikowaniu oferty usługi zarządzanej](../how-to/publish-managed-services-offers.md)można uzyskać dostęp do nowo dodanej roli w Portal Cloud partner.

## <a name="best-practices-for-defining-users-and-roles"></a>Najlepsze rozwiązania dotyczące definiowania użytkowników i ról

Podczas tworzenia autoryzacji zalecamy następujące najlepsze rozwiązania:

- W większości przypadków użytkownik chce przypisać uprawnienia do grupy użytkowników usługi Azure AD lub nazwy głównej usług, a nie do serii poszczególnych kont użytkowników. Pozwala to na dodawanie lub usuwanie dostępu dla poszczególnych użytkowników bez konieczności aktualizacji i ponownego publikowania planu, gdy zmienią się wymagania dotyczące dostępu.
- Upewnij się, że przestrzegasz zasad najniższych uprawnień, aby użytkownicy mieli tylko uprawnienia potrzebne do ukończenia swoich zadań, pomagając w zmniejszeniu ryzyka wystąpienia błędów. Aby uzyskać więcej informacji, zobacz [zalecane praktyki dotyczące zabezpieczeń](../concepts/recommended-security-practices.md).
- Dołącz użytkownika z [rolą usuwania przydziału rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , aby można było później [usunąć dostęp do delegowania](../how-to/onboard-customer.md#remove-access-to-a-delegation) w razie potrzeby. Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.
- Upewnij się, że każdy użytkownik, który musi [wyświetlić stronę moi klienci w Azure Portal](../how-to/view-manage-customers.md) ma rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zalecanych rozwiązań w zakresie zabezpieczeń dla usługi Azure delegowane zarządzanie zasobami](recommended-security-practices.md)
- Dołączanie klientów do zarządzania zasobami delegowanymi przez platformę Azure za [pomocą szablonów Azure Resource Manager](../how-to/onboard-customer.md) lub [opublikowanie oferty usług zarządzanych w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md).
