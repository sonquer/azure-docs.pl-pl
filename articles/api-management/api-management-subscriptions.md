---
title: Subskrypcje na platformie Azure API Management | Microsoft Docs
description: Dowiedz się więcej na temat koncepcji subskrypcji w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 9144af131e1427d0b3226655c871921ac1d91665
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073423"
---
# <a name="subscriptions-in-azure-api-management"></a>Subskrypcje na platformie Azure API Management

Subskrypcje są ważnym pojęciem w usłudze Azure API Management. Są one Najpopularniejszym sposobem uzyskiwania dostępu do interfejsów API publikowanych za pomocą wystąpienia API Management przez odbiorców interfejsu API. Ten artykuł zawiera omówienie koncepcji.

## <a name="what-are-subscriptions"></a>Co to są subskrypcje?

Przy publikowaniu interfejsów API za pomocą API Management, jest to łatwe i powszechne do zabezpieczenia dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, muszą uwzględnić prawidłowy klucz subskrypcji w żądaniach HTTP, gdy wykonują wywołania do tych interfejsów API. W przeciwnym razie wywołania są odrzucane natychmiast przez bramę API Management. Nie są one przekazywane do usług zaplecza.

Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Subskrypcja jest zasadniczo nazwą kontenera dla pary kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, mogą uzyskać subskrypcje. Nie potrzebują oni jednak zatwierdzania od wydawców interfejsu API. Wydawcy interfejsu API mogą również tworzyć subskrypcje bezpośrednio dla użytkowników interfejsu API.

> [!TIP]
> API Management obsługuje również inne mechanizmy zabezpieczania dostępu do interfejsów API, w tym następujące przykłady:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certyfikaty klienta](api-management-howto-mutual-certificates-for-clients.md)
> - [Listy dozwolonych IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Zakres subskrypcji

Subskrypcje można kojarzyć z różnymi zakresami: produkt, wszystkie interfejsy API lub pojedynczy interfejs API.

### <a name="subscriptions-for-a-product"></a>Subskrypcje dla produktu

Tradycyjnie subskrypcje w API Management były zawsze skojarzone z jednym zakresem [produktu interfejsu API](api-management-terminology.md) . Deweloperzy znalazły listę produktów w portalu dla deweloperów. Następnie przesyłają żądania subskrypcji dla produktów, których chcą używać. Gdy żądanie subskrypcji zostanie zatwierdzone automatycznie lub przez wydawców interfejsu API, programista może użyć tych kluczy w celu uzyskania dostępu do wszystkich interfejsów API w produkcie. W tej chwili Portal deweloperów pokazuje tylko subskrypcje należące do zakresu produktu w sekcji profil użytkownika. 

![Subskrypcje produktu](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> W niektórych scenariuszach wydawcy interfejsu API mogą chcieć opublikować produkt interfejsu API publicznie bez wymogu subskrypcji. Mogą oni usunąć zaznaczenie opcji **Wymagaj subskrypcji** na stronie **ustawienia** produktu w Azure Portal. W związku z tym wszystkie interfejsy API w ramach produktu są dostępne bez klucza interfejsu API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Subskrypcje dla wszystkich interfejsów API lub pojedynczego interfejsu API

Po wprowadzeniu warstwy [zużycia](https://aka.ms/apimconsumptionblog) API Management wprowadziliśmy kilka zmian w celu usprawnienia zarządzania kluczami:
- Najpierw dodaliśmy dwa dodatkowe zakresy subskrypcji: wszystkie interfejsy API i jeden interfejs API. Zakres subskrypcji nie jest już ograniczony do produktu interfejsu API. Teraz można utworzyć klucze zezwalające na dostęp do interfejsu API lub wszystkich interfejsów API w wystąpieniu API Management, bez konieczności tworzenia produktu i dodawania do nich interfejsów API. Ponadto każde wystąpienie API Management jest teraz z niemodyfikowalną subskrypcją wszystkie interfejsy API. Ta subskrypcja ułatwia testowanie i debugowanie interfejsów API w konsoli testowej.

- Następnie API Management umożliwia teraz korzystanie z subskrypcji autonomicznych. Subskrypcje nie są już wymagane do skojarzenia z kontem dewelopera. Ta funkcja jest przydatna w scenariuszach, na przykład gdy kilku deweloperów lub zespołów współużytkuje subskrypcję.

- Na koniec wydawcy interfejsu API mogą teraz [tworzyć subskrypcje](api-management-howto-create-subscriptions.md) bezpośrednio w Azure Portal:

    ![Elastyczne subskrypcje](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Następne kroki
Uzyskaj więcej informacji na API Management:

+ Zapoznaj [](api-management-terminology.md) się z innymi pojęciami w API Management.
+ Postępuj [](import-and-publish.md) zgodnie z naszymi samouczkami, aby dowiedzieć się więcej o API Management.
+ Zapoznaj się z naszą [stroną często](api-management-faq.md) zadawanych pytań.
