---
title: Użyj przeglądarki zadań & widoku zadań — Azure Data Lake Analytics
description: W tym artykule opisano, jak używać przeglądarki zadań i widoku zadań dla Azure Data Lake Analytics zadań.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309947"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Użyj przeglądarki zadań i widoku zadań dla Azure Data Lake Analytics
Usługi Azure Data Lake Analytics archiwizują przesłane zadania w magazynie zapytań. W tym artykule dowiesz się, jak używać przeglądarki zadań i widoku zadań w Azure Data Lake Tools for Visual Studio, aby znaleźć historyczne informacje o zadaniu. 

Domyślnie usługa Data Lake Analytics archiwizuje zadania przez 30 dni. Okres ważności można skonfigurować na podstawie Azure Portal przez skonfigurowanie niestandardowych zasad wygasania. Po wygaśnięciu nie będzie można uzyskać dostępu do informacji o zadaniu. 

## <a name="prerequisites"></a>Wymagania wstępne
Zobacz [Data Lake Tools for Visual Studio — wymagania wstępne](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otwórz przeglądarkę zadań
Dostęp do przeglądarki zadań za pośrednictwem **Eksplorator serwera > Azure > Data Lake Analytics > zadań** w programie Visual Studio.  Korzystając z przeglądarki zadań, można uzyskać dostęp do magazynu zapytań konta Data Lake Analytics. W przeglądarce zadań po lewej stronie jest wyświetlany magazyn zapytań zawierający podstawowe informacje o zadaniu i widok zadania po prawej stronie zawierające szczegółowe informacje o zadaniu.

## <a name="job-view"></a>Widok zadania
Widok zadania przedstawia szczegółowe informacje o zadaniu. Aby otworzyć zadanie, kliknij dwukrotnie zadanie w przeglądarce zadań lub otwórz je w menu Data Lake, klikając pozycję Widok zadania. Powinno zostać wyświetlone okno dialogowe z adresem URL zadania.

![Przeglądarka zadań Data Lake Tools programu Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Widok zadania zawiera:

* Podsumowanie zadania
  
    Odśwież widok zadania, aby wyświetlić nowsze informacje o uruchomionych zadaniach.
  
  * Stan zadania (Graf):
    
      Stan zadania przedstawia etapy zadania:
    
      ![Stan faz zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Opracowywani Przekaż skrypt do chmury, kompilując i optymalizując skrypt za pomocą usługi kompilacji.
    * Umieszczony Zadania są umieszczane w kolejce, gdy oczekują na wystarczającą ilość zasobów lub zadania przekraczają maksymalną liczbę współbieżnych zadań na konto. Ustawienie priorytetu Określa sekwencję zadań w kolejce — niższy numer, tym wyższy priorytet.
    * Uruchomion Zadanie jest w rzeczywistości uruchomione na koncie Data Lake Analytics.
    * Finalizowanie Trwa kończenie zadania (na przykład finalizowanie pliku).
      
      Zadanie może zakończyć się niepowodzeniem w każdej fazie. Na przykład błędy kompilacji w fazie przygotowywania, błędy przekroczenia limitu czasu w fazie kolejkowanej oraz błędy wykonywania w fazie wykonywania itd.
  * Informacje podstawowe
    
      Podstawowe informacje o zadaniu są wyświetlane w dolnej części panelu podsumowanie zadania.
    
      ![Stan faz zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Wynik zadania: Powiodło się lub zakończyło się niepowodzeniem. Zadanie może zakończyć się niepowodzeniem w każdej fazie.
    * Łączny czas trwania: Czas zegarowy (czas trwania) między przesyłaniem i czasem zakończenia.
    * Łączny czas obliczeń: Suma każdego czasu wykonywania wierzchołka, można go traktować jako godzinę wykonywania zadania w tylko jednym wierzchołku. Zapoznaj się z sumą wierzchołków, aby uzyskać więcej informacji o wierzchołku.
    * Godzina przesłania/rozpoczęcia/zakończenia: Czas, w którym usługa Data Lake Analytics otrzymuje zadanie przesłaniania/uruchomienia zadania lub kończy zadanie pomyślnie.
    * Kompilacja/kolejkowane/uruchomione: Czas zegara ściennego spędzony w fazie przygotowywania/kolejkowane/uruchomione.
    * Koncie Konto Data Lake Analytics używane do uruchamiania zadania.
    * Tworzone Użytkownik, który przesłał zadanie, może być kontem osoby rzeczywistej lub kontem systemowym.
    * Priorytet: Priorytet zadania. Im niższa wartość, tym wyższy priorytet. Ma to wpływ tylko na sekwencję zadań w kolejce. Ustawienie wyższego priorytetu nie powoduje przełożonego uruchomionych zadań.
    * Równoległości Żądana Maksymalna liczba współbieżnych Azure Data Lake Analytics jednostek (ADLAUs), aliasów. Obecnie jeden wierzchołek jest równy jednej maszynie wirtualnej z dwoma rdzeniami wirtualnymi i sześć GB pamięci RAM, chociaż może to zostać uaktualnione w przyszłości Data Lake Analytics aktualizacje.
    * Pozostałe bajty: Bajty, które muszą zostać przetworzone do momentu ukończenia zadania.
    * Bajty do odczytu/zapisu: Bajty odczytu/zapisu od momentu uruchomienia zadania.
    * Łączna liczba wierzchołków: Zadanie jest podzielone na wiele elementów pracy, każda część pracy jest nazywana wierzchołkiem. Ta wartość opisuje, ile elementów pracy składa się z zadania. Można rozważyć użycie wierzchołka jako podstawowej jednostki procesu, aliasu Azure Data Lake Analytics Unit (ADLAU), a wierzchołków można uruchamiać równolegle. 
    * Ukończono/uruchomiono/niepowodzenie: Liczba wierzchołków ukończonych/uruchomionych/zakończonych niepowodzeniem. Wierzchołki mogą kończyć się niepowodzeniem z powodu błędów kodu użytkownika i systemu, ale automatyczne ponawianie prób w systemie nie powiodło się. Jeśli wierzchołk nadal nie powiedzie się po ponowieniu próby, całe zadanie zakończy się niepowodzeniem.
* Graf zadania
  
    Skrypt U-SQL reprezentuje logikę przekształcania danych wejściowych z danymi wyjściowymi. Skrypt jest kompilowany i zoptymalizowany pod kątem fizycznego planu wykonywania w fazie przygotowywania. Wykres zadania polega na wyświetleniu fizycznego planu wykonania.  Na poniższym diagramie przedstawiono proces:
  
    ![Stan faz zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Zadanie jest podzielone na wiele elementów pracy. Każda część pracy nazywa się wierzchołkiem. Wierzchołki są pogrupowane jako indeks górny (czyli etap) i wizualizowane jako Graf zadań. W wykresach zadań zielonych etapów są wyświetlane etapy.
  
    Każdy wierzchołek w fazie ma ten sam rodzaj pracy z różnymi fragmentami tych samych danych. Na przykład, jeśli masz plik z jedną TB danych, a z niego odczytane są setki wierzchołków, każda z nich odczytuje fragment. Te wierzchołki są pogrupowane w tym samym etapie i działają na różnych fragmentach tego samego pliku wejściowego.
  
  * <a name="state-information"></a>Informacje o etapie
    
      W konkretnym etapie niektóre liczby są widoczne w plakietki.
    
      ![Etap wykresu zadań Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1: Nazwa etapu, nazwana przez liczbę i metodę operacji.
    * 84 wierzchołki: Łączna liczba wierzchołków na tym etapie. Ilustracja wskazuje, ile fragmentów pracy jest podzielony na ten etap.
    * 12,90 s/wierzchołek: Średni czas wykonywania wierzchołka dla tego etapu. Ten rysunek jest obliczany według SUM (każdy czas wykonywania wierzchołka)/(całkowita liczba wierzchołków). Co oznacza, że można przypisać wszystkie wierzchołki wykonane równolegle, cały etap zostanie ukończony w 12,90 s. Oznacza to również, że wszystkie prace w tym etapie są wykonywane sekwencyjnie, a koszt będzie #vertices * średni czas.
    * Zapisano 850 895 wierszy: Całkowita liczba wierszy zapisywana w tym etapie.
    * R/W: Ilość danych do odczytu/zapisu w tym etapie w bajtach.
    * Świat Kolory są używane na etapie, aby wskazać inny stan wierzchołka.
      
      * Zielony wskazuje, że wierzchołek zakończył się pomyślnie.
      * Pomarańczowy wskazuje, że wierzchołk jest ponawiany. Ponowienie próby nie powiodło się, ale jest automatycznie ponawiane i powiodło przez system, a ogólny etap został ukończony pomyślnie. Jeśli wierzchołek ponawia próbę, ale nadal się nie powiodło, kolor zmieni się na czerwony, a całe zadanie zakończyło się niepowodzeniem.
      * Czerwony oznacza niepowodzenie, co oznacza, że niektóre wierzchołki zostały powtórzone kilka razy przez system, ale nadal nie powiodły się. Ten scenariusz powoduje niepowodzenie całego zadania.
      * Niebieska oznacza, że jest uruchomiony określony wierzchołek.
      * Biały wskazuje, że wierzchołk jest oczekiwany. Wierzchołk może oczekiwać na zaplanowanie, gdy ADLAU stanie się dostępny, lub może czekać na dane wejściowe, ponieważ dane wejściowe mogą nie być gotowe.
      
      Więcej szczegółów na temat etapu można znaleźć, aktywując wskaźnik myszy według jednego stanu:
      
      ![Szczegóły etapu wykresu zadań Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Wierzchołki Opisuje szczegóły wierzchołków, na przykład ile wierzchołków łącznie, ile wierzchołków zostało ukończonych, czy nie zakończyły się powodzeniem, czy nadal działają/oczekują na to itd.
  * Odczyt danych — dane krzyżowe/wewnętrzne pod: Pliki i dane są przechowywane w wielu zasobnikach w rozproszonym systemie plików. Wartość w tym miejscu opisuje, jak dużo danych zostało odczytanych w tym samym lub krzyżowo.
  * Łączny czas obliczeń: Suma każdego czasu wykonania wierzchołka na etapie, można go traktować jako czas, gdy wszystkie prace na etapie są wykonywane tylko w jednym wierzchołku.
  * Dane i wiersze zapisywane/odczytane: Wskazuje, ile danych lub wierszy zostało odczytanych/zapisanych lub muszą zostać odczytane.
  * Błędy odczytu wierzchołka: Opisuje, ile wierzchołków zakończyło się niepowodzeniem podczas odczytu danych.
  * Odrzucenia duplikatu wierzchołka: Jeśli wierzchołk działa zbyt wolno, system może zaplanować wiele wierzchołków, aby uruchomić tę samą część pracy. Wierzchołki odnoszące się zostaną odrzucone po pomyślnym ukończeniu jednego z wierzchołków. Odrzucanie duplikatów wierzchołków rejestruje liczbę wierzchołków, które są odrzucane jako duplikaty na etapie.
  * Odwołania wierzchołka: Wierzchołek zakończył się pomyślnie, ale ponowne uruchomienie nie powiodło się z kilku powodów. Na przykład, jeśli wierzchołek podrzędny utraci pośrednie dane wejściowe, zostanie poproszony wierzchołka nadrzędnego o ponowne uruchomienie.
  * Wykonania harmonogramu wierzchołków: Łączny czas zaplanowanych wierzchołków.
  * Minimalny/średni/maksymalny odczyt danych wierzchołka: Minimalna/średnia/wartość maksymalna każdego wierzchołka odczytu danych.
  * Trwania Czas zegarowy, który wykonuje etap, należy załadować profil, aby wyświetlić tę wartość.
  * Odtwarzanie zadania
    
      Data Lake Analytics uruchamia zadania i archiwizuje wierzchołki, w których są uruchomione informacje o zadaniach, takie jak kiedy wierzchołki są uruchomione, zatrzymane, zakończone niepowodzeniem i jak są ponawiane itd. Wszystkie informacje są automatycznie rejestrowane w magazynie zapytań i przechowywane w jego profilu zadań. Profil zadania można pobrać przy użyciu okna "Załaduj profil" w widoku zadań. po pobraniu profilu zadania można wyświetlić odtwarzanie zadania.
    
      Odtwarzanie zadań to epitomea Wizualizacja informacji o tym, co się stało w klastrze. Ułatwia to monitorowanie postępów wykonywania zadań i wizualne wykrywanie anomalii wydajności oraz wąskich gardeł w bardzo krótkim czasie (mniejszym niż 30 s zazwyczaj).
  * Wyświetlanie mapy cieplnej zadania 
    
      Mapę ciepła zadań można wybrać za pomocą listy rozwijanej wyświetlanej w grafie zadania. 
    
      ![Wyświetlanie mapy sterty grafu zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Pokazuje on mapę operacji we/wy, czas i przepływność cieplną zadania, za pomocą którego można sprawdzić, gdzie zadanie spędza większość czasu, lub czy zadanie jest zadaniem granicy we/wy itd.
    
      ![Przykład mapy sterty grafu zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Wykonywane Postęp wykonywania zadania można znaleźć w sekcji Informacje o etapie.
    * Odczyt/zapis danych: Mapa cieplna łącznej ilości danych odczytywanych/zapisywana na każdym etapie.
    * Czas obliczeń: Mapa cieplna SUM (w każdym czasie wykonywania wierzchołka) można rozważyć, jak długo będzie trwać, jeśli wszystkie prace na etapie zostaną wykonane z tylko 1 wierzchołkiem.
    * Średni czas wykonywania na węzeł: Mapa cieplna SUM (każdy czas wykonywania wierzchołka)/(numer wierzchołka). Co oznacza, że można przypisać wszystkie wierzchołki wykonywane równolegle, cały etap zostanie wykonany w tym przedziale czasowym.
    * Przepływność wejścia/wyjścia: Mapa cieplna przepływności wejścia/wyjścia każdego etapu, można potwierdzić, czy zadanie to zadanie powiązane we/wy.
* Operacje dotyczące metadanych
  
    Niektóre operacje na metadanych można wykonywać w skrypcie U-SQL, takich jak tworzenie bazy danych, usuwanie tabeli itp. Te operacje są wyświetlane w operacji metadanych po kompilacji. W tym miejscu możesz znaleźć potwierdzenia, utworzyć jednostki, usunąć jednostki.
  
    ![Operacje na metadanych widoku zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historia stanu
  
    Historia stanu jest również wizualizowana w podsumowaniu zadań, ale więcej informacji można znaleźć tutaj. Można znaleźć szczegółowe informacje, takie jak czas przygotowania zadania, do kolejki, rozpoczęcia pracy i zakończenia. Ponadto możesz sprawdzić, ile razy zadanie zostało skompilowane (CcsAttempts: 1), gdy zadanie zostało wysłane do klastra w rzeczywistości (szczegóły: Wysyłanie zadania do klastra) itp.
  
    ![Historia stanu widoku Azure Data Lake Analytics zadania](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostyka
  
    Narzędzie automatycznie diagnozuje wykonywanie zadania. Alerty zostaną odebrane w przypadku wystąpienia błędów lub problemów z wydajnością w zadaniach. Należy pamiętać, że należy pobrać profil, aby uzyskać pełne informacje w tym miejscu. 
  
    ![Diagnostyka widoku zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Ostrzeżeni Alert jest wyświetlany tutaj z ostrzeżeniem kompilatora. Możesz kliknąć link "x Issues" (problemy), aby uzyskać więcej szczegółów po wyświetleniu alertu.
  * Zbyt długi przebieg wierzchołka: Jeśli dowolny z wierzchołków zakończy się niezgodnie z upływem czasu (powiedzie się 5 godzin), można znaleźć tutaj problemy.
  * Użycie zasobów: W przypadku przydzielenia większej lub niewystarczającej liczby równoległości w tym miejscu zostaną znalezione problemy. Możesz również kliknąć pozycję użycie zasobów, aby wyświetlić więcej szczegółów i wykonać scenariusze co do tego, aby znaleźć lepszą alokację zasobów (więcej informacji można znaleźć w tym przewodniku).
  * Sprawdzanie pamięci: Jeśli którykolwiek z wierzchołków korzysta z więcej niż 5 GB pamięci, w tym miejscu zostaną znalezione problemy. Wykonanie zadania może zostać przerwane przez system, jeśli zużywa więcej pamięci niż ograniczenie systemowe.

## <a name="job-detail"></a>Szczegóły zadania
Szczegóły zadania przedstawia szczegółowe informacje o zadaniu, w tym o skrypcie, zasobach i widoku wykonywania wierzchołków.

![Szczegóły zadania Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skrypt
  
    Skrypt U-SQL zadania jest przechowywany w magazynie zapytań. Możesz wyświetlić oryginalny skrypt U-SQL i ponownie przesłać go, jeśli jest to konieczne.
* Zasoby
  
    Dane wyjściowe kompilacji zadania przechowywane w magazynie zapytań można znaleźć za pomocą zasobów. Na przykład można znaleźć "algebry. xml", który jest używany do wyświetlania grafu zadania, zarejestrowanych zestawów itd.
* Widok wykonywania wierzchołka
  
    Pokazuje szczegóły wykonania wierzchołków. Profil zadania archiwizuje każdy dziennik wykonywania wierzchołków, taki jak całkowita ilość danych odczytywanych/zapisywana, środowisko uruchomieniowe, stan itp. Za pomocą tego widoku możesz uzyskać więcej szczegółowych informacji na temat sposobu uruchomienia zadania. Aby uzyskać więcej informacji, zobacz [Korzystanie z widoku wykonywania wierzchołka w Data Lake narzędzia dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Następne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby użyć widoku wykonywania wierzchołków, zobacz [Korzystanie z widoku wykonywania wierzchołka w Data Lake narzędzia dla programu Visual Studio.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

