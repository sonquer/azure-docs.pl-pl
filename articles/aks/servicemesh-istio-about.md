---
title: Przegląd Istio
description: Uzyskaj przegląd Istio
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3db2a3d75df08e7c668b1983ad9b4493446475df
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530513"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Przegląd

[Istio][istio] to w pełni funkcjonalna, dostosowywać i rozszerzalna siatka usług.

## <a name="architecture"></a>Architektura

Istio udostępnia płaszczyznę danych, która składa się z przyczepek opartych na [wysłannika][envoy-proxy]. Te inteligentne serwery proxy kontrolują cały ruch sieciowy do i z sieci i obciążeń z siatką.

Płaszczyzna kontroli zarządza konfiguracją, zasadami i telemetrii za pośrednictwem następujących [składników][what-is-istio]:

- **Mikser** — wymusza kontrolę dostępu i zasady użytkowania. Zbiera dane telemetryczne z serwerów proxy, które są wypychane do [Prometheus][prometheus].

- **Pilotaż** — zapewnia odnajdywanie i zasady zarządzania ruchem i konfigurację dla serwerów proxy.

- **Cytadeli** — zapewnia funkcje tożsamości i zabezpieczeń, które umożliwiają mTLS między usługami.

- I zapewnia konfigurację dla składników.

Na poniższym diagramie architektury pokazano, jak działa różne składniki w płaszczyźnie danych i płaszczyzny kontroli.


![Omówienie składników i architektury Istio.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Kryteria wyboru

Ważne jest zrozumienie i rozważenie następujących zagadnień podczas oceniania Istio obciążeń:

- [Cele projektu](#design-goals)
- [Możliwość](#capabilities)
- [Scenariusze](#scenarios)


### <a name="design-goals"></a>Cele projektu

Następujące cele projektowe [dotyczą][design-goals] projektu Istio:

- **Maksymalizowanie przejrzystości** — Zezwalanie na wdrażanie z minimalną ilością pracy w celu uzyskania wartości rzeczywistej z systemu.

- **Rozszerzalność** — musi być w stanie rozwijać i dostosowywać się do zmieniających się potrzeb.

- **Przenośność** — łatwe uruchamianie w różnych środowiskach — w chmurze i lokalnie.

- **Ujednolicenie zasad** — spójność w definicji zasad między różnymi zasobami.


### <a name="capabilities"></a>Możliwości

Istio zapewnia następujący zestaw możliwości:

- **Siatka** — bramy (wiele klastrów), maszyny wirtualne (rozszerzenie siatki)

- **Zarządzanie ruchem** — Routing, podział, przekroczenia limitu czasu, wyłączniki, ponowne próby, ruch przychodzący, ruch wychodzący

- **Zasady** — kontrola dostępu, Limit szybkości, przydział, niestandardowe karty zasad

- **Zabezpieczenia** — uwierzytelnianie (JWT), autoryzacja, szyfrowanie (mTLS), zewnętrzny urząd certyfikacji (magazyn HashiCorp)

- **Zauważalność** — złote metryki, dublowanie, śledzenie, karty niestandardowe, Prometheus, grafana

### <a name="scenarios"></a>Scenariusze

Istio jest dobrze dostosowane do i sugerowane dla następujących scenariuszy:

- Wymagaj rozszerzalności i bogaty zestaw funkcji

- Rozszerzanie siatki w celu uwzględnienia obciążeń opartych na maszynach wirtualnych

- Siatka usługi z obsługą wiele klastrów

## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano, jak można zainstalować usługę Istio w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalowanie Istio w usłudze Azure Kubernetes Service (AKS)][istio-install]

Możesz również poznać koncepcje Istio i dodatkowe modele wdrażania:

- [Pojęcia Istio][what-is-istio]
- [Modele wdrażania Istio][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
