---
title: Offline FairPlay Streaming for iOS with Azure Media Services v3
description: This topic gives an overview and shows how to use Azure Media Services to dynamically encrypt your HTTP Live Streaming (HLS) content with Apple FairPlay in offline mode.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 70256046089a59df1de79b78124c5d60fde77080
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705942"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay Streaming for iOS with Media Services v3

 Azure Media Services provides a set of well-designed [content protection services](https://azure.microsoft.com/services/media-services/content-protection/) that cover:

- PlayReady firmy Microsoft
- Google Widevine
    
    Widevine is a service provided by Google Inc. and subject to the terms of service and Privacy Policy of Google, Inc.
- Technologia FairPlay firmy Apple
- Szyfrowanie AES-128

Digital rights management (DRM)/Advanced Encryption Standard (AES) encryption of content is performed dynamically upon request for various streaming protocols. DRM license/AES decryption key delivery services also are provided by Media Services.

Besides protecting content for online streaming over various streaming protocols, offline mode for protected content is also an often-requested feature. Offline-mode support is needed for the following scenarios:

* Playback when internet connection isn't available, such as during travel.
* Some content providers might disallow DRM license delivery beyond a country/region's border. If users want to watch content while traveling outside of the country/region, offline download is needed.
* In some countries/regions, internet availability and/or bandwidth is still limited. Users might choose to download first to be able to watch content in a resolution that is high enough for a satisfactory viewing experience. In this case, the issue typically isn't network availability but limited network bandwidth. Over-the-top (OTT)/online video platform (OVP) providers request offline-mode support.

This article covers FairPlay Streaming (FPS) offline-mode support that targets devices running iOS 10 or later. This feature isn't supported for other Apple platforms, such as watchOS, tvOS, or Safari on macOS.

> [!NOTE]
> Offline DRM is only billed for making a single request for a license when you download the content. Any errors are not billed.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zaimplementowaniem funkcji DRM w trybie offline dla programu FairPlay na urządzeniu z systemem iOS 10 +:

* Przejrzyj ochronę zawartości online dla FairPlay: 

    - [Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple](fairplay-license-overview.md)
    - [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
    - Przykład platformy .NET obejmujący konfigurację strumienia FPS w trybie online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Uzyskaj zestaw SDK FPS z sieci Apple Developer Network. Zestaw SDK FPS zawiera dwa składniki:

    - Zestaw SDK serwera FPS, który zawiera moduł zabezpieczeń (KSM), przykłady klienta, specyfikację i zestaw wektorów testów.
    - Pakiet wdrożeniowy FPS, który zawiera specyfikację D, wraz z instrukcjami dotyczącymi sposobu generowania certyfikatu FPS, klucza prywatnego określonego dla klienta i klucza tajnego aplikacji. Firma Apple emituje pakiet wdrożeniowy FPS tylko do licencjonowanych dostawców zawartości.
* Klonowanie https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Aby dodać konfiguracje FairPlay, należy zmodyfikować kod [zaszyfrowany za pomocą technologii DRM przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w Azure Media Services

W metodzie [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) wykonaj następujące czynności:

Usuń komentarz z kodu, który konfiguruje opcję zasad FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Usuń także komentarz do kodu, który dodaje CBCS ContentKeyPolicyOption do listy ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Włącz tryb offline

Aby włączyć tryb offline, Utwórz niestandardowe StreamingPolicy i użyj jego nazwy podczas tworzenia StreamingLocator w [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Teraz Twoje konto Media Services jest skonfigurowane do dostarczania licencji FairPlay w trybie offline.

## <a name="sample-ios-player"></a>Przykładowy odtwarzacz iOS

Obsługa trybu offline FPS jest dostępna tylko w systemie iOS 10 i nowszych. Zestaw SDK serwera FPS (wersja 3,0 lub nowsza) zawiera dokument i przykład dla trybu offline w trybie online. Zestaw SDK serwera (w wersji 3,0 lub nowszej) zawiera następujące dwa elementy związane z trybem offline:

* Dokument: "odtwarzanie w trybie offline za pomocą FairPlay streaming i HTTP Live Streaming". Apple, 14 września 2016. W zestawie SDK serwera FPS w wersji 4,0 ten dokument jest scalany z głównym dokumentem FPS.
* Przykładowy kod: HLSCatalog Sample (część zestawu SDK serwera firmy Apple dla systemu) dla trybu offline systemu \FairPlay w wersji 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. W aplikacji przykładowej HLSCatalog następujące pliki kodu są używane do implementowania funkcji trybu offline:

    - Plik kodu AssetPersistenceManager. SWIFT: AssetPersistenceManager jest główną klasą w tym przykładzie, który ilustruje sposób:

        - Zarządzaj pobieranymi strumieniami HLS, takimi jak interfejsy API używane do uruchamiania i anulowania pobierania oraz usuwania istniejących zasobów poza urządzeniami.
        - Monitoruj postęp pobierania.
    - Pliki kodu AssetListTableViewController. Swift i AssetListTableViewCell. SWIFT: AssetListTableViewController jest głównym interfejsem tego przykładu. Zawiera listę zasobów, których można użyć do odtwarzania, pobrania, usunięcia lub anulowania pobierania. 

W tych krokach pokazano, jak skonfigurować uruchomiony odtwarzacz systemu iOS. Przy założeniu, że zaczynasz od przykładu HLSCatalog w zestawie SDK serwera FPS w wersji 4.0.1, wprowadź następujące zmiany kodu:

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift Zaimplementuj metodę `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` przy użyciu następującego kodu. Niech "drmUr" będzie zmienną przypisaną do adresu URL HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift Zaimplementuj metodę `requestApplicationCertificate()`. Ta implementacja zależy od tego, czy osadzasz certyfikat (tylko klucz publiczny) z urządzeniem lub hostuje certyfikat w sieci Web. W poniższej implementacji użyto certyfikatu aplikacji hostowanej użytego w przykładach testu. Let "certUrl" to zmienna, która zawiera adres URL certyfikatu aplikacji.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

W przypadku końcowej zintegrowanego testu zarówno adres URL wideo, jak i adres URL certyfikatu aplikacji są podane w sekcji "test zintegrowany".

W HLSCatalog\Shared\Resources\Streams.plist Dodaj adres URL testu wideo. W polu Identyfikator klucza zawartości Użyj adresu URL pozyskiwania licencji FairPlay z protokołem SKD jako wartość unikatową.

![Strumienie aplikacji FairPlay dla systemu iOS w trybie offline](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Jeśli zostały skonfigurowane, użyj własnego adresu URL wideo, adresu URL pozyskiwania licencji FairPlay oraz adresu URL certyfikatu aplikacji. Możesz też przejść do następnej sekcji zawierającej przykłady testowe.

## <a name="integrated-test"></a>Test zintegrowany

Trzy próbki testowe w Media Services obejmują następujące trzy scenariusze:

* Chronione za pomocą FPS z użyciem wideo, audio i alternatywnej ścieżki audio
* Chronione za pomocą FPS z użyciem wideo i audio, ale bez alternatywnej ścieżki audio
* Chroniona FPS, z tylko wideo i bez dźwięku

Te przykłady można znaleźć w [tej witrynie demonstracyjnej](https://aka.ms/poc#22)przy użyciu odpowiedniego certyfikatu aplikacji hostowanego w aplikacji sieci Web platformy Azure.
W przypadku wersji 3 lub 4 zestawu SDK serwera FPS, jeśli główna lista odtwarzania zawiera alternatywny dźwięk, w trybie offline jest odtwarzany tylko dźwięk. W związku z tym należy rozdzielić alternatywny dźwięk. Innymi słowy, drugi i trzeci przykłady wymienione wcześniej działają w trybie online i offline. Przykładowa podano w pierwszej kolejności dźwięk tylko w trybie offline, podczas gdy Transmisja strumieniowa w trybie online działa prawidłowo.

## <a name="faq"></a>Często zadawane pytania

Poniższe często zadawane pytania zapewniają pomoc w rozwiązywaniu problemów:

- **Dlaczego dźwięk jest odtwarzany tylko w trybie offline, ale nie wideo?** Takie zachowanie wydaje się być projektem przykładowej aplikacji. Jeśli istnieje alternatywna ścieżka audio (w przypadku programu HLS) w trybie offline, zarówno system iOS 10, jak i iOS 11 domyślnie są alternatywną ścieżką audio. Aby zrekompensować to zachowanie w trybie offline w trybie online, Usuń alternatywną ścieżkę audio ze strumienia. Aby to zrobić na Media Services, Dodaj filtr manifestu dynamicznego "audio-Only = false". Innymi słowy, adres URL HLS ma koniec. ISM/manifest (format = M3U8-AAPL, audio-Only = false). 
- **Dlaczego nadal Odtwarzaj tylko dźwięk bez wideo w trybie offline po dodaniu tylko audio = FAŁSZ?** W zależności od projektu klucza pamięci podręcznej usługi Content Delivery Network (CDN) zawartość może być buforowana. Przeczyść pamięć podręczną.
- **Czy tryb offline jest również obsługiwany w systemie iOS 11 oprócz systemu iOS 10?** Tak. Tryb offline FPS jest obsługiwany w systemach iOS 10 i iOS 11.
- **Dlaczego nie mogę znaleźć dokumentu "odtwarzanie w trybie offline za pomocą FairPlay streaming i HTTP Live Streaming" w zestawie SDK serwera FPS?** Ponieważ zestaw FPS SDK serwera w wersji 4, ten dokument został scalony w przewodniku programowania strumieniowego FairPlay.
- **Co to jest struktura plików pobierana/w trybie offline na urządzeniach z systemem iOS?** Pobrana struktura plików na urządzeniu z systemem iOS wygląda podobnie do poniższego zrzutu ekranu. W folderze `_keys` są przechowywane pobrane licencje FPS z jednym plikiem magazynu dla każdego hosta usługi licencjonowania. W folderze `.movpkg` są przechowywane zawartość audio i wideo. Pierwszy folder o nazwie kończącej się znakiem łącznika, po którym następuje wartość liczbowa, zawiera zawartość wideo. Wartość liczbowa jest PeakBandwidthą dla odwzorowań wideo. Drugi folder o nazwie kończącej się znakiem łącznika, po którym następuje 0, zawiera zawartość audio. Trzeci folder o nazwie "Data" zawiera główną listę odtwarzania zawartości FPS. Na koniec plik Boot. xml zawiera pełny opis zawartości folderu `.movpkg`. 

![Struktura pliku przykładowej aplikacji w trybie offline FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Przykładowy plik Boot. XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [stosować ochronę przy użyciu algorytmu AES-128](protect-with-aes128.md)
