---
title: Wyświetlanie listy obiektów BLOB przy użyciu platformy .NET — Azure Storage
description: Dowiedz się, jak wyświetlić listę obiektów BLOB w kontenerze na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET. Przykłady kodu przedstawiają sposób wyświetlania listy obiektów BLOB w postaci płaskiej listy lub sposobu wyświetlania listy obiektów BLOB hierarchicznie, tak jakby były zorganizowane w katalogi lub foldery.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 3deb3bd0ff66bb31691c4c380595771e3bf2c681
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75691056"
---
# <a name="list-blobs-with-net"></a>Wyświetlanie listy obiektów BLOB przy użyciu platformy .NET

Podczas wyświetlania listy obiektów blob z kodu można określić szereg opcji zarządzania wynikami zwracanymi z usługi Azure Storage. Możesz określić liczbę wyników do zwrócenia w każdym zestawie wyników, a następnie pobrać kolejne zestawy. Można określić prefiks do zwrócenia obiektów blob, których nazwy zaczynają się od znaku lub ciągu. I można wyświetlić listę obiektów BLOB w strukturze płaskiej listy lub hierarchicznie. Hierarchiczna lista zwraca obiekty blob, tak jakby były zorganizowane w folderach. 

W tym artykule przedstawiono sposób wyświetlania listy obiektów BLOB przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-blob-listing-options"></a>Omówienie opcji listy obiektów BLOB

Aby wyświetlić listę obiektów BLOB na koncie magazynu, wywołaj jedną z następujących metod:

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Aby wyświetlić listę obiektów BLOB w kontenerze, wywołaj jedną z następujących metod:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Przeciążenia dla tych metod udostępniają dodatkowe opcje zarządzania sposobem zwracania obiektów BLOB przez operację wyświetlania. Te opcje są opisane w poniższych sekcjach.

### <a name="manage-how-many-results-are-returned"></a>Zarządzanie liczbą zwracanych wyników

Domyślnie operacja tworzenia listy zwraca do 5000 wyników jednocześnie. Aby zwrócić mniejszy zestaw wyników, podaj wartość różną od zera dla `maxresults` parametru podczas wywoływania jednej z metod **ListBlobs** .

Jeśli operacja tworzenia listy zwróci więcej niż 5000 obiektów blob lub jeśli określono wartość dla `maxresults` w taki sposób, że operacja tworzenia listy zwróci podzestaw kontenerów na koncie magazynu, usługa Azure Storage zwraca *token kontynuacji* z listą obiektów BLOB. Token kontynuacji jest wartością nieprzezroczystą, która służy do pobierania następnego zestawu wyników z usługi Azure Storage.

W kodzie Sprawdź wartość tokenu kontynuacji, aby określić, czy ma ona wartość null. Gdy token kontynuacji ma wartość null, zestaw wyników jest zakończony. Jeśli token kontynuacji nie ma wartości null, ponownie wywołaj operację tworzenia listy, przekazując token kontynuacji, aby pobrać następny zestaw wyników, dopóki token kontynuacji nie będzie miał wartości null.

### <a name="filter-results-with-a-prefix"></a>Filtruj wyniki przy użyciu prefiksu

Aby odfiltrować listę kontenerów, Określ ciąg dla parametru `prefix`. Ciąg prefiksu może zawierać jeden lub więcej znaków. Usługa Azure Storage zwraca tylko obiekty blob, których nazwy zaczynają się od tego prefiksu.

### <a name="return-metadata"></a>Metadane zwrotne

Aby zwrócić metadane obiektu BLOB z wynikami, określ wartość **metadanych** dla wyliczenia [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) . Usługa Azure Storage obejmuje metadane z zwróconymi wszystkimi obiektami BLOB, więc nie trzeba wywoływać jednej z metod **FetchAttributes** w tym kontekście, aby pobrać metadane obiektu BLOB.

### <a name="flat-listing-versus-hierarchical-listing"></a>Płaska lista w porównaniu z listą hierarchiczną

Obiekty blob w usłudze Azure Storage są zorganizowane z płaskim modelem, a nie hierarchicznym modelem (na przykład klasycznym systemem plików). Można jednak organizować obiekty blob w *katalogi wirtualne* , aby naśladować strukturę folderów. Katalog wirtualny stanowi część nazwy obiektu BLOB i jest wskazywany przez znak ogranicznika.

Aby zorganizować obiekty blob w katalogi wirtualne, użyj znaku ograniczającego w nazwie obiektu BLOB. Domyślny znak ogranicznika jest ukośnikiem (/), ale można określić dowolny znak jako ogranicznik.

Jeśli nazwy obiektów BLOB są nastawiane przy użyciu ogranicznika, możesz wybrać hierarchie obiektów BLOB hierarchicznie. W przypadku operacji na hierarchicznej liście usługa Azure Storage zwraca wszystkie katalogi wirtualne i obiekty blob poniżej obiektu nadrzędnego. Operację tworzenia listy cyklicznie można wywołać w celu przechodzenia do hierarchii, podobnie jak w przypadku programistycznego przechodzenia do klasycznego systemu plików.

## <a name="use-a-flat-listing"></a>Korzystanie z płaskiej listy

Domyślnie operacja tworzenia listy zwraca obiekty blob w postaci płaskiej listy. W przypadku płaskiej listy obiekty blob nie są zorganizowane według katalogu wirtualnego.

Poniższy przykład wyświetla listę obiektów BLOB w określonym kontenerze przy użyciu płaskiej listy z określonym opcjonalnym rozmiarem segmentu i zapisuje nazwę obiektu BLOB w oknie konsoli.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Przykładowe dane wyjściowe są podobne do:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Korzystanie z listy hierarchicznej

Gdy wywołasz operację tworzenia listy hierarchicznie, usługa Azure Storage zwraca katalogi wirtualne i obiekty blob na pierwszym poziomie hierarchii. Właściwość [prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) każdego katalogu wirtualnego jest ustawiona, aby można było przekazać prefiks w wywołaniu cyklicznym w celu pobrania następnego katalogu.

Aby wyświetlić listę obiektów BLOB hierarchicznie, ustaw parametr `useFlatBlobListing` metody list na **wartość false**.

Poniższy przykład wyświetla listę obiektów BLOB w określonym kontenerze przy użyciu płaskiej listy z określonym opcjonalnym rozmiarem segmentu i zapisuje nazwę obiektu BLOB do okna konsoli.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Przykładowe dane wyjściowe są podobne do:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Nie można wystawić migawek obiektów BLOB w operacji hierarchicznej listy.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Następne kroki

- [Wyświetl listę obiektów BLOB](/rest/api/storageservices/list-blobs)
- [Wyliczanie zasobów obiektów BLOB](/rest/api/storageservices/enumerating-blob-resources)
