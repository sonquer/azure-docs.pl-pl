---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 409001 DeviceAlreadyExists
description: Dowiedz się, jak naprawić błąd 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960791"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

W tym artykule opisano przyczyny i rozwiązania **409001 błędów DeviceAlreadyExists** .

## <a name="symptoms"></a>Objawy

Podczas próby zarejestrowania urządzenia w IoT Hub żądanie kończy się niepowodzeniem z błędem **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Przyczyna

Istnieje już urządzenie o tym samym IDENTYFIKATORze urządzenia w usłudze IoT Hub. 

## <a name="solution"></a>Rozwiązanie

Użyj innego identyfikatora urządzenia i spróbuj ponownie.