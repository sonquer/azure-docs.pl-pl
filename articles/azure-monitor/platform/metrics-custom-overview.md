---
title: Metryki niestandardowe w Azure Monitor
description: Dowiedz się więcej o metrykach niestandardowych w Azure Monitor i sposobach ich modelowania.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 744958fc44a8d10bbc8ca5d44af8c473548ae5ca
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669163"
---
# <a name="custom-metrics-in-azure-monitor"></a>Metryki niestandardowe w Azure Monitor

Podczas wdrażania zasobów i aplikacji na platformie Azure należy rozpocząć zbieranie danych telemetrycznych w celu uzyskania szczegółowych informacji o wydajności i kondycji. Platforma Azure sprawia, że niektóre metryki są dostępne dla użytkownika. Te metryki są nazywane standardem lub platformą. Jednak są one ograniczone. Możesz chcieć zebrać niektóre niestandardowe wskaźniki wydajności lub metryki specyficzne dla danej firmy, aby zapewnić dokładniejsze informacje.
Te metryki **niestandardowe** można zbierać za pośrednictwem danych telemetrycznych aplikacji, agenta uruchamianego w zasobach platformy Azure, a nawet z zewnętrznego systemu monitorowania i przesyłanego bezpośrednio do Azure monitor. Po opublikowaniu w usłudze Azure Monitor można przeglądać, wysyłać zapytania i otrzymywać alerty dotyczące niestandardowych metryk dla zasobów platformy Azure i aplikacji obok standardowych metryk emitowanych przez platformę Azure.

## <a name="send-custom-metrics"></a>Wysyłanie metryk niestandardowych
Niestandardowe metryki mogą być wysyłane do Azure Monitor za pomocą kilku metod:
- Instrumentacja aplikacji przy użyciu zestawu Azure Application Insights SDK i wysyłania niestandardowej telemetrii do Azure Monitor. 
- Zainstaluj rozszerzenie Windows Diagnostyka Azure (funkcji wad) na [maszynie wirtualnej platformy Azure](collect-custom-metrics-guestos-resource-manager-vm.md), [zestawie skalowania maszyn wirtualnych](collect-custom-metrics-guestos-resource-manager-vmss.md), [klasycznej maszynie wirtualnej](collect-custom-metrics-guestos-vm-classic.md)lub [klasycznej Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) i Wyślij liczniki wydajności do Azure monitor. 
- Zainstaluj [agenta InfluxData telegraf](collect-custom-metrics-linux-telegraf.md) na maszynie wirtualnej z systemem Linux systemu Azure i wysyłaj metryki przy użyciu wtyczki danych wyjściowych Azure monitor.
- Wysyłać niestandardowe metryki [bezpośrednio do interfejsu API REST Azure monitor](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

W przypadku wysyłania niestandardowych metryk do Azure Monitor, każdy punkt danych lub wartość, raportowane muszą zawierać następujące informacje.

### <a name="authentication"></a>Authentication
Aby przesłać niestandardowe metryki do Azure Monitor, jednostka, która przesyła metrykę, musi być prawidłowym tokenem Azure Active Directory (Azure AD) w nagłówku **okaziciela** żądania. Istnieje kilka obsługiwanych metod uzyskiwania prawidłowego tokenu okaziciela:
1. [Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Zwraca tożsamość do zasobu platformy Azure, na przykład maszynę wirtualną. Tożsamość usługi zarządzanej (MSI) jest zaprojektowana w celu przyznania zasobom uprawnień do wykonywania określonych operacji. Przykładem jest umożliwienie zasobowi emisji metryk dotyczących siebie. Do zasobu lub jego pliku MSI można przyznać uprawnienia **wydawcy metryk monitorowania** dla innego zasobu. Za pomocą tego uprawnienia plik MSI może również emitować metryki dla innych zasobów.
2. Nazwa [główna usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). W tym scenariuszu aplikacja usługi Azure AD lub usługa może mieć przypisane uprawnienia do emisji metryk dotyczących zasobu platformy Azure.
Aby uwierzytelnić żądanie, Azure Monitor sprawdza poprawność tokenu aplikacji przy użyciu kluczy publicznych usługi Azure AD. Istniejące rola **wydawcy metryk monitorowania** ma już to uprawnienie. Jest on dostępny w Azure Portal. Nazwa główna usługi, w zależności od tego, jakie zasoby emitują metryki niestandardowe, może mieć rolę **wydawcy metryk monitorowania** w wymaganym zakresie. Przykłady to subskrypcja, Grupa zasobów lub określony zasób.

> [!NOTE]  
> Po zażądaniu tokenu usługi Azure AD w celu wyemitowania metryk niestandardowych upewnij się, że dla odbiorców lub zasobów zażądano tokenu https://monitoring.azure.com/. Pamiętaj, aby uwzględnić końcowy znak "/".

### <a name="subject"></a>Podmiot
Ta właściwość służy do przechwytywania identyfikatora zasobu platformy Azure, dla którego jest raportowana Metryka niestandardowa. Te informacje zostaną zakodowane w adresie URL wywoływanego wywołania interfejsu API. Każdy interfejs API może przesyłać tylko wartości metryk dla pojedynczego zasobu platformy Azure.

> [!NOTE]  
> Nie można emitować metryk niestandardowych względem identyfikatora zasobu grupy zasobów lub subskrypcji.
>
>

### <a name="region"></a>Region
Ta właściwość przechwytuje, w jakim regionie platformy Azure jest wdrażany zasób, dla którego są emitowane metryki. Metryki muszą być emitowane do tego samego Azure Monitor regionalnego punktu końcowego, co region, w którym jest wdrożony zasób. Na przykład niestandardowe metryki maszyny wirtualnej wdrożonej w regionie zachodnie stany USA muszą zostać wysłane do regionu punktu końcowego Azure Monitor zachodniej. Informacje o regionie również są kodowane w adresie URL wywołania interfejsu API.

> [!NOTE]  
> W publicznej wersji zapoznawczej metryki niestandardowe są dostępne tylko w podzbiorze regionów świadczenia usługi Azure. Lista obsługiwanych regionów została udokumentowana w dalszej części tego artykułu.
>
>

### <a name="timestamp"></a>Znacznik czasu
Każdy punkt danych wysyłany do Azure Monitor musi być oznaczony sygnaturą czasową. Ta sygnatura czasowa przechwytuje datę i godzinę, o której wartość metryki jest mierzona lub zbierana. Azure Monitor akceptuje dane metryk z sygnaturami czasowymi w ciągu ostatnich i 5 minut w przyszłości. Sygnatura czasowa musi być w formacie ISO 8601.

### <a name="namespace"></a>Przestrzeń nazw
Przestrzenie nazw umożliwiają kategoryzowanie i grupowanie podobnych metryk jednocześnie. Używając przestrzeni nazw, można osiągnąć izolację między grupami metryk, które mogą zbierać różne szczegółowe dane lub wskaźniki wydajności. Na przykład może istnieć przestrzeń nazw o nazwie **contosomemorymetrics** , która śledzi metryki użycia pamięci, które profilują aplikację. Inna przestrzeń nazw o nazwie **contosoapptransaction** może śledzić wszystkie metryki dotyczące transakcji użytkownika w aplikacji.

### <a name="name"></a>Nazwa
**Nazwa** to nazwa metryki, która jest raportowana. Zwykle nazwa jest wystarczająco opisowa, aby pomóc w ustaleniu, co jest mierzone. Przykładem jest Metryka, która mierzy liczbę bajtów pamięci używanych na danej maszynie wirtualnej. Może istnieć Nazwa metryki, taka jak **bajty pamięci**.

### <a name="dimension-keys"></a>Klucze wymiarów
Wymiar to para klucz lub wartość ułatwiająca opisywanie dodatkowych cech dotyczących zbieranych metryk. Korzystając z dodatkowych cech, można zebrać więcej informacji na temat metryki, która umożliwia dokładniejszy wgląd w szczegółowe dane. Na przykład Metryka **bajtów pamięci w użyciu** może mieć klucz wymiaru o nazwie **Process** , który przechwytuje, ile bajtów pamięci każdy proces na maszynie wirtualnej zużywa. Za pomocą tego klucza można odfiltrować metrykę, aby zobaczyć, ile procesów specyficznych dla pamięci używa lub do identyfikowania pięciu pierwszych procesów według użycia pamięci.
Wymiary są opcjonalne, nie wszystkie metryki mogą mieć wymiary. Metryka niestandardowa może zawierać maksymalnie 10 wymiarów.

### <a name="dimension-values"></a>Wartości wymiarów
Przy raportowaniu punktu danych metryki dla każdego klucza wymiaru w raportowanej metryki istnieje odpowiadająca wartość wymiaru. Na przykład możesz chcieć zgłosić pamięć używaną przez ContosoApp na maszynie wirtualnej:

* Nazwa metryki będzie **używana przez bajty pamięci**.
* Klucz wymiaru będzie **przetwarzany**.
* Wartość wymiaru to **ContosoApp. exe**.

Podczas publikowania wartości metryki można określić tylko jedną wartość wymiaru dla każdego klucza wymiaru. Jeśli zbierasz to samo użycie pamięci dla wielu procesów na maszynie wirtualnej, możesz zgłosić wiele wartości metryk dla tej sygnatury czasowej. Każda wartość metryki będzie określać inną wartość wymiaru dla klucza wymiaru **procesu** .
Wymiary są opcjonalne, nie wszystkie metryki mogą mieć wymiary. Jeśli wpis metryki definiuje klucze wymiarów, odpowiednie wartości wymiarów są obowiązkowe.

### <a name="metric-values"></a>Wartości metryk
Azure Monitor przechowuje wszystkie metryki w interwałach dokładności jednej minuty. Rozumiemy, że w ciągu danej minuty może być konieczne kilkakrotne Przepróbkowanie metryk. Przykładem jest użycie procesora CPU. Lub może być konieczne zmierzenie dla wielu dyskretnych zdarzeń. Przykładem są opóźnienia transakcji logowania. Aby ograniczyć liczbę nieprzetworzonych wartości, które mają być emitowane i płatne w Azure Monitor, można lokalnie agregować i emitować wartości:

* **Minimum**: minimalna obserwowana wartość ze wszystkich próbek i pomiarów w ciągu minuty.
* **Max**: Maksymalna obserwowana wartość ze wszystkich próbek i pomiarów w ciągu minuty.
* **Sum**: suma wszystkich obserwowanych wartości ze wszystkich próbek i pomiarów w ciągu minuty.
* **Liczba**: liczba próbek i pomiarów wykonanych w ciągu minuty.

Na przykład jeśli w danej chwili wystąpiło 4 transakcje logowania do aplikacji, wyniki mierzonych opóźnień dla każdego z nich mogą być następujące:

|Transakcja 1|Transakcja 2|Transakcja 3|Transakcja 4|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|
|

Następnie wynikająca z niej publikacja metryk Azure Monitor będzie następująca:
* Min.: 4
* Maks.: 16
* Suma: 40
* Liczba: 4

Jeśli aplikacja nie może wstępnie agregować lokalnie i musi emitować każdą dyskretną próbkę lub zdarzenie bezpośrednio po kolekcji, można emitować nieprzetworzone wartości miary. Na przykład za każdym razem, gdy w aplikacji jest wykonywana transakcja logowania, publikuje się metrykę w celu Azure Monitor z tylko jednym pomiarem. W przypadku transakcji logowania, która zajęła 12 MS, publikacja metryk będzie następująca:
* Min.: 12
* Maks.: 12
* Suma: 12
* Liczba: 1

Za pomocą tego procesu można emitować wiele wartości dla tej samej metryki oraz kombinację wymiarów w danej minucie. Azure Monitor następnie pobiera wszystkie nieprzetworzone wartości dla danej minuty i agreguje je razem.

### <a name="sample-custom-metric-publication"></a>Przykładowa publikacja metryki niestandardowej
W poniższym przykładzie utworzysz metrykę niestandardową o nazwie **bajty pamięci używanej** w **profilu pamięci** przestrzeni nazw metryki dla maszyny wirtualnej. Metryka zawiera pojedynczy wymiar o nazwie **Process**. Dla danego znacznika czasu emitujemy wartości metryk dla dwóch różnych procesów:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, rozszerzenie diagnostyki oraz Agent telegraf InfluxData są już skonfigurowane do emisji wartości metryk względem poprawnego regionalnego punktu końcowego i przenoszenia wszystkich powyższych właściwości w każdej emisji.
>
>

## <a name="custom-metric-definitions"></a>Niestandardowe definicje metryk
Nie ma potrzeby definiowania wstępnie zdefiniowanej metryki niestandardowej w Azure Monitor przed wyemitowaniem. Każdy opublikowany punkt danych metryki zawiera przestrzeń nazw, nazwę i informacje o wymiarze. W związku z tym podczas pierwszej emisji metryki niestandardowej do Azure Monitor zostanie automatycznie utworzona definicja metryki. Ta definicja metryki jest następnie wykrywalna dla dowolnego zasobu, z którym Metryka jest emitowana za pośrednictwem definicji metryk.

> [!NOTE]  
> Azure Monitor nie obsługuje jeszcze definiowania **jednostek** dla metryki niestandardowej.

## <a name="using-custom-metrics"></a>Korzystanie z metryk niestandardowych
Po przesłaniu metryk niestandardowych do Azure Monitor można je przeglądać za pośrednictwem Azure Portal i wysyłać do nich zapytania za pośrednictwem Azure Monitor interfejsów API REST. Możesz również utworzyć na nich alerty, aby powiadomić Cię, gdy zostaną spełnione określone warunki.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Przeglądaj niestandardowe metryki za pomocą Azure Portal
1.  Przejdź do witryny [Azure Portal](https://portal.azure.com).
2.  Wybierz okienko **monitorowanie** .
3.  Wybierz pozycję **Metryki**.
4.  Wybierz zasób, względem którego wyemitowano metryki niestandardowe.
5.  Wybierz przestrzeń nazw metryk dla metryki niestandardowej.
6.  Wybierz metrykę niestandardową.

## <a name="supported-regions"></a>Obsługiwane regiony
W publicznej wersji zapoznawczej możliwość publikowania metryk niestandardowych jest dostępna tylko w podzbiorze regionów świadczenia usługi Azure. To ograniczenie oznacza, że metryki mogą być publikowane tylko dla zasobów w jednym z obsługiwanych regionów. W poniższej tabeli przedstawiono zestaw obsługiwanych regionów świadczenia usługi Azure dla metryk niestandardowych. Wyświetla także odpowiednie punkty końcowe, które metryki dla zasobów w tych regionach powinny być publikowane w:

|Region platformy Azure |Prefiks regionu punktu końcowego|
|---|---|
| **Stany Zjednoczone i Kanada** | |
|Zachodnio-środkowe stany USA | https:\//westcentralus.monitoring.azure.com/ |
|Zachodnie stany USA 2       | https:\//westus2.monitoring.azure.com/ |
|Północno-środkowe stany USA | https:\//northcentralus.monitoring.azure.com
|Południowo-środkowe stany USA| https:\//southcentralus.monitoring.azure.com/ |
|Środkowe stany USA      | https:\//centralus.monitoring.azure.com |
|Kanada Środkowa | https:\//canadacentral.Monitoring.Azure.Comc
|Wschodnie stany USA| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Europa Północna    | https:\//northeurope.monitoring.azure.com/ |
|Europa Zachodnia     | https:\//westeurope.monitoring.azure.com/ |
|Południowe Zjednoczone Królestwo | https:\//uksouth.monitoring.azure.com
|Francja Środkowa | https:\//francecentral.monitoring.azure.com |
| **Kazał** | |
|Północna Republika Południowej Afryki | https:\//southafricanorth.monitoring.azure.com
| **Azja** | |
|Indie Środkowe | https:\//centralindia.monitoring.azure.com
|Australia Wschodnia | https:\//australiaeast.monitoring.azure.com
|Japonia Wschodnia | https:\//japaneast.monitoring.azure.com
|Azja Południowo-Wschodnia  | https:\//southeastasia.monitoring.azure.com |
|Azja Wschodnia | https:\//eastasia.monitoring.azure.com
|Korea Środkowa   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Limity przydziału i ograniczenia
Azure Monitor nakładają następujące limity użycia na metryki niestandardowe:

|Kategoria|Limit|
|---|---|
|Aktywna seria czasowa/subskrypcje/region|50 000|
|Klucze wymiarów na metrykę|10|
|Długość ciągu dla przestrzeni nazw metryk, nazw metryk, kluczy wymiarów i wartości wymiarów|256 znaków|

Aktywna seria czasowa jest definiowana jako każda unikatowa kombinacja metryki, klucza wymiaru lub wartości wymiaru, które miały wartości metryk opublikowanych w ciągu ostatnich 12 godzin.

## <a name="next-steps"></a>Następne kroki
Użyj niestandardowych metryk z różnych usług: 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Zestaw skalowania maszyn wirtualnych](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Virtual Machines platformy Azure (wersja klasyczna)](collect-custom-metrics-guestos-vm-classic.md)
 - [Maszyna wirtualna z systemem Linux przy użyciu agenta telegraf](collect-custom-metrics-linux-telegraf.md)
 - [Interfejs API REST](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Cloud Services klasyczny](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
