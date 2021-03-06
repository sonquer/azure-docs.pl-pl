---
title: Debugowanie Azure Functions programu PowerShell lokalnie
description: Dowiedz się, jak opracowywać funkcje przy użyciu programu PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d15858e1fb535189891303a89b00021027f1e8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227034"
---
# <a name="debug-powershell-azure-functions-locally"></a>Debugowanie Azure Functions programu PowerShell lokalnie

Azure Functions umożliwia tworzenie funkcji jako skryptów programu PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Można debugować funkcje programu PowerShell lokalnie, podobnie jak wszystkie skrypty programu PowerShell, używając następujących standardowych narzędzi programistycznych:

* [Visual Studio Code](https://code.visualstudio.com/): Edytor tekstów bezpłatny, lekki i typu open source firmy Microsoft z rozszerzeniem programu PowerShell, który oferuje pełne środowisko programistyczne programu PowerShell.
* Konsola programu PowerShell: debugowanie przy użyciu tych samych poleceń, które służą do debugowania dowolnego innego procesu programu PowerShell.

[Azure Functions Core Tools](functions-run-local.md) obsługuje lokalne debugowanie Azure Functions, w tym funkcje programu PowerShell.

## <a name="example-function-app"></a>Przykładowa aplikacja funkcji

Aplikacja funkcji używana w tym artykule ma pojedynczą funkcję wyzwalaną przez protokół HTTP i ma następujące pliki:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Ta aplikacja funkcji jest podobna do tej, którą uzyskasz po zakończeniu [przewodnika Szybki Start dla programu PowerShell](functions-create-first-function-powershell.md).

Kod funkcji w `run.ps1` wygląda następująco:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Ustawianie punktu dołączania

Aby debugować każdą funkcję programu PowerShell, funkcja musi zostać zatrzymana w celu dołączenia debugera. `Wait-Debugger` polecenie cmdlet przerywa wykonywanie i czeka na debuger.

Wszystko, co musisz zrobić, to dodanie wywołania polecenia cmdlet `Wait-Debugger` tuż powyżej instrukcji `if`, w następujący sposób:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Debugowanie rozpocznie się w instrukcji `if`. 

Mając `Wait-Debugger` na miejscu, można teraz debugować funkcje przy użyciu obu Visual Studio Code lub konsoli programu PowerShell.

## <a name="debug-in-visual-studio-code"></a>Debugowanie w Visual Studio Code

Aby debugować funkcje programu PowerShell w Visual Studio Code, musisz mieć zainstalowane następujące elementy:

* [Rozszerzenie programu PowerShell dla Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code](functions-create-first-function-vs-code.md)
* [Program PowerShell Core 6,2 lub nowszy](/powershell/scripting/install/installing-powershell-core-on-windows)

Po zainstalowaniu tych zależności Załaduj istniejący projekt funkcji programu PowerShell lub [Utwórz pierwszy projekt funkcji programu PowerShell](functions-create-first-function-powershell.md).

>[!NOTE]
> Jeśli projekt nie ma wymaganych plików konfiguracji, zostanie wyświetlony monit o ich dodanie.

### <a name="set-the-powershell-version"></a>Ustaw wersję programu PowerShell

Instalacja programu PowerShell Core obok siebie przy użyciu programu Windows PowerShell. Ustaw dla programu PowerShell rdzeń jako wersję programu PowerShell, która ma być używana z rozszerzeniem programu PowerShell dla Visual Studio Code.

1. Naciśnij klawisz F1, aby wyświetlić paletę poleceń, a następnie wyszukaj ciąg `Session`.

1. Wybierz polecenie **PowerShell: Pokaż menu sesji**.

1. Jeśli **Bieżąca sesja** nie jest w programie **PowerShell Core 6**, wybierz polecenie **Przełącz do: PowerShell Core 6**.

Gdy plik programu PowerShell jest otwarty, zobaczysz wersję wyświetlaną w kolorze zielonym w prawym dolnym rogu okna. Wybranie tego tekstu spowoduje również wyświetlenie menu sesji. Aby dowiedzieć się więcej, zobacz [Wybieranie wersji programu PowerShell, która ma być używana z rozszerzeniem](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Uruchom aplikację funkcji

Sprawdź, czy `Wait-Debugger` jest ustawiona w funkcji, w której chcesz dołączyć debuger.  Po dodaniu `Wait-Debugger` można debugować aplikację funkcji przy użyciu Visual Studio Code.

Wybierz okienko **debugowanie** , a następnie **Dołącz do funkcji programu PowerShell**.

![Oknie](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Możesz również nacisnąć klawisz F5, aby rozpocząć debugowanie.

Operacja Rozpocznij debugowanie wykonuje następujące zadania:

* Uruchamia `func extensions install` w terminalu, aby zainstalować wszystkie rozszerzenia Azure Functions wymagane przez aplikację funkcji.
* Uruchamia `func host start` w terminalu, aby uruchomić aplikację funkcji na hoście funkcji.
* Dołącz debuger programu PowerShell do obszaru działania programu PowerShell w środowisku uruchomieniowym funkcji.

>[!NOTE]
> Należy upewnić się, że PSWorkerInProcConcurrencyUpperBound jest ustawiona na 1, aby zapewnić poprawne środowisko debugowania w Visual Studio Code. Jest to wartość domyślna.

Gdy aplikacja funkcji jest uruchomiona, potrzebna jest osobna konsola programu PowerShell do wywoływania funkcji wyzwalanej przez protokół HTTP.

W takim przypadku konsola programu PowerShell jest klientem programu. `Invoke-RestMethod` jest używany do wyzwalania funkcji.

W konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Zauważysz, że odpowiedź nie jest natychmiast zwracana. Dzieje się tak, ponieważ `Wait-Debugger` podłączono debugera i wykonanie programu PowerShell zakończyło się w trybie przerwania, jak tylko to możliwe. Jest to spowodowane [koncepcją BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), która została omówiona w dalszej części. Po naciśnięciu przycisku `continue` debuger teraz przerwie się w wierszu bezpośrednio po `Wait-Debugger`.

W tym momencie debuger jest dołączony i można wykonać wszystkie normalne operacje debugera. Aby uzyskać więcej informacji na temat używania debugera w Visual Studio Code, zobacz [oficjalną dokumentację](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Po zakończeniu i pełnym wywołaniu skryptu można zauważyć, że:

* Konsola programu PowerShell, która wykonała `Invoke-RestMethod`, zwróciła wynik
* Zintegrowana konsola programu PowerShell w Visual Studio Code oczekuje na wykonanie skryptu

Później po wywołaniu tej samej funkcji debuger w rozszerzeniu programu PowerShell jest dzielony bezpośrednio po `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Debugowanie w konsoli programu PowerShell

>[!NOTE]
> W tej sekcji założono, że użytkownik przeczytał [Azure Functions Core Tools dokumenty](functions-run-local.md) i wie, jak za pomocą polecenia `func host start` uruchomić aplikację funkcji.

Otwórz konsolę, `cd` do katalogu aplikacji funkcji i uruchom następujące polecenie:

```sh
func host start
```

Po uruchomieniu aplikacji funkcji i `Wait-Debugger` na miejscu możesz dołączyć do procesu. Potrzebujesz dwóch dodatkowych konsol programu PowerShell.

Jedna z konsol działa jako klient. W tym celu należy wywołać `Invoke-RestMethod`, aby wyzwolić funkcję. Na przykład możesz uruchomić następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Zauważ, że nie zwróci odpowiedzi, która jest wynikiem `Wait-Debugger`. Obszar działania programu PowerShell czeka teraz na dołączenie debugera. Przyjrzyjmy się.

W innej konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Get-PSHostProcessInfo
```

To polecenie cmdlet zwraca tabelę, która wygląda jak następujące dane wyjściowe:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Zanotuj `ProcessId` dla elementu w tabeli, używając `ProcessName` jako `dotnet`. Ten proces to aplikacja funkcji.

Następnie uruchom następujący fragment kodu:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Po uruchomieniu debuger przerwie i pokazuje coś, jak następujące dane wyjściowe:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

W tym momencie nastąpi zatrzymanie w punkcie przerwania w [debugerze programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). W tym miejscu można wykonywać wszystkie typowe operacje debugowania, przekroczyć, wkroczć, kontynuować, zakończyć i inne. Aby wyświetlić kompletny zestaw poleceń debugowania dostępnych w konsoli programu, uruchom polecenie `h` lub `?`.

Możesz również ustawić punkty przerwania na tym poziomie za pomocą polecenia cmdlet `Set-PSBreakpoint`.

Gdy będziesz kontynuować i w pełni wywoływać swój skrypt, Zauważ, że:

* Konsola programu PowerShell, w której uruchomiono `Invoke-RestMethod`, teraz zwróci wynik.
* Konsola programu PowerShell, w której uruchomiono `Debug-Runspace`, oczekuje na wykonanie skryptu.

Tę samą funkcję można wywołać ponownie (przy użyciu `Invoke-RestMethod` na przykład), a debuger przerwie się po prawej stronie `Wait-Debugger` polecenie.

## <a name="considerations-for-debugging"></a>Zagadnienia dotyczące debugowania

Podczas debugowania kodu funkcji należy pamiętać o następujących kwestiach.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` może spowodować przerwanie debugera w nieoczekiwanym miejscu

Rozszerzenie programu PowerShell używa `Debug-Runspace`, które z kolei opiera się na funkcji `BreakAll` programu PowerShell. Ta funkcja informuje program PowerShell o zatrzymaniu pierwszego polecenia, które jest wykonywane. Takie zachowanie daje możliwość ustawienia punktów przerwania w debugowanym obszarze działania.

Środowisko uruchomieniowe Azure Functions uruchamia kilka poleceń przed faktycznym wywołaniem skryptu `run.ps1`, dzięki czemu debuger zakończy działanie w `Microsoft.Azure.Functions.PowerShellWorker.psm1` lub `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

W przypadku tej przerwy należy uruchomić polecenie `continue` lub `c`, aby pominąć ten punkt przerwania. Następnie zatrzymasz się w oczekiwanym punkcie przerwania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat opracowywania funkcji przy użyciu programu PowerShell, zobacz [Azure Functions przewodnik dewelopera programu PowerShell](functions-reference-powershell.md).
