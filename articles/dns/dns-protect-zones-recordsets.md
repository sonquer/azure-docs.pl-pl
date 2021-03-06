---
title: Ochrona Strefy DNS i rekordów — Azure DNS
description: W tej ścieżce szkoleniowej Rozpocznij ochronę stref DNS i zestawów rekordów w Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: cef242f920178b9d2f4c4103310df21c80721ada
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526573"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chronić strefy i rekordy DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Strefy i rekordy DNS są istotnymi zasobami. Usunięcie strefy DNS lub pojedynczego rekordu DNS może spowodować awarię usługi. Należy pamiętać, że strefy i rekordy DNS są chronione przed nieautoryzowanymi lub przypadkowymi zmianami.

W tym artykule wyjaśniono, jak Azure DNS umożliwia ochronę prywatnych stref DNS i rekordów przed takimi zmianami.  Stosujemy dwie zaawansowane funkcje zabezpieczeń zapewniane przez Azure Resource Manager: [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) i [blokad zasobów](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Access Control oparte na rolach (RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem użytkowników, grup i zasobów platformy Azure. Za pomocą RBAC można przyznać poziom dostępu wymagany użytkownikom. Aby uzyskać więcej informacji o sposobach zarządzania dostępem przez funkcję RBAC, zobacz [co to jest Access Control oparte na rolach](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Rola współautor strefy DNS

Rola współautor strefy DNS to wbudowana rola służąca do zarządzania prywatnymi zasobami DNS. Ta rola stosowana do użytkownika lub grupy umożliwia im zarządzanie zasobami DNS.

*Grupa zasobów* zawiera pięć stref dla firmy Contoso Corporation. Przyznanie uprawnienia współautora strefy DNS administratora DNS tej grupie zasobów umożliwia pełną kontrolę nad tymi strefami DNS. Pozwala to uniknąć udzielania niepotrzebnych uprawnień. Administrator DNS nie może tworzyć ani zatrzymywać maszyn wirtualnych.

Najprostszym sposobem przypisywania uprawnień RBAC jest [za pośrednictwem Azure Portal](../role-based-access-control/role-assignments-portal.md).  

Otwórz przystawkę **Kontrola dostępu (IAM)** dla grupy zasobów, a następnie wybierz pozycję **Dodaj**, a następnie wybierz rolę **współautor strefy DNS** . Wybierz wymaganych użytkowników lub grupy, aby przyznać uprawnienia.

![Kontrola RBAC na poziomie grupy zasobów za pomocą Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Uprawnienia można także [przyznawać przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-rbac"></a>Kontrola RBAC na poziomie strefy

Reguły RBAC platformy Azure mogą być stosowane do subskrypcji, grupy zasobów lub pojedynczego zasobu. Ten zasób może być pojedynczą strefą DNS lub pojedynczym zestawem rekordów.

Na przykład grupa zasobów moja *zasobów* zawiera strefę *contoso.com* i podstrefę *Customers.contoso.com*. Rekordy CNAME są tworzone dla każdego konta klienta. Konto administratora używane do zarządzania rekordami CNAME ma przypisane uprawnienia do tworzenia rekordów w strefie *Customers.contoso.com* . Konto może zarządzać tylko *Customers.contoso.com* .

Uprawnienia kontroli RBAC na poziomie strefy można udzielać za pośrednictwem Azure Portal.  Otwórz przystawkę **Kontrola dostępu (IAM)** dla strefy, wybierz pozycję **Dodaj**, a następnie wybierz rolę **współautor strefy DNS** i wybierz wymaganych użytkowników lub grupy, aby przyznać uprawnienia.

![Kontrola RBAC na poziomie strefy DNS za pośrednictwem Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Uprawnienia można także [przyznawać przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>Poziom zestawu rekordów RBAC

Uprawnienia są stosowane na poziomie zestawu rekordów.  Użytkownik otrzymuje kontrolę do wpisów, których potrzebują, i nie może wprowadzać żadnych innych zmian.

Uprawnienia RBAC na poziomie rekordu można skonfigurować za pomocą Azure Portal przy użyciu przycisku **Access Control (IAM)** na stronie zestawu rekordów:

![Ustawianie kontroli RBAC na poziomie rekordu za pośrednictwem Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Na poziomie zestawu rekordów można także udzielić uprawnień RBAC [przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Role niestandardowe

Wbudowana rola współautor strefy DNS umożliwia pełną kontrolę nad zasobem DNS. Istnieje możliwość utworzenia własnych niestandardowych ról platformy Azure w celu zapewnienia bardziej precyzyjnej kontroli.

Konto używane do zarządzania rekordami CNAME ma przyznane uprawnienie do zarządzania rekordami CNAME. Konto nie może modyfikować rekordów innych typów. Konto nie może wykonywać operacji na poziomie strefy, takich jak usuwanie strefy.

Poniższy przykład przedstawia definicję roli niestandardowej do zarządzania tylko rekordami CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Właściwość Actions definiuje następujące uprawnienia specyficzne dla systemu DNS:

* `Microsoft.Network/dnsZones/CNAME/*` przyznaje pełną kontrolę nad rekordami CNAME
* `Microsoft.Network/dnsZones/read` przyznaje uprawnienia do odczytu stref DNS, ale nie zmodyfikuje ich, umożliwiając wyświetlenie strefy, w której jest tworzony rekord CNAME.

Pozostałe akcje są kopiowane z [wbudowanej roli współautor strefy DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Niestandardowa rola RBAC pozwala zapobiegać usuwaniu zestawów rekordów, mimo że nie zezwala na ich aktualizację, nie jest skuteczną kontrolą. Zapobiega to usuwaniu zestawów rekordów, ale nie uniemożliwia ich modyfikacji.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, włącznie z usunięciem wszystkich rekordów, aby pozostawić pusty zestaw rekordów. Ma to taki sam efekt jak usunięcie zestawu rekordów z punktu widzenia rozpoznawania nazw DNS.

Niestandardowych definicji ról nie można obecnie definiować za pośrednictwem Azure Portal. Rolę niestandardową opartą na tej definicji roli można utworzyć przy użyciu Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Można go również utworzyć za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Rolę można następnie przypisać w taki sam sposób jak wbudowane role, zgodnie z opisem we wcześniejszej części tego artykułu.

Aby uzyskać więcej informacji na temat tworzenia i przypisywania ról niestandardowych oraz zarządzania nimi, zobacz [role niestandardowe w usłudze Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Blokady zasobów

Azure Resource Manager obsługuje inny typ kontroli zabezpieczeń, umożliwiając zablokowanie zasobów. Blokady zasobów są stosowane do zasobu i obowiązują dla wszystkich użytkowników i ról. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/management/lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

Istnieją dwa typy blokad zasobów: **CanNotDelete** i **ReadOnly**. Te typy blokad można zastosować do strefy Prywatna strefa DNS lub do pojedynczego zestawu rekordów. W poniższych sekcjach opisano kilka typowych scenariuszy oraz sposób ich obsługi przy użyciu blokad zasobów.

### <a name="protecting-against-all-changes"></a>Ochrona przed wszystkimi zmianami

Aby zapobiec wprowadzaniu zmian, Zastosuj blokadę tylko do odczytu do strefy. Ta blokada uniemożliwia tworzenie nowych zestawów rekordów, a istniejące zestawy rekordów można modyfikować lub usuwać.

Blokady zasobów na poziomie strefy można tworzyć za pomocą Azure Portal.  Na stronie strefa DNS wybierz pozycję **blokady**, a następnie wybierz pozycję **+ Dodaj**:

![Blokady zasobów na poziomie strefy za pośrednictwem Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

Blokady zasobów na poziomie strefy można także utworzyć za pośrednictwem [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest):

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create):

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Ochrona pojedynczych rekordów

Aby zapobiec modyfikacji istniejącego rekordu DNS, należy zastosować blokadę tylko do odczytu zestawu rekordów.

> [!NOTE]
> Stosowanie blokady CanNotDelete do zestawu rekordów nie jest skuteczną kontrolą. Zapobiega to usuwaniu zestawu rekordów, ale nie uniemożliwia jego modyfikacji.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, włącznie z usunięciem wszystkich rekordów, aby pozostawić pusty zestaw rekordów. Ma to taki sam efekt jak usunięcie zestawu rekordów z punktu widzenia rozpoznawania nazw DNS.

Blokady zasobów na poziomie zestawu rekordów można obecnie skonfigurować tylko przy użyciu Azure PowerShell.  Nie są one obsługiwane w interfejsie wiersza polecenia Azure Portal ani platformy Azure.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Ochrona przed usunięciem strefy

Gdy strefa zostanie usunięta w Azure DNS, wszystkie zestawy rekordów w strefie zostaną usunięte.  Tej operacji nie można cofnąć. Przypadkowe usunięcie strefy krytycznej może mieć znaczący wpływ na działalność biznesową.  Ważne jest, aby chronić przed przypadkowym usunięciem strefy.

Zastosowanie blokady CanNotDelete do strefy uniemożliwia usunięcie strefy. Blokady są dziedziczone przez zasoby podrzędne. Blokada uniemożliwia usunięcie jakichkolwiek zestawów rekordów w strefie. Zgodnie z opisem w powyższej uwadze nie obowiązuje, ponieważ rekordy nadal mogą być usuwane z istniejących zestawów rekordów.

Alternatywnie należy zastosować blokadę CanNotDelete do zestawu rekordów w strefie, na przykład zestawu rekordów SOA. Strefa nie jest usuwana bez również usuwania zestawów rekordów. Ta blokada chroni przed usunięciem strefy, pozostawiając jednocześnie możliwość modyfikacji zestawów rekordów w strefie. Jeśli podjęto próbę usunięcia strefy, Azure Resource Manager wykryje to usunięcie. Usunięcie spowoduje również usunięcie zestawu rekordów SOA, Azure Resource Manager blokuje wywołanie, ponieważ rekord SOA jest zablokowany.  Żadne zestawy rekordów nie są usuwane.

Następujące polecenie programu PowerShell tworzy blokadę CanNotDelete względem rekordu SOA danej strefy:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Inną opcją zapobiegania przypadkowemu usuwaniu strefy jest użycie roli niestandardowej. Ta rola gwarantuje, że konta używane do zarządzania strefami nie mają uprawnień do usuwania strefy. 

W przypadku konieczności usunięcia strefy można wymusić usunięcie dwuetapowe:

 - Najpierw Udziel uprawnień do usuwania strefy
 - Następnie Udziel uprawnień do usunięcia strefy.

Rola niestandardowa działa dla wszystkich stref, do których uzyskuje dostęp te konta. Konta z uprawnieniami do usuwania strefy, takie jak właściciel subskrypcji, nadal mogą przypadkowo usunąć strefę.

Istnieje możliwość użycia obu metod — blokad zasobów i ról niestandardowych — w tym samym czasie, co w przypadku podejścia do ochrony strefy DNS.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z usługą RBAC, zobacz Wprowadzenie do [zarządzania dostępem w Azure Portal](../role-based-access-control/overview.md).
* Aby uzyskać więcej informacji na temat pracy z blokadami zasobów, zobacz [blokowanie zasobów przy użyciu Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
