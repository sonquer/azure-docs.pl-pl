---
title: Połącz z Oracle Database
description: Wstawianie rekordów i zarządzanie nimi za pomocą Oracle Database interfejsów API REST i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789430"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Wprowadzenie do łącznika Oracle Database

Korzystając z łącznika Oracle Database, tworzysz przepływy pracy organizacyjne, które korzystają z danych w istniejącej bazie danych. Ten łącznik może nawiązać połączenie z lokalną Oracle Database lub maszyną wirtualną platformy Azure z zainstalowaną Oracle Database. Za pomocą tego łącznika możesz:

* Kompiluj swój przepływ pracy, dodając nowego klienta do bazy danych klientów lub aktualizując zamówienie w bazie danych zamówień.
* Użyj akcji, aby uzyskać wiersz danych, Wstaw nowy wiersz, a nawet Usuń. Na przykład po utworzeniu rekordu w usłudze Dynamics CRM Online (wyzwalacz), a następnie wstawieniu wiersza w Oracle Database (Akcja). 

W tym artykule pokazano, jak używać łącznika Oracle Database w aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Obsługiwane wersje programu Oracle: 
    * Oracle 9 i nowsze
    * Oprogramowanie klienckie Oracle 8.1.7 i nowsze

* Zainstaluj lokalną bramę danych. [Łączenie z danymi lokalnymi z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md) zawiera listę kroków. Brama jest wymagana do nawiązania połączenia z lokalną Oracle Database lub z maszyną wirtualną platformy Azure z zainstalowaną Oracle DB. 

    > [!NOTE]
    > Lokalna Brama danych działa jako most i zapewnia bezpieczny transfer danych między lokalnymi danymi (danymi, które nie znajdują się w chmurze) i aplikacjami logiki. Ta sama Brama może być używana z wieloma usługami i wieloma źródłami danych. W związku z tym wystarczy zainstalować bramę tylko raz.

* Zainstaluj klienta Oracle na komputerze, na którym zainstalowano lokalną bramę danych. Pamiętaj, aby zainstalować 64-bitową Dostawca danych Oracle dla programu .NET z bazy danych Oracle:  

  [64-bit ODAC 12c release 4 (12.1.0.2.4) dla systemu Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Jeśli klient Oracle nie jest zainstalowany, podczas próby utworzenia lub użycia połączenia wystąpi błąd. Zobacz typowe błędy w tym artykule.


## <a name="add-the-connector"></a>Dodawanie łącznika

> [!IMPORTANT]
> Ten łącznik nie ma żadnych wyzwalaczy. Ma tylko akcje. Dlatego podczas tworzenia aplikacji logiki należy dodać kolejny wyzwalacz, aby uruchomić aplikację logiki, taką jak **harmonogram-cykl**lub **żądanie/odpowiedź-odpowiedź**. 

1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki.

2. Na początku aplikacji logiki Wybierz wyzwalacz **żądanie/odpowiedź-żądanie** : 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Wybierz pozycję **Zapisz**. Podczas zapisywania zostanie automatycznie wygenerowany adres URL żądania. 

4. Wybierz pozycję **nowy krok**, a następnie wybierz pozycję **Dodaj akcję**. Wpisz `oracle`, aby wyświetlić dostępne akcje: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Jest to również najszybszy sposób wyświetlania wyzwalaczy i akcji dostępnych dla każdego łącznika. Wpisz część nazwy łącznika, na przykład `oracle`. Projektant wyświetla wszystkie wyzwalacze i wszystkie akcje. 

5. Wybierz jedną z akcji, na przykład **Oracle Database — Pobierz wiersz**. Wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**. Wprowadź nazwę serwera Oracle, metodę uwierzytelniania, nazwę użytkownika, hasło, a następnie wybierz bramę:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po nawiązaniu połączenia wybierz tabelę z listy, a następnie wprowadź identyfikator wiersza w tabeli. Musisz znać identyfikator tabeli. Jeśli nie wiesz, skontaktuj się z administratorem Oracle DB i Pobierz dane wyjściowe z `select * from yourTableName`. Zapewnia to informacje, które należy wykonać.

    W poniższym przykładzie dane zadania są zwracane z bazy danych kadr: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. W tym następnym kroku można użyć dowolnego z innych łączników do skompilowania przepływu pracy. Jeśli chcesz przetestować pobieranie danych z programu Oracle, Wyślij do siebie wiadomość e-mail z danymi z programu Oracle przy użyciu jednego z łączników wysyłania wiadomości e-mail, takiego jak Office 365 lub gmail. Użyj tokenów dynamicznych z tabeli Oracle, aby utworzyć `Subject` i `Body` wiadomości e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Zapisz** aplikację logiki, a następnie wybierz pozycję **Uruchom**. Zamknij projektanta i przejrzyj historię uruchamiania dla stanu. Jeśli to się nie powiedzie, wybierz wiersz komunikatu z błędem. Zostanie otwarty projektant, który pokazuje, który krok nie powiódł się, a także informacje o błędzie. Jeśli się powiedzie, należy odebrać wiadomość e-mail z dodanymi informacjami.


### <a name="workflow-ideas"></a>Pomysły dotyczące przepływu pracy

* Chcesz monitorować #oraclego i umieścić tweety w bazie danych, aby można było wykonywać zapytania i używać ich w innych aplikacjach. W aplikacji logiki Dodaj wyzwalacz `Twitter - When a new tweet is posted` i wprowadź **#oracle** hasztagów. Następnie Dodaj akcję `Oracle Database - Insert row` i wybierz tabelę:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Komunikaty są wysyłane do kolejki Service Bus. Chcesz uzyskać te komunikaty i umieścić je w bazie danych. W aplikacji logiki Dodaj wyzwalacz `Service Bus - when a message is received in a queue` i wybierz kolejkę. Następnie Dodaj akcję `Oracle Database - Insert row` i wybierz tabelę:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Typowe błędy

#### <a name="error-cannot-reach-the-gateway"></a>**Błąd**: nie można nawiązać połączenia z bramą

**Przyczyna**: lokalna Brama danych nie może nawiązać połączenia z chmurą. 

Środki **zaradcze**: Upewnij się, że brama jest uruchomiona na komputerze lokalnym, na którym został zainstalowany, i czy może nawiązać połączenie z Internetem.  Nie zaleca się instalowania bramy na komputerze, który może zostać wyłączony lub uśpiony. Możesz również ponownie uruchomić usługę lokalna Brama danych (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Błąd**: używany dostawca jest przestarzały: element "System. Data. OracleClient wymaga oprogramowania klienckiego Oracle w wersji 8.1.7 lub nowszej.". Zobacz [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) , aby zainstalować oficjalnego dostawcę.

**Przyczyna**: zestaw SDK klienta Oracle nie jest zainstalowany na komputerze, na którym jest uruchomiona lokalna Brama danych.  

**Rozwiązanie**: Pobierz i Zainstaluj zestaw SDK klienta Oracle na tym samym komputerze, na którym znajduje się lokalna Brama danych.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Błąd**: tabela "[TableName]" nie definiuje żadnych kolumn klucza

**Przyczyna**: tabela nie ma klucza podstawowego.  

**Rozwiązanie**: Łącznik Oracle Database wymaga użycia tabeli z kolumną klucza podstawowego.

#### <a name="currently-not-supported"></a>Obecnie nieobsługiwane

* Widoki 
* Dowolna tabela z kluczami złożonymi
* Zagnieżdżone typy obiektów w tabelach
 
## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/oracle/). 

## <a name="get-some-help"></a>Uzyskaj pomoc

[Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) jest doskonałym miejscem, w którym można zadawać pytania, odpowiadać na nie i dowiedzieć się, co inni użytkownicy Logic Apps robią. 

Możesz pomóc ulepszyć Logic Apps i łączniki, głosując i przesyłając swoje pomysły w [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Następne kroki
[Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)i Eksploruj dostępne łączniki w Logic Apps na [liście interfejsów API](apis-list.md).
