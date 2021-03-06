---
title: Wdrażanie aplikacji w sposób predykcyjny przy użyciu platformy ARM
description: Dowiedz się, jak wdrożyć wiele aplikacji Azure App Service jako pojedynczą jednostkę i w sposób przewidywalny przy użyciu szablonów zarządzania zasobami platformy Azure i skryptów programu PowerShell.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 62d0bf776b2d0c97d95b992ed6a1fd2a356e467a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75967379"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Udostępnianie i wdrażanie mikrousług na platformie Azure
Ten samouczek pokazuje, jak zainicjować i wdrożyć aplikację składającą się z [mikrousług](https://en.wikipedia.org/wiki/Microservices) w [Azure App Service](https://azure.microsoft.com/services/app-service/) jako pojedynczą jednostkę i w sposób przewidywalny przy użyciu szablonów grup zasobów JSON i skryptów programu PowerShell. 

Podczas aprowizacji i wdrażania aplikacji o dużej skali, które składają się z wysoce rozłączonych mikrousług, powtarzalność i przewidywalność są decydujące dla sukcesu. [Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia tworzenie mikrousług obejmujących aplikacje sieci Web, zaplecza mobilne i aplikacje interfejsu API. [Azure Resource Manager](../azure-resource-manager/management/overview.md) umożliwia zarządzanie wszystkimi mikrousługami jako jednostką wraz z zależnościami zasobów, takimi jak baza danych i ustawienia kontroli źródła. Teraz możesz również wdrożyć taką aplikację przy użyciu szablonów JSON i prostego skryptu programu PowerShell. 

## <a name="what-you-will-do"></a>Co należy zrobić
W samouczku zostanie wdrożona aplikacja obejmująca następujące możliwości:

* Dwie App Service aplikacje (tj. dwie mikrousługi)
* SQL Database zaplecza
* Ustawienia aplikacji, parametry połączenia i kontrola źródła
* Application Insights, alerty, ustawienia skalowania automatycznego

## <a name="tools-you-will-use"></a>Narzędzia, które będą używane
W tym samouczku zostaną użyte następujące narzędzia. Ponieważ nie jest to obszerna dyskusja nad narzędziami, chcę przejść do kompleksowego scenariusza i po prostu przedstawić krótkie wprowadzenie do każdego z nich i gdzie można znaleźć więcej informacji na jego temat. 

### <a name="azure-resource-manager-templates-json"></a>Szablony Azure Resource Manager (JSON)
Za każdym razem, gdy tworzysz aplikację w Azure App Service, na przykład Azure Resource Manager używa szablonu JSON do tworzenia całej grupy zasobów z zasobami składników. Złożony szablon z [portalu Azure Marketplace](/azure/marketplace) może obejmować bazę danych, konta magazynu, plan App Service, samą aplikację, reguły alertów, ustawienia aplikacji, ustawienia automatycznego skalowania i wiele innych. wszystkie te szablony są dostępne dla użytkownika za pośrednictwem programu PowerShell. Aby uzyskać więcej informacji na temat szablonów Azure Resource Manager, zobacz [tworzenie Azure Resource Manager szablonów](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Zestaw Azure SDK 2,6 dla programu Visual Studio
Najnowszy zestaw SDK zawiera ulepszenia obsługi szablonów Menedżer zasobów w edytorze JSON. Możesz użyć tej funkcji, aby szybko utworzyć szablon grupy zasobów od podstaw lub otworzyć istniejący szablon JSON (na przykład pobrany szablon galerii) do modyfikacji, wypełnić plik parametrów, a nawet wdrożyć grupę zasobów bezpośrednio z zasobu platformy Azure Rozwiązaniu grupy.

Aby uzyskać więcej informacji, zobacz [zestaw Azure SDK 2,6 dla programu Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 lub nowszy
Począwszy od wersji 0.8.0, instalacja Azure PowerShell obejmuje moduł Azure Resource Manager oprócz modułu platformy Azure. Ten nowy moduł umożliwia tworzenie skryptów wdrożenia grup zasobów.

Aby uzyskać więcej informacji, zobacz [używanie Azure PowerShell z Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure
To [Narzędzie do podglądu](https://resources.azure.com) umożliwia zapoznanie się z definicjami JSON wszystkich grup zasobów w ramach subskrypcji i poszczególnych zasobów. W narzędziu można edytować definicje JSON zasobu, usuwać całą hierarchię zasobów i tworzyć nowe zasoby.  Informacje, które są łatwo dostępne w tym narzędziu, są bardzo przydatne w przypadku tworzenia szablonów, ponieważ pokazują, jakie właściwości należy ustawić dla określonego typu zasobu, prawidłowych wartości itd. Możesz nawet utworzyć grupę zasobów w [witrynie Azure Portal](https://portal.azure.com/), a następnie sprawdzić jej definicje JSON w narzędziu Eksplorator, aby ułatwić templatize grupy zasobów.

### <a name="deploy-to-azure-button"></a>Przycisk Wdróż na platformie Azure
Jeśli używasz usługi GitHub do kontroli źródła, możesz umieścić [przycisk Wdróż na platformie Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) w pliku Readme. MD, który umożliwia interfejs użytkownika wdrożenia z kluczem na platformie Azure. Można to zrobić dla dowolnej prostej aplikacji, która pozwala na wdrożenie całej grupy zasobów przez umieszczenie pliku azuredeploy. JSON w katalogu głównym repozytorium. Ten plik JSON, który zawiera szablon grupy zasobów, będzie używany przez przycisk Wdróż na platformie Azure w celu utworzenia grupy zasobów. Aby zapoznać się z przykładem, zobacz [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) przykład, który będzie używany w tym samouczku.

## <a name="get-the-sample-resource-group-template"></a>Pobierz szablon przykładowej grupy zasobów
Teraz przyjrzyjmy się do niego.

1. Przejdź do przykładu App Service [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .
2. W readme.md kliknij pozycję **Wdróż na platformie Azure**.
3. Nastąpi przekierowanie do witryny [wdrożenia na platformę Azure](https://deploy.azure.com) i poproszenie o wprowadzenie parametrów wdrożenia. Należy zauważyć, że większość pól jest wypełniana nazwą repozytorium i kilka losowych ciągów. Możesz zmienić wszystkie pola, jeśli chcesz, ale jedyną czynnością, którą trzeba wprowadzić, są SQL Server logowania administracyjnego i hasło, a następnie kliknij przycisk **dalej**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Następnie kliknij przycisk **Wdróż** , aby rozpocząć proces wdrażania. Po zakończeniu działania procesu, kliknij link http://todoapp*xxxx*. azurewebsites.NET, aby przejrzeć wdrożoną aplikację. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Interfejs użytkownika może działać nieco wolniej po pierwszym przejściu do niego, ponieważ aplikacje są właśnie uruchamiane, ale przekonasz się, że jest to w pełni funkcjonalna aplikacja.
5. Na stronie Wdrażanie kliknij link **Zarządzaj** , aby wyświetlić nową aplikację w witrynie Azure Portal.
6. Na liście rozwijanej **podstawowe** kliknij link grupy zasobów. Należy również pamiętać, że aplikacja jest już połączona z repozytorium GitHub w ramach **projektu zewnętrznego**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. W bloku Grupa zasobów Zwróć uwagę, że w grupie zasobów znajdują się już dwie aplikacje i jeden SQL Database.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Wszystko, co zostało już przeprowadzone w ciągu kilku krótkich minut, to w pełni wdrożona aplikacja dwuletnia ze wszystkimi składnikami, zależnościami, ustawieniami, bazą danych i publikowaniem ciągłym, skonfigurowanym przez zautomatyzowaną organizację w Azure Resource Manager. Wszystkie te czynności zostały wykonane przez dwie rzeczy:

* Przycisk Wdróż na platformie Azure
* azuredeploy. JSON w katalogu głównym repozytorium

Te same dziesiątki, setki lub tysiące czasu można wdrożyć za każdym razem. Powtarzalność i przewidywalność tego podejścia umożliwiają łatwe i bezpieczne wdrażanie aplikacji o dużej skali.

## <a name="examine-or-edit-azuredeployjson"></a>Badaj (lub Edytuj) AZUREDEPLOY. KODU
Teraz przyjrzyjmy się konfigurowaniu repozytorium GitHub. Będziesz używać edytora JSON w zestawie SDK platformy Azure, więc jeśli jeszcze nie zainstalowano [zestawu Azure .NET sdk 2,6](https://azure.microsoft.com/downloads/), zrób to teraz.

1. Sklonuj repozytorium [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) przy użyciu ulubionego narzędzia Git. Na poniższym zrzucie ekranu robię to w Team Explorer w Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. W katalogu głównym repozytorium Otwórz plik azuredeploy. JSON w programie Visual Studio. Jeśli nie widzisz okienka konspektu JSON, musisz zainstalować zestaw Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nie mogę opisać wszystkich szczegółów w formacie JSON, ale sekcja [więcej zasobów](#resources) zawiera linki do uczenia się w języku szablonów grupy zasobów. W tym miejscu chcę pokazać interesujące funkcje, które mogą pomóc Ci rozpocząć tworzenie własnego szablonu niestandardowego dla wdrożenia aplikacji.

### <a name="parameters"></a>Parametry
Zapoznaj się z sekcją parametrów, aby zobaczyć, że w przypadku większości tych parametrów przycisk **Wdróż na platformie Azure** monituje o wprowadzenie. Witryna za przyciskiem **Wdróż na platformie Azure** wypełnia wejściowy interfejs użytkownika przy użyciu parametrów zdefiniowanych w azuredeploy. JSON. Te parametry są używane w ramach definicji zasobów, takich jak nazwy zasobów, wartości właściwości itp.

### <a name="resources"></a>Zasoby
W węźle zasoby można zobaczyć, że są zdefiniowane 4 zasoby najwyższego poziomu, w tym wystąpienie SQL Server, plan App Service i dwie aplikacje. 

#### <a name="app-service-plan"></a>Plan usługi App Service
Zacznijmy od prostego zasobu poziomu głównego w formacie JSON. W konspekcie JSON kliknij plan App Service o nazwie **[hostingPlanName]** , aby wyróżnić odpowiedni kod JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Należy zauważyć, że `type` element określa ciąg dla planu App Service (jest on nazywany farmą serwerów o długim, długim czasie temu), a inne elementy i właściwości są wypełniane przy użyciu parametrów zdefiniowanych w pliku JSON, a ten zasób nie zawiera żadnych zasobów zagnieżdżonych.

> [!NOTE]
> Należy zauważyć, że wartość `apiVersion` informuje platformę Azure, która wersja interfejsu API REST używa definicji zasobu JSON z i może mieć wpływ na sposób formatowania zasobu wewnątrz `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Następnie kliknij zasób SQL Server o nazwie **SqlServer** w konspekcie JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Zwróć uwagę na następujące informacje o wyróżnionym kodzie JSON:

* Użycie parametrów zapewnia, że utworzone zasoby mają nazwy i są skonfigurowane w taki sposób, aby były spójne ze sobą.
* Zasób programu SQLServer ma dwa zagnieżdżone zasoby, każda z nich ma inną wartość `type`.
* Zagnieżdżone zasoby wewnątrz `“resources”: […]`, w których zdefiniowano bazę danych i reguły zapory, mają element `dependsOn`, który określa identyfikator zasobu na poziomie głównym zasobu SQLServer. Oznacza to, że Azure Resource Manager, "przed utworzeniem tego zasobu, ten zasób musi już istnieć. a jeśli inny zasób jest zdefiniowany w szablonie, utwórz go najpierw.
  
  > [!NOTE]
  > Aby uzyskać szczegółowe informacje na temat korzystania z funkcji `resourceId()`, zobacz [Azure Resource Manager funkcji szablonu](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* Wynikiem elementu `dependsOn` jest to, że Azure Resource Manager mogą wiedzieć, które zasoby mogą być tworzone równolegle, a zasoby muszą być tworzone sekwencyjnie. 

#### <a name="app-service-app"></a>Aplikacja usługi App Service
Teraz przechodźmy na same rzeczywiste aplikacje, które są bardziej skomplikowane. Kliknij aplikację [zmienne (apiSiteName ')] w konspekcie JSON, aby zaznaczyć swój kod JSON. Zauważysz, że rzeczy są znacznie bardziej interesujące. W tym celu porozmawiamy o tych funkcjach po jednym:

##### <a name="root-resource"></a>Zasób główny
Aplikacja zależy od dwóch różnych zasobów. Oznacza to, że Azure Resource Manager utworzy aplikację dopiero po utworzeniu zarówno planu App Service, jak i wystąpienia SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Ustawienia aplikacji
Ustawienia aplikacji są również zdefiniowane jako zasób zagnieżdżony.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

W `properties` elementu `config/appsettings`są dostępne dwa ustawienia aplikacji w formacie `"<name>" : "<value>"`.

* `PROJECT` to [ustawienie KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , które informuje wdrożenie platformy Azure, który projekt ma być używany w rozwiązaniu z programem Visual Studio. Pokażę później sposób skonfigurowania kontroli źródła, ale ponieważ kod ToDoApp znajduje się w wieloprojektowym rozwiązaniu Visual Studio, to ustawienie jest wymagane.
* `clientUrl` jest po prostu ustawieniem aplikacji, którego używa kod aplikacji.

##### <a name="connection-strings"></a>Parametry połączeń
Parametry połączenia są również zdefiniowane jako zasób zagnieżdżony.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

W `properties` elementu dla `config/connectionstrings`każdy ciąg połączenia jest również definiowany jako para Nazwa: wartość z określonym formatem `"<name>" : {"value": "…", "type": "…"}`. Dla elementu `type` możliwe wartości to `MySql`, `SQLServer`, `SQLAzure`i `Custom`.

> [!TIP]
> Aby uzyskać ostateczną listę typów parametrów połączenia, uruchom następujące polecenie w Azure PowerShell: \[enum]:: GetNames ("Microsoft. WindowsAzure. Commands. Utilities. Web. Services. webentities. DatabaseType")
> 
> 

##### <a name="source-control"></a>Kontrola źródła
Ustawienia kontroli źródła są również zdefiniowane jako zasób zagnieżdżony. Azure Resource Manager używa tego zasobu do konfigurowania ciągłego publikowania (zobacz ostrzeżenia na `IsManualIntegration` później), a także do uruchamiania automatycznego wdrażania kodu aplikacji podczas przetwarzania pliku JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` i `branch` powinny być całkiem intuicyjne i powinny wskazywać na repozytorium Git oraz nazwę gałęzi, z której ma zostać opublikowany. Są one definiowane przez parametry wejściowe. 

Należy pamiętać, że w `dependsOn` element, który oprócz samego zasobu aplikacji, `sourcecontrols/web` również zależy od `config/appsettings` i `config/connectionstrings`. Jest to spowodowane tym, że po skonfigurowaniu `sourcecontrols/web` zostanie automatycznie podjęta próba wdrożenia, skompilowania i uruchomienia kodu aplikacji przez proces wdrażania platformy Azure. W związku z tym Wstawianie tej zależności pomaga upewnić się, że aplikacja ma dostęp do wymaganych ustawień aplikacji i parametrów połączenia przed uruchomieniem kodu aplikacji. 

> [!NOTE]
> Należy również pamiętać, że `IsManualIntegration` jest ustawiona na `true`. Ta właściwość jest niezbędna w tym samouczku, ponieważ nie jest ona własnością repozytorium GitHub i w związku z tym nie może udzielić uprawnienia do platformy Azure w celu skonfigurowania ciągłego publikowania z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (np. wypychania automatycznych aktualizacji repozytorium na platformę Azure). Wartość domyślna `false` dla określonego repozytorium można użyć tylko w przypadku skonfigurowania poświadczeń usługi GitHub właściciela w [Azure Portal](https://portal.azure.com/) przed. Innymi słowy, jeśli wcześniej skonfigurowano kontrolę źródła w serwisie GitHub lub BitBucket dla dowolnej aplikacji w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń użytkownika, platforma Azure zapamiętaje poświadczenia i użyje ich za każdym razem, gdy wdrożono dowolną aplikację z serwisu GitHub lub BitBucket w przyszłości. Jeśli jednak jeszcze tego nie zrobiono, wdrożenie szablonu JSON zakończy się niepowodzeniem, gdy Azure Resource Manager próbuje skonfigurować ustawienia kontroli źródła aplikacji, ponieważ nie może zalogować się do usługi GitHub lub BitBucket przy użyciu poświadczeń właściciela repozytorium.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porównanie szablonu JSON ze wdrożoną grupą zasobów
W tym miejscu możesz przejść przez wszystkie bloki aplikacji w [witrynie Azure Portal](https://portal.azure.com/), ale istnieje inne narzędzie, które jest równie użyteczne. Przejdź do narzędzia [Azure Resource Explorer](https://resources.azure.com) Preview, które zapewnia reprezentację w formacie JSON wszystkich grup zasobów w Twoich subskrypcjach, ponieważ rzeczywiście istnieją w zapleczu platformy Azure. Możesz również zobaczyć, jak hierarchia JSON grupy zasobów na platformie Azure odpowiada hierarchii w pliku szablonu, który jest używany do jego tworzenia.

Na przykład po przejściu do narzędzia [Azure Resource Explorer](https://resources.azure.com) i rozszerzeniu węzłów w Eksploratorze można zobaczyć grupę zasobów i zasoby poziomu głównego, które są zbierane w ramach odpowiednich typów zasobów.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Jeśli przejdziesz do szczegółów aplikacji, powinno być możliwe wyświetlenie szczegółów konfiguracji aplikacji podobnej do poniższego zrzutu ekranu:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Ponownie zagnieżdżone zasoby powinny mieć hierarchię podobną do tych w pliku szablonu JSON i powinny być widoczne ustawienia aplikacji, parametry połączenia itp., odpowiednio odzwierciedlone w okienku JSON. Brak ustawień w tym miejscu może wskazywać na problem z plikiem JSON i może pomóc w rozwiązywaniu problemów z plikiem szablonu JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Wdróż samodzielnie szablon grupy zasobów
Przycisk **Wdróż na platformie Azure** jest świetny, ale umożliwia wdrożenie szablonu grupy zasobów w azuredeploy. JSON tylko wtedy, gdy wypychano już plik azuredeploy. JSON do usługi GitHub. Zestaw Azure .NET SDK udostępnia także narzędzia do wdrażania dowolnego pliku szablonu JSON bezpośrednio z komputera lokalnego. W tym celu wykonaj poniższe kroki:

1. W programie Visual Studio kliknij kolejno pozycje **Plik** > **Nowy** > **Projekt**.
2. Kliknij **przycisk C# Visual** > **Cloud** > **grupie zasobów platformy Azure**, a następnie kliknij przycisk **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. W obszarze **Wybierz szablon platformy Azure**wybierz pozycję **pusty szablon** i kliknij przycisk **OK**.
4. Przeciągnij plik azuredeploy. JSON do folderu **Template** w nowym projekcie.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. W Eksplorator rozwiązań Otwórz skopiowany plik azuredeploy. JSON.
6. Na przykład w celu dodania do pliku JSON standardowych zasobów usługi Application Insights, klikając pozycję **Dodaj zasób**. Jeśli interesuje Cię wdrażanie pliku JSON, przejdź do kroku Wdróż.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Wybierz pozycję **Application Insights dla Web Apps**, upewnij się, że zaznaczono istniejący plan App Service i aplikację, a następnie kliknij przycisk **Dodaj**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Teraz będzie można zobaczyć kilka nowych zasobów, w zależności od zasobów i ich działania, w zależności od planu App Service lub aplikacji. Te zasoby nie są włączane w istniejącej definicji i można je zmienić.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. W konspekcie JSON kliknij pozycję **AppInsights automatyczne skalowanie** , aby zaznaczyć swój kod JSON. Jest to ustawienie skalowania dla planu App Service.
9. W wyróżnionym kodzie JSON Znajdź `location` i `enabled` właściwości i ustaw je tak jak pokazano poniżej.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. W konspekcie JSON kliknij pozycję **CPUHigh appInsights** , aby zaznaczyć swój kod JSON. Jest to alert.
11. Znajdź `location` i `isEnabled` właściwości i ustaw je, tak jak pokazano poniżej. Wykonaj te same inne trzy alerty (purpurowe cebule).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Teraz można przystąpić do wdrożenia. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **wdróż** > **nowe wdrożenie**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Zaloguj się do konta platformy Azure, jeśli jeszcze tego nie zrobiono.
14. Wybierz istniejącą grupę zasobów w ramach subskrypcji lub Utwórz nową, wybierz **azuredeploy. JSON**, a następnie kliknij przycisk **Edytuj parametry**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Teraz będzie można edytować wszystkie parametry zdefiniowane w pliku szablonu w tabeli całkiem. Parametry, które definiują ustawienia domyślne, mają już wartości domyślne, a parametry, które definiują listę dozwolonych wartości, będą wyświetlane jako listy rozwijane.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Wypełnij wszystkie puste parametry i Użyj [adresu repozytorium GitHub do ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) **w ramach ponownego wypełniania**. Następnie kliknij przycisk **Zapisz**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Skalowanie automatyczne to funkcja oferowana w warstwie **standardowa** lub wyższa, a alerty na poziomie planu są oferowane w warstwie **podstawowa** lub wyższej, należy ustawić parametr **SKU** na **Standard** lub **Premium** , aby wyświetlić wszystkie nowe zasoby usługi App Insights.
    > 
    > 
16. Kliknij przycisk **Deploy (Wdróż)** . Jeśli zaznaczono opcję **Zapisz hasła**, hasło zostanie zapisane w pliku parametrów **w postaci zwykłego tekstu**. W przeciwnym razie podczas procesu wdrażania zostanie wyświetlony monit o podanie hasła do bazy danych.

Gotowe. Teraz wystarczy przejść do witryny [Azure Portal](https://portal.azure.com/) i narzędzia [Azure Resource Explorer](https://resources.azure.com) , aby wyświetlić nowe alerty i ustawienia skalowania automatycznego dodane do aplikacji wdrożonej w formacie JSON.

Kroki opisane w tej sekcji zostały wykonane głównie w następujący sposób:

1. Przygotowany plik szablonu
2. Utworzono plik parametrów, aby przejść do pliku szablonu
3. Wdrożono plik szablonu z plikiem parametrów

Ostatni krok jest łatwo wykonywany przez polecenie cmdlet programu PowerShell. Aby zobaczyć, co program Visual Studio miał podczas wdrażania aplikacji, Otwórz Scripts\Deploy-AzureResourceGroup.ps1. W tym miejscu istnieje dużo kodu, ale po prostu wyróżnienie całego kodu potrzebnego do wdrożenia pliku szablonu za pomocą pliku parametrów.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Ostatnim poleceniem cmdlet, `New-AzureResourceGroup`, jest to, że w rzeczywistości wykonuje akcję. Wszystko to powinno zademonstrować, że za pomocą narzędzi można w sposób nieprzewidywalny wdrożyć aplikację w chmurze. Za każdym razem, gdy uruchamiasz polecenie cmdlet w tym samym szablonie z tym samym plikiem parametrów, uzyskasz ten sam wynik.

## <a name="summary"></a>Podsumowanie
W DevOps, powtarzalność i przewidywalność to klucze do każdego pomyślnego wdrożenia aplikacji na dużą skalę składającej się z mikrousług. W tym samouczku wdrożono aplikację dwuczęściową na platformie Azure jako pojedynczą grupę zasobów przy użyciu szablonu Azure Resource Manager. Miejmy nadzieję, że posiadasz wiedzę, której potrzebujesz, aby rozpocząć konwersję aplikacji na platformę Azure do szablonu i można ją przewidzieć i wdrożyć. 

<a name="resources"></a>

## <a name="more-resources"></a>Więcej zasobów
* [Język szablonu Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Funkcje szablonu Azure Resource Manager](../azure-resource-manager/templates/template-functions.md)
* [Wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Rozwiązywanie problemów z wdrożeniami grup zasobów na platformie Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składni i właściwościach JSON dla typów zasobów wdrożonych w tym artykule, zobacz:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft. SQL/serwery/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft. Web/dopuszczalna](/azure/templates/microsoft.web/serverfarms)
* [Microsoft. Web/witryny](/azure/templates/microsoft.web/sites)
* [Microsoft. Web/Sites/miejsca](/azure/templates/microsoft.web/sites/slots)
* [Microsoft. Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)