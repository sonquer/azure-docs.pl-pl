---
title: Importowanie lub eksportowanie danych za pomocą usługi Azure App Configuration | Microsoft Docs
description: Dowiedz się, jak importować lub eksportować dane do lub z konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 64fcc8396fc1b771d0095ee595fd177d7fe99b58
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899514"
---
# <a name="import-or-export-configuration-data"></a>Importowanie lub eksportowanie danych konfiguracji

Konfiguracja aplikacji platformy Azure obsługuje operacje importowania i eksportowania danych. Te operacje służą do pracy z danymi konfiguracyjnymi zbiorczo i wymiany danych między magazynem konfiguracji aplikacji i projektem kodu. Na przykład można skonfigurować jeden magazyn konfiguracji aplikacji do testowania i drugi dla środowiska produkcyjnego. Następnie można skopiować ustawienia aplikacji między nimi za pośrednictwem pliku, aby nie trzeba było wprowadzać danych dwa razy.

Ten artykuł zawiera Przewodnik dotyczący importowania i eksportowania danych przy użyciu konfiguracji aplikacji.

## <a name="import-data"></a>Importowanie danych

Import przenosi dane konfiguracji do magazynu konfiguracji aplikacji z istniejącego źródła, a nie wprowadza go ręcznie. Funkcja import służy do migrowania danych do magazynu konfiguracji aplikacji lub agregowania danych z wielu źródeł. Konfiguracja aplikacji obsługuje importowanie z pliku JSON, YAML lub właściwości.

Importuj dane przy użyciu [Azure Portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-import.md). W Azure Portal wykonaj następujące kroki:

1. Przejdź do magazynu konfiguracji aplikacji, a następnie wybierz pozycję **Importuj/Eksportuj**.

2. Na karcie **Importowanie** wybierz pozycję **plik konfiguracji** > **usługi źródłowej** .

3. Wybierz opcję dla **typu pliku** > **języka** .

4. Wybierz ikonę **folderu** i przejdź do pliku do zaimportowania.

    ![Importuj plik](./media/import-file.png)

5. Wybierz **separator**i opcjonalnie wprowadź **prefiks** do użycia dla zaimportowanych nazw kluczy.

6. Opcjonalnie możesz wybrać **etykietę**.

7. Wybierz pozycję **Zastosuj** , aby zakończyć importowanie.

    ![Zakończono Importowanie pliku](./media/import-file-complete.png)

## <a name="export-data"></a>Eksportuj dane

Eksportuj dane konfiguracji zapisu przechowywane w konfiguracji aplikacji do innego miejsca docelowego. Użyj funkcji eksportu, na przykład, aby zapisać dane w magazynie konfiguracji aplikacji do pliku, który jest osadzony w kodzie aplikacji podczas wdrażania.

Eksportuj dane przy użyciu [Azure Portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-export.md). W Azure Portal wykonaj następujące kroki:

1. Przejdź do magazynu konfiguracji aplikacji, a następnie wybierz pozycję **Importuj/Eksportuj**.

2. Na karcie **eksport** wybierz opcję docelowy **plik konfiguracji** > **usługi** .

3. Opcjonalnie wprowadź **prefiks** i wybierz **etykietę** oraz punkt w czasie, który mają zostać wyeksportowane.

4. Wybierz >  **separatora**.

5. Wybierz pozycję **Zastosuj** , aby zakończyć eksport.

    ![Zakończono eksportowanie pliku](./media/export-file-complete.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji sieci Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
