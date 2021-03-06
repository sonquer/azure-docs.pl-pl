---
title: 'VPN Gateway platformy Azure: Rozwiązywanie problemów z połączeniami punkt-lokacja: Mac OS X klientów'
description: Kroki umożliwiające rozwiązywanie problemów z połączeniami klienta sieci VPN P2S Mac OS X
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425729"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Rozwiązywanie problemów z połączeniami sieci VPN typu punkt-lokacja z klientów Mac OS X w sieci VPN

Ten artykuł pomaga rozwiązać problemy z połączeniem punkt-lokacja z systemem Mac OS X przy użyciu natywnego klienta sieci VPN i protokołu IKEv2. Klient VPN Mac dla protokołu IKEv2 jest bardzo proste i nie zezwala na wiele dostosowywania. Dostępne są tylko cztery ustawienia, które muszą być sprawdzone:

* Adres serwera
* Identyfikator zdalnego
* Identyfikator lokalny
* Ustawienia uwierzytelniania
* Wersja systemu operacyjnego (10.11 lub nowszej)


## <a name="VPNClient"></a>Rozwiązywanie problemów z uwierzytelnianiem opartym na certyfikatach
1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **Ustawienia sieci** , naciskając klawisze Command + Shift, a następnie wpisz "VPN", aby sprawdzić ustawienia klienta sieci VPN. Z listy kliknij wpis sieci VPN, który musi zostać zbadana.

   ![Uwierzytelnianie oparte na certyfikatach protokołu IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Sprawdź, czy **adres serwera** jest pełną nazwą FQDN i zawiera cloudapp.NET.
3. **Identyfikator zdalny** powinien być taki sam jak adres serwera (FQDN bramy).
4. **Lokalny identyfikator** powinien być taki sam jak **podmiot** certyfikatu klienta.
5. Kliknij pozycję **Ustawienia uwierzytelniania** , aby otworzyć stronę Ustawienia uwierzytelniania.

   ![ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Upewnij się, że wybrano **certyfikat** z listy rozwijanej.
7. Kliknij przycisk **Wybierz** i sprawdź, czy wybrano prawidłowy certyfikat. Kliknij przycisk **OK** , aby zapisać zmiany.

## <a name="ikev2"></a>Rozwiązywanie problemów z uwierzytelnianiem nazwy użytkownika i hasła

1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **Ustawienia sieci** , naciskając klawisze Command + Shift, a następnie wpisz "VPN", aby sprawdzić ustawienia klienta sieci VPN. Z listy kliknij wpis sieci VPN, który musi zostać zbadana.

   ![Hasło nazwy użytkownika protokołu IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Sprawdź, czy **adres serwera** jest pełną nazwą FQDN i zawiera cloudapp.NET.
3. **Identyfikator zdalny** powinien być taki sam jak adres serwera (FQDN bramy).
4. **Identyfikator lokalny** może być pusty.
5. Kliknij przycisk **Ustawienia uwierzytelniania** i sprawdź, czy wybrano pozycję "username" z listy rozwijanej.

   ![ustawienia uwierzytelniania](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Sprawdź, czy wprowadzono poprawne poświadczenia.

## <a name="additional"></a>Dodatkowe kroki

Jeśli spróbujesz wykonać poprzednie kroki i wszystko jest prawidłowo skonfigurowane, Pobierz program [Wireshark](https://www.wireshark.org/#download) i Przechwyć pakiet.

1. Odfiltruj protokół *ISAKMP* i sprawdź, czy **IKE_SA** pakiety. Powinien być w stanie zapoznać się ze szczegółami oferty SKOJARZENIA zabezpieczeń w obszarze **ładunek: zabezpieczenia**. 
2. Sprawdź, czy klient i serwer cechują się wspólnym zestawem.

   ![pakiet](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Jeśli w śladach sieci nie ma odpowiedzi serwera, sprawdź, czy na stronie Konfiguracja bramy platformy Azure w witrynie sieci Web Azure Portal włączono protokół IKEv2.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkową pomoc, zobacz [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
