---
title: Odczytywanie danych z platformy Azure Event Hubs przechwycone z aplikacji w języku Python (Najnowsza wersja)
description: W tym artykule opisano sposób pisania kodu w języku Python w celu przechwytywania danych wysyłanych do centrum zdarzeń i odczytywania przechwyconych danych zdarzeń z konta usługi Azure Storage.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 34583ef49b2f919391af3fe5700a558b2dc40700
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187244"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Przechwytywanie danych Event Hubs w usłudze Azure Storage i odczytywanie ich przy użyciu języka Python (Azure-eventhub w wersji 5)

Centrum zdarzeń można skonfigurować w taki sposób, aby dane wysyłane do centrum zdarzeń były przechwytywane na koncie usługi Azure Storage lub Azure Data Lake Storage generacji 1 lub Gen 2. W tym artykule opisano sposób pisania kodu w języku Python w celu wysyłania zdarzeń do centrum zdarzeń i odczytywania przechwyconych danych z **usługi Azure Blob Storage**. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Omówienie funkcji przechwytywania Event Hubs](event-hubs-capture-overview.md).

Ten przewodnik Szybki Start używa [zestawu Azure Python SDK](https://azure.microsoft.com/develop/python/) do zademonstrowania funkcji przechwytywania. Aplikacja *Sender.py* wysyła symulowane dane telemetryczne środowiska do centrów zdarzeń w formacie JSON. Centrum zdarzeń jest skonfigurowany do używania funkcji przechwytywania do zapisywania tych danych do magazynu obiektów Blob w partiach. Aplikacja *capturereader.py* odczytuje te obiekty blob i tworzy plik dołączany dla każdego urządzenia. Następnie aplikacja zapisuje dane w plikach CSV.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji 5 zestawu SDK języka Python platformy Azure Event Hubs. Aby zapoznać się z przewodnikiem Szybki Start, który używa wersji 1 zestawu SDK języka Python, zobacz [ten artykuł](event-hubs-capture-python.md). 

W ramach tego przewodnika Szybki start wykonasz następujące czynności: 

> [!div class="checklist"]
> * Utwórz w Azure Portal konto magazynu obiektów blob platformy Azure i kontener.
> * Utwórz przestrzeń nazw Event Hubs przy użyciu Azure Portal.
> * Utwórz centrum zdarzeń z włączoną funkcją przechwytywania i połącz je z kontem magazynu.
> * Wyślij dane do centrum zdarzeń przy użyciu skryptu języka Python.
> * Odczytywanie i przetwarzanie plików z Event Hubs przechwytywania przy użyciu innego skryptu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Narzędzia Python 2,7 i 3,5 lub nowsze z zainstalowanym i zaktualizowanym programem PIP.  
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).  
- Aktywna przestrzeń nazw Event Hubs i centrum zdarzeń.
[Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń w przestrzeni nazw](event-hubs-create.md). Zapisz nazwę Event Hubs przestrzeni nazw, nazwę centrum zdarzeń i podstawowy klucz dostępu dla przestrzeni nazw. Aby uzyskać klucz dostępu, zobacz [pobieranie parametrów połączenia Event Hubs](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Domyślna nazwa klucza to *RootManageSharedAccessKey*. W tym przewodniku szybki start potrzebny będzie tylko klucz podstawowy. Parametry połączenia nie są potrzebne.  
- Konto usługi Azure Storage, kontener obiektów BLOB na koncie magazynu oraz parametry połączenia z kontem magazynu. Jeśli nie masz tych elementów, wykonaj następujące czynności:  
    1. [Utwórz konto usługi Azure Storage](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Tworzenie kontenera obiektów BLOB na koncie magazynu](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Pobierz parametry połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)

    Pamiętaj, aby zarejestrować parametry połączenia i nazwę kontenera do późniejszego użycia w tym przewodniku Szybki Start.  
- Włącz funkcję przechwytywania dla centrum zdarzeń. Aby to zrobić, postępuj zgodnie z instrukcjami w temacie [Włączanie przechwytywania Event Hubs przy użyciu Azure Portal](event-hubs-capture-enable-through-portal.md). Wybierz konto magazynu i kontener obiektów blob, który został utworzony w poprzednim kroku. Funkcję można również włączyć podczas tworzenia centrum zdarzeń.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Utwórz skrypt w języku Python do wysyłania zdarzeń do Centrum zdarzeń
W tej sekcji utworzysz skrypt języka Python, który wysyła zdarzenia 200 (10 urządzeń * 20 zdarzeń) do centrum zdarzeń. Te zdarzenia są przykładowym odczytywaniem w środowisku, który jest wysyłany w formacie JSON. 

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code][Visual Studio Code].
2. Utwórz skrypt o nazwie *Sender.py*. 
3. Wklej poniższy kod do *Sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Zastąp następujące wartości w skryptach:  
    * Zastąp `EVENT HUBS NAMESPACE CONNECTION STRING` parametrami połączenia dla przestrzeni nazw Event Hubs.  
    * Zastąp `EVENT HUB NAME` nazwą centrum zdarzeń.  
5. Uruchom skrypt, aby wysłać zdarzenia do centrum zdarzeń.  
6. W Azure Portal można sprawdzić, czy centrum zdarzeń odebrało komunikaty. Przejdź do widoku **komunikaty** w sekcji **metryki** . Odśwież stronę, aby zaktualizować wykres. Wyświetlanie komunikatów przez stronę może potrwać kilka sekund. 

    [![sprawdzić, czy centrum zdarzeń odebrało komunikaty](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Utworzyć skrypt języka Python, aby odczytywać Twoje pliki przechwytywania
W tym przykładzie przechwycone dane są przechowywane w usłudze Azure Blob Storage. Skrypt w tej sekcji odczytuje przechwycone pliki danych z konta usługi Azure Storage i generuje pliki CSV, aby można je było łatwo otworzyć i wyświetlić. Zobaczysz 10 plików w bieżącym katalogu roboczym aplikacji. Te pliki będą zawierać odczyty środowiska dla 10 urządzeń. 

1. W edytorze języka Python Utwórz skrypt o nazwie *capturereader.py*. Ten skrypt odczytuje przechwycone pliki i tworzy plik dla każdego urządzenia, aby zapisać dane tylko dla tego urządzenia.
2. Wklej poniższy kod do *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Zastąp `AZURE STORAGE CONNECTION STRING` parametrami połączenia dla konta usługi Azure Storage. Nazwa kontenera utworzonego w tym przewodniku szybki start to *przechwytywanie*. Jeśli użyto innej nazwy kontenera, Zastąp *przechwycenie* nazwą kontenera na koncie magazynu. 

## <a name="run-the-scripts"></a>Uruchom skrypty
1. Otwórz wiersz polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety języka Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Zmień katalog na katalog, w którym zapisano *Sender.py* i *capturereader.py*, i uruchom następujące polecenie:
   
   ```
   python sender.py
   ```
   
   To polecenie uruchamia nowy proces języka Python do uruchomienia nadawcy.
3. Poczekaj kilka minut, aż przechwycenie zostanie uruchomione, a następnie wprowadź następujące polecenie w oryginalnym oknie poleceń:
   
   ```
   python capturereader.py
   ```

   Ten procesor przechwytywania używa katalogu lokalnego do pobierania wszystkich obiektów blob z konta magazynu i kontenera. Przetwarza wszystkie te, które nie są puste i zapisuje wyniki jako pliki CSV w katalogu lokalnym.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się [z przykładami w języku Python w witrynie GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
