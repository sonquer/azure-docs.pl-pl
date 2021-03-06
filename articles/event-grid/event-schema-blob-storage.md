---
title: Schemat zdarzeń magazynu obiektów Blob Azure Event Grid
description: Opisuje właściwości, które są dostarczane dla zdarzeń usługi BLOB Storage z Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 4a71f50a130bd9b22965d39fa942b47c70857a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844483"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schemat zdarzeń Azure Event Grid dla usługi BLOB Storage

Ten artykuł zawiera właściwości i schemat zdarzeń usługi BLOB Storage. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [Źródło zdarzeń magazynu](event-sources.md#storage).

>[!NOTE]
> Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia w wersji 2)** i **BlobStorage** obsługują integrację zdarzeń. **Magazyn (genral cel v1)** nie *obsługuje integracji* z programem Event Grid.

## <a name="list-of-events-for-blob-rest-apis"></a>Lista zdarzeń dla interfejsów API REST obiektów BLOB

Te zdarzenia są wyzwalane, gdy klient tworzy, zastępuje lub usuwa obiekt BLOB przez wywoływanie interfejsów API REST obiektów BLOB.

 |Nazwa zdarzenia |Opis|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Wyzwalane, gdy obiekt BLOB zostanie utworzony lub zastąpiony. <br>To zdarzenie jest wyzwalane, gdy klienci używają operacji `PutBlob`, `PutBlockList`lub `CopyBlob`, które są dostępne w interfejsie API REST usługi BLOB.   |
 |**Microsoft.Storage.BlobDeleted** |Wyzwalane po usunięciu obiektu BLOB. <br>To zdarzenie jest wyzwalane, gdy klienci wywołują operację `DeleteBlob`, która jest dostępna w interfejsie API REST usługi BLOB. |

> [!NOTE]
> Jeśli chcesz się upewnić, że zdarzenie **Microsoft. Storage. BlobCreated** jest wyzwalane tylko wtedy, gdy blokowy obiekt BLOB jest całkowicie zatwierdzony, przefiltruj zdarzenie dla wywołań interfejsu API REST `CopyBlob`, `PutBlob`i `PutBlockList`. Te wywołania interfejsu API wyzwalają zdarzenie **Microsoft. Storage. BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do blokowego obiektu BLOB. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń dla Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista zdarzeń dla Azure Data Lake Storage interfejsu API REST generacji 2

Te zdarzenia są wyzwalane po włączeniu hierarchicznej przestrzeni nazw na koncie magazynu, a klienci wywołują Azure Data Lake Storage Gen2 interfejsy API REST.

|Nazwa zdarzenia|Opis|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Wyzwalane, gdy obiekt BLOB zostanie utworzony lub zastąpiony. <br>To zdarzenie jest wyzwalane, gdy klienci używają `CreateFile` i `FlushWithClose` operacji, które są dostępne w interfejsie API REST Azure Data Lake Storage Gen2. |
|**Microsoft.Storage.BlobDeleted** |Wyzwalane po usunięciu obiektu BLOB. <br>To zdarzenie jest również wyzwalane, gdy klienci wywołują operację `DeleteFile`, która jest dostępna w interfejsie API REST Azure Data Lake Storage Gen2. |
|**Microsoft.Storage.BlobRenamed**|Wyzwalane po zmianie nazwy obiektu BLOB. <br>To zdarzenie jest wyzwalane, gdy klienci używają `RenameFile` operacji dostępnej w interfejsie API REST Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryCreated**|Wyzwalane po utworzeniu katalogu. <br>To zdarzenie jest wyzwalane, gdy klienci używają `CreateDirectory` operacji dostępnej w interfejsie API REST Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryRenamed**|Wyzwalane, gdy zostanie zmieniona nazwa katalogu. <br>To zdarzenie jest wyzwalane, gdy klienci używają `RenameDirectory` operacji dostępnej w interfejsie API REST Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryDeleted**|Wyzwalane po usunięciu katalogu. <br>To zdarzenie jest wyzwalane, gdy klienci używają `DeleteDirectory` operacji dostępnej w interfejsie API REST Azure Data Lake Storage Gen2.|

> [!NOTE]
> Jeśli chcesz się upewnić, że zdarzenie **Microsoft. Storage. BlobCreated** jest wyzwalane tylko wtedy, gdy blokowy obiekt BLOB jest całkowicie zatwierdzony, odfiltruj zdarzenie dla wywołania interfejsu API REST `FlushWithClose`. To wywołanie interfejsu API wyzwala zdarzenie **Microsoft. Storage. BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do blokowego obiektu BLOB. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń dla Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Zawartość odpowiedzi na zdarzenie

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktu końcowego.

Ta sekcja zawiera przykład sposobu, w jaki będą wyglądały dane dla każdego zdarzenia usługi BLOB Storage.

### <a name="microsoftstorageblobcreated-event"></a>Zdarzenie Microsoft. Storage. BlobCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft. Storage. BlobCreated (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów BLOB ma hierarchiczną przestrzeń nazw, dane wyglądają podobnie jak w poprzednim przykładzie, z wyjątkiem tych zmian:

* Klucz `dataVersion` jest ustawiony na wartość `2`.

* Klucz `data.api` jest ustawiony na `CreateFile` ciągu lub `FlushWithClose`.

* Klucz `contentOffset` jest zawarty w zestawie danych.

> [!NOTE]
> Jeśli aplikacje używają `PutBlockList` operacji do przekazania nowego obiektu BLOB do konta, dane nie będą zawierać tych zmian.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft. Storage. BlobDeleted (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów BLOB ma hierarchiczną przestrzeń nazw, dane wyglądają podobnie jak w poprzednim przykładzie, z wyjątkiem tych zmian:

* Klucz `dataVersion` jest ustawiony na wartość `2`.

* Klucz `data.api` jest ustawiony na `DeleteFile`ciągu.

* Klucz `url` zawiera `dfs.core.windows.net`ścieżki.

> [!NOTE]
> Jeśli aplikacje używają `DeleteBlob` operacji do usuwania obiektu BLOB z konta, dane nie będą zawierać tych zmian.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Zdarzenie Microsoft. Storage. BlobRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Zdarzenie Microsoft. Storage. DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Zdarzenie Microsoft. Storage. DirectoryRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Zdarzenie Microsoft. Storage. DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | string | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Event Grid udostępnia tę wartość. |
| subject | string | Ścieżka zdefiniowana przez program Publisher do tematu zdarzenia. |
| eventType | string | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | string | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| id | string | Unikatowy identyfikator zdarzenia. |
| data | obiekt | Dane zdarzenia magazynu obiektów BLOB. |
| dataVersion | string | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | string | Wersja schematu metadanych zdarzenia. Event Grid definiuje schemat właściwości najwyższego poziomu. Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| api | string | Operacja, która wyzwoliła zdarzenie. |
| clientRequestId | string | Identyfikator żądania dostarczonego przez klienta dla operacji interfejsu API magazynu. Tego identyfikatora można użyć do skorelowania dzienników diagnostycznych usługi Azure Storage przy użyciu pola "Client-Request-ID" w dziennikach i można go podać w żądaniach klientów przy użyciu nagłówka "x-MS-Client-Request-ID". Zobacz [format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Identyfikator żądania wygenerowanego przez usługę dla operacji interfejsu API magazynu. Może służyć do skorelowania dzienników diagnostycznych usługi Azure Storage przy użyciu pola "Request-ID-Header" w dziennikach i jest zwracana z inicjowania wywołania interfejsu API w nagłówku "x-MS-Request-ID". Zobacz [format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Element ETag | string | Wartość, której można użyć do warunkowego wykonywania operacji. |
| contentType | string | Typ zawartości określony dla obiektu BLOB. |
| contentLength | liczba całkowita | Rozmiar obiektu BLOB w bajtach. |
| blobType | string | Typ obiektu BLOB. Prawidłowe wartości to "BlockBlob" lub "PageBlob". |
| contentOffset | numer | Przesunięcie w bajtach operacji zapisu wykonane w punkcie, w którym aplikacja wyzwalająca zdarzenie zakończyła zapisywanie do pliku. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw.|
| destinationUrl |string | Adres URL pliku, który będzie istniał po zakończeniu operacji. Na przykład po zmianie nazwy pliku Właściwość `destinationUrl` zawiera adres URL nowej nazwy pliku. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw.|
| sourceUrl |string | Adres URL pliku, który istnieje przed operacją. Na przykład jeśli nazwa pliku zostanie zmieniona, `sourceUrl` zawiera adres URL oryginalnej nazwy pliku przed operacją zmiany nazwy. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw. |
| url | string | Ścieżka do obiektu BLOB. <br>Jeśli klient używa interfejsu API REST usługi BLOB, ten adres URL ma następującą strukturę: *\<Storage-account-name\>. blob.core.windows.net/\<nazwa kontenera\>/\<nazwa pliku\>* . <br>Jeśli klient używa interfejsu API REST Data Lake Storage, ten adres URL ma następującą strukturę: *\<Storage-account-name\>. dfs.core.windows.net/\<File-System-name\>/\<nazwa pliku\>* . |
| recursive | string | `True` wykonać operacji na wszystkich katalogach podrzędnych; w przeciwnym razie `False`. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw. |
| sequencer | string | Nieprzezroczysta wartość ciągu reprezentująca logiczną sekwencję zdarzeń dla każdej konkretnej nazwy obiektu BLOB.  Użytkownicy mogą używać standardowego porównania ciągów, aby zrozumieć względną sekwencję dwóch zdarzeń dla tej samej nazwy obiektu BLOB. |
| storageDiagnostics | obiekt | Dane diagnostyczne są czasami uwzględniane przez usługę Azure Storage. Gdy są obecne, powinny być ignorowane przez odbiorców zdarzeń. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby zapoznać się z wprowadzeniem do pracy ze zdarzeniami usługi BLOB Storage, zobacz [Route BLOB Storage Events — interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
