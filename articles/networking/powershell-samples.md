---
title: Przykłady Azure PowerShell — sieć
description: Przykłady dla programu Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: d1e6027a0dc71f0962293143ca9bd3de67a659e6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844908"
---
# <a name="azure-powershell-samples-for-networking"></a>Przykłady Azure PowerShell dla sieci

Poniższa tabela zawiera linki do skryptów utworzonych przy użyciu Azure PowerShell.

| | |
|-|-|
|**Łączność między zasobami platformy Azure**||
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołu HTTP, a ruch do podsieci zaplecza jest ograniczony do portu 1433 środowiska SQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP i HTTPS. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|**Równoważenie obciążenia i kierunek ruchu**||
| [Równoważenie obciążenia ruchem do maszyn wirtualnych wysokiej dostępności](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy kilka maszyn wirtualnych na platformie o wysokiej dostępności i konfiguracji ze zrównoważonym obciążeniem. |
| [Bezpośredni ruch w wielu regionach w celu zapewnienia wysokiej dostępności aplikacji](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Tworzy dwa plany usługi App Service, dwie aplikacje sieci Web, profil programu Traffic Manager i dwa punkty końcowe w usłudze Traffic Manager. |
| | |
