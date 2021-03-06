---
title: zamiana tekstu na mowę często zadawane pytania
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi zamiana tekstu na mowę.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 19b8be83a3678164197ec0650b07091e941a04d7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110494"
---
# <a name="text-to-speech-frequently-asked-questions"></a>zamiana tekstu na mowę często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym często zadawane pytania, zapoznaj się z [innymi opcjami pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**P: Jaka jest różnica między standardowym modelem głosu i niestandardowym modelem głosu?**

Odp **.: standardowy**model głosu (nazywany także _czcionką głosową_) został przeszkolony przy użyciu danych firmy Microsoft i jest już wdrożony w chmurze. Możesz użyć niestandardowego modelu głosowego, aby dostosować średni model i przenieść Timbre i wyrażenie stylu głosu osoby mówiącej lub nauczyć pełny nowy model na podstawie danych szkoleniowych przygotowanych przez użytkownika. Obecnie coraz więcej i więcej klientów chce, aby botów się z jednym z marek. Niestandardowa platforma do tworzenia głosu to właściwy wybór dla tej opcji.

**P: gdzie mogę zacząć korzystać z standardowego modelu głosu?**

Odp **.: za**pośrednictwem żądań HTTP dostępne są ponad 80 standardowych modeli głosu w ponad 45 językach. Najpierw pobierz [klucz subskrypcji](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Aby wykonać wywołania REST do prewdrażanych modeli głosu, zobacz [interfejs API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**P: Jeśli chcę korzystać z dostosowanego modelu głosu, czy interfejs API jest taki sam jak ten, który jest używany w odniesieniu do standardowych głosów?**

Odp **.: po**utworzeniu i wdrożeniu niestandardowego modelu głosu uzyskasz unikatowy punkt końcowy dla modelu. Aby użyć głosu do mówienia w aplikacjach, należy określić punkt końcowy w żądaniach HTTP. Te same funkcje, które są dostępne w interfejsie API REST dla usługi zamiana tekstu na mowę, są dostępne dla niestandardowego punktu końcowego. Dowiedz się [, jak utworzyć niestandardowy punkt końcowy i korzystać z niego](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**P: Czy muszę przygotować dane szkoleniowe do tworzenia niestandardowych modeli głosowych?**

Odp **.: tak**, musisz samodzielnie przygotować dane szkoleniowe dla niestandardowego modelu głosu.

Kolekcja danych mowy jest wymagana do utworzenia niestandardowego modelu głosu. Ta kolekcja składa się z zestawu plików audio nagrań mowy i pliku tekstowego transkrypcji każdego pliku dźwiękowego. Wynik cyfrowego głosu zależy od jakości danych szkoleniowych. Aby utworzyć dobry głos zamiany tekstu na mowę, ważne jest, aby nagranie odbywało się w cichym pokoju z wysoką jakością, stałym mikrofonem. Spójna głośność, częstotliwość mówienia i gęstość głosu, a nawet spójność mannerisms mowy jest niezwykle ważna dla tworzenia wspaniałego cyfrowego głosu. Zdecydowanie zalecamy nagrywanie głosów w programie nagrywanie Studio.

Obecnie nie zapewniamy obsługi rejestrowania w trybie online ani nie masz żadnych zaleceń dotyczących nagrywania programu Studio. Wymagania dotyczące formatu można znaleźć w temacie [How to Prepare nagrań i transkrypcji](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**P: jakie skrypty należy użyć, aby zarejestrować dane mowy dla niestandardowego szkolenia głosu?**

Odp **.: nie**ograniczamy skryptów do nagrywania głosu. Możesz użyć własnych skryptów, aby zarejestrować mowę. Wystarczy upewnić się, że w danych mowy masz wystarczające pokrycie fonetyczne. Aby nauczyć się niestandardowego głosu, możesz zacząć od małej ilości danych mowy, co może być 50 różnych zdań (około 3-5 minut na mowę). Im więcej danych zapewniasz, tym bardziej naturalny będzie Twój głos. Możesz rozpocząć uczenie pełnej czcionki głosowej, gdy podajesz nagrania więcej niż 2 000 zdań (około 3-4 godz.). Aby uzyskać wysoką jakość głosu, należy przygotować nagrania z więcej niż 6 000 zdań (około 8-10 godz.).

Oferujemy dodatkowe usługi ułatwiające przygotowanie skryptów do nagrywania. Skontaktuj się z działem [obsługi klienta niestandardowego](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) na potrzeby zapytań.

**P: co zrobić, jeśli potrzebuję większej współbieżności niż wartość domyślna lub jaka jest oferowana w portalu?**

Odp **.: można**skalować w górę modelu w przyrostach 20 współbieżnych żądań. Skontaktuj się z działem [obsługi klienta niestandardowego](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) na potrzeby zapytań o wyższym skalowaniu.

**P: Czy można pobrać model i uruchomić go lokalnie?**

Odp **.: nie**można pobrać i wykonać lokalnie modeli.

**P: Czy moje żądania są ograniczone?**

Odp **.: interfejs**API REST ogranicza liczbę żądań do 25 na 5 sekund. Szczegółowe informacje można znaleźć na stronach na [Zamiana tekstu na mowę](text-to-speech.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów](troubleshooting.md)
- [Informacje o wersji](releasenotes.md)
