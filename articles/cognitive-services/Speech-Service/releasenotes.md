---
title: Informacje o wersji — usługa mowy
titleSuffix: Azure Cognitive Services
description: Uruchomiony dziennik wersji funkcji usługi Speech, ulepszenia, poprawki błędów i znane problemy.
services: cognitive-services
author: oscholz
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 235d8788b47355925d93cb3e3835d32e25c1b51f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168159"
---
# <a name="release-notes"></a>Informacje o wersji

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020 — wydanie w styczniu

**Nowe funkcje**

- Konwersacja z wieloma urządzeniami: łączenie wielu urządzeń z tą samą obsługą mowy lub konwersacją tekstową oraz opcjonalne tłumaczenie komunikatów wysyłanych między nimi. Więcej informacji znajduje się w [tym artykule](multi-device-conversation.md). 
- Dodano obsługę rozpoznawania słów kluczowych dla pakietu Android. AAR i dodano obsługę wersji x86 i x64. 
- Obiektyw-C: `SendMessage` i `SetMessageProperty` metody dodane do obiektu `Connection`. [Tutaj](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)znajdziesz dokumentację.
- Interfejs C++ API usługi TTS obsługuje teraz `std::wstring` jako dane wejściowe tekstu syntezy, eliminując konieczność konwersji wstring na ciąg przed przekazaniem go do zestawu SDK. Szczegóły można znaleźć [tutaj](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [Identyfikator języka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) i [Konfiguracja języka źródłowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) są teraz dostępne.
- JavaScript: dodano funkcję do `Connection` obiektu, aby przekazywać niestandardowe wiadomości z usługi mowy jako `receivedServiceMessage`wywołania zwrotnego.
- JavaScript: Dodano obsługę `FromHost API`, aby ułatwić korzystanie z kontenerów Premium i suwerennych chmur. [Tutaj](speech-container-howto.md)znajdziesz dokumentację.
- JavaScript: Firma Microsoft honoruje teraz `NODE_TLS_REJECT_UNAUTHORIZED` dzięki udziałowi z [orgads](https://github.com/orgads). Szczegóły można znaleźć [tutaj](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).


**Istotne zmiany**

- `OpenSSL` został zaktualizowany do wersji 1.1.1 b i jest statycznie połączony z biblioteką podstawową zestawu Speech SDK dla systemu Linux. Może to spowodować przerwanie w przypadku, gdy skrzynka odbiorcza `OpenSSL` nie została zainstalowana w katalogu `/usr/lib/ssl` w systemie. Aby obejść ten problem, zapoznaj się z [naszą dokumentacją](how-to-configure-openssl-linux.md) w obszarze dokumentacja zestawu Speech SDK.
- Zmieniono typ danych zwrócony dla C# `WordLevelTimingResult.Offset` z `int`, aby `long` zezwolić na dostęp do `WordLevelTimingResults`, gdy dane mowy są dłuższe niż 2 minuty.
- `PushAudioInputStream` i `PullAudioInputStream` teraz wysyłać informacje nagłówka WAV do usługi mowy w oparciu o `AudioStreamFormat`, opcjonalnie określone podczas tworzenia. Klienci muszą teraz korzystać z [obsługiwanego formatu wejścia audio](how-to-use-audio-input-streams.md). Wszystkie inne formaty będą uzyskiwać optymalne wyniki rozpoznawania lub mogą powodować inne problemy. 


**Poprawki błędów**

- Zapoznaj się z aktualizacją `OpenSSL` w obszarze istotne zmiany powyżej. Rozwiązano zarówno sporadyczną awarię, jak i problem z wydajnością (Zablokuj rywalizację pod dużym obciążeniem) w systemach Linux i Java. 
- Java: wprowadzono ulepszenia dotyczące zamykania obiektów w scenariuszach o wysokim współbieżności.
- Restrukturyzacja naszego pakietu NuGet. Usunięto trzy kopie `Microsoft.CognitiveServices.Speech.core.dll` i `Microsoft.CognitiveServices.Speech.extension.kws.dll` w folderach lib, dzięki czemu pakiet NuGet jest mniejszy i szybszy i dodaliśmy do kompilowania aplikacji C++ natywnych.
- Poprawiono przykłady [szybkiego](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)startu. Zostały one zakończone bez wyświetlania wyjątku "nie znaleziono mikrofonu" w systemie Linux, MacOS, Windows.
- Naprawiono awarię zestawu SDK z długią funkcją rozpoznawania mowy na niektórych ścieżkach kodu, takich jak [ten przykład](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Wystąpił błąd wdrażania zestawu SDK w środowisku aplikacji sieci Web platformy Azure w celu rozwiązania [problemu z klientem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Naprawiono błąd usługi TTS przy użyciu tagu `<voice>` lub tagu `<audio>` w celu rozwiązania [tego problemu z klientem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Naprawiono błąd TTS 401, gdy zestaw SDK zostanie odzyskany z wstrzymania.
- JavaScript: Naprawiono cykliczne Importowanie danych audio za pośrednictwem udziału z [euirim](https://github.com/euirim). 
- JavaScript: Dodano obsługę ustawiania właściwości usługi, jak dodano w 1,7.
- JavaScript: Rozwiązano problem polegający na tym, że błąd połączenia może spowodować ciągłe, nieudane próby ponownego nawiązania połączenia z użyciem protokołu WebSocket.


**Przykłady**

- Dodano przykład rozpoznawania słów kluczowych dla systemu Android [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- W [tym miejscu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)dodano przykład TTS dla scenariusza serwera.
- Dodano Przewodniki Szybki Start dotyczące konwersacji dla C# urządzeń C++ i [tutaj](quickstarts/multi-device-conversation.md).


**Inne zmiany**

- Rozmiar zoptymalizowanej biblioteki podstawowej zestawu SDK w systemie Android.
- Zestaw SDK w 1.9.0 i lub nowszym obsługuje zarówno typy `int`, jak i `string` w polu wersja sygnatury głosowej dla konwersacji Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019 — wydanie w listopadzie

**Nowe funkcje**

- Dodano interfejs API `FromHost()`, aby ułatwić korzystanie z kontenerów Premium i suwerennych chmur.
- Dodano automatyczne wykrywanie języka źródła dla funkcji rozpoznawania mowy (w języku C++Java i)
- Dodano obiekt `SourceLanguageConfig` dla funkcji rozpoznawania mowy, używany do określania oczekiwanych języków źródłowych (w C++języku Java i)
- Dodano obsługę `KeywordRecognizer` w systemach Windows (platformy UWP), Android i iOS za pomocą pakietów NuGet i programu Unity
- Dodano interfejs API języka Java konwersacji zdalnych w celu przeprowadzenia transkrypcji konwersacji w partiach asynchronicznych.

**Istotne zmiany**

- Funkcje Transcriber konwersacji przenoszone w obszarze przestrzeni nazw `Microsoft.CognitiveServices.Speech.Transcription`.
- Część metod Transcriber konwersacji jest przenoszona do nowej klasy `Conversation`.
- Porzucone wsparcie dla 32-bitowych (architektury ARMv7 i x86) iOS

**Poprawki błędów**

- Napraw w przypadku awarii, jeśli lokalny `KeywordRecognizer` jest używany bez prawidłowego klucza subskrypcji usługi mowy

**Przykłady**

- Przykład platformy Xamarin dla `KeywordRecognizer`
- Przykład aparatu Unity dla `KeywordRecognizer`
- C++i przykłady dla środowiska Java dla automatycznych wykrywanie języka źródłowych.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019 — wydanie z września

**Nowe funkcje**

- Dodano obsługę wersji beta platformy Xamarin w systemach platforma uniwersalna systemu Windows (platformy UWP), Android i iOS
- Dodano obsługę systemu iOS dla aparatu Unity
- Dodano obsługę danych wejściowych `Compressed` ALaw, MuLaw, FLAC w systemach Android, iOS i Linux
- Dodano `SendMessageAsync` w klasie `Connection` do wysyłania komunikatu do usługi
- Dodano `SetMessageProperty` w klasie `Connection` do ustawiania właściwości komunikatu
- Usługi TTS dodaliśmy powiązania dla Java (JRE i Android), Python, Swift i celu-C
- Obsługa odtwarzania w systemie macOS, iOS i Android została dodana przez funkcję TTS.
- Dodano informacje "granica wyrazu" dla usługi TTS.

**Poprawki błędów**

- Rozwiązano problem z kompilacją IL2CPP w systemie Unity 2019 dla systemu Android
- Rozwiązano problem z nieprawidłowo utworzonymi nagłówkami w danych wejściowych pliku WAV, które są przetwarzane nieprawidłowo
- Rozwiązano problem z identyfikatorami UUID, które nie są unikatowe we właściwościach połączenia
- Rozwiązano kilka ostrzeżeń dotyczących specyfikatorów wartości null w powiązaniach SWIFT (może wymagać wprowadzenia małych kodu)
- Rozwiązano błąd, który spowodował, że połączenia protokołu WebSocket są niebezpiecznie zamknięte w ramach obciążenia sieci
- Rozwiązano problem w systemie Android, który czasami powoduje wystąpienie zduplikowanych identyfikatorów nakładów używanych przez `DialogServiceConnector`
- Ulepszenia stabilności połączeń między interakcjami i raportowaniem niepowodzeń (za pośrednictwem zdarzeń `Canceled`) w przypadku wystąpienia `DialogServiceConnector`
- Funkcja uruchamiania sesji `DialogServiceConnector` będzie teraz prawidłowo podawać zdarzenia, w tym podczas wywoływania `ListenOnceAsync()` podczas aktywnej `StartKeywordRecognitionAsync()`
- Rozwiązywanie awarii skojarzonej z odebranymi działaniami `DialogServiceConnector`

**Przykłady**

- Szybki Start dla platformy Xamarin
- Zaktualizowano przewodnik szybkiego startu z systemem Linux ARM64 Information
- Zaktualizowano przewodnik szybkiego startu aparatu Unity przy użyciu informacji systemu iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019 — wydanie z czerwca

**Przykłady**

- Przykłady szybkiego startu dla zamiany tekstu na mowę w platformy UWP i Unity
- Przykład szybkiego startu dla SWIFT w systemie iOS
- Przykłady aparatu Unity dla rozpoznawanie intencji & mowy i tłumaczenia
- Zaktualizowane przykłady przewodnika Szybki Start dla `DialogServiceConnector`

**Ulepszenia/zmiany**

- Przestrzeń nazw okna dialogowego:
  - Zmieniono nazwę polecenia `SpeechBotConnector` na `DialogServiceConnector`
  - Zmieniono nazwę polecenia `BotConfig` na `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` został ponownie zmapowany do `DialogServiceConfig::FromBotSecret()`
  - Wszystkie istniejące bezpośrednie komputery klienckie mowy są nadal obsługiwane po zmianie nazwy
- Aktualizowanie karty REST usługi TTS do obsługi serwera proxy, trwałego połączenia
- Ulepsz komunikat o błędzie po przekazaniu nieprawidłowego regionu
- SWIFT/cel-C:
  - Udoskonalone raportowanie błędów: metody, które mogą spowodować błąd, są teraz obecne w dwóch wersjach: jedna, która uwidacznia obiekt `NSError` dla obsługi błędów, i jeden, który wywołuje wyjątek. Dawniej są dostępne dla Swift. Ta zmiana wymaga adaptacji do istniejącego kodu SWIFT.
  - Ulepszona obsługa zdarzeń

**Poprawki błędów**

- Poprawka dla usługi TTS: gdzie `SpeakTextAsync` w przyszłości, bez oczekiwania na ukończenie renderowania dźwięku
- Poprawka dotycząca przekazywania ciągów w programie C# w celu zapewnienia obsługi pełnego języka
- Naprawa problemu z aplikacją .NET Core w celu załadowania biblioteki podstawowej z platformą docelową net461 w przykładach
- Naprawa sporadycznych problemów związanych z wdrażaniem bibliotek natywnych w folderze wyjściowym w przykładach
- Naprawa niezawodnego zamykania gniazda internetowego
- Naprawianie możliwej awarii podczas otwierania połączenia w przypadku bardzo dużego obciążenia w systemie Linux
- Naprawianie brakujących metadanych w pakiecie struktury dla macOS
- Rozwiązywanie problemów z `pip install --user` w systemie Windows

## <a name="speech-sdk-151"></a>Zestaw Speech SDK 1.5.1

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

**Poprawki błędów**

- Należy rozwiązać FromSubscription, gdy jest używany z transkrypcją konwersacji.
- Usuń usterkę słowa kluczowego wykrywania trendów dla asystentów głosowych.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019 — może wydać

**Nowe funkcje**

- Wykrywania trendów słowa kluczowego (KWS) jest teraz dostępny dla systemów Windows i Linux. Funkcja KWS może działać z dowolnym typem mikrofonu, oficjalną obsługą KWS, ale jest obecnie ograniczona do tablic mikrofonów znajdujących się na sprzęcie platformy Azure urządzenia Kinect DK lub zestawu Speech Devices SDK.
- Funkcja podpowiedzi frazy jest dostępna za pomocą zestawu SDK. Aby uzyskać więcej informacji, zobacz [tutaj](how-to-phrase-lists.md).
- Funkcja transkrypcji konwersacji jest dostępna za pomocą zestawu SDK. Zobacz [tutaj](conversation-transcription-service.md).
- Dodawanie obsługi asystentów głosowych przy użyciu kanału bezpośredniej linii mowy.

**Przykłady**

- Dodano przykłady dla nowych funkcji lub nowych usług obsługiwanych przez zestaw SDK.

**Ulepszenia/zmiany**

- Dodano różne właściwości aparatu rozpoznawania w celu dostosowania zachowania usługi lub wyników usług (takich jak maskowanie wulgarności i innych).
- Teraz można skonfigurować aparat rozpoznawania przy użyciu standardowych właściwości konfiguracji, nawet jeśli aparat rozpoznawania został utworzony `FromEndpoint`.
- Właściwość "cel-C: `OutputFormat` została dodana do `SPXSpeechConfiguration`.
- Zestaw SDK obsługuje teraz Debian 9 jako dystrybucję systemu Linux.

**Poprawki błędów**

- Rozwiązano problem polegający na tym, że zasób osoby mówiącej był zbyt wczesny w zamian tekstu na mowę.

## <a name="speech-sdk-142"></a>Zestaw mowy SDK 1.4.2

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

## <a name="speech-sdk-141"></a>Zestaw Speech SDK 1.4.1

Jest to wersja tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Zapobiegaj ładowaniu protokołu HTTPS-proxy-Agent przez pakiet internetowy.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019 — wydanie z kwietnia

**Nowe funkcje**

- Zestaw SDK obsługuje teraz usługę zamiany tekstu na mowę jako wersję beta. Jest ona obsługiwana na komputerach z C++ systemami Windows i Linux C#w systemach i. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem zamiany tekstu na mowę](text-to-speech.md#get-started).
- Zestaw SDK obsługuje teraz pliki audio MP3 i Opus/OGG jako pliki wejściowe strumienia. Ta funkcja jest dostępna tylko w systemie Linux C++ z C# systemów i i jest obecnie w wersji beta (więcej informacji znajduje się [tutaj](how-to-use-codec-compressed-audio-input-streams.md)).
- Zestaw Speech SDK dla języka Java, .NET Core C++ i dążyć-C uzyskał pomoc techniczną macOS. Obsługa języka C dla macOS jest obecnie w wersji beta.
- iOS: zestaw Speech SDK dla systemu iOS (cel-C) jest teraz również publikowany jako CocoaPod.
- JavaScript: Obsługa mikrofonu innego niż domyślny jako urządzenia wejściowego.
- JavaScript: Obsługa serwera proxy dla środowiska Node. js.

**Przykłady**

- Przykłady użycia zestawu Speech SDK z C++ i z obiektyw-C w systemie macOS zostały dodane.
- Przykłady pokazujące użycie usługi zamiany tekstu na mowę zostały dodane.

**Ulepszenia/zmiany**

- Python: dodatkowe właściwości wyników rozpoznawania są teraz udostępniane za pośrednictwem właściwości `properties`.
- Aby uzyskać dodatkową pomoc w zakresie programowania i debugowania, możesz przekierować informacje dotyczące rejestrowania i diagnostyki zestawu SDK do pliku dziennika (więcej informacji znajdziesz [tutaj](how-to-use-logging.md)).
- JavaScript: Poprawianie wydajności przetwarzania dźwięku.

**Poprawki błędów**

- Mac/iOS: usterka, która doprowadziła do długiego oczekiwania, gdy nie można nawiązać połączenia z usługą mowy została naprawiona.
- Python: poprawianie obsługi błędów dla argumentów w wywołaniach zwrotnych języka Python.
- JavaScript: Naprawiono nieprawidłowe raportowanie stanu dla mowy zakończono w RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019 – luty Refresh

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

**Poprawka błędu**

- Naprawiono przeciek pamięci podczas korzystania z mikrofonu. Nie ma to żadnego oddziaływania na dane wejściowe lub oparte na strumieniu.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019-luty Release

**Nowe funkcje**

- Zestaw Speech SDK obsługuje wybór mikrofonu wejściowego za pomocą klasy `AudioConfig`. Pozwala to na przesyłanie strumieniowe danych audio do usługi mowy z mikrofonu innego niż domyślny. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [wyboru urządzenia wejściowego audio](how-to-select-audio-input-devices.md). Ta funkcja nie jest jeszcze dostępna w języku JavaScript.
- Zestaw Speech SDK obsługuje teraz środowisko Unity w wersji beta. Prześlij opinię w sekcji problemu w [repozytorium przykładowym usługi GitHub](https://aka.ms/csspeech/samples). Ta wersja obsługuje środowisko Unity w systemie Windows x86 i x64 (aplikacje klasyczne lub platforma uniwersalna systemu Windows) oraz Android (ARM32/64, x86). Więcej informacji można znaleźć w naszym [przewodniku szybki start dla aparatu Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- Plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (wysłane w poprzednich wersjach) nie jest już wymagany. Ta funkcja jest teraz zintegrowana z podstawowym zestawem SDK.

**Przykłady**

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)jest dostępna następująca Nowa zawartość:

- Dodatkowe przykłady dla `AudioConfig.FromMicrophoneInput`.
- Dodatkowe przykłady w języku Python dotyczące rozpoznawania i tłumaczenia zamierzeń.
- Dodatkowe przykłady dotyczące korzystania z obiektu `Connection` w systemie iOS.
- Dodatkowe przykłady języka Java do tłumaczenia z danymi wyjściowymi audio.
- Nowy przykład do korzystania z [interfejsu API REST transkrypcji usługi Batch](batch-transcription.md).

**Ulepszenia/zmiany**

- Python
  - Ulepszono weryfikację parametrów i komunikaty o błędach w `SpeechConfig`.
  - Dodaj obsługę obiektu `Connection`.
  - Obsługa 32-bitowego języka Python (x86) w systemie Windows.
  - Zestaw Speech SDK dla języka Python jest poza wersją beta.
- iOS
  - Zestaw SDK jest teraz oparty na zestawie SDK systemu iOS w wersji 12,1.
  - Zestaw SDK obsługuje teraz system iOS w wersji 9,2 lub nowszej.
  - Popraw dokumentację referencyjną i popraw kilka nazw właściwości.
- JavaScript
  - Dodaj obsługę obiektu `Connection`.
  - Dodaj pliki definicji typów dla powiązanego języka JavaScript
  - Wstępna pomoc techniczna i implementacja dla wskazówek dotyczących fraz.
  - Zwracanie kolekcji właściwości przy użyciu notacji JSON usługi dla rozpoznawania
- Biblioteki DLL systemu Windows zawierają teraz zasób wersji.
- W przypadku tworzenia aparatu rozpoznawania `FromEndpoint` można dodać parametry bezpośrednio do adresu URL punktu końcowego. Przy użyciu `FromEndpoint` nie można skonfigurować aparatu rozpoznawania przy użyciu standardowych właściwości konfiguracji.

**Poprawki błędów**

- Pusta nazwa użytkownika serwera proxy i hasło serwera proxy nie zostały poprawnie obsłużone. W tej wersji, jeśli ustawisz nazwę użytkownika serwera proxy i hasło serwera proxy na pusty ciąg, nie zostaną one przesłane podczas nawiązywania połączenia z serwerem proxy.
- Identyfikator sesji utworzony przez zestaw SDK nie zawsze jest losowo w przypadku niektórych języków&nbsp;/środowiskach. Dodano losową inicjalizację generatora, aby rozwiązać ten problem.
- Poprawa obsługi tokenu autoryzacji. Jeśli chcesz użyć tokenu autoryzacji, określ wartość w `SpeechConfig` i pozostaw klucz subskrypcji pusty. Następnie utwórz aparat rozpoznawania w zwykły sposób.
- W niektórych przypadkach obiekt `Connection` nie został prawidłowo wydano. Ten problem został rozwiązany.
- Przykład JavaScript został rozwiązany do obsługi danych wyjściowych audio na potrzeby syntezy tłumaczenia również w przeglądarce Safari.

## <a name="speech-sdk-121"></a>Zestaw Speech SDK 1.2.1

Jest to wersja tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Wygeneruj strumieniowo koniec strumienia. end, a nie na mowę. end.
- Usuń usterkę w pompie audio, która nie zaplanował następnego wysłania, jeśli bieżące wysyłanie nie powiodło się.
- Naprawianie ciągłego rozpoznawania przy użyciu tokenu uwierzytelniania.
- Poprawka usterki dla różnych aparatów rozpoznawania/punktów końcowych.
- Udoskonalenia dokumentacji.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018 — wydanie z grudnia

**Nowe funkcje**

- Python
  - Wersja beta obsługi języka Python (3,5 i nowszych) jest dostępna w tej wersji. Aby uzyskać więcej informacji, zobacz tutaj] (Szybki Start-python.md).
- JavaScript
  - Zestaw Speech SDK dla języka JavaScript został otwarty jako źródło. Kod źródłowy jest dostępny w serwisie [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Teraz obsługujemy Node. js. więcej informacji można znaleźć [tutaj](quickstart-js-node.md).
  - Ograniczenie długości sesji audio zostało usunięte, ponowne połączenie zostanie wykonane automatycznie pod pokryciem.
- `Connection`, obiekt
  - Z `Recognizer`można uzyskać dostęp do obiektu `Connection`. Ten obiekt umożliwia jawne zainicjowanie połączenia z usługą i subskrypcję w celu nawiązania połączenia i rozłączenia zdarzeń.
    (Ta funkcja nie jest jeszcze dostępna w języku JavaScript i Python).
- Obsługa Ubuntu 18,04.
- Android
  - Włączono obsługę funkcji preguard podczas generowania APK.

**Poprawek**

- Udoskonalenia wewnętrznego użycia wątku, Zmniejszanie liczby wątków, blokad i muteksów.
- Ulepszone raportowanie błędów/informacje. W kilku przypadkach komunikaty o błędach nie zostały jeszcze rozpropagowane.
- Zaktualizowano zależności programistyczne w języku JavaScript w celu korzystania z aktualnych modułów.

**Poprawki błędów**

- Naprawiono przecieki pamięci ze względu na niezgodność typów w `RecognizeAsync`.
- W niektórych przypadkach wyjątki były wyciekami.
- Naprawianie przecieku pamięci w argumentach zdarzeń tłumaczenia.
- Rozwiązano problem z blokowaniem po ponownym nawiązaniu połączenia w długotrwałych sesjach.
- Rozwiązano problem, który może prowadzić do braku końcowego wyniku dla nieudanych tłumaczeń.
- C#: Jeśli operacja `async` nie została oczekiwana w wątku głównym, możliwe, że aparat rozpoznawania mógł zostać usunięty przed ukończeniem zadania asynchronicznego.
- Java: Rozwiązano problem powodujący awarię maszyny wirtualnej Java.
- Cel-C: stałe mapowanie wyliczenia; RecognizedIntent został zwrócony zamiast `RecognizingIntent`.
- JavaScript: Ustaw domyślny format wyjściowy na "Simple" w `SpeechConfig`.
- JavaScript: usuwanie niespójności między właściwościami obiektu konfiguracji w języku JavaScript i innych językach.

**Przykłady**

- Zaktualizowano i Naprawiono kilka próbek (na przykład głosy wyjściowe dla tłumaczenia itp.).
- Dodano przykłady dla środowiska Node. js w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Zestaw Speech SDK 1.1.0

**Nowe funkcje**

- Obsługa systemu Android x86/x64.
- Obsługa serwera proxy: w obiekcie `SpeechConfig` można teraz wywołać funkcję, aby ustawić informacje o serwerze proxy (nazwa hosta, port, nazwa użytkownika i hasło). Ta funkcja nie jest jeszcze dostępne w systemie iOS.
- Kod błędu ulepszone i komunikatów. Jeśli rozpoznawanie zwróciło błąd, to właśnie ustawił `Reason` (w anulowanym zdarzeniu) lub `CancellationDetails` (w wyniku rozpoznawania) do `Error`. Zdarzenie anulowane zawiera teraz dwa dodatkowe elementy członkowskie, `ErrorCode` i `ErrorDetails`. Jeśli serwer zwrócił dodatkowe informacje o błędzie z zgłoszonego błędu, będzie teraz dostępna w nowych elementów członkowskich.

**Poprawek**

- Dodano dodatkowa weryfikacja w konfiguracji rozpoznawania i dodano dodatkowe komunikat.
- Ulepszona obsługa wieloletni wyciszenia w środku plik audio.
- Pakiet NuGet: dla projektów programu .NET Framework zapobiega kompilowanie za pomocą konfiguracji AnyCPU.

**Poprawki błędów**

- Naprawiono kilka wyjątków w aparatów rozpoznawania gestów. Ponadto wyjątki są przechwytywane i konwertowane na zdarzenie `Canceled`.
- Napraw przeciek pamięci w przystawce Zarządzanie właściwości.
- Naprawiono usterkę, w którym plik audio w wejściowych mogło powodować awarię aparat rozpoznawania.
- Usunięto usterkę, w którym zdarzenia można odebrane po wystąpieniu zdarzenia zatrzymanie sesji.
- Rozwiązano niektóre sytuacje wyścigu w wielowątkowości.
- Naprawiono systemu iOS problem ze zgodnością, co może spowodować awarię.
- Ulepszenia obsługi systemu Android mikrofonu.
- Usunięto usterkę, w którym rozpoznawania w języku JavaScript będzie ignorować języka rozpoznawania.
- Naprawiono usterkę uniemożliwiającą Ustawianie `EndpointId` (w niektórych przypadkach) w języku JavaScript.
- Zmieniono kolejność parametrów w parametrze zamierzenia w języku JavaScript i Dodano brakujące `AddIntent` sygnatura języka JavaScript.

**Przykłady**

- Dodano C++ i C# przykłady użycia strumienia ściągania i wypychania w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Zestaw Speech SDK 1.0.1

Ulepszenia niezawodności i poprawki:

- Naprawiono potencjalne błąd krytyczny z powodu sytuacji wyścigu w operacji Dispose rozpoznawania
- Naprawiono potencjalny błąd krytyczny w razie nie ustawiono właściwości.
- Dodano dodatkowe błędów i Sprawdzanie parametrów.
- Cel C: stały możliwy błąd krytyczny spowodowane przez nazwę zastępowanie w NSString.
- Cel C: dostosować widoczność interfejsu API
- JavaScript: Naprawiono dotyczące zdarzenia i ich ładunków.
- Udoskonalenia dokumentacji.

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)dodano nowy przykład dla języka JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Services cognitive mowy wersji zestawu SDK 1.0.0: września 2018 r.

**Nowe funkcje**

- Obsługa języka Objective-C w systemie iOS. Zapoznaj się z naszym [przewodnikiem Szybki Start dla systemu iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Obsługa języka JavaScript w przeglądarce. Zapoznaj się z naszym [przewodnikiem Szybki Start dla języka JavaScript](quickstart-js-browser.md).

**Istotne zmiany**

- W tej wersji wprowadzono kilka istotnych zmian.
  Sprawdź [Tę stronę](https://aka.ms/csspeech/breakingchanges_1_0_0) , aby uzyskać szczegółowe informacje.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Services cognitive mowy wersji zestawu SDK 0.6.0: sierpnia 2018 r.

**Nowe funkcje**

- Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy, teraz można przekazywać Windows App certyfikacji Kit (WACK).
  Zapoznaj się z [przewodnikiem Szybki Start platformy UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Obsługa .NET Standard 2.0 w systemie Linux (Ubuntu 16.04 x 64).
- Eksperymentalne: Obsługuje Java 8 Windows (wersja 64-bitowa) i systemu Linux (Ubuntu 16.04 x 64).
  Zapoznaj się z [Java Runtime Environment przewodnika Szybki Start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Zmiana funkcjonalna**

- Uwidocznić dodatkowe szczegółowe informacje na temat błędów połączenia.

**Istotne zmiany**

- W systemie Java (Android) funkcja `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` nie wymaga już parametru Path. Teraz ścieżki jest wykrywany automatycznie na wszystkich obsługiwanych platformach.
- Metoda Get-accessor właściwości `EndpointUrl` w języku Java i C# została usunięta.

**Poprawki błędów**

- W języku Java wynik rozpoznawania translacji dla audio syntezy zaimplementowano teraz.
- Naprawiono usterkę, która może spowodować, że nieaktywny wątków i zwiększonej liczby gniazd nieużywane i open.
- Rozwiązano problem, gdzie rozpoznawania długotrwałych można zakończyć w trakcie transmisji.
- Stała wyścigu rozpoznawania zamknięcia.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Services cognitive mowy wersji zestawu SDK wersję 0.5.0: lipca 2018 r.

**Nowe funkcje**

- Obsługa platformy systemu Android (interfejsu API 23: system Android Marshmallow 6.0 lub nowszej). Zapoznaj się z [przewodnikiem Szybki Start dla systemu Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Obsługa .NET Standard 2.0 na Windows. Zapoznaj się z [przewodnikiem Szybki Start dla platformy .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Eksperymentalne: Obsługa platformy uniwersalnej systemu Windows na Windows (w wersji 1709 lub nowszej).
  - Zapoznaj się z [przewodnikiem Szybki Start platformy UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Uwaga: Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy nie są jeszcze przekazywane Windows App certyfikacji Kit (WACK).
- Obsługuje rozpoznawanie długotrwałych z automatycznego ponownego połączenia.

**Zmiany funkcjonalne**

- `StartContinuousRecognitionAsync()` obsługuje długotrwałe rozpoznawanie.
- Wynik rozpoznawania zawiera więcej pól. Są one przesunięte od początku i czasu trwania (w taktach) rozpoznanego tekstu oraz wartości dodatkowych reprezentujących stan rozpoznawania, na przykład `InitialSilenceTimeout` i `InitialBabbleTimeout`.
- Obsługa tworzenia wystąpienia fabryki AuthorizationToken.

**Istotne zmiany**

- Zdarzenia rozpoznawania: typ zdarzenia `NoMatch` został scalony w zdarzeniu `Error`.
- Nazwa SpeechOutputFormat C# w programie została zmieniona na `OutputFormat`, aby C++zachować wyrównanie z.
- Typ zwracany niektórych metod interfejsu `AudioInputStream` zmienił się nieco:
  - W języku Java Metoda `read` teraz zwraca `long` zamiast `int`.
  - W C#programie Metoda `Read` teraz zwraca `uint` zamiast `int`.
  - W C++programie metody `Read` i `GetFormat` teraz zwracają `size_t` zamiast `int`.
- C++: Wystąpienia strumieni wejściowych audio mogą teraz być przesyłane tylko jako `shared_ptr`.

**Poprawki błędów**

- Naprawiono nieprawidłowe wartości zwracane w wyniku, gdy `RecognizeAsync()` limit czasu.
- Usunięto zależność od media foundation bibliotek na Windows. Zestaw SDK używa teraz podstawowe interfejsy API z Audio.
- Poprawka do dokumentacji: Dodano stronę [regiony](regions.md) , aby opisać Obsługiwane regiony.

**Znany problem**

- Zestaw SDK rozpoznawania mowy dla systemu Android nie raportują wyniki synteza mowy do tłumaczenia. Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Services cognitive mowy wersji zestawu SDK 0.4.0: czerwca 2018 r.

**Zmiany funkcjonalne**

- AudioInputStream

  Aparat rozpoznawania teraz mogą wykorzystywać strumienia jako źródła audio. Aby uzyskać więcej informacji, zobacz temat dotyczący [przewodnika](how-to-use-audio-input-streams.md).

- Format szczegółowych danych wyjściowych

  Podczas tworzenia `SpeechRecognizer`można zażądać `Detailed` lub `Simple` format wyjściowy. `DetailedSpeechRecognitionResult` zawiera ocenę pewności, rozpoznany tekst, nieprzetworzoną postać leksykalną, znormalizowaną postać i znormalizowaną postać z maską.

**Zmiana podziału**

- Zmieniono do `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` w C#.

**Poprawki błędów**

- Rozwiązano problem z wywołania zwrotnego w warstwie USP podczas zamykania.
- Jeśli aparat rozpoznawania używany plik audio w wejściowych, jego został utrzymuje dojście do pliku dłużej niż jest to konieczne.
- Usunięte kilka zakleszczenia między "pompy komunikatów" i aparat rozpoznawania.
- Wygeneruj `NoMatch` wynik po przekroczeniu limitu czasu odpowiedzi z usługi.
- Biblioteki foundation multimediów na Windows są ładowane z opóźnieniem. Ta biblioteka jest wymagana do mikrofonu tylko dane wejściowe.
- Szybkość przekazywania danych audio jest ograniczone do informacje o dwa razy oryginalnego szybkości audio.
- W Windows zestawy języka C# .NET teraz są silnej nazwy.
- Poprawka do dokumentacji: informacje o `Region` są wymagane do utworzenia aparatu rozpoznawania.

Więcej przykładów zostały dodane i są stale aktualizowane. Aby zapoznać się z najnowszym zestawem przykładów, zobacz [repozytorium usługi Speech SDK przykłady](https://aka.ms/csspeech/samples)w witrynie GitHub.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Services cognitive mowy wersji zestawu SDK 0.2.12733: maj 2018 r.

Ta wersja jest pierwsza wersja publiczna wersja zapoznawcza zestawu SDK mowy usługi Cognitive Services.
