---
title: Przywracanie danych na platformie Azure do systemu Windows Server
description: W tym artykule dowiesz się, jak przywrócić dane przechowywane na platformie Azure na komputerze z systemem Windows Server lub Windows za pomocą agenta Microsoft Azure Recovery Services (MARS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: e12596b496483b872f76ccd610fd70710327b586
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450029"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Przywracanie plików do systemu Windows przy użyciu modelu wdrażania Azure Resource Manager

W tym artykule wyjaśniono, jak przywrócić dane z magazynu kopii zapasowych. Aby przywrócić dane, użyj Kreatora odzyskiwania danych w agencie Microsoft Azure Recovery Services (MARS). Przekonaj się:

* Przywróć dane do tego samego komputera, z którego wykonano kopie zapasowe.
* Przywracanie danych do alternatywnej maszyny.

Za pomocą funkcji natychmiastowego przywracania można zainstalować zapisywalną migawkę punktu odzyskiwania jako wolumin odzyskiwania. Następnie można eksplorować wolumin odzyskiwania i kopiować pliki na komputer lokalny, a tym samym wybiórczo przywracając pliki.

> [!NOTE]
> [Aktualizacja Azure Backup stycznia 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) jest wymagana, jeśli chcesz użyć natychmiastowego przywracania do przywracania danych. Ponadto dane kopii zapasowej muszą być chronione w magazynach lokalnych wymienionych w artykule dotyczącym pomocy technicznej. Zapoznaj się z [aktualizacją Azure Backup stycznia 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) , aby uzyskać najnowszą listę ustawień regionalnych, które obsługują natychmiastowe przywracanie.
>

W Azure Portal należy używać natychmiastowego przywracania Recovery Services magazynów. Jeśli dane są przechowywane w magazynach kopii zapasowych, zostały one przekonwertowane do magazynów Recovery Services. Jeśli chcesz użyć natychmiastowego przywracania, Pobierz aktualizację MARS i postępuj zgodnie z procedurami, które wymieniają natychmiastowe przywracanie.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Odzyskiwanie danych na ten sam komputer przy użyciu funkcji natychmiastowego przywracania

Jeśli przypadkowo usunięto plik i chcesz przywrócić go na tym samym komputerze (z którego jest wykonywana kopia zapasowa), następujące kroki pomogą Ci odzyskać dane.

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie zainstalowano przystawkę, przeszukaj komputer lub serwer pod kątem **Microsoft Azure Backup**.

    Aplikacja klasyczna powinna zostać wyświetlona w wynikach wyszukiwania.

2. Wybierz pozycję **Odzyskaj dane** , aby uruchomić kreatora.

    ![Zrzut ekranu przedstawiający Azure Backup z wyróżnionymi odzyskiwaniem danych](./media/backup-azure-restore-windows-server/recover.png)

3. Na stronie **wprowadzenie** , aby przywrócić dane na ten sam serwer lub na tym samym komputerze, wybierz pozycję **ten serwer (`<server name>`)**  > **dalej**.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych Wprowadzenie stronie](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na stronie **Wybierz tryb odzyskiwania** wybierz **poszczególne pliki i foldery** > **dalej**.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych wybierz stronę trybu odzyskiwania](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Opcja przywrócenia poszczególnych plików i folderów wymaga .NET Framework 4.5.2 lub nowszego. Jeśli opcja **pojedyncze pliki i foldery** nie jest widoczna, należy uaktualnić .NET Framework do wersji 4.5.2 lub nowszej, a następnie spróbować ponownie.
 
   > [!TIP]
   > Opcja **poszczególne pliki i foldery** umożliwia szybki dostęp do danych punktów odzyskiwania. Jest to odpowiednie do odzyskiwania poszczególnych plików, których rozmiar nie przekracza 80 GB i oferuje szybkość przesyłania lub kopiowania do 6 MB/s podczas odzyskiwania. Opcja **woluminu** odzyskuje wszystkie dane kopii zapasowej w określonym woluminie. Ta opcja zapewnia szybsze szybkości transferu (do 60 MB/s), co jest idealne do odzyskiwania danych o dużych rozmiarach lub całych woluminów.

5. Na stronie **Wybierz wolumin i datę** Wybierz wolumin zawierający pliki i foldery, które chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. **Pogrubione** daty wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w jednej dacie jest dostępnych wiele punktów odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego **czas** .

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych Wybieranie woluminu i strony daty](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po wybraniu punktu odzyskiwania do przywrócenia wybierz pozycję **Zainstaluj**.

    Azure Backup instaluje lokalny punkt odzyskiwania i używa go jako woluminu odzyskiwania.

7. Na stronie **przeglądanie i odzyskiwanie plików** wybierz pozycję **Przeglądaj** , aby otworzyć Eksploratora Windows, a następnie znajdź pliki i foldery, których chcesz użyć.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych strona przeglądanie i odzyskiwanie plików](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. W Eksploratorze Windows skopiuj pliki i foldery, które chcesz przywrócić, a następnie wklej je do dowolnej lokalizacji lokalnej na serwerze lub komputerze. Można otworzyć lub przesłać strumieniowo pliki bezpośrednio z woluminu odzyskiwania i sprawdzić, czy są odzyskiwane poprawne wersje.

    ![Zrzut ekranu Eksploratora Windows z wyróżnioną pozycją Kopiuj](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Po zakończeniu na stronie **przeglądanie i odzyskiwanie plików** wybierz pozycję **Odinstaluj**. Następnie wybierz pozycję **tak** , aby potwierdzić, że chcesz odinstalować wolumin.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych strona przeglądanie i odzyskiwanie plików](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie wybierzesz opcji **Odinstaluj**, wolumin odzyskiwania pozostanie zainstalowany przez 6 godzin od momentu zainstalowania. Jednak czas instalacji jest rozszerzony do maksymalnie 24 godzin w przypadku trwającej kopii pliku. Podczas montowania woluminu nie będą wykonywane żadne operacje tworzenia kopii zapasowej. Każda operacja tworzenia kopii zapasowej zaplanowana do uruchomienia w czasie, gdy wolumin zostanie zainstalowany, zostanie uruchomiona po odinstalowaniu woluminu odzyskiwania.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Przywracanie danych do alternatywnej maszyny przy użyciu funkcji natychmiastowej przywracania

Jeśli cały serwer zostanie utracony, można nadal odzyskać dane z Azure Backup na inny komputer. Poniższe kroki ilustrują przepływ pracy.

Te kroki obejmują następującą terminologię:

* *Maszyna źródłowa* — oryginalna maszyna, z której wykonano kopię zapasową, która jest obecnie niedostępna.
* *Maszyna docelowa* — maszyna, do której dane są odzyskiwane.
* *Przykładowy magazyn* — magazyn Recovery Services, do którego zarejestrowano maszynę źródłową i maszynę docelową.

> [!NOTE]
> Kopie zapasowe nie mogą zostać przywrócone na komputerze docelowym, na którym działa Starsza wersja systemu operacyjnego. Na przykład kopię zapasową wykonaną z komputera z systemem Windows 7 można przywrócić na komputerze z systemem Windows 7 (lub nowszym). Nie można przywrócić kopii zapasowej wykonanej z komputera z systemem Windows 8 do komputera z systemem Windows 7.
>
>

1. Otwórz przystawkę **Microsoft Azure Backup** na maszynie docelowej.

2. Upewnij się, że maszyna docelowa i maszyna źródłowa są zarejestrowani do tego samego magazynu Recovery Services.

3. Wybierz pozycję **Odzyskaj dane** , aby otworzyć **Kreatora odzyskiwania danych**.

    ![Zrzut ekranu przedstawiający Azure Backup z wyróżnionymi odzyskiwaniem danych](./media/backup-azure-restore-windows-server/recover.png)

4. Na stronie **wprowadzenie** wybierz **inny serwer**.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych Wprowadzenie stronie](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Podaj plik poświadczeń magazynu, który odnosi się do przykładowego magazynu, i wybierz pozycję **dalej**.

    Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub wygasł), Pobierz nowy plik poświadczeń magazynu z przykładowego magazynu w Azure Portal. Po podaniu prawidłowego poświadczenia magazynu zostanie wyświetlona nazwa odpowiedniego magazynu kopii zapasowych.

6. Na stronie **Wybierz serwer kopii zapasowej** wybierz maszynę źródłową z listy wyświetlanych maszyn i podaj hasło. Następnie wybierz przycisk **Dalej**.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych — strona wybierz serwer kopii zapasowej](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na stronie **Wybierz tryb odzyskiwania** wybierz **poszczególne pliki i foldery** > **dalej**.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych wybierz stronę trybu odzyskiwania](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na stronie **Wybierz wolumin i datę** Wybierz wolumin zawierający pliki i foldery, które chcesz przywrócić.

    W kalendarzu wybierz punkt odzyskiwania. **Pogrubione** daty wskazują dostępność co najmniej jednego punktu odzyskiwania. Jeśli w jednej dacie jest dostępnych wiele punktów odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego **czas** .

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych Wybieranie woluminu i strony daty](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Wybierz pozycję **Zainstaluj** , aby lokalnie zainstalować punkt odzyskiwania jako wolumin odzyskiwania na komputerze docelowym.

10. Na stronie **przeglądanie i odzyskiwanie plików** wybierz pozycję **Przeglądaj** , aby otworzyć Eksploratora Windows, a następnie znajdź pliki i foldery, których chcesz użyć.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych strona przeglądanie i odzyskiwanie plików](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. W Eksploratorze Windows skopiuj pliki i foldery z woluminu odzyskiwania i wklej je do lokalizacji maszyny docelowej. Można otworzyć lub przesłać strumieniowo pliki bezpośrednio z woluminu odzyskiwania i sprawdzić, czy są odzyskiwane poprawne wersje.

    ![Zrzut ekranu Eksploratora Windows z wyróżnioną pozycją Kopiuj](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po zakończeniu na stronie **przeglądanie i odzyskiwanie plików** wybierz pozycję **Odinstaluj**. Następnie wybierz pozycję **tak** , aby potwierdzić, że chcesz odinstalować wolumin.

    ![Zrzut ekranu przedstawiający Kreatora odzyskiwania danych strona przeglądanie i odzyskiwanie plików](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie wybierzesz opcji **Odinstaluj**, wolumin odzyskiwania pozostanie zainstalowany przez 6 godzin od momentu zainstalowania. Jednak czas instalacji jest rozszerzony do maksymalnie 24 godzin w przypadku trwającej kopii pliku. Podczas montowania woluminu nie będą wykonywane żadne operacje tworzenia kopii zapasowej. Każda operacja tworzenia kopii zapasowej zaplanowana do uruchomienia w czasie, gdy wolumin zostanie zainstalowany, zostanie uruchomiona po odinstalowaniu woluminu odzyskiwania.
    >

## <a name="next-steps"></a>Następne kroki

Teraz, gdy odzyskasz pliki i foldery, możesz [zarządzać kopiami zapasowymi](backup-azure-manage-windows-server.md).
