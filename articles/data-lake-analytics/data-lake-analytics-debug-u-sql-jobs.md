---
title: Debuguj C# kod dla Azure Data Lake zadań U-SQL
description: W tym artykule opisano sposób debugowania niepowodzenia wierzchołka U-SQL przy użyciu Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315816"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Debuguj kod zdefiniowany C# przez użytkownika dla niezakończonych zadań U-SQL

Język U-SQL udostępnia model rozszerzalności C#przy użyciu programu. W skryptach języka U-SQL łatwe jest wywoływanie C# funkcji i wykonywanie funkcji analitycznych, które nie obsługują języka deklaratywnego przypominającego SQL. Aby dowiedzieć się więcej na temat rozszerzalności U-SQL, zobacz [Przewodnik programowania u-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

W tym przypadku każdy kod może wymagać debugowania, ale trudno jest debugować zadanie rozproszone z niestandardowym kodem w chmurze przy użyciu ograniczonych plików dziennika. [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) udostępnia funkcję o nazwie **Nieudane debugowanie wierzchołka**, co ułatwia debugowanie błędów występujących w kodzie niestandardowym. Gdy zadanie U-SQL zakończy się niepowodzeniem, usługa utrzymuje stan niepowodzenia, a narzędzie pomaga pobrać środowisko awarii chmury do maszyny lokalnej na potrzeby debugowania. Lokalne pobieranie przechwytuje całe środowisko chmury, w tym wszelkie dane wejściowe i kod użytkownika.

Poniższy film wideo demonstruje niepowodzenie debugowania wierzchołka w Azure Data Lake Tools for Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Program Visual Studio wymaga dwóch następujących aktualizacji do korzystania z tej funkcji: [Microsoft Visual C++ 2015 redystrybucyjny Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) i [uniwersalne środowisko uruchomieniowe języka C dla systemu Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Pobieranie wierzchołka nie powiodło się na komputerze lokalnym

Po otwarciu zadania zakończonego niepowodzeniem w Azure Data Lake Tools for Visual Studio zostanie wyświetlony żółty pasek alertu z szczegółowymi komunikatami o błędach na karcie błąd.

1. Kliknij pozycję **Pobierz** , aby pobrać wszystkie wymagane zasoby i strumienie wejściowe. Jeśli pobieranie nie zostanie ukończone, kliknij przycisk **Ponów próbę**.

2. Po zakończeniu pobierania kliknij przycisk **Otwórz** , aby wygenerować lokalne środowisko debugowania. Nowe rozwiązanie debugowania zostanie otwarte i jeśli masz już otwarte rozwiązanie w programie Visual Studio, upewnij się, że Zapisz i zamknij je przed debugowaniem.

![Azure Data Lake Analytics debugowania U-SQL — wierzchołek pobierania programu Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurowanie środowiska debugowania

> [!NOTE]
> Przed debugowaniem upewnij się, że w oknie Ustawienia wyjątku Sprawdź **wyjątki środowiska uruchomieniowego języka wspólnego** (**CTRL + ALT + E**).

![Azure Data Lake Analytics ustawienia programu Visual Studio dotyczące debugowania U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

W nowym uruchomionym wystąpieniu programu Visual Studio możesz lub nie znaleźć kodu źródłowego zdefiniowanego przez C# użytkownika:

1. [Mogę znaleźć kod źródłowy w rozwiązaniu](#source-code-is-included-in-debugging-solution)

2. [Nie mogę znaleźć kodu źródłowego w rozwiązaniu](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Kod źródłowy jest zawarty w rozwiązaniu debugowania

Istnieją dwa przypadki, w C# których kod źródłowy jest przechwytywany:

1. Kod użytkownika jest zdefiniowany w pliku związanym z kodem (zazwyczaj nazwany `Script.usql.cs` w projekcie U-SQL).

2. Kod użytkownika jest zdefiniowany w C# projekcie biblioteki klas dla aplikacji U-SQL i zarejestrowany jako zestaw z **informacjami o debugowaniu**.

Jeśli kod źródłowy zostanie zaimportowany do rozwiązania, można użyć narzędzi debugowania programu Visual Studio (Obejrzyj, zmienne itp.), aby rozwiązać problem:

1. Naciśnij klawisz **F5** można rozpocząć debugowania. Kod jest uruchamiany, dopóki nie zostanie zatrzymany przez wyjątek.

2. Otwórz plik kodu źródłowego i ustaw punkty przerwania, a następnie naciśnij klawisz **F5** , aby debugować kod krok po kroku.

    ![Azure Data Lake Analytics wyjątek debugowania U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Kod źródłowy nie jest uwzględniony w rozwiązaniu debugowania

Jeśli kod użytkownika nie jest uwzględniony w pliku związanym z kodem lub nie zarejestrowano go przy użyciu **informacji debugowania**, kod źródłowy nie jest automatycznie uwzględniany w rozwiązaniu debugowania. W takim przypadku konieczne jest wykonanie dodatkowych czynności w celu dodania kodu źródłowego:

1. Kliknij prawym przyciskiem myszy **rozwiązanie "VertexDebug" > dodaj > istniejący projekt...** , aby znaleźć kod źródłowy zestawu i dodać projekt do rozwiązania debugowania.

    ![Azure Data Lake Analytics Dodaj projekt do debugowania U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Pobierz ścieżkę folderu projektu dla projektu **FailedVertexDebugHost** . 

3. Kliknij prawym przyciskiem myszy **dodany projekt kodu źródłowego zestawu > właściwości**, wybierz kartę **kompilacja** na lewo i wklej skopiowaną ścieżkę kończącą się na \bin\debug jako **wyjście > Ścieżka wyjściowa**. Końcowa ścieżka wyjściowa jest `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`taka sama.

    ![Ścieżka pliku PDB zestawu Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po wykonaniu tych ustawień Rozpocznij debugowanie za pomocą klawisza **F5** i punktów przerwania. Aby rozwiązać problem, można także użyć narzędzi debugowania programu Visual Studio (oglądania, zmiennych itp.).

> [!NOTE]
> Kompiluj ponownie projekt kodu źródłowego zestawu przy każdej modyfikacji kodu w celu wygenerowania zaktualizowanych plików. pdb.

## <a name="resubmit-the-job"></a>Prześlij ponownie zadanie

Po zakończeniu debugowania, jeśli projekt zakończy się pomyślnie, w oknie danych wyjściowych zostanie wyświetlony następujący komunikat:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics debugowanie U-SQL zakończyło się pomyślnie](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Aby ponownie przesłać zadanie zakończone niepowodzeniem:

1. W przypadku zadań z rozwiązaniami związanymi z kodem C# Skopiuj kod do pliku źródłowego związanego z kodem ( `Script.usql.cs`zazwyczaj).

2. W przypadku zadań z zestawami kliknij prawym przyciskiem myszy projekt kodu źródłowego zestawu w debugowanym rozwiązaniu i zarejestruj zaktualizowane zestawy dll w katalogu Azure Data Lake.

3. Prześlij ponownie zadanie U-SQL.

## <a name="next-steps"></a>Następne kroki

- [Przewodnik programowania U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Tworzenie zdefiniowanych przez użytkownika operatorów U-SQL dla zadań Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK U-SQL usługi Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Jak rozwiązywać problemy z nietypowym zadaniem cyklicznym](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
