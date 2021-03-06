---
title: Konfigurowanie macierzy wirtualnej StorSimple jako serwera plików | Microsoft Docs
description: Ten trzeci samouczek wdrożenia macierzy wirtualnej StorSimple powoduje skonfigurowanie urządzenia wirtualnego jako serwera plików.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2d93099f0f76f173cc7e77ab7f24f27d1560835
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516778"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Wdróż wirtualną macierz StorSimple — Skonfiguruj jako serwer plików za pośrednictwem Azure Portal
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Wprowadzenie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

W tym artykule opisano sposób przeprowadzania konfiguracji początkowej, rejestrowania serwera plików StorSimple, kończenia konfiguracji urządzenia i tworzenia udziałów SMB oraz łączenia się z nimi. Jest to ostatni artykuł z serii samouczków wdrażania wymaganych do całkowitego wdrożenia macierzy wirtualnej jako serwera plików lub serwera iSCSI.

Proces instalacji i konfiguracji może potrwać około 10 minut. Informacje zawarte w tym artykule mają zastosowanie tylko do wdrożenia macierzy wirtualnej StorSimple. W przypadku wdrażania urządzeń z serii StorSimple 8000 przejdź do: [Wdróż urządzenie z serii StorSimple 8000 z aktualizacją Update 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji
Przed skonfigurowaniem i skonfigurowaniem macierzy wirtualnej StorSimple upewnij się, że:

* Zainicjowano obsługę macierzy wirtualnej i nastąpiło do niej połączenie, zgodnie z opisem w temacie " [Inicjowanie obsługi StorSimple macierzy wirtualnej w funkcji Hyper-V"](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [Inicjowanie obsługi wirtualnej macierzy StorSimple w oprogramowaniu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługi StorSimple Menedżer urządzeń, która została utworzona w celu zarządzania macierzami wirtualnymi StorSimple. Aby uzyskać więcej informacji, [Zobacz Krok 2: Pobierz klucz](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) rejestracji usługi dla macierzy wirtualnej StorSimple.
* Jeśli jest to druga lub kolejna tablica wirtualna, która jest rejestrowana w istniejącej usłudze StorSimple Menedżer urządzeń, należy mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany, gdy pierwsze urządzenie zostało pomyślnie zarejestrowane w tej usłudze. Jeśli ten klucz został utracony, zobacz [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) dla macierzy wirtualnej StorSimple.

## <a name="step-by-step-setup"></a>Konfiguracja krok po kroku
Wykonaj następujące instrukcje krok po kroku, aby skonfigurować i skonfigurować macierz wirtualną StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Ukończ konfigurację lokalnego interfejsu użytkownika sieci Web i Zarejestruj urządzenie
#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby ukończyć instalację i zarejestrować urządzenie
1. Otwórz okno przeglądarki i Połącz się z lokalnym interfejsem użytkownika sieci Web. Wpisz:
   
   `https://<ip-address of network interface>`
   
   Użyj adresu URL połączenia zanotowanego w poprzednim kroku. Zostanie wyświetlony komunikat o błędzie z informacją, że wystąpił problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj na tej stronie sieci Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Zaloguj się do internetowego interfejsu użytkownika macierzy wirtualnej jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia zmienione w kroku 3: Uruchom macierz wirtualną w [ramach aprowizacji macierzy wirtualnej StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub w [celu aprowizacji macierzy wirtualnej StorSimple w oprogramowaniu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Nastąpi przekierowanie do strony **głównej** . Ta strona zawiera opis różnych ustawień wymaganych do skonfigurowania i zarejestrowania macierzy wirtualnej przy użyciu usługi StorSimple Menedżer urządzeń. Ustawienia **sieci**, **Ustawienia serwera proxy sieci Web**i **Ustawienia czasu** są opcjonalne. Jedyne wymagane ustawienia to **Ustawienia urządzenia** i **Ustawienia chmury**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na stronie **Ustawienia sieci** w obszarze **interfejsy sieciowe**zostaną automatycznie skonfigurowane dane 0. Każdy interfejs sieciowy jest domyślnie ustawiany w celu automatycznego uzyskiwania adresu IP (DHCP). W związku z tym adres IP, podsieć i Brama są automatycznie przypisywane (dla protokołów IPv4 i IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Jeśli podczas aprowizacji urządzenia dodano więcej niż jeden interfejs sieciowy, można je skonfigurować w tym miejscu. Należy pamiętać, że interfejs sieciowy można skonfigurować tylko jako IPv4 lub jako IPv4 i IPv6. Tylko konfiguracje protokołu IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są używane, gdy urządzenie próbuje skontaktować się z dostawcami usług magazynu w chmurze lub rozpoznać urządzenie według nazwy, jeśli jest ono skonfigurowane jako serwer plików. Na stronie **Ustawienia sieciowe** w obszarze **serwery DNS**:
   
   1. Podstawowy i pomocniczy serwer DNS są automatycznie konfigurowane. Jeśli zdecydujesz się skonfigurować statyczne adresy IP, możesz określić serwery DNS. Aby zapewnić wysoką dostępność, zalecamy skonfigurowanie podstawowego i pomocniczego serwera DNS.
   2. Kliknij przycisk **Zastosuj** , aby zastosować i sprawdzić poprawność ustawień sieci.
6. Na stronie **Ustawienia urządzenia** :
   
   1. Przypisz unikatową **nazwę** do urządzenia. Nazwa może składać się z 1-15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) ikonę **serwer plików** dla tworzonego **typu** urządzenia. Serwer plików umożliwi tworzenie folderów udostępnionych.
   3. Ponieważ urządzenie jest serwerem plików, należy przyłączyć urządzenie do domeny. Wprowadź **nazwę domeny**.
   4. Kliknij przycisk **zastosować**.
7. Zostanie wyświetlone okno dialogowe. Wprowadź poświadczenia domeny w określonym formacie. Kliknij ikonę zaznaczania. Poświadczenia domeny zostały zweryfikowane. Jeśli poświadczenia są nieprawidłowe, zostanie wyświetlony komunikat o błędzie.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kliknij przycisk **zastosować**. Spowoduje to zastosowanie i zweryfikowanie ustawień urządzenia.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO) ani dziedziczone. Zasady grupy mogą instalować aplikacje, takie jak oprogramowanie antywirusowe, w macierzy wirtualnej StorSimple. Instalowanie dodatkowego oprogramowania nie jest obsługiwane i może prowadzić do uszkodzenia danych. 
   > 
   > 
9. (Opcjonalnie) skonfiguruj serwer proxy sieci Web. Mimo że konfiguracja serwera proxy sieci Web jest opcjonalna, należy pamiętać, że jeśli używasz serwera proxy sieci Web, możesz go skonfigurować tylko w tym miejscu.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na stronie **serwer proxy sieci Web** :
   
   1. Podaj **adres URL serwera proxy sieci Web** w tym formacie: *http://&lt;Host — adres IP&gt;lub nazwa FQDN:P numer*portu. Należy zauważyć, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelnianie** jako **podstawowa** lub **Brak**.
   3. W przypadku korzystania z uwierzytelniania należy również podać **nazwę użytkownika** i **hasło**.
   4. Kliknij przycisk **zastosować**. Spowoduje to zweryfikowanie i zastosowanie skonfigurowanych ustawień serwera proxy sieci Web.
10. (Opcjonalnie) skonfiguruj ustawienia czasu dla urządzenia, takie jak strefa czasowa i serwery NTP serwerów głównych i dodatkowych. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na stronie **Ustawienia czasu** :
    
    1. Z listy rozwijanej wybierz strefę **czasową** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Domyślna strefa czasowa urządzenia to PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
    2. Określ **podstawowy serwer NTP** dla urządzenia lub Zaakceptuj wartość domyślną Time.Windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
    3. Opcjonalnie można określić **pomocniczy serwer NTP** dla urządzenia.
    4. Kliknij przycisk **zastosować**. Spowoduje to zweryfikowanie i zastosowanie skonfigurowanych ustawień czasu.
11. Skonfiguruj ustawienia chmury dla urządzenia. W tym kroku zakończysz konfigurację urządzenia lokalnego, a następnie zarejestrujesz urządzenie w usłudze StorSimple Menedżer urządzeń.
    
    1. Wprowadź **klucz rejestracji usługi** , który został uzyskany [w kroku 2: Pobierz klucz](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) rejestracji usługi dla macierzy wirtualnej StorSimple.
    2. Jeśli jest to pierwsze urządzenie zarejestrowano w usłudze, zostanie wyświetlony **klucz szyfrowania danych usługi**. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. Ten klucz jest wymagany w przypadku klucza rejestracji usługi w celu zarejestrowania dodatkowych urządzeń w usłudze StorSimple Menedżer urządzeń. 
       
       Jeśli nie jest to pierwsze urządzenie, które jest zarejestrowana w ramach tej usługi, musisz podać klucz szyfrowania danych usługi. Aby uzyskać więcej informacji, zobacz Pobieranie [klucza szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) w lokalnym interfejsie użytkownika sieci Web.
    3. Kliknij pozycję **zarejestruj**. Spowoduje to ponowne uruchomienie urządzenia. Przed pomyślnym zarejestrowaniem urządzenia może być konieczne odczekanie przez 2-3 minut. Po ponownym uruchomieniu urządzenia nastąpi przekierowanie do strony logowania.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Wróć do witryny Azure Portal. Przejdź do obszaru **wszystkie zasoby**i Wyszukaj usługę StorSimple Menedżer urządzeń.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na liście filtrowane wybierz usługę StorSimple Menedżer urządzeń a następnie przejdź do opcji **zarządzanie > urządzeń**. W bloku **urządzenia** Sprawdź, czy urządzenie pomyślnie nawiązał połączenie z usługą i czy ma stan **gotowe do skonfigurowania**.
    
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: Skonfiguruj urządzenie jako serwer plików
Wykonaj następujące kroki w [Azure Portal](https://portal.azure.com/) , aby zakończyć wymaganą konfigurację urządzenia.

#### <a name="to-configure-the-device-as-file-server"></a>Aby skonfigurować urządzenie jako serwer plików
1. Przejdź do usługi StorSimple Menedżer urządzeń, a następnie przejdź do pozycji **zarządzanie > urządzeń**. W bloku **urządzenia** wybierz właśnie utworzone urządzenie. To urządzenie będzie widoczne jako **gotowe do skonfigurowania**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Kliknij urządzenie i zostanie wyświetlony komunikat transparent wskazujący, że urządzenie jest gotowe do instalacji.
   
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kliknij pozycję **Konfiguruj** na pasku poleceń. Spowoduje to otwarcie bloku **Konfiguracja** . W bloku **Konfigurowanie** wykonaj następujące czynności:
   
   1. Nazwa serwera plików jest wypełniana automatycznie.
    
   2. Upewnij się, że szyfrowanie magazynu w chmurze jest ustawione na **włączone**. Spowoduje to zaszyfrowanie wszystkich danych wysłanych do chmury. 
    
   3. Do szyfrowania jest używany klucz 256-bitowy AES z kluczem zdefiniowanym przez użytkownika. Określ klucz znaku 32, a następnie ponownie wprowadź klucz, aby go potwierdzić. Zapisz klucz w aplikacji do zarządzania kluczami do użytku w przyszłości.
    
   4. Kliknij pozycję **Skonfiguruj wymagane ustawienia** , aby określić poświadczenia konta magazynu, które będą używane z urządzeniem. Kliknij pozycję **Dodaj nowe** , jeśli nie skonfigurowano żadnych poświadczeń konta magazynu. **Upewnij się, że używane konto magazynu obsługuje blokowe obiekty blob. Stronicowe obiekty blob nie są obsługiwane.** Więcej informacji na temat [bloków BLOB i stronicowych obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. W bloku **Dodawanie poświadczeń konta magazynu** wykonaj następujące czynności: 

    1. Wybierz bieżącą subskrypcję, jeśli konto magazynu znajduje się w tej samej subskrypcji co usługa. Określ inną to konto magazynu jest poza subskrypcją usługi. 
    
    2. Z listy rozwijanej Wybierz istniejące konto magazynu. 
    
    3. Lokalizacja zostanie automatycznie wypełniona w oparciu o określone konto magazynu. 
    
    4. Włącz protokół SSL, aby zapewnić bezpieczny kanał komunikacji sieciowej między urządzeniem i chmurą.
    
    5. Kliknij przycisk **Dodaj** , aby dodać to poświadczenie konta magazynu. 
   
        ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po pomyślnym utworzeniu poświadczenia konta magazynu zostanie zaktualizowany blok **Konfigurowanie** , aby wyświetlić określone poświadczenia konta magazynu. Kliknij pozycję **Konfiguruj**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Zobaczysz, że serwer plików jest tworzony. Po pomyślnym utworzeniu serwera plików użytkownik zostanie powiadomiony.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Stan urządzenia zmieni się również na **online**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Możesz dalej dodać udział.

## <a name="step-3-add-a-share"></a>Krok 3: Dodawanie udziału
Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/), aby utworzyć udział.

#### <a name="to-create-a-share"></a>Aby utworzyć udział
1. Wybierz urządzenie serwera plików skonfigurowane w poprzednim kroku, a następnie kliknij przycisk **...** (lub kliknij prawym przyciskiem myszy). W menu kontekstowym wybierz pozycję **Dodaj udział**. Alternatywnie możesz kliknąć przycisk **+ Dodaj udział** na pasku poleceń urządzenia.
   
   ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Określ następujące ustawienia udziału:

   1. Unikatowa nazwa udziału. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.
    
   2. Opcjonalny **Opis** udziału. Opis pomoże zidentyfikować właścicieli udziałów.
    
   3. **Typ** udziału. Typ może być przypięty **warstwowo** lub **lokalnie**, z warstwą domyślną. W przypadku obciążeń wymagających lokalnych gwarancji, małych opóźnień i większej wydajności Wybierz udział przypięty **lokalnie** . Dla wszystkich innych danych wybierz udział **warstwowy** .
      Udział przypięty lokalnie jest elastycznie zainicjowany i zapewnia, że podstawowe dane udziału pozostają na urządzeniu lokalnym i nie zostaną przelane do chmury. Udział warstwowy z drugiej strony jest alokowany elastycznie. Podczas tworzenia udziału warstwowego zostanie zainicjowana obsługa 10% miejsca w warstwie lokalnej i 90% miejsca w chmurze. Na przykład jeśli Zainicjowano obsługę woluminu o pojemności 1 TB, 100 GB będzie znajdować się w miejscu lokalnym, a w chmurze zostanie użyta 900 GB. To z kolei oznacza, że w przypadku braku całego lokalnego miejsca na urządzeniu nie można zainicjować obsługi administracyjnej udziału warstwowego.
   
   4. W polu **Ustaw domyślne pełne uprawnienia do** , przypisz uprawnienia użytkownikowi lub grupie, która uzyskuje dostęp do tego udziału. Określ nazwę użytkownika lub grupy użytkowników w formacie *John\@contoso.com* . Zalecamy użycie grupy użytkowników (zamiast pojedynczego użytkownika) w celu umożliwienia administratorom dostępu do tych udziałów. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.
   
   5. Kliknij przycisk **Dodaj** , aby utworzyć udział. 
    
       ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału.
   
       ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Po utworzeniu udziału przy użyciu określonych ustawień blok **udziałów** zostanie zaktualizowany w celu odzwierciedlenia nowego udziału. Domyślnie monitorowanie i wykonywanie kopii zapasowych jest włączone dla udziału.
   
      ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: Łączenie z udziałem
Teraz musisz nawiązać połączenie z co najmniej jednym udziałem utworzonym w poprzednim kroku. Wykonaj te kroki na hoście z systemem Windows Server połączonym z wirtualną tablicą StorSimple.

#### <a name="to-connect-to-the-share"></a>Aby nawiązać połączenie z udziałem
1. Naciśnij ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) klawisze + R. W oknie uruchamiania Określ  *&#92; &#92; &lt;nazwę&gt; serwera plików* jako ścieżkę, zastępując *nazwę serwera plików* nazwą urządzenia przypisaną do serwera plików. Kliknij przycisk **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Spowoduje to otwarcie Eksploratora plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. Wybierz i kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Teraz możesz dodawać pliki do tych udziałów i tworzyć kopie zapasowe.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [zarządzać wirtualną tablicą StorSimple](storsimple-ova-web-ui-admin.md)za pomocą lokalnego interfejsu użytkownika sieci Web.

