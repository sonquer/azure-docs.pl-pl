---
title: Wdróż istniejący plik wykonywalny na platformie Azure Service Fabric
description: Dowiedz się, jak spakować istniejącą aplikację jako plik wykonywalny gościa, aby można ją było wdrożyć w klastrze Service Fabric.
ms.topic: conceptual
ms.date: 07/02/2017
ms.openlocfilehash: cdbc965d0e8ec4a8f42fbe438b8ac6ddfe05a1b3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75377110"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Pakowanie i wdrażanie istniejącego pliku wykonywalnego do Service Fabric
Przy pakowaniu istniejącego pliku wykonywalnego jako [plik wykonywalny gościa](service-fabric-guest-executables-introduction.md)można wybrać opcję użycia szablonu projektu programu Visual Studio lub [ręcznie utworzyć pakiet aplikacji](#manually). Przy użyciu programu Visual Studio Struktura pakietu aplikacji i pliki manifestu są tworzone przez nowy szablon projektu.

> [!TIP]
> Najprostszym sposobem spakowania istniejącego pliku wykonywalnego systemu Windows w usłudze jest użycie programu Visual Studio i systemu Linux w celu użycia narzędzia Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Pakowanie i wdrażanie istniejącego pliku wykonywalnego za pomocą programu Visual Studio
Program Visual Studio zawiera Service Fabric szablon usługi ułatwiający wdrożenie pliku wykonywalnego gościa w klastrze Service Fabric.

1. Wybierz pozycję **plik** > **Nowy projekt**i Utwórz aplikację Service Fabric.
2. Wybierz **plik wykonywalny gościa** jako szablon usługi.
3. Kliknij przycisk **Przeglądaj** , aby wybrać folder z plikiem wykonywalnym i uzupełnić pozostałe parametry w celu utworzenia usługi.
   * *Zachowanie pakietu kodu*. Można ustawić, aby skopiować całą zawartość folderu do projektu programu Visual Studio, co jest przydatne, jeśli plik wykonywalny nie zmienia się. Jeśli oczekujesz, że plik wykonywalny zostanie zmieniony i chcesz mieć możliwość dynamicznego pobierania nowych kompilacji, możesz połączyć się z folderem. Podczas tworzenia projektu aplikacji w programie Visual Studio można użyć folderów połączonych. Spowoduje to połączenie z lokalizacją źródłową z projektu, co umożliwia zaktualizowanie pliku wykonywalnego gościa w jego źródłowym miejscu docelowym. Te aktualizacje stają się częścią pakietu aplikacji podczas kompilacji.
   * *Program* określa plik wykonywalny, który powinien zostać uruchomiony w celu uruchomienia usługi.
   * *Argumenty* określa argumenty, które powinny być przekazane do pliku wykonywalnego. Może to być lista parametrów z argumentami.
   * *Element workingfolder* określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
     * `CodeBase` określa, że katalog roboczy ma być ustawiony na katalog kodu w pakiecie aplikacji (katalog`Code` przedstawiony w poprzedniej strukturze plików).
     * `CodePackage` określa, że katalog roboczy ma być ustawiony na katalog główny pakietu aplikacji (`GuestService1Pkg` przedstawiony w poprzedniej strukturze plików).
     * `Work` określa, że pliki są umieszczane w podkatalogu o nazwie Work.
4. Nadaj nazwę usłudze i kliknij przycisk **OK**.
5. Jeśli usługa wymaga punktu końcowego na potrzeby komunikacji, można teraz dodać protokół, port i typ do pliku servicemanifest. XML. Na przykład: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Teraz można używać akcji Package i Publish względem lokalnego klastra przez debugowanie rozwiązania w programie Visual Studio. Gdy wszystko będzie gotowe, można opublikować aplikację w klastrze zdalnym lub zaewidencjonować rozwiązanie do kontroli źródła.
7. Przeczytaj temat [Sprawdzanie działającej aplikacji](#check-your-running-application) , aby dowiedzieć się, jak wyświetlić usługę plików wykonywalnych gościa działającą w Service Fabric Explorer.

Przykład przewodnika można znaleźć w temacie [Tworzenie pierwszej aplikacji wykonywalnej gościa przy użyciu programu Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Użyj narzędzia Yeoman do spakowania i wdrożenia istniejącego pliku wykonywalnego w systemie Linux

Procedura tworzenia i wdrażania pliku wykonywalnego gościa w systemie Linux jest taka sama jak w przypadku wdrażania aplikacji CSharp lub Java.

1. Na terminalu wpisz `yo azuresfguest`.
2. Nadaj nazwę aplikacji.
3. Nazwij usługę i podaj szczegóły, w tym ścieżkę pliku wykonywalnego i parametry, do których należy wywołać.

Narzędzia Yeoman tworzy pakiet aplikacji z odpowiednimi plikami aplikacji i manifestów wraz ze skryptami instalacji i dezinstalacji.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ręcznie Pakuj i Wdróż istniejący plik wykonywalny
Proces ręcznego pakowania pliku wykonywalnego gościa jest oparty na następujących ogólnych krokach:

1. Utwórz strukturę katalogów pakietu.
2. Dodaj kod i pliki konfiguracji aplikacji.
3. Edytuj plik manifestu usługi.
4. Edytuj plik manifestu aplikacji.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Tworzenie struktury katalogów pakietu
Możesz rozpocząć od utworzenia struktury katalogów, zgodnie z opisem w artykule [pakowanie aplikacji platformy Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Dodawanie kodu i plików konfiguracji aplikacji
Po utworzeniu struktury katalogów można dodać kod i pliki konfiguracji aplikacji w katalogach Code i config. Możesz również utworzyć dodatkowe katalogi lub podkatalogi w katalogach kodu lub konfiguracji.

Service Fabric wykonuje `xcopy` zawartości katalogu głównego aplikacji, więc nie istnieje wstępnie zdefiniowana struktura do użycia poza tworzeniem dwóch najpopularniejszych katalogów, kodu i ustawień. (Jeśli chcesz, możesz wybrać różne nazwy. Więcej szczegółów znajduje się w następnej sekcji.

> [!NOTE]
> Upewnij się, że zostały uwzględnione wszystkie pliki i zależności, których potrzebuje aplikacja. Service Fabric kopiuje zawartość pakietu aplikacji na wszystkich węzłach w klastrze, w którym zostaną wdrożone usługi aplikacji. Pakiet powinien zawierać cały kod, który musi zostać uruchomiony przez aplikację. Nie należy zakładać, że zależności są już zainstalowane.
>
>

### <a name="edit-the-service-manifest-file"></a>Edytuj plik manifestu usługi
Następnym krokiem jest edytowanie pliku manifestu usługi, aby uwzględnić następujące informacje:

* Nazwa typu usługi. Jest to identyfikator, który Service Fabric służy do identyfikowania usługi.
* Polecenie służące do uruchamiania aplikacji (ExeHost).
* Każdy skrypt, który musi zostać uruchomiony w celu skonfigurowania aplikacji (SetupEntrypoint).

Poniżej znajduje się przykład pliku `ServiceManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

W poniższych sekcjach znajdują się różne części pliku, które należy zaktualizować.

#### <a name="update-servicetypes"></a>Aktualizuj typy ServiceType
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Możesz wybrać dowolną nazwę dla `ServiceTypeName`. Ta wartość jest używana w pliku `ApplicationManifest.xml` do identyfikowania usługi.
* Określ `UseImplicitHost="true"`. Ten atrybut informuje Service Fabric, że usługa jest oparta na samodzielnej aplikacji, Service Fabric więc należy ją uruchomić w ramach procesu i monitorować jej kondycję.

#### <a name="update-codepackage"></a>Aktualizacja CodePackage
Element CodePackage określa lokalizację (i wersję) kodu usługi.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Element `Name` służy do określania nazwy katalogu w pakiecie aplikacji, który zawiera kod usługi. `CodePackage` ma także atrybut `version`. Można go użyć do określenia wersji kodu i może być również używany do uaktualnienia kodu usługi przy użyciu infrastruktury zarządzania cyklem życia aplikacji w Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Opcjonalnie: Aktualizacja SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Element SetupEntryPoint służy do określania dowolnego pliku wykonywalnego lub wsadowego, który należy wykonać przed uruchomieniem kodu usługi. Jest to opcjonalny krok, dlatego nie musi być uwzględniony, jeśli nie jest wymagana Inicjalizacja. SetupEntryPoint jest wykonywane za każdym razem, gdy usługa zostanie uruchomiona ponownie.

Istnieje tylko jeden SetupEntryPoint, więc skrypty instalacyjne muszą być pogrupowane w pojedynczym pliku wsadowym, jeśli instalacja aplikacji wymaga wielu skryptów. SetupEntryPoint może wykonywać dowolny typ pliku: pliki wykonywalne, pliki wsadowe i polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji, zobacz [Konfigurowanie SetupEntryPoint](service-fabric-application-runas-security.md).

W poprzednim przykładzie SetupEntryPoint uruchamia plik wsadowy o nazwie `LaunchConfig.cmd`, który znajduje się w podkatalogu `scripts` katalogu kodu (przy założeniu, że element element workingfolder jest ustawiony na CodeBase).

#### <a name="update-entrypoint"></a>Update EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Element `EntryPoint` w pliku manifestu usługi służy do określania sposobu uruchamiania usługi.

`ExeHost` element Określa plik wykonywalny (i argumenty), który ma zostać użyty do uruchomienia usługi. Opcjonalnie możesz dodać atrybut `IsExternalExecutable="true"` do `ExeHost`, aby wskazać, że program jest zewnętrznym plikiem wykonywalnym poza pakietem kodu. Na przykład `<ExeHost IsExternalExecutable="true">`.

* `Program` określa nazwę pliku wykonywalnego, który powinien uruchomić usługę.
* `Arguments` określa argumenty, które powinny być przekazane do pliku wykonywalnego. Może to być lista parametrów z argumentami.
* `WorkingFolder` określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
  * `CodeBase` określa, że katalog roboczy ma być ustawiony na katalog kodu w pakiecie aplikacji (katalog`Code` w poprzedniej strukturze plików).
  * `CodePackage` określa, że katalog roboczy ma być ustawiony na katalog główny pakietu aplikacji (`GuestService1Pkg` w poprzedniej strukturze plików).
    * `Work` określa, że pliki są umieszczane w podkatalogu o nazwie Work.

Element workingfolder jest przydatne do ustawiania prawidłowego katalogu roboczego, dzięki czemu ścieżki względne mogą być używane przez skrypty aplikacji lub inicjalizacji.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Aktualizowanie punktów końcowych i rejestrowanie przy użyciu Usługa nazewnictwa na potrzeby komunikacji
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
W poprzednim przykładzie element `Endpoint` określa punkty końcowe, na których aplikacja może nasłuchiwać. W tym przykładzie aplikacja Node. js nasłuchuje na protokole HTTP na porcie 3000.

Ponadto możesz polecić Service Fabric opublikować ten punkt końcowy w Usługa nazewnictwa, tak aby inne usługi mogły odnaleźć adres punktu końcowego dla tej usługi. Dzięki temu można komunikować się między usługami, które są plikami wykonywalnymi gościa.
Opublikowany adres punktu końcowego ma postać `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` i `PathSuffix` są atrybutami opcjonalnymi. `IPAddressOrFQDN` to adres IP lub w pełni kwalifikowana nazwa domeny węzła, w którym znajduje się ten plik wykonywalny, i jest obliczana dla Ciebie.

W poniższym przykładzie, po wdrożeniu usługi, w Service Fabric Explorer zobaczysz punkt końcowy podobny do `http://10.1.4.92:3000/myapp/` opublikowany dla wystąpienia usługi. Lub jeśli jest to komputer lokalny, zobaczysz `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Możesz użyć tych adresów z [zwrotnym serwerem proxy](service-fabric-reverseproxy.md) do komunikacji między usługami.

### <a name="edit-the-application-manifest-file"></a>Edytuj plik manifestu aplikacji
Po skonfigurowaniu pliku `Servicemanifest.xml` należy wprowadzić pewne zmiany w pliku `ApplicationManifest.xml`, aby upewnić się, że jest używany poprawny typ usługi i nazwa.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
W `ServiceManifestImport` elementu można określić co najmniej jedną usługę, która ma zostać uwzględniona w aplikacji. Do usługi są przywoływane `ServiceManifestName`, która określa nazwę katalogu, w którym znajduje się plik `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurowanie rejestracji
W przypadku plików wykonywalnych gościa warto zobaczyć dzienniki konsoli, aby dowiedzieć się, czy skrypty aplikacji i konfiguracji pokazują błędy.
Przekierowanie konsoli można skonfigurować w pliku `ServiceManifest.xml` przy użyciu elementu `ConsoleRedirection`.

> [!WARNING]
> Nigdy nie należy używać zasad przekierowania konsoli w aplikacji wdrożonej w środowisku produkcyjnym, ponieważ może to mieć wpływ na tryb failover aplikacji. Służy on *tylko* do lokalnego projektowania i debugowania.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` może służyć do przekierowania danych wyjściowych konsoli (stdout i stderr) do katalogu roboczego. Zapewnia to możliwość sprawdzenia, czy podczas instalacji lub wykonywania aplikacji w klastrze Service Fabric nie występują błędy.

`FileRetentionCount` określa, ile plików jest zapisywanych w katalogu roboczym. Na przykład wartość 5 oznacza, że pliki dziennika dla poprzednich pięciu wykonań są przechowywane w katalogu roboczym.

`FileMaxSizeInKb` określa maksymalny rozmiar plików dziennika.

Pliki dziennika są zapisywane w jednym z katalogów roboczych usługi. Aby określić, gdzie znajdują się pliki, użyj Service Fabric Explorer, aby określić węzeł, w którym usługa jest uruchomiona, i który katalog roboczy jest używany. Ten proces został omówiony w dalszej części tego artykułu.

## <a name="deployment"></a>Wdrażanie
Ostatnim krokiem jest [wdrożenie aplikacji](service-fabric-deploy-remove-applications.md). Poniższy skrypt programu PowerShell przedstawia sposób wdrażania aplikacji w lokalnym klastrze projektowym i uruchamiania nowej usługi Service Fabric.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Kompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów, jeśli pakiet jest duży lub zawiera wiele plików. Przeczytaj więcej [tutaj](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Usługę Service Fabric można wdrożyć w różnych konfiguracjach. Na przykład można je wdrożyć jako jedno lub wiele wystąpień lub można je wdrożyć w taki sposób, że na każdym węźle klastra Service Fabric istnieje jedno wystąpienie usługi.

Parametr `InstanceCount` polecenia cmdlet `New-ServiceFabricService` służy do określania, ile wystąpień usługi należy uruchomić w klastrze Service Fabric. Można ustawić wartość `InstanceCount`, w zależności od typu wdrażanej aplikacji. Dwa najczęstsze scenariusze to:

* `InstanceCount = "1"`. W takim przypadku w klastrze wdrożone jest tylko jedno wystąpienie usługi. Service Fabric Scheduler określa węzeł, w którym ma zostać wdrożona usługa.
* `InstanceCount ="-1"`. W takim przypadku jedno wystąpienie usługi jest wdrażane na każdym węźle w klastrze Service Fabric. Wynik ma jeden (i tylko jedno) wystąpienie usługi dla każdego węzła w klastrze.

Jest to użyteczna konfiguracja dla aplikacji frontonu (na przykład punktu końcowego REST), ponieważ aplikacje klienckie muszą mieć wartość "Połącz" z dowolnym węzłem w klastrze, aby użyć punktu końcowego. Ta konfiguracja może być również używana, gdy na przykład wszystkie węzły klastra Service Fabric są połączone z modułem równoważenia obciążenia. Ruch klienta może być następnie dystrybuowany przez usługę uruchomioną na wszystkich węzłach w klastrze.

## <a name="check-your-running-application"></a>Sprawdź uruchomioną aplikację
W Service Fabric Explorer Zidentyfikuj węzeł, w którym usługa jest uruchomiona. W tym przykładzie działa on w Węzeł1:

![Węzeł, w którym jest uruchomiona usługa](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Jeśli przejdziesz do węzła i przejdziesz do aplikacji, zobaczysz podstawowe informacje o węźle, w tym jego lokalizację na dysku.

![Lokalizacja na dysku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

W przypadku przechodzenia do katalogu przy użyciu Eksplorator serwera można znaleźć katalog roboczy i folder dziennika usługi, jak pokazano na poniższym zrzucie ekranu: 

![Lokalizacja dziennika](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak spakować plik wykonywalny gościa i wdrożyć go w Service Fabric. Zapoznaj się z następującymi artykułami dotyczącymi pokrewnych informacji i zadań.

* [Przykład dotyczący pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), w tym link do wersji wstępnej narzędzia pakowanie
* [Przykład dwóch plików wykonywalnych gościaC# (i NodeJS) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Wdrażanie wielu aplikacji wykonywalnych gości](service-fabric-deploy-multiple-apps.md)
* [Tworzenie pierwszej aplikacji Service Fabric przy użyciu programu Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
