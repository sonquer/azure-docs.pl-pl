---
title: Przewodnik po interfejsie użytkownika usługi Azure IoT Central | Microsoft Docs
description: Zapoznaj się z kluczowymi obszarami interfejsu użytkownika usługi Azure IoT Central, który służy do tworzenia i używania rozwiązania IoT oraz zarządzania nim.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b905b1e86810b25c4c94072d6cd414b993e2a883
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426194"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Przewodnik po interfejsie użytkownika usługi Azure IoT Central



W tym artykule przedstawiono wprowadzenie do interfejsu użytkownika usługi Microsoft Azure IoT Central. Interfejs użytkownika umożliwia tworzenie i używanie rozwiązania usługi Azure IoT Central oraz jego połączonych urządzeń, a także zarządzanie nimi.

Jako _Konstruktor rozwiązań_możesz zdefiniować rozwiązanie IoT Central platformy Azure za pomocą interfejsu użytkownika IoT Central platformy Azure. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Definiowanie typów urządzeń łączących się z rozwiązaniem
* Konfigurowanie reguł i akcji dla urządzeń 
* Dostosowywanie interfejsu użytkownika dla _operatora_ używającego rozwiązania

_Operator_ używa interfejsu użytkownika usługi Azure IoT Central, aby zarządzać rozwiązaniem usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Monitorowanie urządzeń
* Konfigurowanie urządzeń
* Rozwiązywanie i korygowanie problemów z urządzeniami
* Aprowizacja nowych urządzeń.

## <a name="iot-central-homepage"></a>Strona główna IoT Central

Strona [główna IoT Central](https://aka.ms/iotcentral-get-started) jest miejscem, w którym można dowiedzieć się więcej na temat najnowszych wiadomości i funkcji dostępnych na IoT Central, tworzenia nowych aplikacji i uruchamiania istniejącej aplikacji.

> [!div class="mx-imgBorder"]
> ![Strona główna IoT Central](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Tworzenie aplikacji

W sekcji Kompilacja można przeglądać listę odpowiednich szablonów IoT Central branżowych, aby pomóc szybko rozpocząć pracę lub zacząć od podstaw przy użyciu niestandardowego szablonu aplikacji.  
> [!div class="mx-imgBorder"]
> ![IoT Central strony kompilacji](media/overview-iot-central-tour/iot-central-build-pnp.png)

Aby dowiedzieć się więcej, zapoznaj się z przewodnikiem Szybki Start [dotyczącym tworzenia aplikacji platformy Azure IoT Central](quick-deploy-iot-central.md) .

### <a name="launch-your-application"></a>Uruchom aplikację

Możesz uruchomić aplikację IoT Central, przechodząc do adresu URL wybranego przez Ciebie lub konstruktora rozwiązań podczas tworzenia aplikacji. Możesz również wyświetlić listę wszystkich aplikacji, do których masz dostęp, w programie [IoT Central App Manager](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![IoT Central](media/overview-iot-central-tour/app-manager-pnp.png) App Manager

## <a name="navigate-your-application"></a>Nawigowanie po aplikacji

Gdy jesteś w aplikacji IoT, użyj okienka po lewej stronie, aby uzyskać dostęp do różnych obszarów. Okienko po lewej stronie można rozwinąć lub zwinąć, wybierając ikonę z trzema znakami w górnej części okienka:

> [!NOTE]
> Elementy wyświetlane w lewym okienku są zależne od roli użytkownika. Dowiedz się więcej o [zarządzaniu użytkownikami i rolami](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![lewe okienko](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Pulpit nawigacyjny** wyświetla pulpit nawigacyjny aplikacji. Jako *Konstruktor rozwiązań*można dostosować globalny pulpit nawigacyjny dla operatorów. W zależności od ich roli użytkownika operatory mogą również tworzyć własne osobiste pulpity nawigacyjne.
     
     **Urządzenia** umożliwiają zarządzanie połączonymi urządzeniami — w rzeczywistości i symulowane.

     **Grupy urządzeń** umożliwiają wyświetlanie i tworzenie logicznych kolekcji urządzeń określonych przez zapytanie. Możesz zapisać to zapytanie i użyć grup urządzeń za pomocą aplikacji do wykonywania operacji zbiorczych.

     **Reguły** umożliwiają tworzenie i edytowanie reguł do monitorowania urządzeń. Reguły są oceniane na podstawie danych telemetrycznych urządzenia i wyzwalane akcje dostosowywalne.

     **Analiza** umożliwia tworzenie niestandardowych widoków na podstawie danych urządzeń w celu uzyskania szczegółowych informacji z aplikacji.

     **Zadania** umożliwiają zarządzanie urządzeniami na dużą skalę przez wykonywanie operacji zbiorczych.

     **Szablony urządzeń** to miejsce, w którym można tworzyć i zarządzać charakterystyką urządzeń, które łączą się z Twoją aplikacją.

     **Eksport danych** umożliwia skonfigurowanie eksportu ciągłego do usług zewnętrznych, takich jak magazyn i kolejki.

     **Administracja** to miejsce, w którym można zarządzać ustawieniami, dostosowaniami, rozliczeniami, użytkownikami i rolami aplikacji.

     **IoT Central** pozwala *administratorom* na powrót do Menedżera aplikacji IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Wyszukiwanie, pomoc, motyw i pomoc techniczna

Górne menu jest wyświetlane na każdej stronie:

> [!div class="mx-imgBorder"]
> ![pasek narzędzi](media/overview-iot-central-tour/toolbar-pnp.png)

* Aby wyszukać szablony urządzeń i urządzenia, wprowadź wartość **wyszukiwania** .
* Aby zmienić język lub motyw interfejsu użytkownika, wybierz ikonę **Ustawienia** . Dowiedz się więcej o [zarządzaniu preferencjami aplikacji](howto-manage-preferences.md)
* Aby wylogować się z aplikacji, wybierz ikonę **konta** .
* Aby uzyskać pomoc i pomoc techniczną, wybierz pozycję rozwijaną **Pomoc**, aby uzyskać listę zasobów. W aplikacji w ramach bezpłatnego planu cenowego zasoby pomocy technicznej obejmują dostęp do [rozmowy na żywo](howto-show-hide-chat.md).

Możesz wybrać jasny lub ciemny motyw interfejsu użytkownika:

> [!NOTE]
> Opcja wyboru między jasnym i ciemnym motywem nie jest dostępna, jeśli administrator skonfigurował niestandardowy motyw dla aplikacji.

> [!div class="mx-imgBorder"]
> ![wybrać motyw dla interfejsu użytkownika](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Pulpit nawigacyjny
> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny](media/overview-iot-central-tour/dashboard-pnp.png)

* Pulpit nawigacyjny to pierwsza strona wyświetlana podczas logowania do aplikacji IoT Central platformy Azure. Jako *Konstruktor rozwiązań*można tworzyć i dostosowywać wiele pulpitów nawigacyjnych aplikacji dla innych użytkowników. Dowiedz się więcej [na temat dodawania kafelków do pulpitu nawigacyjnego](howto-add-tiles-to-your-dashboard.md)

* Jeśli masz rolę *użytkownika, możesz*utworzyć osobiste pulpity nawigacyjne, aby monitorować interesujące Cię informacje. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie osobistych pulpitów nawigacyjnych usługi Azure IoT Central](howto-create-personal-dashboards.md) .

### <a name="devices"></a>Urządzenia

> [!div class="mx-imgBorder"]
> Strona ![urządzeń](media/overview-iot-central-tour/devices-pnp.png)

Na stronie eksploratora są wyświetlane _urządzenia_ w aplikacji usługi Azure IoT Central pogrupowane według _szablonu urządzeń_. 

* Szablon urządzenia definiuje typ urządzenia, który może łączyć się z aplikacją.
* Urządzenie reprezentuje rzeczywiste lub symulowane urządzenie w aplikacji.

Aby dowiedzieć się więcej, zobacz [monitorowanie urządzeń](./quick-monitor-devices.md) — Szybki Start. 

### <a name="device-groups"></a>Grupy urządzeń

> [!div class="mx-imgBorder"]
> Strona ![grupy urządzeń](media/overview-iot-central-tour/device-groups-pnp.png)

Grupa urządzeń to kolekcja powiązanych urządzeń. *Konstruktor rozwiązań* definiuje zapytanie w celu zidentyfikowania urządzeń uwzględnionych w grupie urządzeń. Grupy urządzeń służą do wykonywania operacji zbiorczych w aplikacji. Aby dowiedzieć się więcej, zobacz artykuł [Używanie grup urządzeń w aplikacji platformy Azure IoT Central](tutorial-use-device-groups.md) .

### <a name="rules"></a>Reguły
> [!div class="mx-imgBorder"]
> Strona reguł ![](media/overview-iot-central-tour/rules-pnp.png)

Strona reguły umożliwia definiowanie reguł na podstawie danych telemetrycznych, stanu lub zdarzeń urządzeń. Po wygenerowaniu reguły może wyzwolić jedną lub więcej akcji — takich jak wysyłanie wiadomości e-mail, powiadamianie systemu zewnętrznego za pośrednictwem alertów elementu webhook itd. Aby dowiedzieć się więcej, zobacz samouczek [konfigurowania reguł](tutorial-create-telemetry-rules.md) . 

### <a name="analytics"></a>Analiza

> [!div class="mx-imgBorder"]
> ](media/overview-iot-central-tour/analytics-pnp.png) strony analizy ![

Analiza umożliwia tworzenie niestandardowych widoków na podstawie danych urządzenia w celu uzyskania szczegółowych informacji z aplikacji. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie analizy dla aplikacji platformy Azure IoT Central](howto-create-analytics.md) .

### <a name="jobs"></a>Zadania

> [!div class="mx-imgBorder"]
> Strona zadań ![](media/overview-iot-central-tour/jobs-pnp.png)

Strona zadania umożliwia uruchamianie zbiorczych operacji zarządzania urządzeniami na urządzeniach. Można aktualizować właściwości, ustawienia i wykonywanie poleceń dla grup urządzeń. Aby dowiedzieć się więcej, zobacz artykuł [Uruchamianie zadania](howto-run-a-job.md).

### <a name="device-templates"></a>Szablony urządzeń

> [!div class="mx-imgBorder"]
> Strona ![szablonów urządzeń](media/overview-iot-central-tour/templates-pnp.png)

Na stronie szablonów urządzeń konstruktor tworzy szablony urządzeń w aplikacji i zarządza nimi. Szablon urządzenia określa charakterystykę urządzeń, takich jak:

* Pomiary danych telemetrycznych, stanowych i zdarzeń
* Właściwości
* Polecenia
* Widoki

*Konstruktor rozwiązań* umożliwia również tworzenie formularzy i pulpitów nawigacyjnych dla operatorów używanych do zarządzania urządzeniami.

Aby dowiedzieć się więcej, zobacz samouczek [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](howto-set-up-template.md). 

### <a name="data-export"></a>Eksport danych
> [!div class="mx-imgBorder"]
> ](media/overview-iot-central-tour/export-pnp.png) strony eksportu danych ![

Eksport danych umożliwia konfigurowanie strumieni danych, takich jak dane telemetryczne, z aplikacji do systemów zewnętrznych. Aby dowiedzieć się więcej, zobacz artykuł [Eksportowanie danych do usługi Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>{1&gt;Administracja&lt;1}
> [!div class="mx-imgBorder"]
> ](media/overview-iot-central-tour/administration-pnp.png) Strona administracyjna ![

Strona Administracja umożliwia skonfigurowanie i dostosowanie aplikacji IoT Central. W tym miejscu możesz zmienić nazwę aplikacji, adres URL, Tworzenie motywów, zarządzanie użytkownikami i rolami, utworzyć tokeny interfejsu API i wyeksportować aplikację. Aby dowiedzieć się więcej, zobacz artykuł [Administer your Azure IoT Central application (Administrowanie aplikacją usługi Azure IoT Central)](howto-administer.md).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z omówieniem usługi Azure IoT Central i układem interfejsu użytkownika następnym sugerowanym krokiem jest wykonanie przewodnika Szybki start [Create an Azure IoT Central application (Tworzenie aplikacji usługi Azure IoT Central)](quick-deploy-iot-central.md).
