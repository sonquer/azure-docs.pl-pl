---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183264"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne aktualizacje za pośrednictwem programu Windows PowerShell dla usługi StorSimple
1. Otwórz okno konsoli szeregowej urządzenia i wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Wpisz hasło. Domyślne hasło jest *Password1*. 
2. W wierszu polecenia wpisz polecenie:
   
     `Get-HcsUpdateAvailability`
   
    Jeśli aktualizacje są dostępne i czy aktualizacje są uciążliwe lub niezakłócającego, otrzymasz powiadomienie.
3. W wierszu polecenia wpisz polecenie:
   
     `Start-HcsUpdate`
   
    Rozpocznie się proces aktualizacji.

> [!IMPORTANT]
> * To polecenie dotyczy tylko regularne aktualizacje. Uruchom to polecenie na tylko jeden kontroler, ale oba kontrolery zostaną zaktualizowane. 
> * Można zauważyć praca awaryjna kontrolera podczas procesu aktualizacji; Jednak przełączenie w tryb failover nie wpłynie na dostępność systemu lub operacji.
> 
> 

