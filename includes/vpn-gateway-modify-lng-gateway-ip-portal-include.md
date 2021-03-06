---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183066"
---
### <a name="gwipnoconnection"></a> Aby zmodyfikować adres IP bramy sieci lokalnej — Brak połączenia bramy

Użyj przykładu, aby zmodyfikować bramę sieci lokalnej bez połączenia bramy. Podczas modyfikowania tej wartości możesz również zmodyfikować prefiksy adresów.

1. W zasobie bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. W **adresu IP** zmodyfikuj adres IP.
3. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

### <a name="gwipwithconnection"></a>Aby zmodyfikować adresu IP bramy sieci lokalnej — istniejące połączenie bramy

Aby zmodyfikować bramę sieci lokalnej, która ma połączenie, musisz najpierw usunąć połączenie. Po usunięciu połączenia możesz zmodyfikować adres IP bramy i ponownie utworzyć nowe połączenie. Jednocześnie możesz również zmodyfikować prefiksy adresów. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania adresu IP bramy nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.
 
#### <a name="1-remove-the-connection"></a>1. Usuń połączenie.

1. W zasobie bramy sieci lokalnej w **ustawienia** kliknij **połączeń**.
2. Kliknij przycisk **...**  w wierszu dla połączenia, następnie kliknij przycisk **Usuń**.
3. Kliknij przycisk **Zapisz** można zapisać ustawień.

#### <a name="2-modify-the-ip-address"></a>2. Modyfikowanie adresu IP.

Jednocześnie możesz również zmodyfikować prefiksy adresów.

1. W **adresu IP** zmodyfikuj adres IP.
2. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

#### <a name="3-recreate-the-connection"></a>3. Ponowne utworzenie połączenia.

1. Przejdź do bramy sieci wirtualnej dla sieci wirtualnej. (Nie brama sieci lokalnej.)
2. Na bramy sieci wirtualnej w **ustawienia** kliknij **połączeń**.
3. Kliknij przycisk **+ Dodaj** otworzyć **Dodaj połączenie** bloku.
4. Utwórz ponownie połączenie.
5. Kliknij przycisk **OK** do utworzenia połączenia.