---
title: Service Fabric platformy Azure — konfigurowanie poświadczeń repozytorium kontenera
description: Skonfiguruj poświadczenia repozytorium, aby pobierać obrazy z rejestru kontenerów
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934993"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Skonfiguruj poświadczenia repozytorium dla aplikacji, aby pobierać obrazy kontenerów

Skonfiguruj uwierzytelnianie rejestru kontenerów, dodając `RepositoryCredentials` do sekcji `ContainerHostPolicies` manifestu aplikacji. Dodaj konto i hasło do rejestru kontenerów (*myregistry.azurecr.IO* w poniższym przykładzie), co umożliwia usłudze pobranie obrazu kontenera z repozytorium.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Zaleca się zaszyfrowanie hasła do repozytorium przy użyciu certyfikatu szyfrowania, który został wdrożony na wszystkich węzłach klastra. Gdy usługa Service Fabric wdroży pakiet usług w klastrze, zaszyfrowany tekst zostanie odszyfrowany za pomocą certyfikatu szyfrowania. Polecenie cmdlet Invoke-ServiceFabricEncryptText jest używane do utworzenia zaszyfrowanego tekstu dla hasła dodawanego do pliku ApplicationManifest.xml.
Aby uzyskać więcej informacji na temat certyfikatów i semantyki szyfrowania, zobacz [Zarządzanie kluczami tajnymi](service-fabric-application-secret-management.md) .

## <a name="configure-cluster-wide-credentials"></a>Konfigurowanie poświadczeń dla całego klastra

Service Fabric umożliwia skonfigurowanie poświadczeń dla całego klastra, które mogą być używane jako domyślne poświadczenia repozytorium przez aplikacje.

Tę funkcję można włączać lub wyłączać, dodając atrybut `UseDefaultRepositoryCredentials` do `ContainerHostPolicies` w ApplicationManifest. XML z wartością `true` lub `false`.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric następnie używa domyślnych poświadczeń repozytorium, które można określić w ClusterManifest w sekcji `Hosting`.  Jeśli `UseDefaultRepositoryCredentials` jest `true`, Service Fabric odczytuje następujące wartości z ClusterManifest:

* DefaultContainerRepositoryAccountName (ciąg)
* DefaultContainerRepositoryPassword (ciąg)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (ciąg)

Poniżej znajduje się przykład, co można dodać do sekcji `Hosting` w pliku ClusterManifestTemplate. JSON. Sekcję `Hosting` można dodać podczas tworzenia klastra lub później w ramach uaktualnienia konfiguracji. Aby uzyskać więcej informacji, zobacz [Zmienianie ustawień klastra platformy azure Service Fabric](service-fabric-cluster-fabric-settings.md) i zarządzanie wpisami [tajnymi aplikacji platformy Azure Service Fabric](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Użyj tokenów jako poświadczeń rejestru

Service Fabric obsługuje używanie tokenów jako poświadczeń do pobierania obrazów dla kontenerów.  Ta funkcja wykorzystuje *zarządzaną tożsamość* zestawu skalowania maszyn wirtualnych do uwierzytelniania w rejestrze, eliminując konieczność zarządzania poświadczeniami użytkowników.  Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) .  Korzystanie z tej funkcji wymaga wykonania następujących czynności:

1. Upewnij się, że dla maszyny wirtualnej jest włączona *tożsamość zarządzana przypisana przez system* .

    ![Azure Portal: Tworzenie opcji tożsamości zestawu skalowania maszyn wirtualnych](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Udziel uprawnień do zestawu skalowania maszyn wirtualnych w celu ściągania/odczytu obrazów z rejestru. W bloku Access Control (IAM) Azure Container Registry w Azure Portal Dodaj *przypisanie roli* dla maszyny wirtualnej:

    ![Dodawanie podmiotu maszyny wirtualnej do ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Następnie zmodyfikuj manifest aplikacji. W sekcji `ContainerHostPolicies` Dodaj `‘UseTokenAuthenticationCredentials=”true”`atrybutu.

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Flaga `UseDefaultRepositoryCredentials` ustawiona na wartość true, gdy `UseTokenAuthenticationCredentials` ma wartość true, spowoduje wystąpienie błędu podczas wdrażania.

## <a name="next-steps"></a>Następne kroki

* Zobacz więcej informacji o [uwierzytelnianiu rejestru kontenerów](../container-registry/container-registry-authentication.md).
