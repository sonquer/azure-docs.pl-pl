---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111204"
---
| **Dostawca** | **Rodzina urządzeń** | **Wersja oprogramowania układowego** |
| --- | --- | --- |
|Cisco | ISR| IOS 15,1 (wersja zapoznawcza)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2-No BGP) dla ASA poniżej 9,8 |
|Cisco | ASA | ASA RouteBased (IKEv2-No BGP) dla ASA 9.8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12. x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v 1.10 x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v 1.10 x RouteBased BGP|

> [!NOTE]
> ( * ) Wymagane: NarrowAzureTrafficSelectors (Enable UsePolicyBasedTrafficSelectors Option) i CustomAzurePolicies (IKE/IPsec)
>
