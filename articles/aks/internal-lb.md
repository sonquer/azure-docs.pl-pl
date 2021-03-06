---
title: Tworzenie wewnętrznego modułu równoważenia obciążenia w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć i użyć wewnętrznego modułu równoważenia obciążenia, aby udostępnić swoje usługi za pomocą usługi Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 8b0b0358534c30407c9841d5c23560623545cf64
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045011"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Korzystanie z wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service (AKS)

Aby ograniczyć dostęp do aplikacji w usłudze Azure Kubernetes Service (AKS), można utworzyć i użyć wewnętrznego modułu równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępna tylko dla aplikacji działających w tej samej sieci wirtualnej co klaster Kubernetes. W tym artykule opisano sposób tworzenia i używania wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer jest dostępny w dwóch jednostkach SKU — *podstawowa* i *standardowa*. Domyślnie standardowa jednostka SKU jest używana podczas tworzenia klastra AKS.  Podczas tworzenia usługi przy użyciu typu jako modułu równoważenia obciążenia otrzymasz ten sam typ LB jak podczas aprowizacji klastra. Aby uzyskać więcej informacji, zobacz [porównanie jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Nazwa główna usługi klastra AKS wymaga uprawnień do zarządzania zasobami sieci, jeśli używana jest istniejąca podsieć lub Grupa zasobów. Ogólnie rzecz biorąc Przypisz rolę *współautor sieci* do nazwy głównej usługi w odniesieniu do zasobów delegowanych. Aby uzyskać więcej informacji o uprawnieniach, zobacz [delegowanie dostępu AKS do innych zasobów platformy Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Aby utworzyć wewnętrzny moduł równoważenia obciążenia, Utwórz manifest usługi o nazwie `internal-lb.yaml` przy użyciu *modułu równoważenia* obciążenia typu usługi i *Azure-load-module —* adnotacja wewnętrzna, jak pokazano w następującym przykładzie:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Wdróż wewnętrzny moduł równoważenia obciążenia przy użyciu [polecenia kubectl Zastosuj][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f internal-lb.yaml
```

Moduł równoważenia obciążenia platformy Azure jest tworzony w grupie zasobów węzła i połączony z tą samą siecią wirtualną co klaster AKS.

Po wyświetleniu szczegółów usługi adres IP wewnętrznego modułu równoważenia obciążenia jest wyświetlany w kolumnie *zewnętrzny adres IP* . W tym kontekście *zewnętrzny* jest w odniesieniu do zewnętrznego interfejsu modułu równoważenia obciążenia, a nie otrzymuje publicznego zewnętrznego adresu IP. Zmiana adresu IP z *\<oczekujących\>* na rzeczywisty wewnętrzny adres IP może potrwać minutę lub dwa, jak pokazano w następującym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Określ adres IP

Jeśli chcesz użyć określonego adresu IP z wewnętrznym modułem równoważenia obciążenia, Dodaj właściwość *loadBalancerIP* do manifestu YAML modułu równoważenia obciążenia. Określony adres IP musi znajdować się w tej samej podsieci co klaster AKS i nie może być już przypisany do zasobu.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Po wdrożeniu i wyświetleniu szczegółów usługi adres IP w kolumnie *External-IP* odzwierciedla określony adres IP:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Korzystanie z sieci prywatnych

Podczas tworzenia klastra AKS można określić zaawansowane ustawienia sieci. Takie podejście umożliwia wdrożenie klastra w istniejącej sieci wirtualnej platformy Azure i podsieci. Jednym z scenariuszy jest wdrożenie klastra AKS w sieci prywatnej połączonej ze środowiskiem lokalnym i uruchamianie usług dostępnych wewnętrznie. Aby uzyskać więcej informacji, zobacz Konfigurowanie własnych podsieci sieci wirtualnej za pomocą usługi [korzystającą wtyczki kubenet][use-kubenet] lub [Azure CNI][advanced-networking].

Nie są wymagane żadne zmiany w poprzednich krokach w celu wdrożenia wewnętrznego modułu równoważenia obciążenia w klastrze AKS, który korzysta z sieci prywatnej. Moduł równoważenia obciążenia jest tworzony w tej samej grupie zasobów co klaster AKS, ale połączony z prywatną siecią wirtualną i podsiecią, jak pokazano w następującym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Może być konieczne przyznanie jednostce usługi dla klastra AKS roli *współautor sieci* do grupy zasobów, w której są wdrożone zasoby sieci wirtualnej platformy Azure. Wyświetl nazwę główną usługi za pomocą [AZ AKS show][az-aks-show], taką jak `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Aby utworzyć przypisanie roli, użyj polecenia [AZ role przypisanie Create][az-role-assignment-create] .

## <a name="specify-a-different-subnet"></a>Określ inną podsieć

Aby określić podsieć dla modułu równoważenia obciążenia, Dodaj do usługi adnotację *Azure-load-module-Internal-Subnet* . Określona podsieć musi znajdować się w tej samej sieci wirtualnej co klaster AKS. Po wdrożeniu *zewnętrzny adres IP* modułu równoważenia obciążenia jest częścią określonej podsieci.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Usuwanie usługi równoważenia obciążenia

Po usunięciu wszystkich usług, które korzystają z wewnętrznego modułu równoważenia obciążenia, sam moduł równoważenia obciążenia również zostaje usunięty.

Możesz również bezpośrednio usunąć usługę jako z dowolnym zasobem Kubernetes, takim jak `kubectl delete service internal-app`, co spowoduje również usunięcie bazowego modułu równoważenia obciążenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach Kubernetes Services w [dokumentacji usług Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
