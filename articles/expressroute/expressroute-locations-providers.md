---
title: 'Lokalizacje i dostawcy połączeń: Azure ExpressRoute | Microsoft Docs'
description: Ten artykuł zawiera szczegółowe omówienie lokalizacji, w których są oferowane usługi, i sposobu łączenia z regionami świadczenia usługi Azure. Informacje są posortowane według lokalizacji.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/15/2020
ms.author: cherylmc
ms.openlocfilehash: a46bdb47ee92a422d10aad5b41487530d23d1ec9
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484693"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Lokalizacje według dostawcy](expressroute-locations.md)
> * [Dostawcy według lokalizacji](expressroute-locations-providers.md)


Tabele w tym artykule zawierają informacje o ExpressRoute geograficznym i lokalizacjach, dostawcach połączeń ExpressRoute i integratorach systemu ExpressRoute (SIs).

> [!Note]
> Regiony platformy Azure i lokalizacje ExpressRoute są dwa odrębne i różne koncepcje, co oznacza, że różnica między tymi dwoma ma kluczowe znaczenie dla eksplorowania połączeń sieci hybrydowej platformy Azure. 
>
>

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure
Regiony platformy Azure to Globalne centra danych, w których znajdują się zasoby obliczeniowe, sieci i magazynu platformy Azure. Podczas tworzenia zasobu platformy Azure klient musi wybrać lokalizację zasobu. Lokalizacja zasobu określa, w którym centrum danych Azure (lub strefa dostępności) jest tworzony zasób.

## <a name="expressroute-locations"></a>Lokalizacje usługi ExpressRoute
Lokalizacje ExpressRoute (czasami określane jako lokalizacje komunikacji równorzędnej lub lokalizacje dopełnienia) to miejsca, w których znajdują się urządzenia Microsoft Enterprise Edge (MSEE). Lokalizacje ExpressRoute są punktami wejścia do sieci firmy Microsoft — i są dystrybuowane globalnie, dzięki czemu klienci mogą łączyć się z siecią firmy Microsoft na całym świecie. Te lokalizacje to miejsce, w którym partnerzy ExpressRoute i klienci z bezpośrednią ExpressRoute mogą emitować połączenia krzyżowe do sieci firmy Microsoft. Ogólnie rzecz biorąc, lokalizacja ExpressRoute nie musi być zgodna z regionem świadczenia usługi Azure. Klient może na przykład utworzyć obwód usługi ExpressRoute z lokalizacją zasobu *Wschodnie stany USA*w lokalizacji komunikacji równorzędnej w *Seattle* .

Będziesz mieć dostęp do usług Azure we wszystkich regionach regionu geopolitycznego, jeśli połączysz się przynajmniej z jedną lokalizacją usługi ExpressRoute w tym regionie. 

## <a name="locations"></a>Regiony platformy Azure do ExpressRoute lokalizacji w regionie geopolitycznym
Poniższa tabela zawiera mapę regionów świadczenia usługi Azure dla lokalizacji usługi ExpressRoute w regionie geopolitycznym.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Australia — instytucje rządowe** | Australia Środkowa, Australia Środkowa 2 |Canberra, Canberra2 |
| **Europa** | Francja środkowa, Francja Południowa, Niemcy Północne, Niemcy Środkowo-Zachodnie, Europa Północna, Norwegia Wschodnia, Norwegia Zachodnia, Szwajcaria Północna, Szwajcaria Zachodnia, Zachodnie Zjednoczone Królestwo, Południowe Zjednoczone Królestwo, Europa Zachodnia |Amsterdam, Amsterdam2, Kopenhaga, Dublin, Frankfurt, Genewa, Londyn, London2, Marsylii, Mediolan, Monachium, Newport (Walia), Oslo, Paryż, Stavanger, Sztokholm, Zurych, Monachium |
| **Ameryka Północna** | Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Zachodnio-środkowe stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nowy Jork, San Antonio, Seattle, Dolina Krzemowa, krzem Valley2, Waszyngton DC, Waszyngton DC2, Montrealu, Quebec City, Toronto |
| **Azja** | Azja Wschodnia, Azja Południowo-Wschodnia | Bangkok, Hongkong SAR, Hongkong Kong2, Dżakarta, Kuala Lumpur, Singapur, Singapur2, Tajpej |
| **Indie** | Indie Zachodnie, Indie Środkowe, Indie Południowe |Chennai, Chennai2, Mumbaj, Mumbaj2 |
| **Japonia** | Japońska Zachodnia, Japonia Wschodnia |Osaka, Tokio, Tokyo2 |
| **Oceanii** | Australia Południowo-Wschodnia, Australia Wschodnia |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Korea Południowa** | Korea Środkowa, Korea Południowa |Busan, Seul|
| **Emiraty** | Europa Północna, Zjednoczone Emiraty Arabskie | Dubaj, Dubai2 |
| **Republika Południowej Afryki** | Zachodnia Republika Południowej Afryki, Północna Republika Południowej Afryki |Kapsztad, Johannesburg |
| **Ameryka Południowa** | Brazylia Południowa |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiony platformy Azure i granice geopolityczne dla chmur krajowych
W poniższej tabeli zamieszczono informacje o regionach i granicach geopolitycznych chmur krajowych.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Chmura administracji USA** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia, US DoD (region środkowy), US DoD (region wschodni)  |Chicago, Dallas, Nowy Jork, Phoenix, San Antonio, Seattle, Dolina Krzemowa, Waszyngton |
| **Chiny Wschodnie** |Chiny Wschodnie, Chiny Wschodnie 2 |Szanghaj, Szanghaj 2 |
| **Chiny Północne** |Chiny Północne, Chiny Północne 2 |Pekin, Pekin 2 |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="partners"></a>Dostawcy połączenia usługi ExpressRoute

W poniższej tabeli przedstawiono lokalizacje połączeń i dostawców usług dla każdej lokalizacji. Jeśli chcesz wyświetlić dostawców usług i lokalizacje, w których świadczą usługi, zobacz [Lokalizacje według dostawcy usług](expressroute-locations.md).

* **Lokalne regiony platformy Azure** to te, które [ExpressRoute lokalnie](expressroute-faqs.md) w każdej lokalizacji komunikacji równorzędnej mogą uzyskać dostęp. **n/a** wskazuje, że ExpressRoute Local nie jest dostępny w tej lokalizacji komunikacji równorzędnej.

* **Strefa** dotyczy [cen](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Globalny komercyjny platformę Azure
| **Lokalizacja** | **Ulica** | **Strefa** | **Lokalne regiony platformy Azure** | **ER Direct** | **Dostawcy usług** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa Zachodnia | 10G, 100G | Sieci aryaka Networks, w & T teleobligacje, Brytyjskie telekomunikacyjne, Colt, Equinix, euNetworks, GÉANT, międzychmurowe, Interxion, KPN, IX zasięg, 3 komunikacja, Megaport, NTT Communications, pomarańczowy, Tata Communications, Telefonica, firma Telenor, Telia, Verizon Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa Zachodnia | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, pomarańczowy, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | Nie dotyczy | Nie dotyczy | Equinix, Megaport |
| **Auckland** | [Grupa Vocus NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | Nie dotyczy | ° | Devoli, kordia, Megaport, Spark NZ, Vocus Grupa NZ |
| **Bangkok** | [AIS](http://business.ais.co.th/solution/microsoft-azure.html?category=cloud) | 2 | Nie dotyczy | ° | AIS |
| **Pusan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Korea Południowa | Nie dotyczy | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia Środkowa | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia Środkowa 2| 10G, 100G | CDC |
| **Kapsztad** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Zachodnia Republika Południowej Afryki | ° | BCX, Internet Solutions — Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Indie Południowe | ° | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Indie Południowe | Nie dotyczy | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Środkowo-północne stany USA | 10G, 100G | Sieci aryaka Networks, w & T teleobligacji, CenturyLink Cloud Connect, Cologix, Colt, Comcast, CoreSite, Equinix, międzychmurowe, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, przebieg, Telia, Verizon, Zayo |
| **Kopenhaga** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | Nie dotyczy | ° | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | Nie dotyczy | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Środkowo-zachodnie stany USA | Nie dotyczy | CoreSite, Megaport, Zayo |
| **Dubaj** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Północne Zjednoczone Emiraty Arabskie | Nie dotyczy | Etisalat Zjednoczone Emiraty Arabskie |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Północne Zjednoczone Emiraty Arabskie | Nie dotyczy | du datamena, Megaport, pomarańczowy, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa Północna | 10G, 100G | Colt, EIR, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Niemcy Środkowo-Zachodnie | 10G, 100G | Colt, DE-CIX, GEANT, Interxion, Megaport, pomarańczowy, Telia |
| **Genewie** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Szwajcaria Zachodnia | 10G, 100G | Equinix, Megaport |
| **SRA Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Azja Wschodnia | Nie dotyczy | Sieci aryaka networkse, telekomunikacyjne brytyjskie, CenturyLink Cloud Connect, dyrektor Telecom, Chiny Telecom, Equinix, międzychmurowe, Megaport, NTT Communications, pomarańcze, PCCW Global Limited, Tata Communications, Telia, Verizon |
| **Kong2 SRA Hongkong** | [I](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Nie dotyczy | ° | |
| **Dżakarta** | Telkom Indonezja | 4 | Nie dotyczy | ° | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Północna Republika Południowej Afryki | ° | Kolumbia Telecom, Internet Solutions — Cloud Connect, ciecz Telecom, pomarańczowy, Teraco |
| **Kuala Lumpur** | [CZAS dotCom Menara cele](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | Nie dotyczy | Nie dotyczy | TIME dotCom |
| **Las Vegas** | [Przełącz LV](https://www.switch.com/las-vegas) | 1 | Nie dotyczy | Nie dotyczy | CenturyLink Cloud Connect, Megaport |
| **Londyn** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Południowe Zjednoczone Królestwo | 10G, 100G | W & T teleobligacje, Brytyjskie Telecom, Colt, Equinix, euNetworks, międzychmurowe, rozwiązania internetowe — Cloud Connect, Interxion, JISC, Level 3 Communications, Megaport, MTN, NTT Communications, pomarańczowy, PCCW Global Limited, Tata Communications, teledomu-KDDI, Firma Telenor, Telia, Verizon, Vodafone, Zayo |
| **Londyn2** | [Dwa domowe północ](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Południowe Zjednoczone Królestwo | 10G, 100G | CenturyLink Cloud Connect, Colt, IX zasięg, Equinix, Megaport, teledomu — KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | Nie dotyczy | Nie dotyczy | CoreSite, Equinix, Megaport, neutrony, NTT, Transtelco, Zayo |
| **Marsylia** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Francja Południowa | Nie dotyczy | DE-CIX, GEANT, Interxion, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australia Południowo-Wschodnia | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | Nie dotyczy | ° | C3ntro, Equinix, Megaport, neutrony |
| **Mediolan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | Nie dotyczy | ° | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | Nie dotyczy | 10G, 100G | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbaj** | Tata Communications | 2 | Indie Zachodnie | Nie dotyczy | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbaj2** | Airtel | 2 | Indie Zachodnie | Nie dotyczy | Airtel, Sify, Vodafone Idea |
| **Monachium** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | Nie dotyczy | 10G, 100G | |
| **Nowy Jork** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | Nie dotyczy | Nie dotyczy | CenturyLink Cloud Connect, Colt, CoreSite, Equinix, incloud, Megaport, Packet, Zayo |
| **Newport (Walia)** | [Dane nowej generacji](https://www.nextgenerationdata.co.uk) | 1 | Zachodnie Zjednoczone Królestwo | Nie dotyczy | Brytyjskie Telecom telekomunikacyjne, Colt, Level 3, dane nowej generacji |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japonia Zachodnia | 10G, 100G | Colt, Equinix, Internet Initiative Japonia Inc.-IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norwegia Wschodnia | 10G, 100G | Megaport, firma Telenor, Telia |
| **Paryż** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Francja Środkowa | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | Nie dotyczy | ° | Megaport, NextDC |
| **Miasto Quebec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kanada Wschodnia | Nie dotyczy | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Środkowo-południowe stany USA | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix z dodatkiem SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazylia Południowa | Nie dotyczy | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Zachodnie stany USA 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Korea Środkowa | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Dolina Krzemowa** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Zachodnie stany USA | 10G, 100G | Aryaka Networks Networks, w & T teleobligacje, Kolumbia Telecom, CenturyLink Cloud Connect, Colt, Comcast, CoreSite, Equinix, międzychmurowe, Internet2, IX zasięg, pakiet, PacketFabric, Level 3 Communications, Megaport, pomarańczowy, przebieg, Tata Communications, Telia Verizon, Zayo |
| **Valley2 krzemu** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Zachodnie stany USA | 10G, 100G | Colt, CoreSite | 
| **Singapur** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Azja Południowo-Wschodnia | 10G, 100G | Sieci aryaka Networks, w & T teleobligacje, Telecom, brytyjskie, międzynarodowe, International Communications, Equinix, międzychmurowe, na poziomie 3 komunikacja, Megaport, NTT Communications, pomarańczowy, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | [Przełącznik globalny Tai/Inseng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Azja Południowo-Wschodnia | 10G, 100G | Chiny Unicom Global, Colt, Epsilon (Global Communications), Megaport, SingTel |
| **Stavanger** | [Zielony górski kontroler DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norwegia Zachodnia | 10G, 100G | |
| **Sztokholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | Nie dotyczy | ° | Equinix, Telia |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australia Wschodnia | 10G, 100G | AARNet, w & T teleobligacje, Brytyjskie Telecom, Devoli, Equinix, kordia, Megaport, NEXTDC, NTT Communications, Optus, pomarańczowy, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australia Wschodnia | 10G, 100G | Megaport, NextDC |
| **Tajpej** | Chief Telecom | 2 | Nie dotyczy | ° | Dyrektor telekomunikacyjny, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japonia Wschodnia | 10G, 100G | Sieci aryaka Networks, w & T teleobligacje, BBIX, Brytyjskie Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japonia Inc.-IIJ, Megaport, NTT Communications, NTT wschód, pomarańczowy, Softbank, Verizon |
| **Tokyo2** | [Na Tokio](https://www.attokyo.com/) | 2 | Japonia Wschodnia | 10G, 100G | |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Kanada Środkowa | 10G, 100G | W & T teleobligacji, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX zasięg Megaport, Telus, Verizon, Zayo |
| **Waszyngton** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Wschodnie stany USA, Wschodnie stany USA 2 | 10G, 100G | Aryaka Networks Networks, w & T teleobligacje, Kolumbia Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, CoreSite, Equinix, Internet2, międzychmurowe, IX zasięg, 3 komunikacja, Megaport, neutrony, sieci, NTT Communications, pomarańczowy, PacketFabric, SES , Przebieg, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Waszyngton 2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Wschodnie stany USA, Wschodnie stany USA 2 | 10G, 100G | CenturyLink Cloud Connect, CoreSite, Intelsat, Viasat, Zayo | 
| **Zurych** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Szwajcaria Północna | 10G, 100G | Międzychmurowe, Interxion, Megaport, Swisscom |

 **+** oznacza wkrótce

### <a name="national-cloud-environments"></a>Krajowe środowiska chmury

Chmury krajowe platformy Azure są odizolowane od siebie i z globalnej komercyjnej platformy Azure. ExpressRoute dla jednej chmury platformy Azure nie może połączyć się z regionami platformy Azure w innych.

### <a name="us-government-cloud"></a>Chmura administracji USA
| **Lokalizacja** | **Ulica** | **Lokalne regiony platformy Azure**| **ER Direct** | **Dostawcy usług** |
| --- | --- | --- | --- | --- |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Nie dotyczy | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | Nie dotyczy | 10G, 100G | Equinix, Megaport, Verizon |
| **Nowy Jork** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | Nie dotyczy | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | Administracja USA — Arizona | Nie dotyczy | W & T teleobligacji, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | Administracja USA — Teksas | Nie dotyczy | CenturyLink Cloud Connect, Megaport |
| **Dolina Krzemowa** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | Nie dotyczy | 10G, 100G | W & T, Equinix, poziom 3 komunikacja, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Nie dotyczy | Nie dotyczy | Equinix, Megaport |
| **Waszyngton** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD (region wschodni), US Gov Wirginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Chiny
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Pekin** |China Telecom |
| **Pekin 2** | Chiny Telecom, Chiny Unicom, GDS |
| **Szanghaj** |China Telecom |
| **Szanghaj 2** | Chiny Telecom, GDS |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach)

### <a name="germany"></a>Niemcy
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Łączność za pośrednictwem dostawców programu Exchange
Jeśli dostawca połączenia nie został wymieniony w poprzednich sekcjach, możesz i tak utworzyć połączenie.

* Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy jest on połączony z dowolną wymianą z tabeli powyżej. Użyj poniższych linków, aby zebrać więcej informacji o usługach oferowanych przez dostawców wymiany. Kilku dostawców połączenia jest już połączonych z wymianami sieci Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Poproś dostawcę połączenia o rozszerzenie sieci o wybraną lokalizację komunikacji równorzędnej.
  * Dopilnuj, by dostawca połączenia rozszerzył łączność w sposób wysoko dostępny, aby nie wystąpiły żadne punkty awarii.
* Zamów obwód usługi ExpressRoute z wymianą jako dostawcą połączenia, aby połączyć się z firmą Microsoft.
  * Wykonaj kroki opisane w artykule [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Tworzenie obwodu usługi ExpressRoute), aby skonfigurować łączność.

## <a name="connectivity-through-satellite-operators"></a>Łączność za pośrednictwem operatorów satelitarnych
Jeśli jesteś zdalny i nie masz łączności z włóknami lub chcesz poznać inne opcje łączności, możesz sprawdzić następujące operatory satelity. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Łączność za pośrednictwem dodatkowych dostawców usług
| **Lokalizacja** | **Exchange** | **Dostawcy połączeń** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, poziom 3 komunikacja | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, perski Bridge International, Kalaam Telecom Bahrajn B. S. C, MainOne, Nianet, POST Telecom Luksemburg, Proximus, TDC Erhverv, Telecom Italia musujący, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle korony
| **Kapsztad** | Teraco | MTN |
| **Chicago** | Equinix| Castle korony, spektrum korporacyjne, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Korona Castle, Odlewia danych, całe przedsiębiorstwa, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **>SRA Hongkong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londyn** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Castle korony, spektrum korporacyjne, Transtelco |
| **Madryt** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Technologie bramy, Inc. Aptum Technologies, Rogers, zirro |
| **Nowy Jork** |Equinix, Megaport | Altice Business, koroner Castle, spektrum korporacyjne, Webair |
| **Paryż** | Equinix | Proximus |
| **Miasto Quebec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Dolina Krzemowa** |Coresite, Equinix | COX Business, spektrum korporacyjne, windstream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC telekomunikacyjne, Epsilon Telecom Limited, LGA Telecom, droga z informacjami Zjednoczonymi (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | -Gate Technologies Inc., Beanfield Metroconnect, technologie Aptum, IVedha Inc, Rogers, Thinktel, zirro|
| **Waszyngton** |Equinix | Altice Business, BICS, Cox Business, Korona Castle, GTT Communications Inc, Epsilon telekomunikacyjne Limited, Masergy, windstream |

## <a name="expressroute-system-integrators"></a>Integratorzy systemu ExpressRoute
Włączanie prywatnej łączności do własnych potrzeb może być wyzwaniem w zależności od skali sieci. Możesz pracować z dowolnymi integratorami systemu wymienionymi w poniższej tabeli, którzy ułatwiają dołączanie do usługi ExpressRoute.

| **Kontynent** | **Integratorzy systemów** |
| --- | --- |
| **Azja** |Avanade Inc., OneAs1a |
| **Australia** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Ameryka Północna** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Ameryka Południowa** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
