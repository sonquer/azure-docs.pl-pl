---
title: Przewodnik dotyczący badania urządzeń IoT Azure Security Center | Microsoft Docs
description: W tym przewodniku wyjaśniono, jak używać usługi IoT Azure Security Center na potrzeby badania podejrzanego urządzenia IoT przy użyciu Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596241"
---
# <a name="investigate-a-suspicious-iot-device"></a>Zbadaj podejrzane urządzenie IoT

Azure Security Center dla alertów usługi IoT zawierają jasne informacje o tym, kiedy urządzenia IoT są podejrzane o uczestnictwo w podejrzanych działaniach, a także o tym, czy zostały naruszone zabezpieczenia urządzenia. 

W tym przewodniku Skorzystaj z sugestii dotyczących badania, aby pomóc w ustaleniu potencjalnego ryzyka dla organizacji, określić sposób korygowania i poznać najlepsze sposoby zapobiegania podobnym atakom w przyszłości.  

> [!div class="checklist"]
> * Znajdowanie danych urządzenia
> * Badanie przy użyciu zapytań KQL


## <a name="how-can-i-access-my-data"></a>Jak mogę uzyskać dostęp do moich danych?

Domyślnie usługi Azure Security Center dla IoT przechowują alerty zabezpieczeń i zalecenia w obszarze roboczym Log Analytics. Możesz również przechowywać pierwotne dane zabezpieczeń.

Aby zlokalizować obszar roboczy Log Analytics magazynu danych:

1. Otwórz Centrum IoT Hub, 
1. W obszarze **zabezpieczenia**kliknij pozycję **Przegląd**, a następnie wybierz pozycję **Ustawienia**.
1. Zmień szczegóły konfiguracji obszaru roboczego Log Analytics. 
1. Kliknij polecenie **Zapisz**. 

Wykonaj następujące czynności konfiguracyjne, aby uzyskać dostęp do danych przechowywanych w obszarze roboczym Log Analytics:

1. Wybierz i kliknij Azure Security Center alert IoT w IoT Hub. 
1. Kliknij przycisk **dalszych badań**. 
1. Wybierz, **Aby zobaczyć, które urządzenia mają ten alert kliknij tutaj i Wyświetl kolumnę DeviceID**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Kroki badania dla podejrzanych urządzeń IoT

Aby wyświetlić szczegółowe informacje i nieprzetworzone dane dotyczące urządzeń IoT, przejdź do obszaru roboczego Log Analytics, [Aby uzyskać dostęp do danych](#how-can-i-access-my-data).

Zobacz przykładowe zapytania KQL, aby rozpocząć badanie alertów i działań na urządzeniu.

### <a name="related-alerts"></a>Powiązane alerty

Aby dowiedzieć się, czy inne alerty zostały wyzwolone w tym samym czasie, użyj następującego zapytania KQL:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Użytkownicy z dostępem

Aby dowiedzieć się, którzy użytkownicy mają dostęp do tego urządzenia, użyj następującego zapytania KQL: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Te dane służą do odnajdowania: 
- Którzy użytkownicy mają dostęp do urządzenia?
- Czy użytkownicy z dostępem mają oczekiwane poziomy uprawnień?

### <a name="open-ports"></a>Otwórz porty

Aby dowiedzieć się, które porty urządzenia są obecnie używane lub były używane, użyj następującego zapytania KQL: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Te dane służą do odnajdowania:
- Które gniazda nasłuchujące są obecnie aktywne na urządzeniu?
- Czy gniazda nasłuchujące, które są obecnie aktywne, są dozwolone?
- Czy istnieją podejrzane adresy zdalne połączone z urządzeniem?

### <a name="user-logins"></a>Logowania użytkowników

Aby znaleźć użytkowników zalogowanych do urządzenia, użyj następującego zapytania KQL: 
 
 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Użyj wyników zapytania, aby odkryć:
- Którzy użytkownicy zalogowani na urządzeniu?
- Czy użytkownicy zalogowani, powinni zalogować się?
- Czy użytkownicy zalogowani w programie nawiązują połączenie z oczekiwanych lub nieoczekiwanych adresów IP?
  
### <a name="process-list"></a>Lista procesów

Aby dowiedzieć się, czy lista procesów jest zgodnie z oczekiwaniami, użyj następującego zapytania KQL: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Użyj wyników zapytania, aby odkryć:

- Czy na urządzeniu są uruchomione jakieś podejrzane procesy?
- Czy procesy były wykonywane przez odpowiednich użytkowników?
- Czy wszystkie wykonania wiersza polecenia zawierają poprawne i oczekiwane argumenty?

## <a name="next-steps"></a>Następne kroki

Po zbadaniu urządzenia i lepszym zrozumieniu zagrożeń warto rozważyć [skonfigurowanie niestandardowych alertów](quickstart-create-custom-alerts.md) w celu usprawnienia stan bezpieczeństwa rozwiązań IoT. Jeśli nie masz jeszcze agenta urządzenia, rozważ [wdrożenie agenta zabezpieczeń](how-to-deploy-agent.md) lub [zmianę konfiguracji istniejącego agenta urządzenia](how-to-agent-configuration.md) w celu poprawienia wyników. 
