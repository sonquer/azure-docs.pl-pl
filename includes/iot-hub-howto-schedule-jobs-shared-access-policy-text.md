---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049071"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

W tym artykule opisano tworzenie usługi zaplecza, która planuje zadanie wywoływania metody bezpośredniej na urządzeniu, planuje zadanie aktualizowania sznurka urządzenia i monitoruje postęp poszczególnych zadań. Aby wykonać te operacje, usługa musi mieć uprawnienia do **odczytu rejestru** i **zapisu rejestru** . Domyślnie każde Centrum IoT Hub jest tworzone z użyciem zasad dostępu współdzielonego o nazwie **registryReadWrite** , które przyznają te uprawnienia.
