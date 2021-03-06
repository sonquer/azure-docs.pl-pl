---
title: Kojarzenie równorzędnych elementów ASN z subskrypcją platformy Azure przy użyciu portalu
titleSuffix: Azure
description: Kojarzenie równorzędnych elementów ASN z subskrypcją platformy Azure przy użyciu portalu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912133"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Kojarzenie równorzędnych elementów ASN z subskrypcją platformy Azure przy użyciu portalu

Przed przesłaniem żądania komunikacji równorzędnej należy najpierw skojarzyć numer ASN z subskrypcją platformy Azure, wykonując poniższe kroki.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu programu [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Utwórz PeerAsn, aby skojarzyć swój numer ASN z subskrypcją platformy Azure

### <a name="sign-in-to-the-portal"></a>Logowanie się do portalu
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Zarejestruj dla dostawcy zasobów komunikacji równorzędnej
Zarejestruj się w celu uzyskania dostawcy zasobów komunikacji równorzędnej w ramach subskrypcji, wykonując poniższe kroki. Jeśli to nie zrobisz, zasoby platformy Azure wymagane do skonfigurowania komunikacji równorzędnej są niedostępne.

1. Kliknij pozycję **subskrypcje** w lewym górnym rogu portalu. Jeśli go nie widzisz, kliknij pozycję **więcej usług** i wyszukaj go.

    > [!div class="mx-imgBorder"]
    > ![otwarte subskrypcje](./media/rp-subscriptions-open.png)

1. Kliknij subskrypcję, której chcesz użyć do komunikacji równorzędnej.

    > [!div class="mx-imgBorder"]
    > ![uruchomić subskrypcję](./media/rp-subscriptions-launch.png)

1. Po otwarciu subskrypcji po lewej stronie kliknij pozycję **dostawcy zasobów**. Następnie w okienku po prawej stronie Wyszukaj *komunikację równorzędną* w oknie wyszukiwania lub Użyj paska przewijania, aby znaleźć pozycję **Microsoft. peering** i sprawdzić **stan**. Jeśli stan jest ***zarejestrowany***, Pomiń poniższe kroki i przejdź do sekcji **Tworzenie PeerAsn**. Jeśli stan to ***NotRegistered***, wybierz pozycję **Microsoft. Komunikacja równorzędna** i kliknij pozycję **zarejestruj**.

    > [!div class="mx-imgBorder"]
    > ](./media/rp-register-start.png) rozpoczęcia rejestracji ![

1. Zwróć uwagę, że stan zmieni się na ***zarejestrowanie***.

    > [!div class="mx-imgBorder"]
    > ![](./media/rp-register-progress.png) rejestracji

1. Poczekaj chwilę lub na ukończenie rejestracji. Następnie kliknij przycisk **Odśwież** i sprawdź, czy stan jest ***zarejestrowany***.

    > [!div class="mx-imgBorder"]
    > zakończono rejestrację ![](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Utwórz PeerAsn
Można utworzyć nowy zasób PeerAsn do kojarzenia numeru systemu autonomicznego (ASN) z subskrypcją platformy Azure. Można skojarzyć wiele numerów WPW z subskrypcją, tworząc **PeerAsn** dla każdego numeru ASN, który należy skojarzyć.

1. Kliknij pozycję **Utwórz zasób,**  > **zobaczyć wszystkie**.

    > [!div class="mx-imgBorder"]
    > ![Search PeerAsn](./media/peerasn-seeall.png)

1. Wyszukaj ciąg *PeerAsn* w polu wyszukiwania i naciśnij klawisz *Enter* na klawiaturze. Na stronie wyników kliknij pozycję zasób **PeerAsn** .

    > [!div class="mx-imgBorder"]
    > ![uruchamiania PeerAsn](./media/peerasn-launch.png)

1. Po uruchomieniu **PeerAsn** kliknij pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Utwórz PeerAsn](./media/peerasn-create.png)

1. Na stronie **Skojarz element równorzędny ASN** , w obszarze **podstawowe** karty, wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > Karta ![podstawy PeerAsn](./media/peerasn-basics-tab.png)

    * **Nazwa** odpowiada nazwie zasobu i może być dowolna.  
    * Wybierz **subskrypcję** , która ma być skojarzona z numerem ASN.
    * **Nazwa elementu równorzędnego** odpowiada nazwie firmy i musi być możliwie jak najbliżej profilu PeeringDB. Należy zauważyć, że wartość obsługuje tylko znaki a-z, A-Z i spację
    * Wprowadź numer ASN w polu **peer ASN** .
    * Kliknij pozycję **Utwórz nowy** i wprowadź **adres E-mail** i **numer telefonu** dla centrum operacji sieciowych (noc).
1. Następnie kliknij pozycję **Przegląd + Utwórz** i obserwuj, że w portalu jest uruchamiana podstawowa weryfikacja wprowadzonych informacji. Ten element jest wyświetlany na Wstążce u góry, jak *trwa sprawdzanie poprawności końcowej...*

    > [!div class="mx-imgBorder"]
    > Karta ![przegląd PeerAsn](./media/peerasn-review-tab-validation.png)

1. Gdy komunikat na Wstążce *przejdzie do walidacji*, sprawdź informacje i prześlij żądanie, klikając pozycję **Utwórz**. Jeśli sprawdzanie poprawności nie zostanie zakończone, kliknij przycisk **Wstecz** i powtórz powyższe kroki w celu zmodyfikowania żądania i upewnienia się, że wprowadzone wartości nie mają żadnych błędów.

    > [!div class="mx-imgBorder"]
    > Karta ![przegląd PeerAsn](./media/peerasn-review-tab.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone poniżej.

    > [!div class="mx-imgBorder"]
    > ![powodzenie PeerAsn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Wyświetl stan elementu PeerAsn
Po pomyślnym wdrożeniu zasobu PeerAsn należy poczekać, aż firma Microsoft zatwierdzi żądanie skojarzenia. Zatwierdzenie może potrwać do 12 godzin. Po zatwierdzeniu otrzymasz powiadomienie o adresie e-mail wprowadzonym w powyższej sekcji.

> [!IMPORTANT]
> Poczekaj, aż ValidationState przełączy "zatwierdzone" przed przesłaniem żądania komunikacji równorzędnej. Zatwierdzenie może potrwać do 12 godzin.

## <a name="modify-peerasn"></a>Modyfikuj PeerAsn
Modyfikowanie PeerAsn nie jest obecnie obsługiwane. Jeśli musisz zmodyfikować, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Usuń PeerAsn
Usuwanie elementu PeerAsn nie jest obecnie obsługiwane. Jeśli musisz usunąć PeerAsn, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)
* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md)