---
title: Tworzenie rozwiązań detalicznych za pomocą usługi Azure IoT Central | Microsoft Docs
description: Dowiedz się więcej o korzystaniu z szablonów aplikacji IoT Central platformy Azure do tworzenia połączonej logistyki, cyfrowego centrum dystrybucji, analizy w sklepie, monitorowania warunków, wypełniania, zarządzania magazynem i rozwiązań detalicznych.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 808a17fcf303c6eb72f11f5379fa172302dc782a
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059860"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Tworzenie rozwiązań dla handlu detalicznego za pomocą usługi Azure IoT Central



Azure IoT Central to platforma aplikacji IoT, która zmniejsza obciążenie i koszty związane z opracowywaniem i konserwowaniem rozwiązań IoT klasy korporacyjnej oraz zarządzania nimi. Wybranie opcji kompilowania za pomocą usługi Azure IoT Central umożliwia skoncentrowanie się na czasie, pieniędzy i energii związanej z transformą działalności w firmie za pomocą danych IoT, a nie tylko utrzymaniem i aktualizacją złożonej i ciągle rosnącej infrastruktury IoT.

W tym artykule opisano kilka szablonów aplikacji IoT Central specyficznych dla handlu detalicznego. Jako Konstruktor rozwiązań możesz użyć tych szablonów do kompilowania rozwiązań IoT, które optymalizują łańcuchy dostaw, ulepszania środowisk w sklepie dla klientów i śledzenia spisu bardziej wydajnie.

> [!div class="mx-imgBorder"]
> ![Omówienie usługi Azure IoT Handle](./media/overview-iot-central-retail/retail-app-templates.png)

W poniższych sekcjach opisano możliwości tych szablonów aplikacji:

## <a name="connected-logistics"></a>Połączona logistyka

Globalne wydatki na logistykę powinny dotrzeć do $10 600 000 000 000 w 2020. Transport kont towarów w przypadku większości tego rodzaju wydatków i dostawców wysyłki podlega intensywnej presji konkurencji i ograniczeniom.

Za pomocą czujników IoT można zbierać i monitorować warunki otoczenia, takie jak temperatura, wilgotność, pochylenie, wstrząsy, światło i lokalizacja wysyłki. Dane telemetryczne zebrane z czujników i urządzeń IoT można połączyć z innymi źródłami danych, takimi jak pogoda i informacje o ruchu w systemach analizy biznesowej w chmurze.

Zalety połączonego rozwiązania logistycznego obejmują:

* Monitorowanie wysyłki przy użyciu śledzenia i śledzenia w czasie rzeczywistym. 
* Integralność wysyłki z monitorowaniem warunków otoczenia w czasie rzeczywistym.
* Bezpieczeństwo przed kradzieżą, utratą lub uszkodzeniem wysyłek.
* Ogrodzenie geograficzne, Optymalizacja tras, zarządzanie flotą i analiza pojazdów.
* Prognozowanie w celu przewidywalnej wysyłki i przybycia wysyłek.

Poniższe zrzuty ekranu przedstawiają wbudowany pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny jest w pełni dostosowywany do konkretnych wymagań dotyczących rozwiązań:

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Aby dowiedzieć się więcej, zobacz samouczek [wdrażanie i przechodzenie przez połączony szablon aplikacji logistycznej](./tutorial-iot-central-connected-logistics-pnp.md) .

## <a name="digital-distribution-center"></a>Cyfrowe centrum dystrybucji

Ponieważ producenci i sprzedawcy detaliczni mają na całym świecie obecność, ich łańcuchy dostaw rozgałęzienia i stają się bardziej skomplikowane. Konsumenci oczekują teraz dużej ilości produktów, które mają być dostępne, a także w ramach jednego lub dwóch dni zakupu. Centra dystrybucji muszą dostosowywać się do tych trendów, jednocześnie utrzymując istniejące nieefektywność. 

Obecnie zależność od pracy ręcznej oznacza, że konta pobierania i pakowania dla 55-65% kosztów centrum dystrybucji. Ręczne pobieranie i pakowanie są również zwykle wolniejsze niż zautomatyzowane systemy i gwałtownie zmieniające się potrzeby związane z zatrudnieniem sprawiają, że jest jeszcze trudniejsze do spełnienia woluminów wysyłkowych. Ta sezonowa fluktuacja skutkuje dużym obrotem dla pracowników i zwiększa prawdopodobieństwo wystąpienia kosztownych błędów.

Rozwiązania oparte na kamerach z obsługą IoT mogą dostarczać zalety transformacji, włączając w to cyfrową pętlę opinii. Dane przesyłane przez centrum dystrybucji prowadzą do szczegółowych informacji umożliwiających podjęcie odpowiednich działań, które z kolei powodują lepsze dane.

Zalety centrum dystrybucji cyfrowej obejmują:

* Aparaty fotograficzne monitorują towary w miarę ich przybycia i przechodzą przez system przenośnika.
* Automatyczna identyfikacja wadliwych towarów.
* Wydajne śledzenie kolejności.
* Obniżone koszty, Zwiększona produktywność i zoptymalizowane użycie.

Poniższy zrzut ekranu przedstawia wbudowany pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny jest w pełni dostosowywany do konkretnych wymagań dotyczących rozwiązań: 

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego Centrum dystrybucji cyfrowej](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Aby dowiedzieć się więcej, zobacz samouczek [wdrażanie i przechodzenie przez centrum dystrybucji aplikacji w formie elektronicznej](./tutorial-iot-central-digital-distribution-center-pnp.md) .

## <a name="in-store-analytics---condition-monitoring"></a>Monitorowanie warunku analizy w sklepie

W przypadku wielu detalistów warunki środowiskowe w ich magazynach są kluczowym odróżnianiem od ich konkurentów. Detaliści chcą zachować przyjemne warunki w swoich magazynach w celu skorzystania z korzyści dla klientów.  

Jako Konstruktor rozwiązań można użyć szablonu aplikacji monitorowanie warunków analizy w sklepie IoT Central, aby utworzyć kompleksowe rozwiązanie. Szablon aplikacji umożliwia cyfrowe łączenie się ze środowiskiem magazynu detalicznego i monitorowanie go przy użyciu różnych rodzajów urządzeń czujników. Te urządzenia czujnika generują dane telemetryczne, które można przekonwertować na usługi Business Insights w celu zmniejszenia kosztów operacyjnych i stworzenia doskonałego środowiska dla klientów.

Użyj szablonu aplikacji, aby:

* Połącz różne czujniki IoT z wystąpieniem aplikacji IoT Central.
* Monitoruj kondycję sieci czujnika i zarządzaj nimi oraz urządzeniami bramy w środowisku.
* Tworzenie reguł niestandardowych wokół warunków środowiska w magazynie w celu wyzwalania alertów dla menedżerów sklepów.
* Przekształć warunki środowiska w sklepie w szczegółowe informacje, które zespół ds. sprzedaży może wykorzystać w celu poprawy obsługi klienta.
* Wyeksportuj zagregowane informacje do istniejących lub nowych aplikacji firmowych, aby zapewnić przydatną i czasową informację dla pracowników detalicznych.

Szablon aplikacji zawiera zestaw szablonów urządzeń i używa zestawu symulowanych urządzeń do wypełnienia pulpitu nawigacyjnego. 

Poniższy zrzut ekranu przedstawia wbudowany pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny jest w pełni dostosowywany do konkretnych wymagań dotyczących rozwiązań: 

> [!div class="mx-imgBorder"]
> ![monitorowanie warunku analizy w sklepie](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Aby dowiedzieć się więcej, zobacz samouczek [Tworzenie aplikacji analizy w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) .

## <a name="in-store-analytics---checkout"></a>Analiza w sklepie — wyewidencjonowywanie

W przypadku niektórych sprzedawców detalicznych środowisko wyewidencjonowywania w swoich magazynach jest kluczem odróżniającym od ich konkurencji. Detaliści chcą zapewnić bezproblemowe środowisko wyewidencjonowywania w swoich sklepach, aby zachęcić klientów do powrotu.  

Jako Konstruktor rozwiązań możesz użyć szablonu aplikacji do wyewidencjonowania analiz IoT Central w sklepie, aby skompilować rozwiązanie, które umożliwia uzyskanie wglądu w informacje o strefie wyewidencjonowywania sklepu w sklepie do pracowników detalicznych. Na przykład czujniki mogą podawać informacje o długości kolejki i średni czas oczekiwania dla każdego toru wyewidencjonowania.

Użyj szablonu aplikacji, aby:

* Połącz różne czujniki IoT z wystąpieniem aplikacji IoT Central.
* Monitoruj kondycję sieci czujnika i zarządzaj nimi oraz urządzeniami bramy w środowisku.
* Tworzenie reguł niestandardowych wokół stanu wyewidencjonowania w sklepie w celu wyzwalania alertów dla pracowników detalicznych.
* Przekształć warunki wyewidencjonowania w sklepie, aby uzyskać szczegółowe informacje, które zespół ds. sprzedaży może wykorzystać do poprawy obsługi klienta.
* Wyeksportuj zagregowane informacje do istniejących lub nowych aplikacji firmowych, aby zapewnić przydatną i czasową informację dla pracowników detalicznych.

Szablon aplikacji zawiera zestaw szablonów urządzeń i używa zestawu symulowanych urządzeń do wypełnienia pulpitu nawigacyjnego danymi o obsadzeniu Lane. 

Poniższy zrzut ekranu przedstawia wbudowany pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny jest w pełni dostosowywany do konkretnych wymagań dotyczących rozwiązań: 

> [!div class="mx-imgBorder"]
> ![wyewidencjonowania analiz w sklepie](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Aby dowiedzieć się więcej, zobacz samouczek [Tworzenie aplikacji analizy w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) .

## <a name="smart-inventory-management"></a>Inteligentne zarządzanie magazynem

Spis to zapasy towarów, które znajdują się w sprzedaży detalicznej. Zarządzanie spisem ma kluczowe znaczenie dla zapewnienia, że odpowiedni produkt znajduje się w odpowiednim miejscu w odpowiednim czasie. Sprzedaż detaliczna musi równoważyć koszty magazynowania zbyt dużej ilości zasobów, aby nie mieć wystarczających elementów w magazynie, aby zaspokoić zapotrzebowanie.

Dane IoT generowane na podstawie tagów identyfikacji radiowej (RFID), sygnałów nawigacyjnych i kamer zapewniają okazje do usprawnienia procesów zarządzania zapasami. Dane telemetryczne zebrane z czujników i urządzeń IoT można połączyć z innymi źródłami danych, takimi jak pogoda i informacje o ruchu w systemach analizy biznesowej w chmurze.

Zalety zarządzania magazynem inteligentnym obejmują:

* Zmniejszenie ryzyka związanego z magazynowaniem elementów i zapewnienie odpowiedniego poziomu usług klienta. 
* Szczegółowa analiza i szczegółowe informacje o dokładności spisu w czasie niemal rzeczywistym.
* Narzędzia ułatwiające podjęcie decyzji dotyczącej odpowiedniej ilości zapasów do przechowywania w celu spełnienia zamówień klienta.

Ten szablon aplikacji koncentruje się na łączności urządzeń oraz konfiguracji i zarządzania urządzeniami czytników RFID i Bluetooth Low Energy (beli).

Poniższy zrzut ekranu przedstawia wbudowany pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny jest w pełni dostosowywany do konkretnych wymagań dotyczących rozwiązań:

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Aby dowiedzieć się więcej, zobacz samouczek [wdrażanie i przechodzenie przez szablon aplikacji do zarządzania magazynem inteligentnym](./tutorial-iot-central-smart-inventory-management-pnp.md) .

## <a name="micro-fulfillment-center"></a>Centrum mikrorealizacji

W coraz większym stopniu konkurencyjnych sprzedaży detalicznych detalistów stale zwiększają nacisk, aby zamknąć przerwy między zapotrzebowaniem i realizacją. Nowym trendem, który miał na celu rozwiązanie rosnącego zapotrzebowania na konsumenta, jest przechowywanie spisu w bliskich klientów końcowych i w odwiedzanych przez nich sklepach.

Szablon aplikacji Centrum IoT Central Micro-zrealizować umożliwia konstruktorom rozwiązań monitorowanie i zarządzanie wszystkimi aspektami w pełni zautomatyzowanych centrów realizacji. Szablon zawiera zestaw czujników monitorowania warunku i operatorów zautomatyzowanych, które przyspieszają proces opracowywania rozwiązania. Te urządzenia czujnika przechwytują znaczące sygnały, które mogą być konwertowane na usługi Business Insights umożliwiające detalistom obniżenie kosztów operacyjnych i tworzenie środowisk dla klientów.

Szablon aplikacji umożliwia wykonywanie: 

- Bezproblemowo łącz różne rodzaje czujników IoT, takich jak w przypadku programu robotów lub czujników monitorowania warunku, do wystąpienia aplikacji IoT Central.
- Monitoruj kondycję sieci czujnika i zarządzaj nimi oraz wszystkie urządzenia bramy w środowisku.
- Utwórz reguły niestandardowe wokół warunków środowiska w centrum realizacji, aby wyzwolić odpowiednie alerty.
- Przekształć warunki środowiska w centrum realizacji w szczegółowe dane, które mogą być wykorzystywane przez zespół ds. handlu detalicznego.
- Wyeksportuj zagregowane informacje do istniejących lub nowych aplikacji firmowych, aby uzyskać korzyści dla członków działu sprzedaży detalicznej.

Poniższy zrzut ekranu przedstawia wbudowany pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny jest w pełni dostosowywany do konkretnych wymagań dotyczących rozwiązań:

> [!div class="mx-imgBorder"]
> ](./media/overview-iot-central-retail/MFC-Dashboard.png) centrum ![Micro-zrealizować

Aby dowiedzieć się więcej, zapoznaj się z samouczkiem [wdrażanie i przechodzenie do szablonu aplikacji Centrum programu Micro-zrealizować](./tutorial-micro-fulfillment-center-pnp.md) .

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć tworzenie rozwiązania do sprzedaży detalicznej:

* Rozpocznij pracę z samouczkiem [Tworzenie aplikacji do analizy w Sklepie Azure IoT Central, w](./tutorial-in-store-analytics-create-app-pnp.md) której przedstawiono sposób tworzenia rozwiązania przy użyciu jednego z szablonów aplikacji do analizy w sklepie.
* [Wdrażanie i przechodzenie przez połączony szablon aplikacji logistycznej](./tutorial-iot-central-connected-logistics-pnp.md).
* [Wdróż i przeprowadź procedurę za pomocą szablonu aplikacji Digital Distribution Center](./tutorial-iot-central-digital-distribution-center-pnp.md).
* [Wdróż i przeprowadź procedurę przy użyciu szablonu aplikacji do zarządzania magazynem inteligentnym](./tutorial-iot-central-smart-inventory-management-pnp.md).
* [Wdróż i zapoznaj się z szablonem aplikacji Centrum programu Micro-zrealizować](./tutorial-micro-fulfillment-center-pnp.md).
* Dowiedz się więcej o IoT Central w [IoT Central przegląd](../preview/overview-iot-central.md).
