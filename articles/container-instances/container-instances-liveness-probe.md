---
title: Konfigurowanie sondy na żywo w wystąpieniu kontenera
description: Dowiedz się, jak skonfigurować sondy na żywo w celu ponownego uruchomienia kontenerów w złej kondycji w Azure Container Instances
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934164"
---
# <a name="configure-liveness-probes"></a>Konfigurowanie sond żywotności

Aplikacje kontenerowe mogą być uruchamiane przez dłuższy czas, co spowodowało uszkodzenie Stanów, które mogą wymagać naprawy przez ponowne uruchomienie kontenera. Azure Container Instances obsługuje sondy na żywo, dzięki czemu można skonfigurować kontenery w ramach grupy kontenerów w celu ponownego uruchomienia, jeśli funkcje krytyczne nie działają. Sonda na żywo zachowuje się jak [sonda Kubernetes na żywo](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

W tym artykule wyjaśniono, jak wdrożyć grupę kontenerów zawierającą sondę na żywo, pokazującą automatyczne ponowne uruchomienie symulowanego kontenera w złej kondycji.

Azure Container Instances obsługuje również [sondy gotowości](container-instances-readiness-probe.md), które można skonfigurować, aby zapewnić, że ruch osiągnie kontener tylko wtedy, gdy jest on gotowy do obsługi.

> [!NOTE]
> Obecnie nie można używać sondy na żywo w grupie kontenerów wdrożonej w sieci wirtualnej.

## <a name="yaml-deployment"></a>Wdrożenie YAML

Utwórz plik `liveness-probe.yaml` za pomocą poniższego fragmentu kodu. Ten plik definiuje grupę kontenerów, która składa się z kontenera NGNIX, który ostatecznie stał się w złej kondycji.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Uruchom następujące polecenie, aby wdrożyć tę grupę kontenerów z powyższą konfiguracją YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Uruchom polecenie

Wdrożenie zawiera właściwość `command` definiującą polecenie uruchamiania uruchamiane po pierwszym uruchomieniu kontenera. Ta właściwość akceptuje tablicę ciągów. To polecenie symuluje przejście w stan złej kondycji.

Najpierw uruchamia sesję bash i tworzy plik o nazwie `healthy` w katalogu `/tmp`. A następnie uśpienie przez 30 sekund przed usunięciem pliku, a następnie przejście do trybu uśpienia 10 minut:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Dynamiczność — polecenie

To wdrożenie definiuje `livenessProbe`, który obsługuje `exec` na żywo, który działa jak sprawdzenie na żywo. Jeśli to polecenie kończy się wartością różną od zera, kontener zostanie zamknięty i ponownie uruchomiony, sygnalizując, że nie można odnaleźć pliku `healthy`. Jeśli to polecenie zakończy się pomyślnie z kodem zakończenia 0, nie zostanie podjęta żadna akcja.

Właściwość `periodSeconds` określa, że polecenie ma być wykonywane co 5 sekund.

## <a name="verify-liveness-output"></a>Sprawdzanie danych wyjściowych na żywo

W ciągu pierwszych 30 sekund plik `healthy` utworzony przez polecenie uruchamiania istnieje. Gdy polecenie Live jest sprawdza obecność pliku `healthy`, kod stanu zwraca wartość 0, sygnalizujący powodzenie, dlatego nie następuje ponowne uruchomienie.

Po 30 sekundach polecenie `cat /tmp/healthy` zaczyna kończyć się niepowodzeniem, powodując wystąpienie złej kondycji i kasowanie zdarzeń.

Te zdarzenia można wyświetlić w Azure Portal lub interfejs wiersza polecenia platformy Azure.

![Zdarzenie w złej kondycji portalu][portal-unhealthy]

Wyświetlając zdarzenia w Azure Portal, zdarzenia typu `Unhealthy` są wywoływane z niepowodzeniem polecenia na żywo. Kolejne zdarzenie jest typu `Killing`, co oznacza usunięcie kontenera, aby można było rozpocząć ponowne uruchomienie. Liczba ponownych uruchomień dla kontenera jest zwiększana za każdym razem, gdy wystąpi zdarzenie.

Ponowne uruchomienia są wykonywane w miejscu, tak aby zasoby, takie jak publiczne adresy IP i zawartość specyficzną dla węzła, były zachowywane.

![Licznik ponownego uruchamiania portalu][portal-restart]

Jeśli sonda na żywo zakończy się niepowodzeniem i wyzwala zbyt wiele ponownych uruchomień, kontener wejdzie w odwrotny sposób.

## <a name="liveness-probes-and-restart-policies"></a>Sondy na żywo i zasady ponownego uruchamiania

Zasady ponownego uruchamiania zastępują zachowanie ponownego uruchamiania wyzwalane przez sondy na żywo. Na przykład jeśli ustawisz `restartPolicy = Never` *i* sondę na żywo, Grupa kontenerów nie zostanie ponownie uruchomiona z powodu niepowodzenia sprawdzenia na żywo. Grupa kontenerów jest raczej zgodna z zasadami ponownego uruchamiania grupy kontenerów `Never`.

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach mogą wymagać sondy na żywo, aby włączyć automatyczne ponowne uruchamianie, jeśli funkcja wstępna nie działa prawidłowo. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie kontenerów zadań w Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
