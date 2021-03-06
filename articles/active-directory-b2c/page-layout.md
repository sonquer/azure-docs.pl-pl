---
title: Wersje układu strony
titleSuffix: Azure AD B2C
description: Historia wersji układu stron dla dostosowywania interfejsu użytkownika w zasadach niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2a0a786d3e2135467c7279c76bae273bff0ba2d0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149511"
---
# <a name="page-layout-versions"></a>Wersje układu strony

Pakiety układów stron są okresowo aktualizowane w celu uwzględnienia poprawek i ulepszeń w ich elementach strony. Następujący dziennik zmian określa zmiany wprowadzone w każdej wersji.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Strona z własnym potwierdzeniem (`selfasserted`)
  - Dodano obsługę [formantów wyświetlania](display-controls.md) w zasadach niestandardowych.

## <a name="120"></a>1.2.0

- Wszystkie strony
  - Poprawki ułatwień dostępu
  - Teraz możesz dodać atrybut `data-preload="true"` w tagach HTML, aby kontrolować kolejność ładowania dla CSS i JavaScript.
    - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
    - Kontrolowanie kolejności, w której Tagi `script` są pobierane i wykonywane przed załadowaniem strony.
  - Pole adresu e-mail jest teraz `type=email` a klawiatury przenośne zapewniają poprawne sugestie
  - Obsługa funkcji tłumaczenia dla programu Chrome
- Ujednolicone i samodzielne strony
  - Pola username/email i Password używają teraz elementu `form` HTML, aby umożliwić programowi Microsoft Edge i Internet Explorer (IE) prawidłowe Zapisywanie tych informacji.

## <a name="110"></a>1.1.0

- Strona wyjątku (globalexception)
  - Poprawka ułatwień dostępu
  - Usunięto komunikat domyślny, gdy nie ma kontaktu z zasad
  - Usunięto domyślny kod CSS
- Strona usługi MFA (wieloskładnikowa)
  - Przycisk "Potwierdź kod" został usunięty
  - Pole wejściowe dla kodu przyjmuje teraz tylko dane wejściowe z maksymalnie sześciu (6) znaków
  - Strona automatycznie podejmie próbę zweryfikowania kodu wprowadzonego po wprowadzeniu 6-cyfrowy kod bez kliknięcia przycisku
  - Jeśli kod jest nieprawidłowy, pole wejściowe zostanie automatycznie wyczyszczone
  - Po trzech (3) próbach z nieprawidłowym kodem B2C wysyła błąd z powrotem do jednostki uzależnionej
  - Poprawki ułatwień dostępu
  - Usunięto domyślny kod CSS
- Strona z własnym potwierdzeniem (selfasserted)
  - Usunięto alert dotyczący anulowania
  - Klasa CSS dla elementów Error
  - Ulepszone wyświetlanie/ukrywanie logiki błędów
  - Usunięto domyślny kod CSS
- Ujednolicony Dostawca SSP (unifiedssp)
  - Dodano kontrolkę nie wylogowuj mnie (KMSI)

## <a name="100"></a>1.0.0

- Wersja początkowa

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat dostosowywania interfejsu użytkownika aplikacji w zasadach niestandardowych, zobacz [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych](custom-policy-ui-customization.md).
