---
title: Tworzenie węzłów wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć klaster usługi Azure Kubernetes Services (AKS), który używa węzłów wirtualnych do uruchamiania tego programu.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 2133d1aefe3363a2fb2af73c004e22ba0a0239ca
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901509"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Tworzenie i Konfigurowanie klastra usługi Azure Kubernetes Services (AKS) w celu używania węzłów wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Aby szybko skalować obciążenia aplikacji w klastrze usługi Azure Kubernetes Service (AKS), można użyć węzłów wirtualnych. Dzięki węzłom wirtualnym można szybko zainicjowania obsługi pojemności i płacisz na sekundę w czasie wykonywania. Nie musisz czekać, aż klaster Kubernetes ma wdrażać węzły obliczeniowe maszyny wirtualnej w celu uruchamiania dodatkowych zasobników. Węzły wirtualne są obsługiwane tylko w przypadku systemów i węzłów systemu Linux.

W tym artykule pokazano, jak utworzyć i skonfigurować zasoby sieci wirtualnej i klaster AKS, a następnie włączyć węzły wirtualne.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Węzły wirtualne umożliwiają komunikację sieciową między jednostkami, które działają w ACI i klastrze AKS. W celu zapewnienia tej komunikacji zostanie utworzona podsieć sieci wirtualnej i przypisane uprawnienia delegowane. Węzły wirtualne działają tylko w przypadku klastrów AKS utworzonych przy użyciu *zaawansowanej* sieci. Domyślnie klastry AKS są tworzone z użyciem sieci *podstawowej* . W tym artykule opisano sposób tworzenia sieci wirtualnej i podsieci, a następnie wdrażania klastra AKS, który korzysta z zaawansowanej sieci.

Jeśli nie korzystasz wcześniej z ACI, zarejestruj dostawcę usług w ramach subskrypcji. Stan rejestracji dostawcy ACI można sprawdzić za pomocą polecenia [AZ Provider list][az-provider-list] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Dostawca *Microsoft. ContainerInstance* powinien raportować jako *zarejestrowane*, jak pokazano w następujących przykładowych danych wyjściowych:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Jeśli dostawca jest wyświetlany jako *NotRegistered*, zarejestruj dostawcę przy użyciu polecenia [AZ Provider Register][az-provider-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Dostępność regionalna

W przypadku wdrożeń węzłów wirtualnych obsługiwane są następujące regiony:

* Australia Wschodnia (australiaeast)
* Środkowe stany USA (środkowe)
* Wschodnie stany USA (wschód)
* Wschodnie stany USA 2 (eastus2)
* Japonia Wschodnia (japaneast)
* Europa Północna (northeurope)
* Azja Południowo-Wschodnia (southeastasia)
* Zachodnio-środkowe stany USA (westcentralus)
* Europa Zachodnia (westeurope)
* Zachodnie stany USA (Zachodnie)
* Zachodnie stany USA 2 (westus2)

## <a name="known-limitations"></a>Znane ograniczenia
Funkcjonalność węzłów wirtualnych jest w dużym stopniu zależna od zestawu funkcji ACI. Następujące scenariusze nie są jeszcze obsługiwane w przypadku węzłów wirtualnych

* Używanie nazwy głównej usługi do ściągania obrazów ACR. [Obejście](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) polega na użyciu wpisów [tajnych Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Virtual Network ograniczenia](../container-instances/container-instances-vnet.md) , w tym wirtualne sieci równorzędne, zasady sieci Kubernetes i ruch wychodzący do Internetu za pomocą sieciowych grup zabezpieczeń.
* Inicjuj kontenery
* [Aliasy hostów](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) dla elementu exec w ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nie będzie wdrażać zasobników w węźle wirtualnym
* [Węzły systemu Windows Server (obecnie dostępne w wersji zapoznawczej w AKS)](windows-container-cli.md) nie są obsługiwane razem z węzłami wirtualnymi. Węzłów wirtualnych można użyć do zaplanowania kontenerów systemu Windows Server bez potrzeby węzłów systemu Windows Server w klastrze AKS.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create][az-network-vnet-create] . Poniższy przykład tworzy nazwę sieci wirtualnej *myVnet* z prefiksem adresu *10.0.0.0/8*i podsiecią o nazwie *myAKSSubnet*. Prefiks adresu tej podsieci domyślnie *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Teraz Utwórz dodatkową podsieć dla węzłów wirtualnych przy użyciu polecenia [AZ Network VNET Subnet Create][az-network-vnet-subnet-create] . Poniższy przykład tworzy podsieć o nazwie *myVirtualNodeSubnet* z prefiksem adresu *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Tę jednostkę usługi może automatycznie utworzyć interfejs wiersza polecenia platformy Azure lub portal. Inna możliwość to jej wstępne utworzenie i przypisanie dodatkowych uprawnień.

Utwórz jednostkę usługi za pomocą polecenia [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametr `--skip-assignment` umożliwia ograniczenie przypisywania dodatkowych uprawnień.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe są podobne do poniższego przykładu:

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Zwróć uwagę na wartości *appId* i *password*. Te wartości są używane w kolejnych krokach.

## <a name="assign-permissions-to-the-virtual-network"></a>Przypisywanie uprawnień do sieci wirtualnej

Aby umożliwić klastrowi korzystanie z sieci wirtualnej i zarządzanie nią, należy przyznać jednostce usługi AKS odpowiednie prawa do używania zasobów sieciowych.

Najpierw Pobierz identyfikator zasobu sieci wirtualnej za pomocą polecenia [AZ Network VNET show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Aby udzielić poprawnego dostępu do klastra AKS do korzystania z sieci wirtualnej, Utwórz przypisanie roli za pomocą polecenia [AZ role przypisanie Create][az-role-assignment-create] . Zastąp ciągi `<appId`> i `<vnetId>` wartościami określonymi w dwóch poprzednich krokach.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Klaster AKS jest wdrażany w podsieci AKS utworzonej w poprzednim kroku. Pobierz identyfikator tej podsieci za pomocą polecenia [AZ Network VNET Subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Użyj polecenia [AZ AKS Create][az-aks-create] , aby utworzyć klaster AKS. W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Zastąp `<subnetId>` IDENTYFIKATORem uzyskanym w poprzednim kroku, a następnie `<appId>` i `<password>` z 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Po kilku minutach polecenie zostanie zakończone i zwróci informacje o klastrze sformatowanym przy użyciu formatu JSON.

## <a name="enable-virtual-nodes-addon"></a>Włącz dodatek węzłów wirtualnych

Aby włączyć węzły wirtualne, teraz użyj polecenia [AZ AKS Enable-dodatkis][az-aks-enable-addons] . W poniższym przykładzie zastosowano podsieć o nazwie *myVirtualNodeSubnet* , która została utworzona w poprzednim kroku:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. Ten krok umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes do ich użycia.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
kubectl get nodes
```

Następujące przykładowe dane wyjściowe pokazują utworzony węzeł maszyny wirtualnej, a następnie węzeł wirtualny dla systemu Linux, *Virtual-Node-ACI-Linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdrażanie przykładowej aplikacji

Utwórz plik o nazwie `virtual-node.yaml` i skopiuj go do następującego YAML. Do zaplanowania kontenera w węźle są zdefiniowane [nodeSelector][node-selector] i [tolerowanie][toleration] .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Uruchom aplikację za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] .

```console
kubectl apply -f virtual-node.yaml
```

Użyj polecenia [polecenia kubectl Get][kubectl-get] -Binding z argumentem `-o wide`, aby wyprowadzić listę wartości z obszaru i zaplanowanego węzła. Należy zauważyć, że `aci-helloworld` pod zaplanowano w węźle `virtual-node-aci-linux`.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod przypisywany jest wewnętrzny adres IP z podsieci sieci wirtualnej platformy Azure delegowanej do użycia z węzłami wirtualnymi.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w Azure Container Registry, [Skonfiguruj i użyj wpisu tajnego Kubernetes][acr-aks-secrets]. Bieżące ograniczenie węzłów wirtualnych polega na tym, że nie można używać zintegrowanego uwierzytelniania podstawowego usługi Azure AD. Jeśli nie używasz wpisu tajnego, nie można uruchomić i utworzyć raportu o błędzie `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Przetestuj węzeł wirtualny pod

Aby przetestować pod kątem działania w węźle wirtualnym, przejdź do aplikacji demonstracyjnej za pomocą klienta sieci Web. Jako że pod przypisywanym wewnętrznym adresem IP można szybko przetestować to połączenie z innego elementu pod względem klastra AKS. Utwórz test pod i Dołącz do niego sesję terminalu:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
```

Zainstaluj `curl` w obszarze `apt-get`przy użyciu:

```console
apt-get update && apt-get install -y curl
```

Uzyskaj teraz dostęp do adresu Twojego użytkownika przy użyciu `curl`, takich jak *http://10.241.0.4* . Podaj własny wewnętrzny adres IP przedstawiony w poprzednim `kubectl get pods` polecenie:

```console
curl -L http://10.241.0.4
```

Zostanie wyświetlona aplikacja demonstracyjna, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zamknij sesję terminalu z testem pod `exit`. Po zakończeniu sesji, pod, jest usunięty.

## <a name="remove-virtual-nodes"></a>Usuń węzły wirtualne

Jeśli nie chcesz już używać węzłów wirtualnych, możesz je wyłączyć za pomocą polecenia [AZ AKS Disable-dodatkis][az aks disable-addons] . 

Najpierw usuń plik HelloWorld pod kontrolą uruchomienia w węźle wirtualnym:

```azurecli-interactive
kubectl delete -f virtual-node.yaml
```

Następujące przykładowe polecenie wyłącza węzły wirtualne systemu Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Teraz Usuń zasoby sieci wirtualnej i grupę zasobów:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zaplanowano w węźle wirtualnym i przypisano prywatny, wewnętrzny adres IP. Zamiast tego można utworzyć wdrożenie usługi i skierować ruch do swojego użytkownika przy użyciu modułu równoważenia obciążenia lub kontrolera transferu danych przychodzących. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowego kontrolera danych wejściowych w AKS][aks-basic-ingress].

Węzły wirtualne są często jednym składnikiem rozwiązania do skalowania w AKS. Aby uzyskać więcej informacji na temat skalowania rozwiązań, zobacz następujące artykuły:

- [Korzystanie z funkcji automatycznego skalowania w Kubernetes w poziomie][aks-hpa]
- [Korzystanie z automatycznego skalowania klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładem skalowania automatycznego dla węzłów wirtualnych][virtual-node-autoscale]
- [Przeczytaj więcej na temat biblioteki Open Source Kubelet wirtualnej][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
