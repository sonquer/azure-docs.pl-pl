---
title: Splunk Azure Monitor kwerendy dziennika | Microsoft Docs
description: Pomoc dla użytkowników zaznajomionych z usługą Splunk w celu uczenia się zapytań dzienników Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397747"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk Azure Monitor zapytanie dziennika

Ten artykuł ma na celu ułatwienie użytkownikom, którzy znają Splunk, aby poznać język zapytań Kusto w celu pisania zapytań dzienników w Azure Monitor. Bezpośrednie porównania są wykonywane między tymi dwoma, aby zrozumieć kluczowe różnice, a także podobieństwa, w których można korzystać z istniejącej wiedzy.

## <a name="structure-and-concepts"></a>Struktura i pojęcia

W poniższej tabeli porównano koncepcje i struktury danych między Splunk i dziennikami Azure Monitor.

 | Pojęcie  | Splunk | Azure Monitor |  Komentarz
 | --- | --- | --- | ---
 | Jednostka wdrożenia  | cluster |  cluster |  Azure Monitor zezwala na dowolne zapytania między klastrami. Splunk nie. |
 | Pamięć podręczna danych |  zasobników  |  Zasady pamięci podręcznej i przechowywania |  Kontroluje okres i poziom buforowania danych. To ustawienie ma bezpośredni wpływ na wydajność zapytań i koszt wdrożenia. |
 | Logiczna partycja danych  |  index  |  baza danych  |  Umożliwia logiczne rozdzielenie danych. Obie implementacje zezwalają na złożenie i łączenie między tymi partycjami. |
 | Metadane zdarzeń strukturalnych | ND | table |  Splunk nie ma koncepcji uwidocznionej w języku wyszukiwania metadanych zdarzeń. Dzienniki Azure Monitor mają koncepcję tabeli, która zawiera kolumny. Każde wystąpienie zdarzenia jest zamapowane na wiersz. |
 | Rekord danych | zdarzenie | wiersza |  Tylko zmiana terminologii. |
 | Atrybut rekordu danych | pole |  Kolumny |  W Azure Monitor jest to wstępnie zdefiniowane jako część struktury tabeli. W Splunk każde zdarzenie ma swój własny zestaw pól. |
 | Typy | typu |  typu |  Azure Monitor typy danych są bardziej jawne, ponieważ są ustawione w kolumnach. Obie funkcje umożliwiają dynamiczne działanie z typami danych i w przybliżeniu równoważnym zestawem elementów DataType, w tym obsługi JSON. |
 | Zapytanie i wyszukiwanie  | search | query |  Pojęcia są zasadniczo takie same między Azure Monitor i Splunk. |
 | Czas pozyskiwania zdarzeń | Czas systemowy | ingestion_time () |  W Splunk każde zdarzenie pobiera sygnaturę czasową systemową, która jest indeksowana przez zdarzenie. W Azure Monitor można zdefiniować zasady o nazwie ingestion_time, które ujawniają kolumnę systemową, do której można odwoływać się za pomocą funkcji ingestion_time (). |

## <a name="functions"></a>Functions

Poniższa tabela zawiera funkcje w Azure Monitor, które są równoważne z funkcjami Splunk.

|Splunk | Azure Monitor |Komentarz
|---|---|---
|strcat | strcat()| (1) |
|split  | Split () | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>ToInt — () | (1) |
|prawym górnym<br>dołu |ToUpper ()<br>ToLower ()|(1) |
| replace | Zamień () | (1)<br> Należy również pamiętać, że podczas `replace()` są trzy parametry w obu produktach, parametry są różne. |
| substr — | podciąg () | (1)<br>Należy również zauważyć, że Splunk używa indeksów jednostronicowych. Azure Monitor notatki dotyczące indeksów liczonych od zera. |
| tolower |  ToLower () | (1) |
| toupper | ToUpper () | (1) |
| {1&gt;dopasowanie&lt;1} | dopasowuje wyrażenie regularne |  (2)  |
| regex | dopasowuje wyrażenie regularne | W Splunk, `regex` jest operatorem. W Azure Monitor jest operatorem relacyjnym. |
| searchmatch | == | W Splunk, `searchmatch` umożliwia wyszukiwanie dokładnego ciągu.
| losowe | rand()<br>Rand (n) | Funkcja Splunk zwraca liczbę z przestawu od zera do 2<sup>31</sup>-1. Azure Monitor "zwraca liczbę z zakresu od 0,0 do 1,0 lub w przypadku podanego parametru, między 0 a n-1.
| teraz | now() | (1)
| relative_time | totimespan() | (1)<br>W Azure Monitor Splunk odpowiednik relative_time (datetimeVal, offsetVal) to datetimeVal + ToTimeSpan (offsetVal).<br>Na przykład, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> staje się <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) w Splunk funkcja jest wywoływana z operatorem `eval`. W Azure Monitor jest używany jako część `extend` lub `project`.<br>(2) w Splunk funkcja jest wywoływana z operatorem `eval`. W Azure Monitor może być używana z operatorem `where`.


## <a name="operators"></a>Operatory

Poniższe sekcje zawierają przykłady użycia różnych operatorów między Splunk i Azure Monitor.

> [!NOTE]
> Na potrzeby poniższego przykładu _reguła_ pola Splunk jest mapowana na tabelę w Azure monitor i domyślna sygnatura czasowa Splunk jest mapowana do kolumny Logs Analytics _ingestion_time ()_ .

### <a name="search"></a>Search
W Splunk można pominąć słowo kluczowe `search` i określić ciąg nieujęty w cudzysłów. W Azure Monitor należy uruchomić każde zapytanie z `find`, ciąg bez cudzysłowu jest nazwą kolumny, a wartość wyszukiwania musi być ciągiem ujętym w cudzysłów. 

| |  | |
|:---|:---|:---|
| Splunk | **wyszukiwania** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtr
Zapytania dziennika Azure Monitor rozpoczynają się od tabelarycznego zestawu wyników, w którym filtr. W Splunk filtrowanie jest operacją domyślną w bieżącym indeksie. Można również użyć operatora `where` w Splunk, ale nie jest to zalecane.

| |  | |
|:---|:---|:---|
| Splunk | **wyszukiwania** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **miejscu** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Pobieranie n zdarzeń/wierszy do inspekcji 
Zapytania dziennika Azure Monitor obsługują również `take` jako alias do `limit`. W Splunk, jeśli wyniki są uporządkowane, `head` zwróci pierwsze n wyników. W Azure Monitor, limit nie jest uporządkowany, ale zwraca pierwsze n znalezionych wierszy.

| |  | |
|:---|:---|:---|
| Splunk | **MTP** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **granice** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Pobieranie pierwszych n zdarzeń/wierszy uporządkowanych według pola/kolumny
W przypadku końcowych wyników w Splunk używany `tail`. W Azure Monitor można określić kierunek porządkowania z `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **MTP** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Do góry** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozszerzanie zestawu wyników przy użyciu nowych pól/kolumn
Splunk ma również funkcję `eval`, która nie jest porównywalna z operatorem `eval`. Operator `eval` w Splunk i operator `extend` w Azure Monitor obsługują tylko funkcje skalarne i operatory arytmetyczne.

| |  | |
|:---|:---|:---|
| Splunk | **powiadomienie** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **sunąć** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Zmiana nazwy 
Azure Monitor używa operatora `project-rename`, aby zmienić nazwę pola. `project-rename` zezwala, aby zapytanie korzystało ze wszystkich indeksów wstępnie skompilowanych dla pola. Splunk ma operator `rename`, aby zrobić to samo.

| |  | |
|:---|:---|:---|
| Splunk | **ZmieńNazwę** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projekt — zmiana nazwy** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formatuj wyniki/projekcję
Splunk prawdopodobnie nie ma operatora podobnego do `project-away`. Za pomocą interfejsu użytkownika można odfiltrować pola.

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**projekt — poza** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregacja
Zobacz [agregacje w dzienniku Azure monitor kwerendy](aggregations.md) dla różnych funkcji agregacji.

| |  | |
|:---|:---|:---|
| Splunk | **Statystyki** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Podsumuj** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Join
Sprzężenie w Splunk ma istotne ograniczenia. Podzapytanie ma limit 10000 wyników (ustawiony w pliku konfiguracyjnym wdrożenia) i ograniczoną liczbę typów sprzężenia.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sortuj
W Splunk, aby sortować w kolejności rosnącej, należy użyć operatora `reverse`. Azure Monitor obsługuje także Definiowanie miejsca, w którym należy umieścić wartości null, na początku lub na końcu.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **Porządkuj według** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Rozszerzanie z wieloma wartościami
Jest to podobny operator w Splunk i Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Aspekty wyników, interesujące pola
W Log Analytics w Azure Portal tylko pierwsza kolumna jest widoczna. Wszystkie kolumny są dostępne za pomocą interfejsu API.

| |  | |
|:---|:---|:---|
| Splunk | **pola** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **Facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Cofnij duplikat
Zamiast tego można użyć `summarize arg_min()`, aby odwrócić kolejność wybranego rekordu.

| |  | |
|:---|:---|:---|
| Splunk | **deduplikacji** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **Podsumuj arg_max ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z lekcjami dotyczącymi [pisania zapytań dzienników w Azure monitor](get-started-queries.md).
