---
title: Statyczny adres IP dla ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć statyczny publiczny adres IP dla ruchu wychodzącego w klastrze usługi Azure Kubernetes Service (AKS) i korzystać z niego
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 67471d688e64244067a7537bc87c379da4a69c03
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696359"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Użyj statycznego publicznego adresu IP dla ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)

Domyślnie adres IP ruchu wychodzącego z klastra usługi Azure Kubernetes Service (AKS) jest losowo przypisywany. Ta konfiguracja nie jest idealna, gdy konieczne jest zidentyfikowanie adresu IP w celu uzyskania dostępu do usług zewnętrznych, na przykład. Zamiast tego może być konieczne przypisanie statycznego adresu IP, który może być listy dozwolonych na potrzeby dostępu do usługi.

W tym artykule opisano sposób tworzenia i używania statycznego publicznego adresu IP do użycia z ruchem wychodzącym w klastrze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Ruch wychodzący — Omówienie

Ruch wychodzący z klastra AKS jest zgodny z [konwencjami Azure Load Balancer][outbound-connections]. Przed utworzeniem pierwszej usługi Kubernetes typu `LoadBalancer` , węzły agenta w klastrze AKS nie będą częścią żadnej puli Azure Load Balancer. W tej konfiguracji węzły nie mają publicznego adresu IP na poziomie wystąpienia. Platforma Azure tłumaczy przepływ wychodzący na publiczny adres IP, który nie jest konfigurowalny ani deterministyczny.

Po utworzeniu usługi Kubernetes typu `LoadBalancer` , węzły agentów są dodawane do puli Azure Load Balancer. W przypadku przepływu wychodzącego platforma Azure tłumaczy ją na pierwszy publiczny adres IP skonfigurowany w ramach modułu równoważenia obciążenia. Ten publiczny adres IP jest prawidłowy tylko dla cykl życia tego zasobu. Po usunięciu usługi równoważenia obciążenia Kubernetes zostanie również usunięty skojarzony z nią moduł równoważenia i adres IP. Jeśli chcesz przypisać określony adres IP lub zachować adres IP dla ponownie wdrożonych usług Kubernetes, możesz utworzyć statyczny publiczny adres IP i używać go.

## <a name="create-a-static-public-ip"></a>Tworzenie statycznego publicznego adresu IP

Pobierz nazwę grupy zasobów za pomocą polecenia [AZ AKS show][az-aks-show] i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera grupę zasobów węzła dla nazwy klastra AKS *myAKSCluster* w grupie zasobów nazwa *zasobu:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz Utwórz statyczny publiczny adres IP za pomocą polecenia [AZ Network Public IP Create][az-network-public-ip-create] . Określ nazwę grupy zasobów węzła uzyskaną w poprzednim poleceniu, a następnie nazwę zasobu adresu IP, na przykład *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Adres IP jest wyświetlany, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Możesz później uzyskać publiczny adres IP za pomocą polecenia [AZ Network Public-IP list][az-network-public-ip-list] . Określ nazwę grupy zasobów węzła, a następnie zapytanie o *adres IP* , jak pokazano w następującym przykładzie:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Aby utworzyć usługę ze statycznym publicznym adresem IP, Dodaj `loadBalancerIP` Właściwość i wartość statycznego publicznego adresu IP do manifestu YAML. Utwórz plik o nazwie `egress-service.yaml` i skopiuj w następującym YAML. Podaj własny publiczny adres IP utworzony w poprzednim kroku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Utwórz usługę i wdrożenie za pomocą `kubectl apply` polecenia.

```console
kubectl apply -f egress-service.yaml
```

Ta usługa konfiguruje nowy adres IP frontonu na Azure Load Balancer. Jeśli nie skonfigurowano żadnych innych adresów IP, **cały** ruch wychodzący powinien teraz korzystać z tego adresu. W przypadku skonfigurowania wielu adresów na Azure Load Balancer ruch wychodzący używa pierwszego adresu IP w ramach tego modułu równoważenia obciążenia.

## <a name="verify-egress-address"></a>Weryfikuj adres ruchu wychodzącego

Aby sprawdzić, czy statyczny publiczny adres IP jest używany, można użyć usługi wyszukiwania DNS, takiej jak `checkip.dyndns.org`.

Rozpocznij i Dołącz do podstawowego *Debian* pod:

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Aby uzyskać dostęp do witryny sieci Web z poziomu kontenera, `apt-get` Użyj do `curl` zainstalowania w kontenerze.

```console
apt-get update && apt-get install curl -y
```

Teraz można użyć zawieszania, aby uzyskać dostęp do witryny *checkIP.dyndns.org* . Adres IP ruchu wychodzącego jest wyświetlany, jak pokazano w poniższych przykładowych danych wyjściowych. Ten adres IP jest zgodny ze statycznym publicznym adresem IP utworzonym i zdefiniowanym dla usługi równoważenia obciążenia:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Następne kroki

Aby uniknąć konserwacji wielu publicznych adresów IP na Azure Load Balancer, zamiast tego można użyć kontrolera transferu danych przychodzących. Kontrolery transferu danych przychodzących zapewniają dodatkowe korzyści, takie jak zakończenie protokołu SSL/TLS, obsługa ponownego zapisywania identyfikatorów URI i międzystrumieniowe szyfrowanie SSL/TLS. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowego kontrolera danych wejściowych w AKS][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
