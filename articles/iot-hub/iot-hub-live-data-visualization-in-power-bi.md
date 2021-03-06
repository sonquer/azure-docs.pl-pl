---
title: Wizualizacja danych w czasie rzeczywistym danych FRM Azure IoT Hub — Power BI
description: Użyj Power BI, aby wizualizować dane temperatury i wilgotności zbierane z czujnika i wysyłane do usługi Azure IoT Hub.
author: robinsh
keywords: Wizualizacja danych w czasie rzeczywistym, Wizualizacja danych na żywo, Wizualizacja danych czujników
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954626"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z poziomu platformy Azure IoT Hub przy użyciu Power BI

![Diagram kompleksowy](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym, które usługa Azure IoT Hub otrzymuje przy użyciu Power BI. Jeśli chcesz spróbować wizualizować dane w centrum IoT przy użyciu aplikacji sieci Web, zobacz [Używanie aplikacji sieci Web do wizualizacji danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co robisz

* Przygotuj Centrum IoT Hub do dostępu do danych, dodając grupę odbiorców.

* Utwórz, skonfiguruj i uruchom zadanie Stream Analytics na potrzeby transferu danych z Centrum IoT Hub do konta usługi Power BI.

* Utwórz i Opublikuj raport Power BI, aby wizualizować dane.

## <a name="what-you-need"></a>Co jest potrzebne

* Ukończ samouczek [gry online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jedno z samouczków dotyczących urządzeń; na przykład [Raspberry Pi przy użyciu środowiska Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Te artykuły obejmują następujące wymagania:
  
  * Aktywna subskrypcja platformy Azure.
  * Usługa Azure IoT Hub w ramach Twojej subskrypcji.
  * Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT Hub.

* A Power BI account. ([Wypróbuj Power BI bezpłatnie](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, Konfigurowanie i uruchamianie zadania Stream Analytics

Zacznijmy od utworzenia zadania Stream Analytics. Po utworzeniu zadania należy zdefiniować dane wejściowe, dane wyjściowe i zapytanie używane do pobierania danych.

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **utwórz zasób** > **Internet rzeczy** > **zadanie Stream Analytics**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupy zasobów, która jest używana przez Centrum IoT Hub.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupa zasobów.

   ![Tworzenie zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Wybierz pozycję **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie Stream Analytics.

2. W obszarze **topologia zadania**wybierz pozycję **dane wejściowe**.

3. W okienku **dane** wejściowe wybierz pozycję **Dodaj strumień wejściowy**, a następnie wybierz pozycję **IoT Hub** z listy rozwijanej. W okienku nowe dane wejściowe wprowadź następujące informacje:

   **Alias wejściowy**: wprowadź unikatowy alias dla danych wejściowych.

   **Podaj IoT Hub z subskrypcji**: Wybierz ten przycisk radiowy.

   **Subskrypcja**: wybierz subskrypcję platformy Azure, która jest używana w tym samouczku.

   **IoT Hub**: Wybierz IoT Hub używany w tym samouczku.

   **Punkt końcowy**: wybierz pozycję **Obsługa komunikatów**.

   **Nazwa zasad dostępu współdzielonego**: wybierz nazwę zasad dostępu współdzielonego, które mają być używane przez zadanie Stream Analytics dla Centrum IoT. Na potrzeby tego samouczka możesz wybrać pozycję *Usługa*. Zasady *usługi* są tworzone domyślnie w nowych centrach IoT i przyznają uprawnienia do wysyłania i odbierania na punktach końcowych w chmurze uwidocznionych przez Centrum IoT. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu i uprawnienia](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klucz zasad dostępu współdzielonego**: to pole jest wypełniane automatycznie na podstawie wyboru nazwy zasad dostępu współdzielonego.

   **Grupa konsumentów**: Wybierz utworzoną wcześniej grupę odbiorców.

   Pozostaw domyślne wszystkie inne pola.

   ![Dodawanie danych wejściowych do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **topologia zadania**wybierz pozycję dane **wyjściowe**.

2. W okienku dane **wyjściowe** wybierz pozycję **Dodaj** i **Power BI**.

3. W okienku **Power BI nowe dane wyjściowe** wybierz pozycję **Autoryzuj** i postępuj zgodnie z monitami, aby zalogować się do konta Power BI.

4. Po zalogowaniu się do Power BI wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias dla danych wyjściowych.

   **Obszar roboczy grupy**: Wybierz obszar roboczy grupy docelowej.

   **Nazwa zestawu danych**: Wprowadź nazwę zestawu danych.

   **Nazwa tabeli**: Wprowadź nazwę tabeli.

   ![Dodawanie danych wyjściowych do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

   ![Dodawanie zapytania do zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Wybierz pozycję **Zapisz**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu Stream Analytics wybierz pozycję **Przegląd**, a następnie wybierz pozycję **rozpocznij** > **teraz** > **Rozpocznij**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Utwórz i Opublikuj raport Power BI, aby wizualizować dane

1. Upewnij się, że przykładowa aplikacja jest uruchomiona na urządzeniu. Jeśli nie, możesz zapoznać się z samouczkami w obszarze [Konfigurowanie urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/en-us/).

3. Wybierz obszar roboczy, który jest używany, **Mój obszar roboczy**.

4. Wybierz pozycję **zestawy danych**.

   Powinien zostać wyświetlony zestaw danych, który został określony podczas tworzenia danych wyjściowych dla zadania Stream Analytics.

5. Dla utworzonego zestawu danych wybierz pozycję **Dodaj raport** (Pierwsza ikona z prawej strony nazwy zestawu danych).

   ![Tworzenie raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   1. W okienku **wizualizacje** na stronie Tworzenie raportu wybierz ikonę wykres liniowy, aby dodać wykres liniowy.

   2. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.

   3. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   4. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

      Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

      ![Dodaj wykres liniowy dla temperatury do raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby to zrobić, wykonaj te same kroki i umieść **EventEnqueuedUtcTime** na osi x i **wilgotności** na osi y.

   ![Dodaj wykres liniowy dla wilgotności do raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Wybierz pozycję **Zapisz** , aby zapisać raport.

9. W okienku po lewej stronie wybierz pozycję **raporty** , a następnie wybierz właśnie utworzony raport.

10. Wybierz pozycję **plik** > **Publikowanie w sieci Web**.

11. Wybierz pozycję **Utwórz kod osadzania**, a następnie wybierz pozycję **Publikuj**.

Otrzymasz link do raportu, który można udostępnić każdemu użytkownikowi, który będzie miał dostęp do raportów, i fragment kodu, którego możesz użyć do zintegrowania raportu w blogu lub witrynie sieci Web.

![Publikowanie raportu Power BI firmy Microsoft](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Firma Microsoft oferuje również [Power BI aplikacje mobilne](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) do wyświetlania Power BI pulpitów nawigacyjnych i raportów na urządzeniu przenośnym oraz współpracy z nimi.

## <a name="next-steps"></a>Następne kroki

Pomyślnie użyto Power BI do wizualizacji danych czujników w czasie rzeczywistym z usługi Azure IoT Hub.

Aby uzyskać inny sposób wizualizacji danych z usługi Azure IoT Hub, zobacz [Korzystanie z aplikacji sieci Web w celu wizualizowania danych czujników w czasie rzeczywistym z poziomu platformy azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
