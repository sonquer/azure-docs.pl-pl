---
title: Konfigurowanie uwierzytelniania subskrybenta elementu webhook — Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurowanie uwierzytelniania subskrybenta elementu webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841734"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurowanie uwierzytelniania subskrybenta elementu webhook

Ten przewodnik zawiera przykłady możliwych konfiguracji subskrybentów elementu webhook dla modułu Event Grid. Domyślnie tylko punkty końcowe HTTPS są akceptowane dla subskrybentów elementu webhook. Moduł Event Grid zostanie odrzucony, jeśli Subskrybenci przedstawiają certyfikat z podpisem własnym.

## <a name="allow-only-https-subscriber"></a>Zezwalaj tylko na subskrybenta HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Zezwalaj na subskrybenta HTTPS z certyfikatem z podpisem własnym

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ustaw właściwość `outbound__webhook__allowUnknownCA` na `true` tylko w środowiskach testowych, ponieważ zazwyczaj używasz certyfikatów z podpisem własnym. W przypadku obciążeń produkcyjnych zaleca się ich ustawienie na **wartość false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Zezwalaj na subskrybenta HTTPS, ale Pomijaj weryfikację certyfikatu

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Ustaw właściwość `outbound__webhook__skipServerCertValidation` tak, aby `true` tylko w środowiskach testowych, ponieważ użytkownik nie będzie przedprezentować certyfikatu, który musi zostać uwierzytelniony. W przypadku obciążeń produkcyjnych zaleca się ich ustawienie na **wartość FAŁSZ** .

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Zezwalaj na protokoły HTTP i HTTPS z certyfikatami z podpisem własnym

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ustaw właściwość `outbound__webhook__httpsOnly` na `false` tylko w środowiskach testowych, ponieważ warto najpierw utworzyć subskrybenta protokołu HTTP. W przypadku obciążeń produkcyjnych zaleca się ich ustawienie na **wartość true**
