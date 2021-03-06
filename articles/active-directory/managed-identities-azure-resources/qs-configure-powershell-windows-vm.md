---
title: Konfigurowanie tożsamości zarządzanych na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547215"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule, korzystając z programu PowerShell, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie wirtualnej platformy Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) , jeśli jeszcze tego nie zrobiono.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

Ta sekcja zawiera informacje na temat włączania i wyłączania tożsamości zarządzanej przypisanej przez system przy użyciu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby można było utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników szybki start dotyczących maszyn wirtualnych platformy Azure, wykonując tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieciowej", "Tworzenie maszyny wirtualnej").
    
    Po wyświetleniu sekcji "Tworzenie maszyny wirtualnej" wprowadź niewielką modyfikację składni polecenia cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm) . Należy dodać parametr `-AssignIdentity:$SystemAssigned`, aby zainicjować obsługę administracyjną dla maszyny wirtualnej z włączoną obsługą tożsamości przypisanej do systemu, na przykład:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzAccount`. Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Najpierw pobierz właściwości maszyny wirtualnej za pomocą polecenia cmdlet `Get-AzVM`. Następnie aby włączyć tożsamość zarządzaną przypisaną przez system, użyj przełącznika `-AssignIdentity` w poleceniu cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) :

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Dodawanie tożsamości przypisanej do systemu maszyn wirtualnych do grupy

Po włączeniu tożsamości przypisanej przez system na maszynie wirtualnej można dodać ją do grupy.  Poniższa procedura umożliwia dodanie tożsamości przypisanej do grupy przez maszynę wirtualną.

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzAccount`. Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Pobierz i zanotuj `ObjectID` (zgodnie z opisem w polu `Id` zwracanych wartości) nazwy głównej usługi maszyny wirtualnej:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Pobierz i zanotuj `ObjectID` (jak określono w polu `Id` zwracanych wartości) grupy:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Dodaj nazwę główną usługi maszyny wirtualnej do grupy:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal wymaga tożsamości zarządzanej przez użytkownika, użyj następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzAccount`. Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Pobierz właściwości maszyny wirtualnej za pomocą polecenia cmdlet `Get-AzVM` i ustaw parametr `-IdentityType` na `UserAssigned`:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości zarządzanej przypisanej do systemu i nie ma żadnych tożsamości zarządzanych przez użytkownika, użyj następujących poleceń:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika z maszyny wirtualnej przy użyciu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej podczas jej tworzenia

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) [maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników szybki start dotyczących maszyn wirtualnych platformy Azure, wykonując tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieciowej", "Tworzenie maszyny wirtualnej"). 
  
    Po wyświetleniu sekcji "Tworzenie maszyny wirtualnej" wprowadź niewielką modyfikację składni polecenia cmdlet [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) . Dodaj `-IdentityType UserAssigned` i `-IdentityID` parametry, aby zainicjować obsługę administracyjną przy użyciu tożsamości przypisanej do użytkownika.  Zastąp `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.  Na przykład:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) [maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzAccount`. Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

   ```powershell
   Connect-AzAccount
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika przy użyciu polecenia cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) .  Zwróć uwagę na `Id` w danych wyjściowych, ponieważ będą one potrzebne w następnym kroku.

   > [!IMPORTANT]
   > Tworzenie tożsamości zarządzanych przypisanych przez użytkownika obsługuje tylko znaki alfanumeryczne, podkreślenie i łącznik (0-9 lub a-z lub A-Z, \_ lub). Ponadto nazwa powinna mieć ograniczoną długość od 3 do 128 znaków, aby przypisanie do maszyny wirtualnej/VMSS działało prawidłowo. Aby uzyskać więcej informacji [, zobacz często zadawane pytania i znane problemy](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Pobierz właściwości maszyny wirtualnej za pomocą polecenia cmdlet `Get-AzVM`. Następnie aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do maszyny wirtualnej platformy Azure, użyj przełącznika `-IdentityType` i `-IdentityID` w poleceniu cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) .  Wartość parametru`-IdentityId` jest `Id` zanotowaną w poprzednim kroku.  Zastąp `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.

   > [!WARNING]
   > Aby zachować wszystkie skojarzone z nimi tożsamości zarządzane przez użytkownika, przypisuje do maszyny wirtualnej, wykonaj zapytanie dotyczące właściwości `Identity` obiektu maszyny wirtualnej (na przykład `$vm.Identity`).  Jeśli zwracane są tożsamości zarządzane przypisane przez użytkownika, należy uwzględnić je w następującym poleceniu wraz z nową tożsamością zarządzaną przypisanego przez użytkownika, którą chcesz przypisać do maszyny wirtualnej.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .

Jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przez użytkownika, możesz usunąć wszystkie z nich oprócz ostatnich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>` jest właściwością nazwy tożsamości zarządzanej przypisanej przez użytkownika, która powinna pozostać na maszynie wirtualnej. Te informacje można znaleźć, badając Właściwość `Identity` obiektu maszyny wirtualnej.  Na przykład `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Jeśli maszyna wirtualna nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć wszystkie zarządzane przez użytkownika tożsamości, użyj następującego polecenia:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Jeśli maszyna wirtualna ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, możesz usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika, przełączając się do korzystania tylko z zarządzanych tożsamości przypisanych do systemu.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md)
- Aby zapoznać się z pełnymi przewodnikami szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz:
  
  - [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
