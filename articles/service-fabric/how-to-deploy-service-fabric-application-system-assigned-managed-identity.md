---
title: Wdrażanie aplikacji Service Fabric z przypisanym przez system
description: W tym artykule opisano sposób przypisywania tożsamości zarządzanej przypisanej przez system do aplikacji Service Fabric platformy Azure
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614829"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Wdrażanie aplikacji Service Fabric przy użyciu tożsamości zarządzanej przypisanej do systemu (wersja zapoznawcza)

Aby uzyskać dostęp do funkcji tożsamości zarządzanej dla aplikacji Service Fabric platformy Azure, należy najpierw włączyć usługę tokenu tożsamości zarządzanej w klastrze. Ta usługa jest odpowiedzialna za uwierzytelnianie aplikacji Service Fabric przy użyciu ich tożsamości zarządzanych i uzyskiwania tokenów dostępu w ich imieniu. Po włączeniu usługi zobaczysz ją w Service Fabric Explorer w sekcji **system** w okienku po lewej stronie, działając w obszarze Nazwa **sieci szkieletowej:/system/ManagedIdentityTokenService** obok innych usług systemowych.

> [!NOTE] 
> Wdrażanie aplikacji Service Fabric z tożsamościami zarządzanymi jest obsługiwane począwszy od wersji interfejsu API `"2019-06-01-preview"`. Możesz także użyć tej samej wersji interfejsu API dla typu aplikacji, wersji typu aplikacji i zasobów usługi. Minimalny obsługiwany Service Fabric środowiska uruchomieniowego to 6,5 ZASTOSUJESZ pakietu CU2. W programie additoin środowisko kompilacji/pakietu powinno również mieć zestaw SDK platformy w wersji ZASTOSUJESZ pakietu CU2 lub nowszej

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

### <a name="application-template"></a>Szablon aplikacji

Aby włączyć aplikację przy użyciu tożsamości zarządzanej przypisanej do systemu, Dodaj właściwość **Identity** do zasobu aplikacji z typem **systemAssigned** , jak pokazano w poniższym przykładzie:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Ta właściwość deklaruje (do Azure Resource Manager i dostawcy zasobów zarządzanych tożsamości i Service Fabric, że ten zasób ma niejawną (`system assigned`) tożsamość zarządzaną.

### <a name="application-and-service-package"></a>Pakiet aplikacji i usługi

1. Zaktualizuj manifest aplikacji, aby dodać element **ManagedIdentity** w sekcji **Principals** zawierający pojedynczy wpis, jak pokazano poniżej:

    **ApplicationManifest. XML**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Ta funkcja mapuje tożsamość przypisaną do aplikacji jako zasób na przyjazną nazwę, aby można było uzyskać dalsze przypisanie do usług składających się na aplikację. 

2. W sekcji **ServiceManifestImport** odpowiadającej usłudze, która ma przypisaną tożsamość zarządzaną, Dodaj element **IdentityBindingPolicy** , jak pokazano poniżej:

    **ApplicationManifest. XML**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Ten element przypisuje tożsamość aplikacji do usługi; bez tego przypisania usługa nie będzie mogła uzyskać dostępu do tożsamości aplikacji. W powyższym fragmencie kodu tożsamość `SystemAssigned` (która jest zastrzeżonym słowem kluczowym) jest zamapowana na definicję usługi pod przyjazną nazwą `WebAdmin`.

3. Zaktualizuj manifest usługi, aby dodać element **ManagedIdentity** w sekcji **resources** o nazwie odpowiadającej wartości ustawienia `ServiceIdentityRef` z definicji `IdentityBindingPolicy` w manifeście aplikacji:

    **Servicemanifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Jest to równoważne mapowanie tożsamości do usługi zgodnie z powyższym opisem, ale z perspektywy definicji usługi. Tożsamość jest przywoływana tutaj przez przyjazną nazwę (`WebAdmin`), zgodnie z deklaracją w manifeście aplikacji.

## <a name="next-steps"></a>Następne kroki
* Przejrzyj [obsługę tożsamości zarządzanych](./concepts-managed-identity.md) w usłudze Azure Service Fabric
* [Wdróż nowy](./configure-new-azure-service-fabric-enable-managed-identity.md) Klaster Service Fabric platformy Azure z obsługą tożsamości zarządzanej 
* [Włącz zarządzaną tożsamość](./configure-existing-cluster-enable-managed-identity-token-service.md) w istniejącym klastrze Service Fabric platformy Azure
* Korzystanie [z zarządzanej tożsamości aplikacji Service Fabric z kodu źródłowego](./how-to-managed-identity-service-fabric-app-code.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Przyznaj aplikacji Service Fabric platformy Azure dostęp do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)
