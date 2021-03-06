---
title: Uzyskaj więcej elementów lub rekordów z podziałem na strony
description: Skonfiguruj stronicowanie, aby przekroczyć domyślny limit rozmiaru strony dla akcji łącznika w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792108"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Uzyskaj więcej danych, elementów lub rekordów przy użyciu stronicowania w Azure Logic Apps

Podczas pobierania danych, elementów lub rekordów przy użyciu akcji łącznika w [Azure Logic Apps](../logic-apps/logic-apps-overview.md)można uzyskać zestawy wyników tak duże, że akcja nie zwróci wszystkich wyników w tym samym czasie. W przypadku niektórych akcji liczba wyników może przekroczyć domyślny rozmiar strony łącznika. W takim przypadku akcja zwraca tylko pierwszą stronę wyników. Na przykład domyślny rozmiar strony dla akcji **Pobierz wiersze** łącznika SQL Server to 2048, ale może się różnić w zależności od innych ustawień.

Niektóre akcje umożliwiają włączenie ustawienia *stronicowania* , dzięki czemu aplikacja logiki może pobrać więcej wyników do limitu stronicowania, ale zwrócić te wyniki jako pojedynczy komunikat po zakończeniu działania. W przypadku korzystania z stronicowania należy określić wartość *progową* , która jest docelowym numerem wyników, które ma zwrócić akcja. Akcja pobiera wyniki do osiągnięcia określonego progu. Gdy łączna liczba elementów jest mniejsza niż określona wartość progowa, Akcja pobiera wszystkie wyniki.

Włączenie ustawienia stronicowania pobiera strony wyników na podstawie rozmiaru strony łącznika. Takie zachowanie oznacza, że czasami można uzyskać więcej wyników niż określony próg. Na przykład podczas korzystania z akcji SQL Server **Pobierz wiersze** , która obsługuje ustawienia stronicowania:

* Domyślny rozmiar strony akcji to 2048 rekordów na stronę.
* Załóżmy, że masz 10 000 rekordów i jako minimum określisz 5000 rekordów.
* Stronicowanie pobiera strony rekordów, aby uzyskać co najmniej określone minimum, Akcja zwraca 6144 rekordów (3 strony x 2048 rekordów), nie 5000 rekordów.

Poniżej znajduje się lista zawierająca tylko niektóre łączniki, w których można przekroczyć domyślny rozmiar strony dla określonych akcji:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Baza danych Oracle](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki i akcja, w której chcesz włączyć podział na strony. Jeśli nie masz aplikacji logiki, zobacz [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Włącz podział na strony

Aby określić, czy akcja obsługuje stronicowanie w Projektancie aplikacji logiki, sprawdź ustawienia akcji dla ustawienia podziału na **strony** . Ten przykład pokazuje, jak włączyć podział na strony w akcji **Pobierz wiersze** SQL Server.

1. W prawym górnym rogu akcji wybierz przycisk wielokropka ( **...** ), a następnie wybierz pozycję **Ustawienia**.

   ![Otwórz ustawienia akcji](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Jeśli akcja obsługuje podział na strony, Akcja pokazuje ustawienie **podział na strony** .

1. Zmień ustawienie **stronicowania** z **wyłączone** na **włączone**. We właściwości **próg** Określ wartość całkowitą dla docelowej liczby wyników, które akcja ma zwrócić.

   ![Określ minimalną liczbę wyników do zwrócenia](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **gotowe**.

## <a name="workflow-definition---pagination"></a>Definicja przepływu pracy — podział na strony

Po włączeniu dzielenia na strony dla akcji, która obsługuje tę możliwość, definicja przepływu pracy aplikacji logiki zawiera właściwość `"paginationPolicy"` wraz z właściwością `"minimumItemCount"` we właściwości `"runtimeConfiguration"` tej akcji, na przykład:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Uzyskaj pomoc techniczną

Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
