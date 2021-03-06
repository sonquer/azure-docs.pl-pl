---
title: Ciągła integracja & ciągłego wdrażania — Azure IoT Edge
description: Skonfiguruj ciągłą integrację i ciągłe wdrażanie — usługi Azure IoT Edge przy użyciu infrastruktury DevOps platformy Azure, Azure potoków
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b99e83a8e71b13183c76321c7076b85a212f021
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510978"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągła integracja i ciągłe wdrażanie w usłudze Azure IoT Edge

Możesz łatwo przyjmować DevOps z aplikacjami Azure IoT Edge przy użyciu wbudowanych zadań Azure IoT Edge w Azure Pipelines. W tym artykule pokazano, jak za pomocą funkcji ciągłej integracji i ciągłego wdrażania Azure Pipelines szybko i Azure IoT Edge wydajniej kompilować, testować i wdrażać aplikacje.

![Diagram — ciągła Integracja i ciągłe dostarczanie gałęzie rozwoju i produkcji](./media/how-to-ci-cd/cd.png)

W tym artykule dowiesz się, jak za pomocą wbudowanych Azure IoT Edge zadań dla Azure Pipelines utworzyć dwa potoki dla rozwiązania IoT Edge. W Azure IoT Edge zadaniach można używać czterech akcji.

* **Obrazy modułów Azure IoT Edge-Build** pobierają kod rozwiązania IoT Edge i kompilują obrazy kontenerów.
* **Obrazy modułów wypychania Azure IoT Edge** wypychania obrazów modułów do określonego rejestru kontenerów.
* **Azure IoT Edge — generowanie manifestu wdrożenia** przyjmuje plik Deployment. Template. JSON i zmienne, a następnie generuje plik manifestu wdrożenia Final IoT Edge.
* **Azure IoT Edge-Deploy do IoT Edge urządzeń** pomaga tworzyć wdrożenia IoT Edge na jednym lub wielu urządzeniach IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium Azure Repos. Jeśli go nie masz, możesz [utworzyć nowe repozytorium Git w projekcie](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Rozwiązanie IoT Edge zostało zatwierdzone i wypchnięte do repozytorium. Jeśli chcesz utworzyć nowe przykładowe rozwiązanie do testowania tego artykułu, postępuj zgodnie z instrukcjami w temacie [programowanie i debugowanie modułów w Visual Studio Code](how-to-vs-code-develop-module.md) lub [opracowywanie i C# debugowanie modułów w programie Visual Studio](how-to-visual-studio-develop-csharp-module.md).

   W tym artykule wystarczy, że jest to folder rozwiązania utworzony przez szablony IoT Edge w Visual Studio Code lub Visual Studio. Nie musisz kompilować, wypchnięciować, wdrażać ani debugować tego kodu przed kontynuowaniem. Te procesy zostaną ustawione w Azure Pipelines.

   Jeśli tworzysz nowe rozwiązanie, najpierw Sklonuj repozytorium lokalnie. Następnie podczas tworzenia rozwiązania można utworzyć je bezpośrednio w folderze repozytorium. W tym miejscu możesz łatwo zatwierdzić i wypchnąć nowe pliki.

* Rejestr kontenerów, w którym można wypchnąć obrazy modułów. Możesz użyć [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub rejestru innej firmy.
* Aktywne [Centrum IoT Hub](../iot-hub/iot-hub-create-through-portal.md) z co najmniej IoT Edge urządzeniami do testowania oddzielnych etapów wdrożenia testowego i produkcyjnego. Możesz skorzystać z artykułów szybkiego startu, aby utworzyć urządzenie IoT Edge w systemie [Linux](quickstart-linux.md) lub [Windows](quickstart.md)

Aby uzyskać więcej informacji o korzystaniu z Azure Repos, zobacz [udostępnianie kodu za pomocą programu Visual Studio i Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurowanie ciągłej integracji

W tej sekcji utworzysz nowy potok kompilacji. Skonfiguruj potok do automatycznego uruchamiania podczas ewidencjonowania wszelkich zmian w przykładowym rozwiązaniu IoT Edge i Opublikuj dzienniki kompilacji.

>[!NOTE]
>W tym artykule jest wykorzystywany projektant wizualny usługi Azure DevOps. Przed wykonaniem kroków opisanych w tej sekcji należy wyłączyć funkcję Podgląd nowego środowiska tworzenia potoku YAML.
>
>1. W usłudze Azure DevOps wybierz ikonę profilu, a następnie wybierz pozycję **funkcje w wersji zapoznawczej**.
>2. Wyłącz **nowe środowisko tworzenia potoku YAML** .
>
>Aby uzyskać więcej informacji, zobacz [Tworzenie potoku kompilacji](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Zaloguj się do organizacji usługi Azure DevOps (**https:\//dev.Azure.com/{Your Organization}/** ) i Otwórz projekt zawierający repozytorium rozwiązań IoT Edge.

   W tym artykule utworzyliśmy repozytorium o nazwie **IoTEdgeRepo**. To repozytorium zawiera **IoTEdgeSolution** , w którym znajduje się kod modułu o nazwie **filtermodule**.

   ![Otwórz projekt DevOps](./media/how-to-ci-cd/init-project.png)

2. Przejdź do Azure Pipelines w projekcie. Otwórz kartę **kompilacje** i wybierz pozycję **Nowy potok**. Lub, jeśli masz już potoki kompilacji, wybierz przycisk **Nowy** . Następnie wybierz kolejno pozycje **Nowy potok kompilacji**.

    ![Tworzenie nowego potoku kompilacji](./media/how-to-ci-cd/add-new-build.png)

3. Postępuj zgodnie z monitami, aby utworzyć potok.

   1. Podaj informacje źródłowe dla nowego potoku kompilacji. Wybierz pozycję **Azure Repos git** jako źródło, a następnie wybierz projekt, repozytorium i gałąź, w której znajduje się kod rozwiązania IoT Edge. Następnie wybierz pozycję **Kontynuuj**.

      ![Wybierz źródło potoku](./media/how-to-ci-cd/pipeline-source.png)

   2. Wybierz **puste zadanie** zamiast szablonu.

      ![Rozpocznij z pustym procesem](./media/how-to-ci-cd/start-with-empty.png)

4. Po utworzeniu potoku nastąpi przejście do edytora potoku. W opisie potoku Wybierz poprawną pulę agentów na podstawie platformy docelowej:

   * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów systemu Linux, wybierz opcję **hostowanych 1604 Ubuntu**

   * Jeśli chcesz skompilować moduły na platformie amd64 dla kontenerów systemu Windows 1809, musisz [skonfigurować samoobsługowy Agent w systemie Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Jeśli chcesz skompilować moduły na platformie arm32v7 lub arm64 dla kontenerów systemu Linux, musisz [skonfigurować agenta samoobsługowego w systemie Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Konfigurowanie puli agentów kompilacji](./media/how-to-ci-cd/configure-env.png)

5. Potok jest wstępnie skonfigurowany przy użyciu zadania o nazwie **zadanie agenta 1**. Wybierz znak plus ( **+** ), aby dodać trzy zadania do zadania: **Azure IoT Edge** dwa razy, **Skopiuj pliki** i **Opublikuj artefakty kompilacji** jeden raz. (Umieść wskaźnik myszy nad nazwą każdego zadania, aby wyświetlić przycisk **Dodaj** ).

   ![Dodawanie Azure IoT Edge zadania](./media/how-to-ci-cd/add-iot-edge-task.png)

   Po dodaniu wszystkich czterech zadań zadanie agenta będzie wyglądać podobnie do poniższego przykładu:

   ![Trzy zadania w potoku kompilacji](./media/how-to-ci-cd/add-tasks.png)

6. Wybierz pierwsze zadanie **Azure IoT Edge** , aby je edytować. To zadanie kompiluje wszystkie moduły w rozwiązaniu z określoną platformą docelową.

   * **Nazwa wyświetlana**: zaakceptuj domyślne **obrazy modułu Azure IoT Edge-Build**.
   * **Akcja**: zaakceptuj domyślne **obrazy modułu kompilacji**.
   * **plik Template. JSON**: Wybierz symbol wielokropka ( **...** ) i przejdź do pliku **Deployment. Template. json** w repozytorium zawierającym IoT Edge rozwiązanie.
   * **Platforma domyślna**: wybierz odpowiednią platformę dla modułów na podstawie docelowego urządzenia IoT Edge.
   * **Zmienne wyjściowe**: zmienne wyjściowe zawierają nazwę odwołania, której można użyć do skonfigurowania ścieżki pliku, w którym zostanie wygenerowany plik wdrożenia. JSON. Ustaw nazwę odwołania na coś do zapamiętania, na przykład **Edge**.

7. Wybierz drugie zadanie **Azure IoT Edge** , aby je edytować. To zadanie powoduje wypchnięcie wszystkich obrazów modułu do rejestru kontenerów, które zostały wybrane.

   * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie.
   * **Akcja**: Użyj listy rozwijanej, aby wybrać **obrazy modułu wypychania**.
   * **Typ rejestru kontenera**: Wybierz typ rejestru kontenerów, który ma być używany do przechowywania obrazów modułu. Formularz zmienia się w zależności od wybranego typu rejestru. W przypadku wybrania **Azure Container Registry**Użyj listy rozwijanej, aby wybrać subskrypcję platformy Azure i nazwę rejestru kontenerów. W przypadku wybrania opcji **ogólne Container Registry**wybierz pozycję **Nowy** , aby utworzyć połączenie usługi rejestru.
   * **plik Template. JSON**: Wybierz symbol wielokropka ( **...** ) i przejdź do pliku **Deployment. Template. json** w repozytorium zawierającym IoT Edge rozwiązanie.
   * **Platforma domyślna**: Wybierz tę samą platformę, która została utworzona przez skompilowane obrazy modułu.

   Jeśli masz wiele rejestry kontenerów do hostowania obrazów modułu, musisz zduplikowane to zadanie, wybierz inny rejestr kontenerów, a następnie użyj **obejścia moduły** w ustawieniach zaawansowanych, aby pominąć obrazów, które nie są w tym określonego rejestru.

8. Wybierz zadanie **Kopiuj pliki** , aby je edytować. To zadanie służy do kopiowania plików do katalogu przemieszczania artefaktu.

   * **Nazwa wyświetlana**: Kopiuj pliki do: folder docelowy.
   * **Zawartość**: Umieść dwa wiersze w tej sekcji `deployment.template.json` i `**/module.json`. Te dwa typy plików są danymi wejściowymi do generowania manifestu wdrażania IoT Edge. Należy skopiować do folderu przemieszczania artefaktu i opublikować go dla potoku wydania.
   * **Folder docelowy**: umieść zmienną `$(Build.ArtifactStagingDirectory)`. Zobacz temat [Tworzenie zmiennych](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) , aby dowiedzieć się więcej o opisie.

9. Wybierz zadanie **Opublikuj artefakty kompilacji** , aby je edytować. Podaj ścieżkę katalogu przemieszczania artefaktu do zadania, aby można było opublikować ścieżkę w potoku wydania.

   * **Nazwa wyświetlana**: publikowanie artefaktu: drop.
   * **Ścieżka do publikowania**: należy umieścić zmienną `$(Build.ArtifactStagingDirectory)`. Zobacz temat [Tworzenie zmiennych](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) , aby dowiedzieć się więcej o opisie.
   * **Nazwa artefaktu**: upuść.
   * **Lokalizacja publikowania artefaktu**: Azure Pipelines.

10. Otwórz kartę **wyzwalacze** i zaznacz pole wyboru **Włącz integrację ciągłą**. Upewnij się, że gałąź z kodem jest dołączony.

    ![Włącz wyzwalacz ciągłej integracji](./media/how-to-ci-cd/configure-trigger.png)

11. Zapisz nowy potok kompilacji przy użyciu przycisku **Zapisz** .

Ten potok jest teraz skonfigurowany do automatycznego uruchamiania podczas wypychania nowego kodu do repozytorium. Ostatnim zadaniem, opublikowanie artefaktów potoku, jest wyzwalanie potoku wydania. Przejdź do następnej sekcji, aby skompilować potok wersji.

## <a name="configure-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania

W tej sekcji utworzysz potok wydania, który jest skonfigurowany do automatycznego uruchamiania, gdy potok kompilacji pospadnie artefakty, a dzienniki wdrażania zostaną wyświetlone w Azure Pipelines.

Utwórz nowy potok i Dodaj nowy etap

1. W **wersji** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki wersji, wybierz przycisk **+ Nowy** i wybierz pozycję **+ Nowy potok wersji**.  

    ![Dodaj potoku tworzenia wersji](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po wyświetleniu monitu o wybranie szablonu wybierz pozycję Rozpocznij od **pustego zadania**.

    ![Uruchom zadanie puste](./media/how-to-ci-cd/start-with-empty-job.png)

3. Nowy potok wydania jest inicjowany z jednym etapem, zwanym **etapem 1**. Zmień nazwę etapu 1 na **dev** i Traktuj ją jako środowisko testowe. Zwykle potoki ciągłego wdrażania mają wiele etapów, w tym **dev**, **Staging** and **prod**. Możesz tworzyć więcej informacji na podstawie DevOps. Zamknij okno Szczegóły etapu po jego zmianie nazwy.

4. Połącz wydanie z artefaktami kompilacji, które są publikowane przez potok kompilacji. Kliknij przycisk **Dodaj** w obszarze artefaktów.

   ![Dodaj artefakty](./media/how-to-ci-cd/add-artifacts.png)  

5. Na **stronie Dodawanie artefaktu**wybierz pozycję **kompilacja**typu źródła. Następnie wybierz projekt i utworzony potok kompilacji. Następnie wybierz pozycję **Dodaj**.

   ![Dodaj artefakt kompilacji](./media/how-to-ci-cd/add-an-artifact.png)

6. Otwórz wyzwalacze artefaktów i wybierz przełącznik, aby włączyć wyzwalacz ciągłego wdrażania. Teraz Nowa wersja zostanie utworzona za każdym razem, gdy zostanie udostępniona nowa kompilacja.

   ![Konfigurowanie wyzwalacza ciągłego wdrażania](./media/how-to-ci-cd/add-a-trigger.png)

7. Etap **dev** jest wstępnie skonfigurowany przy użyciu jednego zadania i zero zadań. Z menu potok wybierz pozycję **zadania** , a następnie wybierz etap **dev** .  Wybierz zadanie i liczbę zadań, aby skonfigurować zadania na tym etapie.

    ![Konfigurowanie zadań deweloperskich](./media/how-to-ci-cd/view-stage-tasks.png)

8. Na etapie **dev** należy zobaczyć domyślne **zadanie agenta**. Można skonfigurować szczegółowe informacje o zadaniu agenta, ale zadanie wdrażania jest zależne od platformy, aby można było użyć **hostowanej program VS2017** lub **hostowanej Ubuntu 1604** w **puli agentów** (lub dowolnego innego agenta zarządzanego przez siebie).

9. Wybierz znak plus ( **+** ), aby dodać dwa zadania. Wyszukaj i Dodaj **Azure IoT Edge** dwa razy.

    ![Dodawanie zadań deweloperskich](./media/how-to-ci-cd/add-task-qa.png)

10. Wybierz pierwsze zadanie **Azure IoT Edge** i skonfiguruj je przy użyciu następujących wartości:

    * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie.
    * **Akcja**: Użyj listy rozwijanej, aby wybrać opcję **Generuj manifest wdrożenia**. Zmiana wartości akcji spowoduje również zaktualizowanie nazwy wyświetlanej zadania tak, aby była zgodna.
    * **plik Template. JSON**: umieść ścieżkę `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. Ścieżka jest publikowana z potoku kompilacji.
    * **Domyślna platforma**: Wybierz tę samą wartość podczas kompilowania obrazów modułów.
    * **Ścieżka wyjściowa**: wprowadź ścieżkę `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. Ta ścieżka to końcowy plik manifestu wdrażania IoT Edge.

    Te konfiguracje ułatwiają zastępowanie adresów URL obrazu modułu w pliku `deployment.template.json`. Funkcja **Generuj manifest wdrożenia** pomaga również zastąpić zmienne dokładnie wartością określoną w pliku `deployment.template.json`. W programie VS/VS Code jest określana wartość rzeczywista w pliku `.env`. W Azure Pipelines ustawiasz wartość na karcie zmienne potoku wydania. Przejdź do karty zmienne i skonfiguruj nazwę i wartość w następujący sposób.

    * **ACR_ADDRESS**: adres Azure Container Registry.
    * **ACR_PASSWORD**: hasło Azure Container Registry.
    * **ACR_USER**: Azure Container Registry nazwy użytkownika.

    Jeśli w projekcie znajdują się inne zmienne, możesz określić nazwę i wartość na tej karcie. W przypadku **wygenerowania manifestu wdrażania** można rozpoznać tylko zmienne, które znajdują się w `${VARIABLE}`, należy upewnić się, że są one używane w plikach `*.template.json`.

    ![Konfigurowanie zmiennych dla potoku wydania](./media/how-to-ci-cd/configure-variables.png)

11. Wybierz drugie zadanie **Azure IoT Edge** i skonfiguruj je przy użyciu następujących wartości:

    * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie.
    * **Akcja**: Użyj listy rozwijanej, aby wybrać opcję **Wdróż do IoT Edge urządzeń**. Zmiana wartości akcji spowoduje również zaktualizowanie nazwy wyświetlanej zadania tak, aby była zgodna.
    * **Subskrypcja platformy Azure**: wybierz subskrypcję zawierającą IoT Hub.
    * **Nazwa IoT Hub**: wybierz Centrum IoT Hub.
    * **Wybierz jedno/wiele urządzeń**: Wybierz, czy potok wydania ma zostać wdrożony na jednym urządzeniu, czy na wielu urządzeniach.
      * W przypadku wdrażania na jednym urządzeniu wprowadź **IoT Edge identyfikator urządzenia**.
      * W przypadku wdrażania na wielu urządzeniach należy określić **warunek docelowy**urządzenia. Warunek docelowy to filtr zgodny z zestawem IoT Edge urządzeń w IoT Hub. Jeśli chcesz użyć znaczników urządzenia jako warunek, należy zaktualizować urządzenie odpowiednie tagi z bliźniaczej reprezentacji urządzenia usługi IoT Hub. Zaktualizuj **IoT Edge identyfikator wdrożenia** i **IoT Edge priorytet wdrożenia** w obszarze Ustawienia zaawansowane. Aby uzyskać więcej informacji na temat tworzenia wdrożenia dla wielu urządzeń, zobacz [opis IoT Edge wdrożeń automatycznych](module-deployment-monitoring.md).
    * Rozwiń pozycję Ustawienia zaawansowane, wybierz pozycję **IoT Edge identyfikator wdrożenia**, umieść `$(System.TeamProject)-$(Release.EnvironmentName)`zmiennej. Mapuje projekt i nazwę wydania na identyfikator wdrożenia IoT Edge.

12. Wybierz pozycję **Zapisz** , aby zapisać zmiany w nowym potoku wydania. Wróć do widoku potoku, wybierając pozycję **potok** z menu.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Sprawdź IoT Edge ciągłej integracji/ciągłego wdrażania z kompilacją a potoki wersji

Aby wyzwolić zadanie kompilacji, możesz wypchnięciu zatwierdzenia do repozytorium kodu źródłowego lub ręcznie wyzwolić ją. W tej sekcji ręcznie wyzwolimy potok ciągłej integracji/ciągłego wdrażania, aby sprawdzić, czy działa. Następnie sprawdź, czy wdrożenie powiodło się.

1. Przejdź do potoku kompilacji utworzonego na początku tego artykułu.

2. Możesz wyzwolić zadanie kompilacji w potoku kompilacji, wybierając przycisk **kolejki** , jak pokazano na poniższym zrzucie ekranu.

    ![Ręczne wyzwalacza](./media/how-to-ci-cd/manual-trigger.png)

3. Wybierz zadanie kompilacji, aby obserwować jego postępy. W przypadku pomyślnego ukończenia potoku kompilacji jest wyzwalane wydanie do etapu **dev** .

    ![Kompilacja dzienników](./media/how-to-ci-cd/build-logs.png)

4. Pomyślne wydanie **deweloperskie** tworzy IoT Edge wdrożenie IoT Edge urządzeń docelowych.

    ![Wydanie do deweloperów](./media/how-to-ci-cd/pending-approval.png)

5. Kliknij pozycję etap **deweloperów** , aby wyświetlić dzienniki wydań.

    ![Dzienniki wydań](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Następne kroki

* Przykład IoT Edge DevOps Best Practices w [projekcie DevOps platformy Azure dla IoT Edge](how-to-devops-project.md)
* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).
