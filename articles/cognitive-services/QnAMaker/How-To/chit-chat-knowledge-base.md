---
title: Dodawanie chit czatu do wiedzy usługi QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodawanie osobistych rozmowy chit do bota sprawia, że konwersacji i bardziej angażujące kiedy tworzysz bazę wiedzy. Usługa QnA Maker umożliwia łatwe dodawanie wstępnie wypełnionych zbiór najważniejsze rozmowy chit, do wiedzy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901213"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Dodaj Chit czatu do bazy wiedzy

Dodawanie chit czatu do bota sprawia, że konwersacji i bardziej interesujące. Funkcja chit rozmowy w usługi QnA maker umożliwia łatwe dodawanie wstępnie wypełnionych zbiór najważniejsze rozmowy chit, do bazy wiedzy (KB). Może to być punkt wyjścia do osobowości Twój bot, a jego pozwoli zaoszczędzić czas i pieniądze, zapisywania ich od podstaw.  

Ten zestaw danych zawiera około 100 scenariuszy Chit-Chat w głosowaniu wielu osób, takich jak Professional, friendly i Witty. Wybierz osoby, który najbardziej przypomina Twój bot głosu. Podana kwerenda użytkownika, usługa QnA Maker próbuje dopasować ją z najbliższego QnA czatu internetowego chit znane.  

Poniżej przedstawiono przykłady różnych elementów osobistych. Możesz zobaczyć wszystkie osobowe [zestawy danych](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) wraz ze szczegółami osobistymi.

Dla kwerendy użytkownika `When is your birthday?`każda osobowość ma zainstalowaną odpowiedź:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Osobowości|Przykład|
|--|--|
|Professional|Wiek nie dotyczy mnie.|
|Wyświetlana|Nie mam już wieku.|
|Witty|Jest bezpłatny okres ważności.|
|Caring|Nie mam wieku.|
|R|Jestem bot, więc nie mam wieku.|
||


## <a name="language-support"></a>Obsługa języków

Chit — zestawy danych czatu są obsługiwane w następujących językach:

|Język|
|--|
|Chiński|
|Polski|
|francuski|
|Niemcy|
|Włoski|
|Japoński|
|Koreański|
|Portugalski|
|Hiszpański|


## <a name="add-chit-chat-during-kb-creation"></a>Dodawanie rozmowy chit podczas tworzenia bazy wiedzy
Podczas tworzenia bazy wiedzy knowledge base, po dodaniu usługi źródłowy adres URL i plików jest opcja dodawania chit rozmowy. Wybierz użytkownika, który jako podstawa chit rozmowy. Jeśli nie chcesz dodać chit rozmowy lub jeśli masz już czatu internetowego chit obsługi w źródłach danych wybierz **Brak**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Dodaj Chit czatu do istniejącej bazy wiedzy
Wybierz wiedzy, a następnie przejdź do **ustawienia** strony. Znajduje się link do wszystkich zestawów rozmowy chit danych w odpowiedniej **tsv** formatu. Osobowości, które chcesz pobrać, a następnie przekaż go jako źródło pliku. Upewnij się, że nie Edytuj format lub metadanych przy pobieraniu i przekazać plik. 
  
![Dodaj chit czatu do istniejącej bazy wiedzy](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Edytuj swojej rozmowy chit pytań i odpowiedzi
Podczas edytowania wiedzy, zobaczysz nowe źródło dla chit czatu internetowego, oparte na użytkownika, które wybrano. Teraz można dodać, zmienić pytania lub edytowania odpowiedzi, podobnie jak za pomocą dowolnego innego źródła. 

![Edytuj znacznie chit rozmowy](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Aby wyświetlić metadane, wybierz pozycję **Wyświetl opcje** na pasku narzędzi, a następnie wybierz pozycję **Pokaż metadane**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Dodaj dodatkowe rozmowy chit pytań i odpowiedzi
Możesz dodać nowe rozmowy chit pytań i odpowiedzi, który nie jest wstępnie zdefiniowane zbioru. Upewnij się, że nie są duplikowania parą pytań i odpowiedzi, która jest już omówione w zestawie chit rozmowy. Po dodaniu żadnych nowych pytań i odpowiedzi czatu chit, pobiera ono dodane do Twojego **redakcyjnych** źródła. Aby upewnić się, że program rangi rozumie, że jest to chit-chat, Dodaj parę klucz/wartość metadanych "Redakcja: chitchat", jak pokazano na poniższej ilustracji:
   
![! [Dodaj Chit-Chat bazami] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Usuń chit rozmowy z istniejącej bazy wiedzy
Wybierz wiedzy, a następnie przejdź do **ustawienia** strony. Źródła określonego rozmowy chit znajduje się w pliku o nazwie wybranego użytkownika. Możesz usunąć to, co plik źródłowy.

![Usuń chit rozmowy z bazy wiedzy](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie usługi QnA Maker](../Overview/overview.md)
