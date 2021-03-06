---
title: Rozpoczęcie korzystania z Azure Security Center dla agentów zabezpieczeń IoT | Microsoft Docs
description: Rozpocznij od poznania, konfigurowania, wdrażania i korzystania z Azure Security Center dla agentów usługi zabezpieczeń IoT na urządzeniach IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664194"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Wprowadzenie do Azure Security Center dla agentów zabezpieczeń urządzeń IoT

Azure Security Center dla agentów zabezpieczeń IoT oferują ulepszone funkcje zabezpieczeń, takie jak monitorowanie połączeń zdalnych, aktywne aplikacje, zdarzenia logowania oraz najlepsze rozwiązania w zakresie konfiguracji systemu operacyjnego. Przejmij kontrolę nad ochroną pola urządzenia przed zagrożeniami i stan zabezpieczeń w ramach jednej usługi. 

Dostępna jest architektura referencyjna dla agentów C# zabezpieczeń systemu Linux i Windows.

Azure Security Center dla agentów zabezpieczeń IoT obsługują nieprzetworzoną kolekcję zdarzeń z systemu operacyjnego urządzenia, agregacji zdarzeń w celu zmniejszenia kosztów i konfiguracji za pośrednictwem sznurka modułu urządzenia. Komunikaty zabezpieczeń są wysyłane przez IoT Hub do Azure Security Center usługi IoT Analytics.

Użyj poniższego przepływu pracy, aby wdrożyć i przetestować Azure Security Center dla agentów zabezpieczeń IoT: 

1. [Włącz Azure Security Center dla usługi IoT IoT Hub](quickstart-onboard-iot-hub.md)
1. Jeśli IoT Hub nie ma zarejestrowanych urządzeń, [Zarejestruj nowe urządzenie](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Utwórz moduł zabezpieczeń azureiotsecurity](quickstart-create-security-twin.md) dla urządzeń.
1. Aby zainstalować agenta na symulowanym urządzeniu platformy Azure zamiast instalować go na rzeczywistym urządzeniu, uruchom [nową maszynę wirtualną platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) w dostępnej strefie. 
1. [Wdróż Azure Security Center agenta zabezpieczeń IoT](how-to-deploy-linux-cs.md) na urządzeniu IoT lub nową maszynę wirtualną.
1. Postępuj zgodnie z instrukcjami [trigger_events](https://aka.ms/iot-security-github-trigger-events) , aby uruchomić niegroźną symulację ataku.
1. Sprawdź, czy w poprzednim kroku Azure Security Center alertów IoT w odpowiedzi na ataki symulowane. Rozpocznij weryfikację po pięciu minutach od uruchomienia skryptu.
1. Poznaj [alerty](concept-security-alerts.md), [zalecenia](concept-recommendations.md)i [głębokie szczegółowe przy użyciu log Analytics](how-to-security-data-access.md) przy użyciu IoT Hub. 


## <a name="next-steps"></a>Następne kroki

- Skonfiguruj [rozwiązanie](quickstart-configure-your-solution.md)
- [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)
- Konfigurowanie [alertów niestandardowych](quickstart-create-custom-alerts.md)
- [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
