---
title: Moduły Azure IoT Edge
description: Oferta modułu IoT Edge w portalu Azure Marketplace dla wydawców aplikacji i usług.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/22/2018
ms.author: pabutler
ms.openlocfilehash: db501c34a4418bc7b08b19105a9b28585288f877
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823064"
---
# <a name="iot-edge-modules"></a>Moduły usługi IoT Edge

Platforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) jest obsługiwana przez chmurę platformy Azure.  Ta platforma umożliwia użytkownikom wdrażanie obciążeń w chmurze do uruchomienia bezpośrednio na urządzeniach IoT.  Moduł IoT Edge może uruchamiać obciążenia w trybie offline i analizować dane lokalnie. Ten typ oferty pomaga zaoszczędzić przepustowość, chronić lokalne i poufne dane oraz zapewnia czas odpowiedzi o małym opóźnieniu.  Masz teraz możliwość skorzystania z tych wstępnie skompilowanych obciążeń. Do tej pory dostępne są tylko kilku rozwiązań pierwszej firmy od firmy Microsoft.  Musisz zainwestować czas i zasoby na tworzenie własnych niestandardowych rozwiązań IoT.

Wprowadzając [moduły IoT Edge w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), mamy teraz jedną lokalizację docelową umożliwiającą wydawcom udostępnianie i sprzedawanie rozwiązań dla odbiorców IoT. Deweloperzy IoT mogą ostatecznie znajdować i kupować funkcje przyspieszające tworzenie rozwiązań.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Najważniejsze zalety modułów IoT Edge w portalu Azure Marketplace:

| **Dla wydawców**    | **Dla klientów (deweloperzy IoT)**  |
| :------------------- | :-------------------|
| Dotrzej do milionów deweloperów, którzy chcą kompilować i wdrażać rozwiązania IoT Edge.  | Twórz IoT Edge rozwiązanie z zachowaniem bezpiecznych i przetestowanych składników. |
| Publikuj raz i uruchamiaj na dowolnym IoT Edge sprzęt obsługujący kontenery. | Skracaj czas wprowadzenia na rynek, wyszukując i wdrażając moduły IoT Edge 1 i innych firm dla konkretnych potrzeb. |
| Zarabiaj z elastycznymi opcjami rozliczania <ul> <li> Bezpłatna i korzystaj z własnej licencji (BYOL). </li> </ul> | Twórz zakupy przy użyciu wybranych modeli rozliczeń. <ul> <li> Bezpłatna i korzystaj z własnej licencji (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co to jest moduł IoT Edge?

Azure IoT Edge pozwala wdrożyć logikę biznesową i zarządzać nią na krawędzi w formie modułów. Moduły Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. Aby dowiedzieć się więcej na temat modułów IoT Edge, zobacz [Opis modułów Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Jaka jest różnica między typem oferty kontenera a typem oferty modułu IoT Edge module?**

Typ oferty modułu IoT Edge jest określonym typem kontenera uruchomionym na urządzeniu IoT Edge. Dostępne są domyślne ustawienia konfiguracji, które mogą być uruchamiane w kontekście IoT Edge i IoT Edge opcjonalnie są zintegrowane z IoT Edge środowiska uruchomieniowego.

## <a name="publishing-your-iot-edge-module"></a>Publikowanie modułu IoT Edge

**Wybieranie odpowiedniego sklepu**

Moduły IoT Edge są publikowane tylko w portalu Azure Marketplace, AppSource nie ma zastosowania.  Aby uzyskać więcej informacji na temat różnic i docelowych odbiorców w sklepie, zobacz [Określanie opcji publikowania dla rozwiązania](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opcje rozliczeń**

Portal Marketplace obsługuje obecnie **bezpłatne** opcje rozliczania **licencji (BYOL)** dla modułów IoT Edge.
 
**Opcje publikowania**

We wszystkich przypadkach moduł IoT Edge powinien wybrać opcję publikowania **Transact** .  Aby uzyskać więcej informacji na temat opcji publikowania, zobacz [Wybieranie opcji publikowania](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) .  

## <a name="eligibility-criteria"></a>Kryteria kwalifikacji

Wszystkie postanowienia Microsoft Azure Marketplace umów i zasad mają zastosowanie do ofert IoT Edge module.  Ponadto istnieją wymagania wstępne i techniczne dla modułów IoT Edge.  

**Wymagania wstępne**

Aby opublikować moduł IoT Edge w portalu Azure Marketplace, należy spełnić następujące wymagania wstępne:

- Dostęp do portal Cloud Partner (CPP). Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace i Podręcznik publikowania AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hostowanie modułu IoT Edge w Azure Container Registry. 
- Zastąp metadane modułu IoT Edge, takie jak (lista niepełna): 
    - Tytuł
    - Opis (w formacie HTML)
    - Obraz logo (format PNG i stałe rozmiary obrazu, w tym 40x40px, 90x90px, 115x115px, 255x115px)
    - Warunki użytkowania i zasady zachowania poufności informacji
    - Domyślna konfiguracja modułu (trasa, wymagane właściwości przędzy, opcje i zmienne środowiskowe)
    - Dokumentacja
    - Skontaktuj się z pomocą techniczną

**Wymagania techniczne**

Podstawowe wymagania techniczne dotyczące modułu IoT Edge, aby można było uzyskać certyfikat i opublikować go w witrynie Azure Marketplace, opisano szczegółowo w temacie [przygotowanie zasobów technicznych modułu IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Dokumentacja i zasoby

[Tworzenie oferty modułu IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) — kroki publikowania nowej oferty modułu IoT Edge za pomocą portalu publikowania w chmurze.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono,

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) się w portalu Marketplace.

Jeśli jesteś zarejestrowanym użytkownikiem i tworzysz nową ofertę lub pracujesz nad istniejącym,

- Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać informacje na temat sposobu publikowania oferty modułu IoT Edge, zobacz temat [Omówienie publikowania oferty IoT Edge module](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) .
