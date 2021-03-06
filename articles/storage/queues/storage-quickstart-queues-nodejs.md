---
title: 'Szybki Start: Biblioteka usługi Azure queue storage V12 — JavaScript'
description: Dowiedz się, jak utworzyć kolejkę i dodać do niej komunikaty przy użyciu biblioteki V12 w usłudze Azure Queue. Następnie dowiesz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 9f666376020d36bd57b2cc9e78a0704a8ec47b4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473122"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Szybki Start: Biblioteka kliencka usługi Azure queue storage V12 dla języka JavaScript

Rozpocznij pracę z biblioteką klienta usługi Azure queue storage w wersji 12 dla języka JavaScript. Azure queue storage to usługa służąca do przechowywania dużej liczby komunikatów do późniejszego pobrania i przetworzenia. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta usługi Azure queue storage V12 dla języka JavaScript, aby:

* Tworzenie kolejki
* Dodawanie komunikatów do kolejki
* Wgląd w wiadomości w kolejce
* Aktualizowanie komunikatu w kolejce
* Odbieranie komunikatów z kolejki
* Usuwanie komunikatów z kolejki
* Usuwanie kolejki

[Dokumentacja interfejsu API odwołująca](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) się do [kodu źródłowego | biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [pakietu (Menedżer pakietów Node)](https://www.npmjs.com/package/@azure/storage-queue) | [przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Bieżący [Node. js](https://nodejs.org/en/download/) dla Twojego systemu operacyjnego.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z biblioteką klienta usługi Azure queue storage V12 for JavaScript.

### <a name="create-the-project"></a>Tworzenie projektu

Tworzenie aplikacji node. js o nazwie *Queues — szybki start-V12*.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla projektu.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Przejdź do nowo utworzonych *kolejek — szybki start-V12* .

    ```console
    cd queues-quickstart-v12
    ```

1. Utwórz nowy plik tekstowy o nazwie *Package. JSON*. Ten plik definiuje projekt node. js. Zapisz ten plik w katalogach *Queues-szybki start-V12* . Poniżej znajduje się zawartość pliku:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Możesz wprowadzić własną nazwę w polu `author`, jeśli chcesz.

### <a name="install-the-package"></a>Zainstaluj pakiet

Nadal w katalogu *Queues-Start-V12* Zainstaluj bibliotekę klienta usługi Azure queue storage dla pakietu JavaScript za pomocą polecenia `npm install`.

```console
npm install
```

 To polecenie odczytuje plik *Package. JSON* i instaluje bibliotekę klienta usługi Azure queue storage V12 dla języka JavaScript i wszystkie biblioteki, od których jest zależna.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz inny nowy plik tekstowy w edytorze kodu
1. Dodawanie wywołań `require` do ładowania modułów platformy Azure i środowiska Node. js
1. Utwórz strukturę dla programu, w tym bardzo podstawową obsługę wyjątków

    Oto kod:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Zapisz nowy plik jako *Queues-QuickStart-V12. js* w katalogu *Queues-Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego. Magazyn kolejek oferuje trzy typy zasobów:

* Konto magazynu
* Kolejka na koncie magazynu
* Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejki](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas języka JavaScript do korzystania z tych zasobów:

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient): Klasa `QueueClient` umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz manipulowanie nimi.
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage): Klasa `QueueMessage` reprezentuje poszczególne obiekty zwracane podczas wywoływania [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności w bibliotece klienta usługi Azure queue storage dla języka JavaScript:

* [Pobierz parametry połączenia](#get-the-connection-string)
* [Tworzenie kolejki](#create-a-queue)
* [Dodawanie komunikatów do kolejki](#add-messages-to-a-queue)
* [Wgląd w wiadomości w kolejce](#peek-at-messages-in-a-queue)
* [Aktualizowanie komunikatu w kolejce](#update-a-message-in-a-queue)
* [Odbieranie komunikatów z kolejki](#receive-messages-from-a-queue)
* [Usuwanie komunikatów z kolejki](#delete-messages-from-a-queue)
* [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj ten kod wewnątrz funkcji `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Określ nazwę nowej kolejki. Poniższy kod dołącza wartość identyfikatora UUID do nazwy kolejki, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki, a także muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również długość od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazewnictwa kolejek, zobacz [nazywanie kolejek i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Utwórz wystąpienie klasy [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) . Następnie Wywołaj metodę [Create](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) , aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na końcu funkcji `main`:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Dodawanie komunikatów do kolejki

Poniższy fragment kodu dodaje komunikaty do kolejki przez wywołanie metody [SendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) . Zapisuje także [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) zwracane z trzeciego wywołania `sendMessage`. Zwrócony `sendMessageResponse` jest używany do aktualizacji zawartości wiadomości w dalszej części tego programu.

Dodaj ten kod na końcu funkcji `main`:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w wiadomości w kolejce

Wgląd w wiadomości w kolejce przez wywołanie metody [peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) . Metoda `peekMessages` pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

Dodaj ten kod na końcu funkcji `main`:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie komunikatu w kolejce

Zaktualizuj zawartość komunikatu, wywołując metodę [updateMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) . Metoda `updateMessage` może zmienić limit czasu i treść komunikatu. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8, który ma rozmiar do 64 KB. Wraz z nową zawartością należy przekazać `messageId` i `popReceipt` z odpowiedzi zapisanej wcześniej w kodzie. Właściwości `sendMessageResponse` identyfikują, którą wiadomość należy zaktualizować.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Pobierz wcześniej dodane wiadomości, wywołując metodę [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) .  W polu `numberOfMessages` Przekaż maksymalną liczbę komunikatów do odebrania dla tego wywołania.

Dodaj ten kod na końcu funkcji `main`:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie komunikatów z kolejki

Usuwanie wiadomości z kolejki po ich odebraniu i przetworzeniu. W takim przypadku przetwarzanie właśnie wyświetla komunikat w konsoli programu.

Usuń komunikaty, wywołując metodę [deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) . Wszystkie komunikaty, które nie zostały jawnie usunięte, zostaną ostatecznie wyświetlone w kolejce w celu przetworzenia ich przez inną szansę.

Dodaj ten kod na końcu funkcji `main`:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu metody [delete](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) .

Dodaj ten kod na końcu funkcji `main` i Zapisz plik:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera i usuwa je przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu zawierającego plik *Queues-QuickStart-V12. js* , a następnie wykonaj następujące `node` polecenie, aby uruchomić aplikację.

```console
node queues-quickstart-v12.js
```

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Przechodzenie przez kod w debugerze i sprawdzanie [Azure Portal](https://portal.azure.com) w trakcie całego procesu. Sprawdź konto magazynu, aby sprawdzić, czy wiadomości w kolejce zostały utworzone i usunięte.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia kolejki i dodawania do niej komunikatów przy użyciu kodu JavaScript. Następnie nauczysz się wglądu, pobierania i usuwania komunikatów. Na koniec wiesz już, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, szybki start i inne dokumenty można znaleźć w temacie:

> [!div class="nextstepaction"]
> [Dokumentacja platformy Azure dla języka JavaScript](https://docs.microsoft.com/azure/javascript/)

* Aby dowiedzieć się więcej, zobacz [Biblioteka klienta kolejki usługi Azure Storage dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
* Aby wyświetlić więcej przykładowych aplikacji usługi Azure queue storage, przejdź do [biblioteki klienta usługi Azure queue storage V12 przykłady JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
