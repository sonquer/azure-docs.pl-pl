---
title: Najlepsze praktyki dla operatorów — tożsamość w usłudze Azure Kubernetes Services (AKS)
description: Poznaj najlepsze rozwiązania operatora klastra dotyczące zarządzania uwierzytelnianiem i autoryzacją klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mlearned
ms.openlocfilehash: 35cded14977988c80455a9c28a0e01e7913d58f4
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069510"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze Azure Kubernetes Service (AKS)

Podczas wdrażania i konserwowania klastrów w usłudze Azure Kubernetes Service (AKS) należy zaimplementować metody zarządzania dostępem do zasobów i usług. Bez tych kontrolek konta mogą mieć dostęp do zasobów i usług, których nie potrzebują. Może być również trudno śledzić, który zestaw poświadczeń był używany do wprowadzania zmian.

Te najlepsze rozwiązania koncentrują się na sposobie zarządzania dostępem i tożsamością klastrów AKS przez operator klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Uwierzytelnianie użytkowników klastrów AKS za pomocą Azure Active Directory
> * Kontrola dostępu do zasobów za pomocą kontroli dostępu opartej na rolach (RBAC)
> * Używanie tożsamości zarządzanej do samodzielnego uwierzytelniania z innymi usługami

## <a name="use-azure-active-directory"></a>Korzystanie z usługi Azure Active Directory

**Wskazówki dotyczące najlepszych** rozwiązań — wdrażanie klastrów AKS z integracją z usługą Azure AD. Za pomocą usługi Azure AD scentralizowano składnik zarządzania tożsamościami. Wszelkie zmiany stanu konta użytkownika lub grupy są automatycznie aktualizowane w dostępie do klastra AKS. Użyj ról lub ClusterRoles i powiązań, jak opisano w następnej sekcji, aby ograniczyć liczbę użytkowników lub grup do najmniejszych wymaganych uprawnień.

Deweloperzy i właściciele aplikacji klastra Kubernetes potrzebują dostępu do różnych zasobów. Kubernetes nie oferuje rozwiązania do zarządzania tożsamościami, które pozwala kontrolować, którzy użytkownicy mogą korzystać z zasobów. Zamiast tego zwykle integrujesz klaster z istniejącym rozwiązaniem tożsamości. Azure Active Directory (AD) zapewnia rozwiązanie do zarządzania tożsamościami gotowe do używania w przedsiębiorstwie i można je zintegrować z klastrami AKS.

W przypadku klastrów zintegrowanych z usługą Azure AD w programie AKS tworzysz *role* lub *ClusterRoles* , które definiują uprawnienia dostępu do zasobów. Następnie można *powiązać* role z użytkownikami lub grupami z usługi Azure AD. Te Kubernetes kontroli dostępu opartej na rolach (RBAC) zostały omówione w następnej sekcji. Integrację usługi Azure AD i sposób kontrolowania dostępu do zasobów można zobaczyć na poniższym diagramie:

![Uwierzytelnianie na poziomie klastra dla integracji Azure Active Directory z usługą AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Deweloperzy są uwierzytelniani za pomocą usługi Azure AD.
1. Punkt końcowy wystawiania tokenów usługi Azure AD wystawia token dostępu.
1. Deweloper wykonuje akcję przy użyciu tokenu usługi Azure AD, takiego jak `kubectl create pod`
1. Kubernetes sprawdza token w Azure Active Directory i pobiera członkostwa w grupach deweloperów.
1. Są stosowane Kubernetes kontroli dostępu opartej na rolach (RBAC) i zasad klastra.
1. Żądanie dewelopera zakończyło się powodzeniem lub nie jest oparte na poprzedniej weryfikacji członkostwa w grupach usługi Azure AD oraz Kubernetes RBAC i zasadach.

Aby utworzyć klaster AKS, który używa usługi Azure AD, zobacz [integrowanie Azure Active Directory z usługą AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Korzystanie z kontroli dostępu opartej na rolach (RBAC)

**Wskazówki dotyczące najlepszych** rozwiązań — Użyj Kubernetes RBAC, aby zdefiniować uprawnienia, które użytkownicy lub grupy mają do zasobów w klastrze. Utwórz role i powiązania, które przypisują najmniej wymagane uprawnienia. Integruj z usługą Azure AD, aby wszelkie zmiany stanu użytkownika lub członkostwa w grupie były automatycznie aktualizowane, a dostęp do zasobów klastra jest aktualny.

W programie Kubernetes można zapewnić szczegółową kontrolę dostępu do zasobów w klastrze. Uprawnienia można definiować na poziomie klastra lub do określonych przestrzeni nazw. Można zdefiniować zasoby, które mogą być zarządzane, oraz z uprawnieniami. Te role są następnie stosowane do użytkowników lub grup z powiązaniem. Aby uzyskać więcej informacji na temat *ról*, *ClusterRoles*i *powiązań*, zobacz [Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)][aks-concepts-identity].

Przykładowo można utworzyć rolę, która przyznaje pełen dostęp do zasobów w przestrzeni nazw o nazwie *finanse-aplikacja*, jak pokazano w poniższym przykładzie YAML manifestu:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Następnie zostanie utworzony element Rolebinding, który wiąże użytkownika usługi Azure AD *developer1\@contoso.com* z roląbinding, jak pokazano w następującym manifeście YAML:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Po uwierzytelnieniu *developer1\@contoso.com* względem klastra AKS mają one pełne uprawnienia do zasobów w przestrzeni nazw *"Finanse aplikacji"* . Dzięki temu można logicznie oddzielić i kontrolować dostęp do zasobów. Kubernetes RBAC należy używać w połączeniu z usługą Azure AD — integrację, zgodnie z opisem w poprzedniej sekcji.

Aby dowiedzieć się, jak za pomocą grup usługi Azure AD kontrolować dostęp do zasobów Kubernetes za pomocą RBAC, zobacz [Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości Azure Active Directory w AKS][azure-ad-rbac].

## <a name="use-pod-identities"></a>Użyj tożsamości pod

**Wskazówki dotyczące najlepszych** rozwiązań — nie używaj stałych poświadczeń w obrębie zasobników ani obrazów kontenerów, ponieważ są one zagrożone ekspozycją lub nadużyciami. Zamiast tego należy użyć tożsamości pod, aby automatycznie żądać dostępu przy użyciu centralnego rozwiązania do obsługi tożsamości usługi Azure AD. Tożsamości pod są przeznaczone wyłącznie do użytku z magazynem systemu Linux i obrazami kontenerów.

Gdy wymagane jest uzyskanie dostępu do innych usług platformy Azure, takich jak Cosmos DB, Key Vault lub Blob Storage, wymaga to poświadczeń dostępu. Te poświadczenia dostępu można zdefiniować za pomocą obrazu kontenera lub wstrzyknąć jako wpis tajny Kubernetes, ale należy go ręcznie utworzyć i przypisać. Często poświadczenia są ponownie używane między zasobnikami i nie są regularnie obracane.

Zarządzane tożsamości dla zasobów platformy Azure (obecnie zaimplementowane jako skojarzony projekt AKS Open Source) pozwalają na automatyczne zażądanie dostępu do usług za pomocą usługi Azure AD. Nie Definiuj ręcznie poświadczeń dla zasobników, zamiast tego żądają tokenu dostępu w czasie rzeczywistym i mogą używać go do uzyskiwania dostępu do przypisanych do nich usług. W programie AKS dwa składniki są wdrażane przez operatora klastra w celu zezwolenia na używanie tożsamości zarządzanych przez y:

* **Serwer tożsamości zarządzania węzłami (NMI)** jest systemem, który działa jako elementu daemonset w każdym WĘŹLE klastra AKS. Serwer NMI nasłuchuje żądań pod względem usług platformy Azure.
* **Zarządzany kontroler tożsamości (MIC)** jest centralnym pod względem uprawnień do wysyłania zapytań do serwera interfejsu API Kubernetes i sprawdza mapowanie tożsamości platformy Azure, które odnosi się do pod.

Gdy usługa podst żąda dostępu do usługi platformy Azure, reguły sieci przekierowują ruch do serwera tożsamości zarządzania węzłami (NMI). Serwer NMI identyfikuje zasobniki, które żądają dostępu do usług platformy Azure na podstawie ich adresów zdalnych, a następnie wysyła zapytanie do zarządzanego kontrolera tożsamości (MIC). MIKROFON sprawdza mapowania tożsamości platformy Azure w klastrze AKS, a następnie serwer NMI żąda tokenu dostępu od Azure Active Directory (AD) w oparciu o mapowanie tożsamości pod. Usługa Azure AD zapewnia dostęp do serwera NMI, który jest zwracany do usługi pod. Ten token dostępu może być używany przez usługę pod, a następnie żądać dostępu do usług na platformie Azure.

W poniższym przykładzie deweloper tworzy pod, który używa tożsamości zarządzanej, aby zażądać dostępu do wystąpienia usługi Azure SQL Server:

![Tożsamości pod pozwalają na automatyczne zażądanie dostępu do innych usług](media/operator-best-practices-identity/pod-identities.png)

1. Operator klastra najpierw tworzy konto usługi, które może służyć do mapowania tożsamości, gdy identyfikatory zasobów żądają dostępu do usług.
1. Serwer NMI i mikrofon są wdrażane w celu przekazywania żądań dostępu do usługi Azure AD na żądanie.
1. Deweloper wdraża usługę pod za pomocą tożsamości zarządzanej, która żąda tokenu dostępu za pomocą serwera NMI.
1. Token jest zwracany do i używany do uzyskiwania dostępu do wystąpienia usługi Azure SQL Server.

> [!NOTE]
> Tożsamości zarządzane pod to projekt open source, który nie jest obsługiwany przez pomoc techniczną platformy Azure.

Aby korzystać z tożsamości pod, zobacz [Azure Active Directory tożsamości dla aplikacji Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Następne kroki

W tym artykule dotyczącym najlepszych rozwiązań opisano uwierzytelnianie i autoryzację klastra i zasobów. Aby zaimplementować niektóre z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Integracja Azure Active Directory z usługą AKS][aks-aad]
* [Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure z usługą AKS][aad-pod-identity]

Aby uzyskać więcej informacji na temat operacji klastra w programie AKS, zobacz następujące najlepsze rozwiązania:

* [Obsługa wielu dzierżawców i izolowania klastrów][aks-best-practices-cluster-isolation]
* [Podstawowe funkcje usługi Kubernetes Scheduler][aks-best-practices-scheduler]
* [Zaawansowane funkcje usługi Scheduler Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
