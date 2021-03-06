---
title: Przewodnik Azure Security Center dla usługi IoT na potrzeby konfiguracji z platformą Azure (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku wyjaśniono, jak skonfigurować usługę Azure Wskaźnikowanie do odbierania danych z Azure Security Center na potrzeby rozwiązania IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: f6e7eddd6ddbcec61c3d8d173891cbc9abaaf08f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463182"
---
> [!IMPORTANT]
> W publicznej wersji zapoznawczej Azure Security Center łącznika danych IoT na platformie Azure.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Łączenie danych z Azure Security Center dla IoT z platformą Azure — wskaźnikiem (wersja zapoznawcza) 

W tym przewodniku dowiesz się, jak połączyć Azure Security Center danych IoT z platformą Azure.  

> [!div class="checklist"]
> * Wymagania wstępne 
> * Ustawienia połączenia
> * Widok alertów Log Analytics 

Łączenie alertów z Azure Security Center dla usługi IoT i przesyłanie strumieniowe ich bezpośrednio do platformy Azure — wskaźnik produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia do **odczytu** i **zapisu** obszaru roboczego.
- **Azure Security Center usługi IoT** muszą być **włączone** na odpowiednich IoT Hubach.
- Musisz mieć uprawnienia do **odczytu** i **zapisu** na **platformie Azure IoT Hub** , które chcesz połączyć.
- Musisz mieć również uprawnienia do **odczytu** i **zapisu** w **grupie zasobów usługi Azure IoT Hub**.

> [!NOTE]
> Aby można było wysyłać ogólne alerty zasobów platformy Azure, w ramach subskrypcji musi być uruchomiona Azure Security Center licencjonowania warstwy standardowej. Dzięki licencjonowaniu w warstwie Bezpłatna wymaganej do Azure Security Center usługi IoT tylko Azure Security Center alertów związanych z IoT są przekazywane do platformy Azure. 

## <a name="connect-to-azure-security-center-for-iot"></a>Nawiązywanie połączenia z usługą Azure Security Center dla IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure Security Center for IoT** .
1. W prawym dolnym okienku kliknij pozycję **Otwórz stronę łącznika**. 
1. Kliknij przycisk **Połącz**obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej. 
    - Jeśli Azure Security Center dla usługi IoT nie jest włączona w tym centrum, zobaczysz komunikat z ostrzeżeniem o włączeniu. Kliknij link **Włącz** , aby uruchomić usługę. 
1. Możesz zdecydować, czy alerty od Azure Security Center dla IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń**wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną w celu automatycznego tworzenia zdarzeń z alertów generowanych w połączonej usłudze zabezpieczeń. Tę regułę można zmienić lub edytować w obszarze **analiza** > **aktywnych** reguł.

> [!NOTE]
>Odświeżenie listy centrów po wprowadzeniu zmian połączenia może zająć 10 sekund lub dłużej. 

## <a name="log-analytics-alert-display"></a>Wyświetlanie alertów Log Analytics

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić Azure Security Center alertów IoT:

1. Otwórz **dzienniki** > **SecurityInsights** > **SecurityAlert**lub wyszukaj ciąg **SecurityAlert**. 
2. Filtruj, aby wyświetlić tylko Azure Security Center dla alertów generowanych przez IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia IoT Hub dane centrum są dostępne na platformie Azure — około 15 minut później.


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z Azure Security Centerami IoT z platformą Azure. Aby dowiedzieć się więcej na temat wykrywania zagrożeń i dostępu do danych zabezpieczeń, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)za pomocą usługi Azure wskaźnikowej.

- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń IoT](how-to-security-data-access.md)