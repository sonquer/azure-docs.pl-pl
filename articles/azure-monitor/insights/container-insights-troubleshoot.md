---
title: Jak rozwiązywanie problemów z usługi Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozwiązywać problemy i rozwiązywanie problemów z usługą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403381"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Rozwiązywanie problemów z usługi Azure Monitor dla kontenerów

Po skonfigurowaniu monitorowania klastra usługi Azure Kubernetes Service (AKS) z usługą Azure Monitor dla kontenerów, można napotkać problem uniemożliwia zbieranie danych i raportowania stanu. Ten artykuł szczegółowo opisuje niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Błąd autoryzacji podczas dołączania lub aktualizowania operacji
Podczas włączania Azure Monitor dla kontenerów lub aktualizowania klastra w celu obsługi zbierania metryk może zostać wyświetlony błąd podobny do poniższego: *klient < tożsamość użytkownika > "z identyfikatorem obiektu" < objectid użytkownika > "nie ma autoryzacji do wykonania akcji" Microsoft. Authorization/roleAssignments/Write "w zakresie*

Podczas procesu dołączania lub aktualizacji należy podjąć próbę przypisania roli **wydawcy metryk monitorowania** do zasobu klastra. Użytkownik inicjujący proces włączania Azure Monitor dla kontenerów lub aktualizacja do obsługi kolekcji metryk musi mieć dostęp do uprawnienia **Microsoft. Authorization/roleAssignments/Write** w zakresie zasobów klastra AKS. Dostęp do tego uprawnienia mają tylko członkowie roli wbudowanej **administratora dostępu** **właściciela** i użytkownika. Jeśli zasady zabezpieczeń wymagają przypisania uprawnień na poziomie szczegółowości, zalecamy wyświetlenie [ról niestandardowych](../../role-based-access-control/custom-roles.md) i przypisanie ich do użytkowników, którzy ich potrzebują. 

Możesz również ręcznie udzielić tej roli z Azure Portal, wykonując następujące czynności:

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Kubernetes**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Azure Kubernetes**.
3. Na liście klastrów Kubernetes wybierz jedną z listy.
2. W menu po lewej stronie kliknij pozycję **Kontrola dostępu (IAM)** .
3. Wybierz pozycję **+ Dodaj** , aby dodać przypisanie roli i wybierz rolę **wydawcy metryk monitorowania** i w obszarze **Wybierz** typ **AKS** , aby filtrować wyniki tylko do podmiotów usługi klastrów zdefiniowanych w ramach subskrypcji. Wybierz z listy odpowiedni dla tego klastra.
4. Wybierz **Zapisz** zakończenie przypisanie roli. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Usługa Azure Monitor dla kontenerów jest włączona, ale nie zgłasza żadnych informacji
Jeśli Azure Monitor dla kontenerów zostanie pomyślnie włączona i skonfigurowana, ale nie będzie można wyświetlić informacji o stanie lub żadne wyniki nie są zwracane z zapytania dziennika, można zdiagnozować problem, wykonując następujące czynności: 

1. Sprawdź stan agenta, uruchamiając polecenie: 

    `kubectl get ds omsagent --namespace=kube-system`

    Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Sprawdź stan wdrożenia za pomocą wersji agenta *06072018* lub później za pomocą polecenia:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Dane wyjściowe powinny podobne do następującego przykładu, co oznacza, że została prawidłowo wdrożona:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Sprawdź stan zasobników, aby sprawdzić, czy działa przy użyciu polecenia: `kubectl get pods --namespace=kube-system`

    Dane wyjściowe powinny być podobne do następującego przykładu, ze stanem *systemem* dla omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Sprawdź dzienniki agenta. Gdy agent konteneryzowanych zostanie wdrożona, uruchamia szybkie sprawdzenie, uruchamiając polecenia OMI i wyświetla wersję agenta i dostawcy. 

5. Aby sprawdzić, czy Agent został wdrożony pomyślnie, uruchom polecenie: `kubectl logs omsagent-484hw --namespace=kube-system`

    Stan powinien wyglądać następująco:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Komunikaty o błędach

Poniższa tabela zawiera podsumowanie znanych błędów, które mogą wystąpić podczas korzystania z usługi Azure Monitor dla kontenerów.

| Komunikaty o błędach  | Działanie |  
| ---- | --- |  
| Komunikat o błędzie `No data for selected filters`  | Może potrwać kilka nawiązać monitorowania przepływu danych w przypadku nowo utworzonego klastrów. Zezwalaj na wyświetlanie danych dla klastra co najmniej 10 do 15 minut. |   
| Komunikat o błędzie `Error retrieving data` | Podczas konfigurowania klastra usługi Azure Kubernetes na potrzeby monitorowania kondycji i wydajności jest nawiązywane połączenie między klastrem i obszarem roboczym usługi Azure Log Analytics. Obszar roboczy usługi Log Analytics służy do przechowywania wszystkich danych monitorowania dla klastra. Ten błąd może wystąpić, gdy obszar roboczy Log Analytics został usunięty. Sprawdź, czy obszar roboczy został usunięty, a jeśli był, należy ponownie włączyć monitorowanie klastra przy użyciu Azure Monitor dla kontenerów i określić istniejący lub utworzyć nowy obszar roboczy. Aby ponownie włączyć, należy [wyłączyć](container-insights-optout.md) monitorowanie klastra i ponownie [włączyć](container-insights-enable-new-cluster.md) Azure monitor dla kontenerów. |  
| `Error retrieving data` Po dodaniu usługi Azure Monitor dla kontenerów za pomocą interfejsu wiersza polecenia az aks | Po włączeniu monitorowania przy użyciu `az aks cli`Azure Monitor dla kontenerów mogą nie być poprawnie wdrożone. Sprawdź, czy rozwiązanie zostało wdrożone. Aby to zrobić, przejdź do obszaru roboczego usługi Log Analytics i sprawdzić, czy rozwiązanie jest dostępne, wybierając **rozwiązania** w okienku po lewej stronie. Aby rozwiązać ten problem, należy ponownie wdrożyć rozwiązanie, postępując zgodnie z instrukcjami wyświetlanymi [sposób wdrażania usługi Azure Monitor dla kontenerów](container-insights-onboard.md) |  

Aby pomóc w zdiagnozowaniu problemu, udostępniliśmy skrypt rozwiązywania problemów dostępne [tutaj](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor dla ReplicaSet kontenerów agentów nie są zaplanowani w klastrze Kubernetes innym niż Azure

Azure Monitor dla agentów kontenerów ReplicaSeta jest zależność od następujących selektorów węzłów w węzłach procesu roboczego (lub agenta) na potrzeby planowania:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Jeśli węzły procesu roboczego nie mają dołączonych etykiet, to nie zostanie zaplanowana usługa ReplicaSet agentów. Aby uzyskać instrukcje na temat sposobu dołączania etykiety, zapoznaj się z [Kubernetesą selektorów etykiet](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Wykresy wydajności nie pokazują procesora ani pamięci węzłów i kontenerów w klastrze nienależącym do platformy Azure

Azure Monitor dla programu Containers Agent, który zbiera metryki wydajności, używa punktu końcowego cAdvisor w agencie węzła. Sprawdź, czy kontener kontenera w węźle został skonfigurowany tak, aby zezwalać na otwieranie `cAdvisor port: 10255` na wszystkich węzłach w klastrze w celu zbierania metryk wydajności.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Klaster Kubernetes spoza platformy Azure nie jest wyświetlany w Azure Monitor dla kontenerów

Aby wyświetlić klaster programu spoza platformy Azure Kubernetes w Azure Monitor dla kontenerów, należy uzyskać dostęp do odczytu w obszarze roboczym Log Analytics, który obsługuje tę usługę Insights, oraz na platformie Azure Insights Resource **ContainerInsights (*obszar roboczy*)** .

## <a name="next-steps"></a>Następne kroki

Przy użyciu funkcji monitorowania, włączyć przechwytywania metryk kondycji dla węzłów klastra usługi AKS i zasobników, te metryki kondycji są dostępne w witrynie Azure portal. Aby dowiedzieć się, jak używać usługi Azure Monitor dla kontenerów, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
