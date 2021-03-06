---
title: Wyszukaj w Azure Data Lake Storage Gen2 (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować zawartość i metadane w Azure Data Lake Storage Gen2. Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905663"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indeksowanie dokumentów w Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Obsługa Azure Data Lake Storage Gen2 jest obecnie dostępna w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Możesz zażądać dostępu do wersji zapoznawczych, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.


Podczas konfigurowania konta usługi Azure Storage można włączyć [hierarchiczną przestrzeń nazw](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Dzięki temu zbieranie zawartości na koncie będzie zorganizowane w hierarchii katalogów i podkatalogów zagnieżdżonych. Włączenie hierarchicznej przestrzeni nazw umożliwia włączenie [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

W tym artykule opisano, jak rozpocząć pracę z dokumentami indeksowanymi, które znajdują się w Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Konfigurowanie indeksatora Azure Data Lake Storage Gen2

Istnieje kilka kroków, które należy wykonać, aby zindeksować zawartość z Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Krok 1. Rejestracja w celu uzyskania podglądu

Utwórz konto w wersji zapoznawczej indeksatora Data Lake Storage Gen2, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). Po zaakceptowaniu w wersji zapoznawczej otrzymasz wiadomość e-mail z potwierdzeniem.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Krok 2. wykonanie kroków konfiguracyjnych indeksowania magazynu obiektów blob platformy Azure

Po otrzymaniu potwierdzenia, że rejestrowanie w wersji zapoznawczej zakończyło się pomyślnie, możesz utworzyć potok indeksowania.

Zawartość i metadane można indeksować z Data Lake Storage Gen2 przy użyciu [interfejsu API REST w wersji 2019-05-06-Preview](search-api-preview.md). W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

Indeksowanie zawartości w Data Lake Storage Gen2 jest takie samo jak indeksowanie zawartości w usłudze Azure Blob Storage. Aby zrozumieć, jak skonfigurować Data Lake Storage Gen2 źródło danych, indeks i indeksator, zapoznaj się z [tematem jak indeksować dokumenty w usłudze azure BLOB Storage za pomocą usługi azure wyszukiwanie poznawcze](search-howto-indexing-azure-blob-storage.md). Artykuł magazynu obiektów BLOB zawiera również informacje o obsługiwanych formatach dokumentów, właściwości metadanych obiektów blob, które są wyodrębniane, indeksowanie przyrostowe i inne. Te informacje będą takie same dla Data Lake Storage Gen2.

## <a name="access-control"></a>Kontrola dostępu

Azure Data Lake Storage Gen2 implementuje [model kontroli dostępu](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) , który obsługuje kontrolę dostępu opartą na ROLACH (RBAC) na platformie Azure oraz listę kontroli dostępu (ACL). Podczas indeksowania zawartości z Data Lake Storage Gen2 usługa Azure Wyszukiwanie poznawcze nie wyodrębni z zawartości informacji RBAC i listy ACL. W związku z tym te informacje nie zostaną uwzględnione w indeksie usługi Azure Wyszukiwanie poznawcze.

Jeśli zachowanie kontroli dostępu dla każdego dokumentu w indeksie jest ważne, to Deweloper aplikacji może wdrożyć [przycinanie zabezpieczeń](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Wykrywanie zmian

Indeksator Data Lake Storage Gen2 obsługuje wykrywanie zmian. Oznacza to, że gdy indeksator uruchamia program, tylko ponownie indeksuje zmienione obiekty blob zgodnie z sygnaturą czasową `LastModified` obiektu BLOB.

> [!NOTE] 
> Data Lake Storage Gen2 zezwala na zmianę nazwy katalogów. Po zmianie nazwy katalogu sygnatury czasowe obiektów BLOB w tym katalogu nie zostaną zaktualizowane. W związku z tym indeksator nie będzie ponownie indeksować tych obiektów BLOB. Jeśli obiekty blob w katalogu mają być ponownie indeksowane po zmianie nazwy katalogu, ponieważ teraz mają nowe adresy URL, należy zaktualizować sygnaturę czasową `LastModified` dla wszystkich obiektów BLOB w katalogu, aby indeksator mógł je ponownie indeksować w przyszłości.
