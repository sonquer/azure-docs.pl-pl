---
title: Wizualizuj dane z usługi Azure Eksplorator danych przy użyciu Grafana
description: W tym artykule dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Grafana, a następnie wizualizować dane z przykładowego klastra.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038008"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Wizualizuj dane z usługi Azure Eksplorator danych w Grafana

Grafana to platforma analityczna, która umożliwia wykonywanie zapytań i wizualizacji danych, a następnie Tworzenie i udostępnianie pulpitów nawigacyjnych na podstawie wizualizacji. Grafana zapewnia *wtyczkę*Eksplorator danych platformy Azure, która umożliwia nawiązywanie połączeń z usługą Azure Eksplorator danych i wizualizowanie danych. W tym artykule dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Grafana, a następnie wizualizować dane z przykładowego klastra.

Skorzystaj z poniższego wideo, aby dowiedzieć się, jak korzystać z wtyczki Eksplorator danych platformy Azure Grafana, skonfigurować platformę Azure Eksplorator danych jako źródło danych dla Grafana, a następnie wizualizować dane. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Alternatywnie możesz [skonfigurować źródło danych](#configure-the-data-source) i [wizualizować dane](#visualize-data) zgodnie z opisem w artykule poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące elementy:

* [Grafana wersja 5.3.0 lub nowszą](https://docs.grafana.org/installation/) dla danego systemu operacyjnego

* [Wtyczka Eksplorator danych platformy Azure dla usługi](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) Grafana

* Klaster zawierający przykładowe dane StormEvents. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](create-cluster-database-portal.md) oraz pozyskiwanie [przykładowych danych do usługi Azure Eksplorator danych](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Określ właściwości i przetestuj połączenie

Przy użyciu jednostki usługi przypisanej do roli *osoby przeglądające* możesz teraz określić właściwości w wystąpieniu Grafana i przetestować połączenie z usługą Azure Eksplorator danych.

1. W Grafana, w menu po lewej stronie wybierz ikonę koła zębatego, a następnie pozycję **źródła danych**.

    ![Źródła danych](media/grafana/data-sources.png)

1. Wybierz pozycję **Dodaj źródło danych**.

1. Na stronie **źródła danych/Nowa** wprowadź nazwę źródła danych, a następnie wybierz typ **Azure Eksplorator danych DataSource**.

    ![Nazwa i typ połączenia](media/grafana/connection-name-type.png)

1. Wprowadź nazwę klastra w postaci https://{ClusterName}. {Region}. Kusto. Windows. NET. Wprowadź inne wartości z Azure Portal lub interfejsu wiersza polecenia. Zapoznaj się z tabelą poniżej poniższej ilustracji.

    ![Connection properties (Właściwości połączenia)](media/grafana/connection-properties.png)

    | Interfejs użytkownika Grafana | Azure Portal | Interfejs wiersza polecenia platformy Azure |
    | --- | --- | --- |
    | Identyfikator subskrypcji | IDENTYFIKATOR SUBSKRYPCJI | SubscriptionId |
    | Identyfikator dzierżawy | Identyfikator katalogu | tenant |
    | Identyfikator klienta | Identyfikator aplikacji | appId |
    | Klucz tajny klienta | Hasło | hasło |
    | | | |

1. Wybierz pozycję **zapisz & test**.

    Jeśli test zakończy się pomyślnie, przejdź do następnej sekcji. Jeśli występują problemy, sprawdź wartości określone w Grafana i Przejrzyj poprzednie kroki.

## <a name="visualize-data"></a>Wizualizowanie danych

Po skonfigurowaniu usługi Azure Eksplorator danych jako źródła danych dla Grafana czas na wizualizację danych. Pokażemy tutaj podstawowy przykład, ale istnieje dużo więcej możliwości. Zaleca się, aby podczas wykonywania [zapytań dotyczących zapisu na platformie Azure Eksplorator danych](write-queries.md) na przykład inne zapytania, które zostaną uruchomione względem przykładowego zestawu danych.

1. W Grafana, w menu po lewej stronie wybierz ikonę znaku plus, a następnie pozycję **pulpit nawigacyjny**.

    ![Utwórz pulpit nawigacyjny](media/grafana/create-dashboard.png)

1. Na karcie **Dodaj** wybierz pozycję **Graph**.

    ![Dodaj wykres](media/grafana/add-graph.png)

1. W panelu Graf wybierz pozycję **Tytuł panelu** , a następnie **Edytuj**.

    ![Panel Edytuj](media/grafana/edit-panel.png)

1. W dolnej części panelu wybierz pozycję **Źródło danych** , a następnie wybierz skonfigurowane źródło danych.

    ![Wybieranie źródła danych](media/grafana/select-data-source.png)

1. W okienku zapytania Skopiuj poniższe zapytanie, a następnie wybierz pozycję **Uruchom**. Zapytanie przedziałuje liczbę zdarzeń dziennie dla przykładowego zestawu danych.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Uruchamianie zapytania](media/grafana/run-query.png)

1. Wykres nie pokazuje żadnych wyników, ponieważ jest domyślnie objęty zakresem danych z ostatnich sześciu godzin. W górnym menu wybierz pozycję **ostatnie 6 godzin**.

    ![Ostatnie sześć godzin](media/grafana/last-six-hours.png)

1. Określ zakres niestandardowy obejmujący 2007, rok uwzględniony w zestawie danych przykładowych StormEvents. Wybierz przycisk **Zastosuj**.

    ![Niestandardowy zakres dat](media/grafana/custom-date-range.png)

    Teraz wykres pokazuje dane z 2007, przedzielonych na dobę.

    ![Ukończony wykres](media/grafana/finished-graph.png)

1. W górnym menu wybierz ikonę Zapisz: ![Ikona zapisywania](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Tworzenie alertów

1. Na stronie głównej pulpitu nawigacyjnego wybierz pozycję **alerty** > **kanały powiadomień** , aby utworzyć nowy kanał powiadomień

    ![Utwórz kanał powiadomień](media/grafana/create-notification-channel.png)

1. Utwórz nowy **kanał powiadomień**, a następnie **Zapisz**.

    ![Utwórz nowy kanał powiadomień](media/grafana/new-notification-channel-adx.png)

1. Na **pulpicie nawigacyjnym**wybierz pozycję **Edytuj** z listy rozwijanej.

    ![Wybieranie opcji Edytuj na pulpicie nawigacyjnym](media/grafana/edit-panel-4-alert.png)

1. Wybierz ikonę dzwonka alertu, aby otworzyć okienko **alertu** . Wybierz pozycję **Utwórz alert**. Wykonaj następujące właściwości w okienku **alertów** .

    ![właściwości alertu](media/grafana/alert-properties.png)

1. Wybierz ikonę **Zapisz pulpit nawigacyjny** , aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)
