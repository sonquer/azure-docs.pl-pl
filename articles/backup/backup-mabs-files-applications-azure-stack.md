---
title: Tworzenie kopii zapasowych plików na maszynach wirtualnych Azure Stack
description: Użyj Azure Backup, aby utworzyć kopię zapasową i odzyskać Azure Stack pliki i aplikacje w środowisku Azure Stack.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: 2bcdf7f720708db1487d7d5cdaee41dc93c05728
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172332"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Tworzenie kopii zapasowych plików i aplikacji w usłudze Azure Stack

Za pomocą Azure Backup można chronić pliki i aplikacje (lub tworzyć ich kopie zapasowe) w Azure Stack. Aby utworzyć kopię zapasową plików i aplikacji, należy zainstalować program Microsoft Azure Backup Server jako maszynę wirtualną działającą na Azure Stack. Pliki można chronić na dowolnym serwerze Azure Stack w tej samej sieci wirtualnej. Po zainstalowaniu Azure Backup Server Dodaj dyski platformy Azure w celu zwiększenia magazynu lokalnego dostępnego dla krótkoterminowych danych kopii zapasowej. Azure Backup Server używa usługi Azure Storage do długoterminowego przechowywania.

> [!NOTE]
> Mimo że Azure Backup Server i System Center Data Protection Manager (DPM) są podobne, program DPM nie jest obsługiwany do użycia z programem Azure Stack.
>

W tym artykule nie omówiono instalacji Azure Backup Server w środowisku Azure Stack. Aby zainstalować Azure Backup Server na Azure Stack, zobacz artykuł [instalowanie Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Tworzenie kopii zapasowych plików i folderów w Azure Stack maszynach wirtualnych na platformie Azure

Aby skonfigurować Azure Backup Server ochrony plików w Azure Stack maszynach wirtualnych, Otwórz konsolę programu Azure Backup Server. Za pomocą konsoli programu można skonfigurować grupy ochrony i chronić dane na maszynach wirtualnych.

1. W konsoli Azure Backup Server kliknij pozycję **Ochrona** , a następnie na pasku narzędzi kliknij pozycję **Nowy** , aby otworzyć kreatora **tworzenia nowej grupy ochrony** .

   ![Konfigurowanie ochrony w konsoli Azure Backup Server](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Otwarcie Kreatora może potrwać kilka sekund. Po otwarciu kreatora kliknij przycisk **dalej** , aby przejść do ekranu **Wybierz typ grupy ochrony** .

   ![Zostanie otwarty Kreator nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na ekranie **Wybieranie typu grupy ochrony** wybierz pozycję **serwery** , a następnie kliknij przycisk **dalej**.

    ![Zostanie otwarty Kreator nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Zostanie otwarty ekran **Wybieranie członków grupy** .

    ![Zostanie otwarty Kreator nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na ekranie **Wybieranie członków grupy** kliknij przycisk **+** , aby rozwinąć listę podelementów. Dla wszystkich elementów, które mają być chronione, zaznacz pole wyboru. Po wybraniu wszystkich elementów kliknij przycisk **dalej**.

    ![Zostanie otwarty Kreator nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Firma Microsoft zaleca umieszczenie w jednej grupie ochrony wszystkich danych, które będą współużytkowane z zasadami ochrony. Aby uzyskać pełne informacje o planowaniu i wdrażaniu grup ochrony, zobacz artykuł System Center DPM, [wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Na ekranie **Wybierz metodę ochrony danych** wpisz nazwę grupy ochrony. Zaznacz pole wyboru dla **opcji chcę uzyskać krótkoterminową ochronę przy użyciu:** i chcę **chronić w trybie online**. Kliknij przycisk **Dalej**.

    ![Zostanie otwarty Kreator nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Aby wybrać opcję chcę **chronić w trybie online**, musisz najpierw wybrać opcję **Chcę uzyskać krótkoterminową ochronę przy użyciu:** dysk. Azure Backup Server nie chroni na taśmie, więc dysk jest jedynym wyborem dla ochrony krótkoterminowej.

5. Na ekranie **Określ cele krótkoterminowe** wybierz, jak długo mają być przechowywane punkty odzyskiwania zapisane na dysku, oraz czas zapisywania przyrostowych kopii zapasowych. Kliknij przycisk **Dalej**.

    > [!IMPORTANT]
    > Danych odzyskiwania operacyjnego (Backup) **nie** należy przechowywać na dyskach dołączonych Azure Backup Server przez więcej niż pięć dni.
    >

    ![Zostanie otwarty Kreator nowej grupy ochrony](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Zamiast wybierać interwał przyrostowych kopii zapasowych, aby uruchomić ekspresową pełną kopię zapasową tuż przed każdym zaplanowanym punktem odzyskiwania, kliknij **tuż przed punktem odzyskiwania**. W przypadku ochrony obciążeń aplikacji Azure Backup Server tworzy punkty odzyskiwania zgodnie z harmonogramem częstotliwości synchronizacji (pod warunkiem, że aplikacja obsługuje przyrostowe kopie zapasowe). Jeśli aplikacja nie obsługuje przyrostowych kopii zapasowych, Azure Backup Server uruchamia ekspresową pełną kopię.

    Dla **punktów odzyskiwania plików**Określ, kiedy mają być tworzone punkty odzyskiwania. Kliknij przycisk **Modyfikuj** , aby ustawić godziny i dni tygodnia, w których są tworzone punkty odzyskiwania.

6. Na ekranie **Przejrzyj przydział dysku** Przejrzyj miejsce na dysku w puli magazynów przydzielone danej grupie ochrony.

    **Łączny rozmiar danych** to rozmiar danych, dla których ma zostać utworzona kopia zapasowa, oraz miejsce na dysku, na którym ma **zostać zainicjowana obsługa** Azure Backup Server jest zalecanym miejscem dla grupy ochrony. Azure Backup Server wybiera idealny wolumin kopii zapasowej na podstawie ustawień. Można jednak edytować opcje woluminu kopii zapasowej w szczegółach alokacji dysku. W przypadku obciążeń wybierz preferowany magazyn z menu rozwijanego. Twoje zmiany zmieniają wartości łącznego magazynu i wolnego magazynu w okienku dostępne Disk Storage. Zajęte miejsce to ilość Azure Backup Server magazynów sugerujących dodanie do woluminu, co umożliwia płynne wykonywanie kopii zapasowych w przyszłości.

7. W obszarze **Wybierz metodę tworzenia repliki**wybierz, jak chcesz obsługiwać początkową pełną replikację danych. Jeśli zdecydujesz się na replikację za pośrednictwem sieci, platforma Azure zaleca wybranie czasu poza godzinami szczytu. W przypadku dużych ilości danych lub mniej niż optymalnych warunków sieciowych należy rozważyć replikowanie danych przy użyciu nośnika wymiennego.

8. W obszarze **Wybierz opcje sprawdzania spójności**wybierz sposób automatyzacji sprawdzania spójności. Włącz sprawdzanie spójności do uruchomienia tylko wtedy, gdy replikacja danych stała się niespójna lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie Uruchom sprawdzanie ręczne, wykonując następujące czynności:
    * W obszarze **Ochrona** w konsoli Azure Backup Server kliknij prawym przyciskiem myszy grupę ochrony i wybierz polecenie **Przeprowadź sprawdzanie spójności**.

9. Jeśli zdecydujesz się na utworzenie kopii zapasowej na platformie Azure, na stronie **Określ dane ochrony w trybie online** upewnij się, że są zaznaczone obciążenia, dla których chcesz utworzyć kopię zapasową na platformie Azure.

10. W obszarze **Określ harmonogram kopii zapasowych online**Określ, kiedy mają być wykonywane przyrostowe kopie zapasowe na platformie Azure.

    Możesz zaplanować uruchamianie kopii zapasowych codziennie na dzień/tydzień/miesiąc/rok oraz godzinę/datę, o której powinny one zostać uruchomione. Kopie zapasowe mogą odbywać się maksymalnie dwa razy dziennie. Za każdym razem, gdy uruchamiane jest zadanie tworzenia kopii zapasowej, punkt odzyskiwania danych jest tworzony na platformie Azure na podstawie kopii kopii zapasowej danych przechowywanych na dysku Azure Backup Server.

11. W obszarze **Określ zasady przechowywania danych online**Określ, w jaki sposób punkty odzyskiwania utworzone na podstawie kopii zapasowych na dzień/tydzień/miesiąc/rok mają być przechowywane na platformie Azure.

12. W obszarze **Wybierz replikację online**Określ, jak następuje początkowa pełna replikacja danych.

13. W obszarze **Podsumowanie**przejrzyj ustawienia. Po kliknięciu przycisku **Utwórz grupę**następuje początkowa replikacja danych. Po zakończeniu replikacji danych na stronie **stan** stan grupy ochrony jest wyświetlany jako **OK**. Początkowe zadanie tworzenia kopii zapasowej odbywa się zgodnie z ustawieniami grupy ochrony.

## <a name="recover-file-data"></a>Odzyskaj dane pliku

Za pomocą konsoli Azure Backup Server można odzyskiwać dane na maszynę wirtualną.

1. W konsoli Azure Backup Server na pasku nawigacyjnym kliknij pozycję **odzyskiwanie** i Wyszukaj dane, które chcesz odzyskać. W okienku wyników wybierz dane.

2. W kalendarzu w sekcji punkty odzyskiwania są dostępne daty pogrubione wskazujące punkty odzyskiwania. Wybierz datę do odzyskania.

3. W okienku **element** możliwy do odzyskania wybierz element, który chcesz odzyskać.

4. W okienku **Akcje** kliknij polecenie **Odzyskaj** , aby otworzyć Kreatora odzyskiwania.

5. Dane można odzyskać w następujący sposób:

    * **Odzyskaj do oryginalnej lokalizacji** — Jeśli komputer kliencki jest połączony za pośrednictwem sieci VPN, ta opcja nie działa. Zamiast tego użyj lokalizacji alternatywnej, a następnie skopiuj dane z tej lokalizacji.
    * **Odzyskaj do lokalizacji alternatywnej**

6. Określ opcje odzyskiwania:

    * W przypadku **zachowania odzyskiwania istniejącej wersji**wybierz opcję **Utwórz kopię**, **Pomiń**lub **Zastąp**. Zastępowanie jest dostępne tylko w przypadku odzyskiwania do oryginalnej lokalizacji.
    * W obszarze **zabezpieczenia przywracania**wybierz opcję **Zastosuj ustawienia komputera docelowego** lub **Zastosuj ustawienia zabezpieczeń wersji punktu odzyskiwania**.
    * Aby zapewnić **ograniczenie**przepustowości sieci, kliknij przycisk **Modyfikuj** , aby włączyć ograniczanie użycia przepustowości sieci.
    * **Powiadomienie** Kliknij przycisk **Wyślij wiadomość e-mail po zakończeniu odzyskiwania**i określ adresatów, którzy otrzymają powiadomienie. Oddziel adresy e-mail przecinkami.
    * Po dokonaniu wyboru kliknij przycisk **dalej** .

7. Przejrzyj ustawienia odzyskiwania i kliknij przycisk **Odzyskaj**.

    >[!Note]
    >Gdy zadanie odzyskiwania jest w toku, wszystkie zadania synchronizacji dla wybranych elementów odzyskiwania są anulowane.

W przypadku korzystania z programu Nowoczesny magazyn kopii zapasowych (MB) serwer plików (EUR) nie jest obsługiwany. Serwer plików EUR ma zależność od Usługa kopiowania woluminów w tle (VSS), która Nowoczesny magazyn kopii zapasowych nie jest używana. Jeśli jest włączona funkcja EUR, wykonaj następujące kroki, aby odzyskać dane:

1. Przejdź do chronionych plików, a następnie kliknij prawym przyciskiem myszy nazwę pliku i wybierz polecenie **Właściwości**.

2. W menu **Właściwości** kliknij pozycję **poprzednie wersje** i wybierz wersję, którą chcesz odzyskać.

## <a name="view-azure-backup-server-with-a-vault"></a>Wyświetlanie Azure Backup Server z magazynem

Aby wyświetlić Azure Backup Server jednostki w Azure Portal, można wykonać następujące czynności:

1. Otwórz Recovery Services magazyn.
2. Kliknij pozycję Infrastruktura kopii zapasowych.
3. Wyświetl serwery zarządzania kopiami zapasowymi.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat używania Azure Backup Server do ochrony innych obciążeń, zobacz jeden z następujących artykułów:

* [Tworzenie kopii zapasowej farmy programu SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Tworzenie kopii zapasowej programu SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
