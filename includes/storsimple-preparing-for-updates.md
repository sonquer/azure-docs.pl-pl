---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183424"
---
## <a name="preparing-for-updates"></a>Przygotowywanie do aktualizacji
Konieczne będzie przed skanowanie i stosowanie aktualizacji należy wykonać następujące czynności:

1. Migawkę w chmurze danych urządzenia.
2. Upewnij się, że Twoje stałe adresy IP kontrolera podlegają routingowi i mogą łączyć się z Internetem. Te stałe adresy IP będą używane do obsługi aktualizacji na urządzeniu. Można to sprawdzić, uruchamiając następujące polecenie cmdlet na każdym kontrolerze z interfejsu programu Windows PowerShell urządzenia:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Przykładowe dane wyjściowe dla połączenie testowe, gdy stałe adresy IP można połączyć się z Internetem**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Po ręcznej kontroli wstępnej została pomyślnie ukończona, możesz przejść do skanowania i zainstalować aktualizacje.

