---
title: Wymagania wstępne dotyczące konfigurowania komunikacji równorzędnej z firmą Microsoft
titleSuffix: Azure
description: Wymagania wstępne dotyczące konfigurowania komunikacji równorzędnej z firmą Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775409"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Wymagania wstępne dotyczące konfigurowania komunikacji równorzędnej z firmą Microsoft

Przed zażądaniem nowej komunikacji równorzędnej lub przekonwertowaniem starszej komunikacji równorzędnej na zasób platformy Azure upewnij się, że poniższe wymagania wstępne zostały spełnione.

## <a name="azure-related-prerequisites"></a>Wymagania wstępne dotyczące platformy Azure
* **Konto Microsoft Azure:** Jeśli nie masz konta Microsoft Azure, Utwórz [konto Microsoft Azure](https://azure.microsoft.com/free). Do skonfigurowania komunikacji równorzędnej jest wymagana prawidłowa i aktywna subskrypcja Microsoft Azure, ponieważ Komunikacja równorzędna jest modelowana jako zasoby w ramach subskrypcji platformy Azure. Należy pamiętać, że:
    * Typy zasobów platformy Azure służące do konfigurowania komunikacji równorzędnej są zawsze bezpłatnymi produktami platformy Azure, czyli nie są naliczone opłaty za tworzenie konta platformy Azure ani Tworzenie subskrypcji ani dostęp do zasobów platformy Azure **PeerAsn** i **komunikacji równorzędnej** w celu skonfigurowania komunikacji równorzędnej. Nie należy mylić z umową komunikacji równorzędnej w przypadku bezpośredniej komunikacji równorzędnej między użytkownikami i firmą Microsoft, których warunki są jawnie omówione w naszym zespole komunikacji równorzędnej. Skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com) , jeśli masz jakieś pytania w tym zakresie.
    * Możesz użyć tej samej subskrypcji platformy Azure, aby uzyskać dostęp do innych produktów platformy Azure lub usług w chmurze, które mogą być bezpłatne lub płatne. Gdy uzyskujesz dostęp do płatnego produktu, zostaną naliczone opłaty.
    * Jeśli tworzysz nowe konto i/lub subskrypcję platformy Azure, możesz skorzystać z bezpłatnych środków na korzystanie z platformy Azure w okresie próbnym, którego możesz użyć do wypróbowania usług Azure Cloud Services. Jeśli chcesz, odwiedź stronę [Microsoft Azure konto](https://azure.microsoft.com/free) , aby uzyskać więcej informacji.

* **Skojarz równorzędny numer ASN:** Przed zażądaniem komunikacji równorzędnej, najpierw Skojarz informacje o ASN i kontakcie z subskrypcją. Postępuj zgodnie z instrukcjami w temacie [kojarzenie równorzędnej usługi ASN z subskrypcją platformy Azure](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Inne wymagania wstępne
* **Profil PeeringDB:** Elementy równorzędne powinny mieć pełny i aktualny profil w usłudze [PeeringDB](https://www.peeringdb.com). Te informacje są używane w naszym systemie rejestracji w celu weryfikacji szczegółowych informacji dotyczących elementów równorzędnych, takich jak informacje NOC, informacje kontaktowe techniczne i ich obecność w urządzeniach komunikacji równorzędnej itp.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md).
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)
* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)