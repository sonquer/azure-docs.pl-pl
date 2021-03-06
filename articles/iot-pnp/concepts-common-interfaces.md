---
title: Common Interfaces — wersja zapoznawcza IoT Plug and Play | Microsoft Docs
description: Opis wspólnych interfejsów dla deweloperów Plug and Play IoT
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f697a0d6aba4f137b75faa2a200424c72aa78c3b
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531415"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Popularne interfejsy Plug and Play IoT

Wszystkie urządzenia Plug and Play IoT powinny implementować niektóre typowe interfejsy. Popularne interfejsy korzystają z rozwiązań IoT, ponieważ zapewniają spójne funkcje. [Certyfikacja](tutorial-build-device-certification.md) wymaga, aby urządzenie zaimplementował kilka typowych interfejsów. Można pobrać typowe definicje interfejsów z repozytorium modelu publicznego.

## <a name="summary-of-common-interfaces"></a>Podsumowanie typowych interfejsów

| Nazwa | ID | Opis | Zaimplementowane przez zestaw SDK usługi Azure IoT | Musi być zadeklarowany w modelu możliwości |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informacje o modelu | urn: azureiot: ModelDiscovery: ModelInformation: 1 | W przypadku urządzeń, które zadeklarują Identyfikator modelu możliwości i interfejsy. Wymagany dla wszystkich urządzeń Plug and Play IoT. | Tak | Nie |
| Informacje o zestawie SDK klienta Digital bliźniaczy | urn: azureiot: Client: SDKInformation: 1 | Zestaw SDK klienta do łączenia urządzenia z platformą Azure. Wymagane na potrzeby [certyfikacji](tutorial-build-device-certification.md) | Tak | Nie |
| Informacje o urządzeniu | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Informacje o urządzeniu i systemie operacyjnym. Wymagane na potrzeby [certyfikacji](tutorial-build-device-certification.md) | Nie | Tak |
| Definicja modelu | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | W przypadku urządzeń Zadeklaruj pełną definicję dla modelu możliwości i interfejsów. Musi być zaimplementowany, gdy definicje modeli nie są hostowane w repozytorium modelu. | Nie | Tak |
| Cyfrowe sznurki | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | Aby deweloperzy rozwiązań mogli pobrać identyfikator modelu możliwości i identyfikatory interfejsów dla dwucyfrowego sznurka. Ten interfejs nie jest zadeklarowany ani zaimplementowany przez urządzenie Plug and Play IoT. | Nie | Nie |

- Zaimplementowane przez zestaw SDK usługi Azure IoT — czy zestaw SDK usługi Azure IoT implementuje możliwości zadeklarowane w interfejsach. Urządzenia Plug and Play IoT korzystające z zestawu Azure IoT SDK nie muszą implementować tego interfejsu.
- Musi być zadeklarowany w modelu możliwości — Jeśli tak, ten interfejs musi być zadeklarowany w sekcji `"implements":` modelu możliwości urządzenia dla tego urządzenia IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Pobierz definicje interfejsu z repozytorium publicznego

### <a name="cli"></a>Interfejs CLI

Przy użyciu rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure można pobrać typowe interfejsy z repozytorium modelu publicznego.

```cmd/sh
az iot pnp interface show --interface {InterfaceID}
```

```cmd/sh
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz polecenie **IoT Plug and Play: Open model Repository** . Wybierz pozycję **repozytorium publiczne**. Repozytorium modelu publicznego zostanie otwarte w VS Code.

1. W repozytorium modelu publicznego wprowadź nazwę interfejsu w polu wyszukiwania.

1. Aby utworzyć lokalną kopię interfejsu, wybierz ją w wynikach wyszukiwania, a następnie wybierz pozycję **Pobierz**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już typowe interfejsy, Oto kilka dodatkowych zasobów:

- [Język definicji Digital bliźniaczy (DTDL)](https://aka.ms/DTDL)
- [Zestaw SDK urządzenia dla języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](https://docs.microsoft.com/rest/api/iothub/device)
