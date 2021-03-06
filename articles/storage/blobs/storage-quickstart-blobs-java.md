---
title: 'Szybki Start: Biblioteka usługi Azure Blob Storage V12 — Java'
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki klienckiej usługi Azure Blob Storage w wersji 12 dla języka Java do tworzenia kontenera i obiektu BLOB w magazynie obiektów BLOB (Object). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: a76b1c8688a6458dc55a106525c77c5979e2e011
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906528"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Java

W tym przewodniku szybki start nauczysz się zarządzać obiektami BLOB przy użyciu języka Java. Obiekty blob są obiektami, które mogą przechowywać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwalne. Będziesz przekazywać, pobierać i wyświetlać listy obiektów blob, a następnie tworzyć i usuwać kontenery.

[Dokumentacja referencyjna interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) |  | pakietu [ | ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob) [kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) [(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Create a storage account (Tworzenie konta magazynu)](../common/storage-account-create.md).
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) w wersji 8 lub nowszej.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> Aby rozpocząć pracę z poprzednią wersją zestawu SDK, zobacz [Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu Java V8 SDK](storage-quickstart-blobs-java-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z biblioteką klienta usługi Azure Blob Storage V12 for Java.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację Java o nazwie *BLOB-Start-V12*.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj Maven, aby utworzyć nową aplikację konsolową o nazwie *BLOB-szybkiego startu-V12*. Wpisz następujące polecenie **MVN** , aby utworzyć "Hello World!" Projekt Java.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Dane wyjściowe generowania projektu powinny wyglądać następująco:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. W obszarze *obiekt BLOB — szybki start V12* katalog Utwórz inny katalog o nazwie *dane*. Jest to miejsce, w którym są tworzone i przechowywane pliki danych obiektów BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz plik *pliku pom. XML* w edytorze tekstów. Dodaj następujący element zależności do grupy zależności.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Przejdź do katalogu */src/Main/Java/com/Blobs/QuickStart*
1. Otwórz plik *App. Java* w edytorze
1. Usuń instrukcję `System.out.println("Hello world!");`
1. Dodaj dyrektywy `import`

Oto kod:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt BLOB w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blob-introduction/blob1.png)

Użyj następujących klas języka Java do korzystania z tych zasobów:

* [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html): Klasa `BlobServiceClient` umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów BLOB. Konto magazynu zapewnia przestrzeń nazw najwyższego poziomu dla Blob service.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): Klasa `BlobServiceClientBuilder` udostępnia interfejs API programu Fluent Builder, który pomaga ułatwić konfigurację i tworzenie wystąpień `BlobServiceClient` obiektów.
* [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html): Klasa `BlobContainerClient` umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektami BLOB.
* [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html): Klasa `BlobClient` umożliwia manipulowanie obiektami BLOB usługi Azure Storage.
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): Klasa `BlobItem` reprezentuje pojedyncze obiekty blob zwrócone przez wywołanie do `listBlobsFlat`.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej usługi Azure Blob Storage dla języka Java:

* [Pobierz parametry połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów BLOB do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów BLOB w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów BLOB](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj następujący kod w metodzie `Main`:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Tworzenie kontenera

Określ nazwę nowego kontenera. Poniższy kod dołącza wartość identyfikatora UUID do nazwy kontenera, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Następnie Utwórz wystąpienie klasy [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) , a następnie Wywołaj metodę [Create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) , aby utworzyć kontener na koncie magazynu.

Dodaj ten kod na końcu metody `Main`:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów BLOB do kontenera

Następujący fragment kodu:

1. Tworzy plik tekstowy w lokalnym katalogu *danych* .
1. Pobiera odwołanie do obiektu [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) przez wywołanie metody [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) kontenera z sekcji [Create a Container](#create-a-container) .
1. Przekazuje lokalny plik tekstowy do obiektu BLOB, wywołując metodę [uploadFromFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) . Ta metoda tworzy obiekt BLOB, jeśli jeszcze nie istnieje, ale nie zastępuje go, jeśli tak się dzieje.

Dodaj ten kod na końcu metody `Main`:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze, wywołując metodę [listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) . W takim przypadku tylko jeden obiekt BLOB został dodany do kontenera, więc operacja tworzenia listy zwraca tylko jeden obiekt BLOB.

Dodaj ten kod na końcu metody `Main`:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt BLOB, wywołując metodę [downloadToFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) . Przykładowy kod dodaje sufiks "DOWNLOAD" do nazwy pliku, aby można było zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na końcu metody `Main`:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu metody [delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) . Powoduje również usunięcie plików lokalnych utworzonych przez aplikację.

Aplikacja wstrzymuje się do wprowadzania danych przez użytkownika, wywołując `System.console().readLine()` przed usunięciem obiektu BLOB, kontenera i plików lokalnych. Jest to dobry szansa, aby sprawdzić, czy zasoby zostały utworzone prawidłowo, przed ich usunięciem.

Dodaj ten kod na końcu metody `Main`:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w folderze lokalnym i przekazuje go do magazynu obiektów BLOB. Przykład następnie wyświetla listę obiektów BLOB w kontenerze i pobiera plik z nową nazwą, aby można było porównać stare i nowe pliki.

Przejdź do katalogu zawierającego plik *pliku pom. XML* i skompiluj projekt przy użyciu następującego polecenia `mvn`.

```console
mvn compile
```

Następnie Skompiluj pakiet.

```console
mvn package
```

Uruchom następujące polecenie `mvn`, aby uruchomić aplikację.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Przed rozpoczęciem procesu oczyszczania Sprawdź, czy w folderze *danych* są dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter** , aby usunąć pliki testowe i zakończyć demonstrację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób przekazywania, pobierania i wyświetlania listy obiektów BLOB za pomocą języka Java.

Aby wyświetlić przykładowe aplikacje dla magazynu obiektów blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykłady języka Java dla zestawu SDK usługi Azure Blob Storage V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Aby dowiedzieć się więcej, zobacz [zestaw Azure SDK dla języka Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* Samouczki, przykłady, Przewodniki Szybki Start i inne dokumenty można znaleźć [na platformie Azure dla deweloperów chmury Java](/azure/java/).
