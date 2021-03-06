---
title: Przywracanie aplikacji z migawki
description: Dowiedz się, jak przywrócić aplikację z migawki. Odzyskaj nieoczekiwaną utratę danych w warstwie Premium przy użyciu automatycznych kopii w tle.
author: ahmedelnably
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: e5b48e05d6bd9a310b4527300126464c4b755c8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671137"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Przywracanie aplikacji na platformie Azure z migawki
W tym artykule pokazano, jak przywrócić aplikację w [Azure App Service](../app-service/overview.md) z migawki. Możesz przywrócić poprzedni stan aplikacji, bazując na jednej z migawek aplikacji. Nie trzeba włączać kopii zapasowych migawek, platforma automatycznie zapisuje migawkę wszystkich aplikacji na potrzeby odzyskiwania danych.

Migawki to przyrostowe kopie w tle i oferują kilka korzyści w stosunku do zwykłych [kopii zapasowych](manage-backup.md):
- Brak błędów kopiowania pliku spowodowanych blokadami plików.
- Brak ograniczenia rozmiaru magazynu.
- Nie jest wymagana żadna konfiguracja.

Przywracanie z migawek jest dostępne dla aplikacji działających w warstwie **Premium** lub wyższych. Aby uzyskać informacje na temat skalowania aplikacji w górę, zobacz [skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md).

## <a name="limitations"></a>Ograniczenia

- Ta funkcja jest obecnie dostępna w wersji zapoznawczej.
- Można przywrócić tylko do tej samej aplikacji lub do gniazda należącego do tej aplikacji.
- App Service powoduje zatrzymanie docelowej aplikacji lub docelowego miejsca podczas przywracania.
- App Service utrzymuje w ciągu trzech miesięcy migawki na potrzeby odzyskiwania danych platformy.
- Migawki można przywracać tylko w ciągu ostatnich 30 dni.
- App Services uruchomione na App Service Environment nie obsługują migawek.
 

## <a name="restore-an-app-from-a-snapshot"></a>Przywracanie aplikacji z migawki

1. Na stronie **Ustawienia** aplikacji w [Azure Portal](https://portal.azure.com)kliknij pozycję **kopie zapasowe** , aby wyświetlić stronę **kopie zapasowe** . Następnie kliknij przycisk **Przywróć** w sekcji **migawka (wersja zapoznawcza)** .
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Na stronie **przywracanie** wybierz migawkę do przywrócenia.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Określ lokalizację docelową przywracania aplikacji w **miejscu docelowym przywracania**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > W przypadku wybrania opcji **Zastąp**wszystkie istniejące dane w bieżącym systemie plików aplikacji zostaną wymazane i zastąpione. Przed kliknięciem przycisku **OK**upewnij się, że jest to co chcesz zrobić.
   > 
   > 
      
   > [!Note]
   > Ze względu na bieżące ograniczenia techniczne można przywrócić tylko do aplikacji w tej samej jednostce skalowania. To ograniczenie zostanie usunięte w przyszłej wersji.
   > 
   > 
   
    Możesz wybrać **istniejącą aplikację** do przywrócenia w gnieździe. Przed użyciem tej opcji należy wcześniej utworzyć miejsce w aplikacji.

4. Można przywrócić konfigurację lokacji.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Kliknij przycisk **OK**.
