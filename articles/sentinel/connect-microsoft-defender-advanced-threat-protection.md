---
title: Łączenie danych usługi Microsoft Defender ATP z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane z zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender do platformy Azure.
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
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256749"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Łączenie alertów z poziomu zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender 


> [!IMPORTANT]
> Pozyskiwanie dzienników zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Alerty z usługi [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) można przesyłać za pomocą jednego kliknięcia. To połączenie umożliwia przesyłanie strumieniowe alertów z usługi Microsoft Defender Advanced Threat Protection na platformę Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Ważna licencja na usługę Microsoft Defender Advanced Threat Protection, która została włączona zgodnie z opisem w temacie [Sprawdzanie aprowizacji licencjonowania i kompletna konfiguracja usługi Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Musisz być administratorem lub administratorem zabezpieczeń w dzierżawie wskaźnikowej platformy Azure.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Łączenie z usługą Microsoft Defender Advanced Threat Protection

Jeśli Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender zostanie wdrożona i pozyskuje dane, alerty mogą być łatwo przesyłane strumieniowo do platformy Azure.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**, kliknij kafelek **Microsoft Defender Advanced Threat Protection** , a następnie wybierz **stronę Otwórz łącznik**.
1. Kliknij przycisk **Połącz**. 
1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Defender ATP, wyszukaj ciąg **SecurityAlert** , a **Nazwa dostawcy** to **MDATP**.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Microsoft Defender ATP do usługi Azure wskaźnikowej. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
