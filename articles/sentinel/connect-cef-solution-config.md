---
title: Skonfiguruj rozwiązanie zabezpieczeń, aby połączyć dane CEF z wersją zapoznawczą platformy Azure wskaźnikiem | Microsoft Docs
description: Dowiedz się, jak skonfigurować rozwiązanie zabezpieczeń, aby połączyć dane CEF z platformą Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 2513638be6f895598f93758d8d8dbdf04c9561e9
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615361"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>Krok 2. Konfigurowanie rozwiązania zabezpieczeń do wysyłania komunikatów CEF

W tym kroku zostaną wykonane niezbędne zmiany konfiguracji dotyczące samego rozwiązania zabezpieczeń w celu wysłania dzienników do agenta CEF.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurowanie rozwiązania za pomocą łącznika

Jeśli rozwiązanie zabezpieczeń ma już istniejący łącznik, użyj instrukcji specyficznych dla łącznika w następujący sposób:

- [Punkt kontrolny](connect-checkpoint.md)
- [Firmy](connect-cisco.md)
- [ExtraHop (x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [Ochrona jednej tożsamości](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro — zabezpieczenia](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Skonfiguruj inne rozwiązanie
Jeśli łącznik nie istnieje dla określonego rozwiązania zabezpieczającego, należy użyć poniższych ogólnych instrukcji dotyczących przekazywania dzienników do agenta CEF.

1. Przejdź do określonego artykułu konfiguracyjnego, aby zapoznać się z instrukcjami dotyczącymi sposobu konfigurowania rozwiązania do wysyłania komunikatów CEF. Jeśli Twoje rozwiązanie nie znajduje się na liście, na urządzeniu należy ustawić te wartości tak, aby urządzenie wysyła niezbędne dzienniki w niezbędnym formacie do agenta dziennika systemu Azure wskaźnikowego na podstawie agenta Log Analytics. Te parametry można modyfikować w urządzeniu, o ile są one również modyfikowane w demona dziennika systemowego w ramach agenta wskaźnikowego platformy Azure.
    - Protokół = TCP
    - Port = 514
    - Format = CEF
    - Adres IP — upewnij się, że wysłano komunikaty CEF na adres IP maszyny wirtualnej dedykowanej do tego celu.

   > [!NOTE]
   > To rozwiązanie obsługuje dziennik systemowy RFC 3164 lub RFC 5424.


1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń CEF, Wyszukaj `CommonSecurityLog`.

1. Przejdź do kroku 3: [Weryfikowanie łączności](connect-cef-verify.md).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń CEF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

