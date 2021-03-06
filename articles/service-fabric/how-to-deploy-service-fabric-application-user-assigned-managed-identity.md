---
title: Wdrażanie aplikacji przy użyciu tożsamości zarządzanej przypisanej przez użytkownika
description: W tym artykule opisano sposób wdrażania aplikacji Service Fabric przy użyciu tożsamości zarządzanej przypisanej przez użytkownika
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: a5eeaf0d6420fa36c0a78f7553ddfd82197d8ec4
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610339"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Wdróż aplikację Service Fabric przy użyciu tożsamości zarządzanej przypisanej przez użytkownika (wersja zapoznawcza)

Aby wdrożyć aplikację Service Fabric z tożsamością zarządzaną, aplikacja musi zostać wdrożona za pomocą Azure Resource Manager, zazwyczaj z szablonem Azure Resource Manager. Aby uzyskać więcej informacji na temat wdrażania Service Fabric aplikacji za poorednictwem Azure Resource Manager, zobacz [Zarządzanie aplikacjami i usługami jako Azure Resource Manager zasobami](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Aplikacje, które nie są wdrożone jako zasób platformy Azure, **nie mogą** mieć tożsamości zarządzanych. 
>
> Wdrożenie aplikacji Service Fabric z tożsamością zarządzaną jest obsługiwane przez interfejs API w wersji `"2019-06-01-preview"`. Możesz także użyć tej samej wersji interfejsu API dla typu aplikacji, wersji typu aplikacji i zasobów usługi.
>

## <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Aby włączyć aplikację z tożsamością przypisaną przez użytkownika, należy najpierw dodać właściwość **Identity** do zasobu aplikacji z typem **userAssigned** i PRZYWOŁYWANYMI tożsamościami przypisanymi przez użytkownika. Następnie Dodaj sekcję **managedIdentities** w sekcji **Właściwości** zasobu **aplikacji** , która zawiera listę przyjaznych nazw mapowania principalId dla każdej tożsamości przypisanej do użytkownika. Aby uzyskać więcej informacji na temat tożsamości przypisanych przez użytkownika [, zobacz Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Szablon aplikacji

Aby włączyć aplikację z tożsamością przypisaną przez użytkownika, najpierw Dodaj właściwość **Identity** do zasobu aplikacji z typem **userAssigned** i przywoływaną tożsamością użytkownika, a następnie Dodaj do niej obiekt **managedIdentities** , który zawiera listę przyjaznych nazw mapowania principalId dla każdej tożsamości przypisanej do użytkownika.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

W powyższym przykładzie nazwa zasobu tożsamości przypisanej do użytkownika jest używana jako przyjazna nazwa tożsamości zarządzanej dla aplikacji. W poniższych przykładach przyjęto, że rzeczywista przyjazna nazwa to "AdminUser".

### <a name="application-package"></a>Pakiet aplikacji

1. Dla każdej tożsamości zdefiniowanej w `managedIdentities` sekcji szablonu Azure Resource Manager Dodaj tag `<ManagedIdentity>` w manifeście aplikacji w sekcji **podmioty zabezpieczeń** . Atrybut `Name` musi pasować do właściwości `name` zdefiniowanej w sekcji `managedIdentities`.

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. W sekcji **ServiceManifestImport** Dodaj **IdentityBindingPolicy** dla usługi, która korzysta z tożsamości zarządzanej. Te zasady mapują `AdminUser` tożsamość na nazwę tożsamości specyficznej dla usługi, która musi zostać dodana do manifestu usługi później.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Zaktualizuj manifest usługi, aby dodać **ManagedIdentity** w sekcji **resources** o nazwie odpowiadającej `ServiceIdentityRef` w `IdentityBindingPolicy` manifestu aplikacji:

    **Servicemanifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Następne kroki

* [Jak używać tożsamości zarządzanej w Service Fabric kodzie aplikacji](how-to-managed-identity-service-fabric-app-code.md)
* [Jak udzielić Service Fabric aplikacji dostępu do innych zasobów platformy Azure](how-to-grant-access-other-resources.md)
