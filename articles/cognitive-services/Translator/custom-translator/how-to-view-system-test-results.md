---
title: Wyświetlanie wyników testu systemu i wdrożenia — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Po pomyślnym zakończeniu szkolenia Przejrzyj testy systemowe, aby przeanalizować wyniki szkolenia. Jeśli masz zadowalające wyniki szkolenia, umieść żądanie wdrożenia dla modelu przeszkolonego.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595602"
---
# <a name="view-system-test-results"></a>Wyświetlanie wyników testu systemu

Po pomyślnym zakończeniu szkolenia Przejrzyj testy systemowe, aby przeanalizować wyniki szkolenia. Jeśli masz zadowalające wyniki szkolenia, umieść żądanie wdrożenia dla modelu przeszkolonego.

## <a name="system-test-results-page"></a>Strona wyników testu systemu

Wybierz projekt, a następnie wybierz kartę modele tego projektu, Znajdź model, którego chcesz użyć, a następnie wybierz kartę test.

Karta test pokazuje:

1.  **Wyniki testów systemu:** Wynik procesu testowego w szkoleniach. Proces testowy daje wynik BLEU.

    **Liczba zdań:** Ile zdań równoległych zostało użytych w zestawie testów.

     **BLEU:** Wynik BLEU wygenerowany dla modelu po zakończeniu szkolenia.

    **Stany** Wskazuje, czy proces testowy jest zakończony, czy w toku.

    ![Wyniki testu systemu](media/how-to/how-to-system-test-results.png)

2.  Kliknij wyniki testu systemu i przejdziesz do strony szczegółów wyników testu. Na tej stronie jest wyświetlane tłumaczenie zdań, które były częścią testu zestawu danych.

3.  Tabela na stronie szczegółów wyniku testu ma dwie kolumny — jeden dla każdego języka w parze. Kolumna w języku źródłowym pokazuje zdanie, które ma zostać przetłumaczone. Kolumna języka docelowego zawiera dwa zdania w każdym wierszu.

    **Umieszczone** To zdanie jest translacją referencyjną zdania źródłowego zgodnie z podanym w testowym zestawie danych.

    **ARIAL** To zdanie to automatyczne tłumaczenie zdania źródłowego wykonane przez model utworzony po przeprowadzeniu szkolenia.

    ![Porównanie wyników testu systemu](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Pobierz test

Kliknij link Pobierz tłumaczenia, aby pobrać plik zip. Plik zip zawiera tłumaczenia maszyn źródłowych w zestawie danych testowych.

![Pobierz test](media/how-to/how-to-system-test-download.png)

To pobrane archiwum zip zawiera trzy pliki.

1.  **Custom. Mt. txt:** Ten plik zawiera tłumaczenia maszyn źródłowych w języku docelowym wykonywane przez model przeszkolony z danymi użytkownika.

2.  **ref. txt:** Ten plik zawiera tłumaczenia liter języka źródłowego w języku docelowym.

3.  **source. txt:** Ten plik zawiera zdania w języku źródłowym.

    ![Pobrane wyniki testu systemu](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Wdrażanie modelu

Aby zażądać wdrożenia:

1.  Wybierz projekt, przejdź do pozycji modele karta.

2. W przypadku modelu, który został pomyślnie przeszkolony, wyświetla przycisk "wdróż", jeśli nie został wdrożony.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model.png)

3.  Kliknij pozycję Wdróż.
4.  Wybierz  pozycję wdrożone dla regionów, w których ma zostać wdrożony model, a następnie kliknij przycisk Zapisz. Można wybrać **wdrożenie** dla wielu regionów.

    ![Wdrażanie modelu](media/how-to/how-to-deploy-model-regions.png)

5.  Stan modelu można wyświetlić w kolumnie Stan.

>[!Note]
>Translator niestandardowy obsługuje 10 wdrożonych modeli w obszarze roboczym w dowolnym momencie.

## <a name="update-deployment-settings"></a>Aktualizowanie ustawień wdrożenia

Aby zaktualizować ustawienia wdrożenia:

1.  Wybierz projekt i przejdź do karty **modele** .

2. W przypadku pomyślnie wdrożonego modelu zostanie wyświetlony przycisk **Aktualizuj** .

    ![Wdrażanie modelu](media/how-to/how-to-update-undeploy-model.png)

3.  Wybierz pozycję **Update** (Aktualizuj).
4.  Wybierz  opcję wdrożone lub Niewdrożone w regionach, w których model ma zostać wdrożony lub rozmieszczony, a następnie kliknij przycisk **Zapisz**.

    ![Wdrażanie modelu](media/how-to/how-to-undeploy-model.png)

>[!Note]
>W przypadku wybrania opcji Niewdrożone dla wszystkich regionów model zostanie wdrożony ze wszystkich regionów i umieszczony w stanie niewdrożonym. Jest ona teraz niedostępna do użycia.

## <a name="next-steps"></a>Następne kroki

- Rozpoczynanie korzystania z modelu tłumaczenia niestandardowych wdrożone za pośrednictwem [V3 interfejs API tekstu usługi Translator firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Dowiedz się, [jak zarządzać ustawieniami](how-to-manage-settings.md) w celu udostępniania obszaru roboczego, zarządzania kluczem subskrypcji.
- Dowiedz się [, jak przeprowadzić migrację obszaru roboczego i projektu](how-to-migrate.md) z usługi [Microsoft Translator Hub](https://hub.microsofttranslator.com)
