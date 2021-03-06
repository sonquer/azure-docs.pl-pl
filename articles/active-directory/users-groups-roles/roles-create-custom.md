---
title: Tworzenie ról niestandardowych w kontroli dostępu opartej na rolach usługi Azure AD | Microsoft Docs
description: Utwórz i przypisz niestandardowe role usługi Azure AD z zakresem zasobów dla zasobów Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025272"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Tworzenie i przypisywanie roli niestandardowej w Azure Active Directory

W tym artykule opisano sposób tworzenia nowych ról niestandardowych w usłudze Azure Active Directory (Azure AD). Podstawowe informacje na temat ról niestandardowych można znaleźć w temacie [role niestandardowe — Omówienie](roles-custom-overview.md). Rolę można przypisać zarówno w zakresie poziomu katalogu, jak i w zakresie zasobów rejestracji aplikacji.

Role niestandardowe można tworzyć na karcie [role i Administratorzy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na stronie Przegląd usługi Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Tworzenie roli w Azure Portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Utwórz nową rolę niestandardową, aby udzielić dostępu do zarządzania rejestracjami aplikacji

1. Zaloguj się do [Centrum administracyjnego usługi Azure ad](https://aad.portal.azure.com) z uprawnieniami administrator ról uprzywilejowanych lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory** > **role i Administratorzy** > **nową rolę niestandardową**.

   ![Tworzenie lub edytowanie ról na stronie role i Administratorzy](./media/roles-create-custom/new-custom-role.png)

1. Na karcie **podstawowe** Podaj nazwę i opis roli, a następnie kliknij przycisk **dalej**.

   ![Podaj nazwę i opis roli niestandardowej na karcie podstawowe](./media/roles-create-custom/basics-tab.png)

1. Na karcie **uprawnienia** wybierz uprawnienia wymagane do zarządzania podstawowymi właściwościami i właściwościami poświadczeń rejestracji aplikacji. Aby uzyskać szczegółowy opis każdego z tych uprawnień, zobacz [podtypy rejestracji aplikacji i uprawnienia w Azure Active Directory](./roles-custom-available-permissions.md).
   1. Najpierw wprowadź wartość "poświadczenia" na pasku wyszukiwania i wybierz uprawnienie `microsoft.directory/applications/credentials/update`.

      ![Wybierz uprawnienia roli niestandardowej na karcie uprawnienia](./media/roles-create-custom/permissions-tab.png)

   1. Następnie wprowadź wartość "podstawowa" na pasku wyszukiwania, wybierz uprawnienie `microsoft.directory/applications/basic/update`, a następnie kliknij przycisk **dalej**.
1. Na karcie **Recenzja + tworzenie** Przejrzyj uprawnienia i wybierz pozycję **Utwórz**.

Rola niestandardowa zostanie wyświetlona na liście dostępnych ról do przypisania.

## <a name="create-a-role-using-powershell"></a>Tworzenie roli przy użyciu programu PowerShell

### <a name="prepare-powershell"></a>Przygotowywanie programu PowerShell

Najpierw należy [pobrać moduł programu PowerShell usługi Azure AD Preview](https://www.powershellgallery.com/packages/AzureADPreview).

Aby zainstalować moduł Azure AD PowerShell, użyj następujących poleceń:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Utwórz rolę niestandardową

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Przypisywanie roli niestandardowej przy użyciu programu Azure AD PowerShell

Przypisz rolę przy użyciu poniższego skryptu programu PowerShell:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Tworzenie roli przy użyciu interfejs API programu Graph

1. Utwórz definicję roli.

    Żądanie HTTP, aby utworzyć niestandardową definicję roli.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Treść

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. Utwórz przypisanie roli.

    Żądanie HTTP, aby utworzyć niestandardową definicję roli.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Treść

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Przypisywanie roli niestandardowej do zasobu

Podobnie jak wbudowane role, role niestandardowe są domyślnie przypisywane do zakresu całego całej organizacji, aby przyznać uprawnienia dostępu do wszystkich rejestracji aplikacji w organizacji. Jednak w przeciwieństwie do wbudowanych ról role niestandardowe można także przypisać do zakresu pojedynczego zasobu usługi Azure AD. Dzięki temu użytkownik może przyznać użytkownikowi uprawnienia do aktualizowania poświadczeń i podstawowych właściwości pojedynczej aplikacji bez konieczności tworzenia drugiej roli niestandardowej.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień dewelopera aplikacji w organizacji usługi Azure AD.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz rejestrację aplikacji, do której chcesz udzielić dostępu do zarządzania. Może być konieczne wybranie opcji **wszystkie aplikacje** , aby wyświetlić pełną listę rejestracji aplikacji w organizacji usługi Azure AD.

    ![Wybierz rejestrację aplikacji jako zakres zasobów dla przypisania roli](./media/roles-create-custom/appreg-all-apps.png)

1. W obszarze Rejestracja aplikacji wybierz pozycję **role i Administratorzy**. Jeśli jeszcze tego nie zrobiono, instrukcje znajdują się w [poprzedniej procedurze](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Wybierz rolę, aby otworzyć stronę **przypisania** .
1. Wybierz pozycję **Dodaj przypisanie** , aby dodać użytkownika. Użytkownikowi zostaną przydzielone wszystkie uprawnienia tylko do wybranej rejestracji aplikacji.

## <a name="next-steps"></a>Następne kroki

- Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](directory-assign-admin-roles.md).
- Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
