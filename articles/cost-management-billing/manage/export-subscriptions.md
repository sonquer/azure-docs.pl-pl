---
title: Eksportowanie informacji najwyższego poziomu dotyczących subskrypcji platformy Azure | Microsoft Docs
description: Opisuje sposób wyświetlania wszystkich identyfikatorów subskrypcji platformy Azure skojarzonych z Twoim kontem.
keywords: ''
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 83b6a9db0107cb35448aec491c81c2630e87ddd7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199725"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Eksportowanie i wyświetlanie informacji najwyższego poziomu dotyczących subskrypcji
Jeśli chcesz wyświetlić zestaw identyfikatorów subskrypcji skojarzonych z poświadczeniami użytkownika, [pobierz plik JSON z informacjami o subskrypcji z Centrum konta platformy Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Pobrany plik JSON zawiera następujące informacje:
- Adres e-mail: Adres e-mail skojarzony z Twoim kontem.
- Identyfikator PUID: Unikatowy identyfikator skojarzony z Twoim kontem rozliczeniowym.
- Identyfikatory subskrypcji: Lista subskrypcji należących do Twojego konta uporządkowane według identyfikatora subskrypcji.

### <a name="subscriptionsjson-sample"></a>Przykładowy plik subscriptions.json

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
