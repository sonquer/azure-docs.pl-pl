---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 409002 LinkCreationConflict
description: Dowiedz się, jak naprawić błąd 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960778"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

W tym artykule opisano przyczyny i rozwiązania **409002 błędów LinkCreationConflict** .

## <a name="symptoms"></a>Objawy

Zobaczysz błąd **409002 LinkCreationConflict** zarejestrowanych w dziennikach diagnostycznych wraz z rozłączeniem urządzenia lub błędem komunikatu z chmury do urządzenia. 

<!-- When using AMQP? -->

## <a name="cause"></a>Przyczyna

Ogólnie rzecz biorąc, ten błąd występuje, gdy IoT Hub wykryje, że klient ma więcej niż jedno połączenie. W rzeczywistości gdy nowe żądanie połączenia zostanie odebrane dla urządzenia z istniejącym połączeniem, IoT Hub zamyka istniejące połączenie z tym błędem.

### <a name="cause-1"></a>Przyczyny 1

W najbardziej typowym przypadku, oddzielnym problemem (takim jak [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) powoduje rozłączenie urządzenia. Urządzenie próbuje ponownie nawiązać połączenie, ale IoT Hub nadal uznaje, że urządzenie jest połączone. IoT Hub zamyka poprzednie połączenie i rejestruje ten błąd.

### <a name="cause-2"></a>Przyczyny 2

Uszkodzona logika po stronie urządzenia powoduje, że urządzenie nawiązuje połączenie, gdy jeden z nich jest już otwarty.

## <a name="solution"></a>Rozwiązanie

Ten błąd zazwyczaj występuje jako efekt uboczny innego, przejściowego problemu, dlatego wyszukasz inne błędy w dziennikach, aby rozwiązać problem. W przeciwnym razie należy wydać nowe żądanie połączenia tylko wtedy, gdy połączenie zostanie porzucane.
