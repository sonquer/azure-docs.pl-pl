---
title: Wdróż kod z plikiem ZIP lub WAR
description: Dowiedz się, jak wdrożyć aplikację w celu Azure App Service z plikiem ZIP (lub plikiem WAR dla deweloperów języka Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945182"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Wdróż aplikację w celu Azure App Service z plikiem ZIP lub WAR

W tym artykule pokazano, jak za pomocą pliku ZIP lub pliku WAR wdrożyć aplikację internetową w celu [Azure App Service](overview.md). 

To wdrożenie pliku ZIP używa tej samej usługi kudu, która ma uprawnienia do ciągłego wdrażania opartych na integracji. Kudu obsługuje następujące funkcje wdrożenia pliku ZIP: 

- Usuwanie plików pozostawionych z poprzedniego wdrożenia.
- Opcja włączania domyślnego procesu kompilacji, w tym przywracania pakietu.
- Dostosowanie wdrożenia, w tym uruchamianie skryptów wdrażania.  
- Dzienniki wdrożenia. 
- Limit rozmiaru pliku wynoszący 2048 MB.

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Wdrożenie pliku WAR wdraża plik [War](https://wikipedia.org/wiki/WAR_(file_format)) w celu App Service uruchamiania aplikacji sieci Web Java. Zobacz [wdrażanie pliku War](#deploy-war-file).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, należy [utworzyć aplikację App Service](/azure/app-service/)lub użyć aplikacji utworzonej dla innego samouczka.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Powyższy punkt końcowy nie działa w przypadku App Services systemu Linux. Zamiast tego Rozważ użycie protokołu FTP lub [interfejsu API Deploy zip](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) .

## <a name="deploy-zip-file-with-azure-cli"></a>Wdróż plik ZIP przy użyciu interfejsu wiersza polecenia platformy Azure

Wdróż przekazany plik ZIP w aplikacji sieci Web za pomocą polecenia [AZ webapp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) .  

W poniższym przykładzie został wdrożony przekazany plik ZIP. W przypadku korzystania z lokalnej instalacji interfejsu wiersza polecenia platformy Azure należy określić ścieżkę do lokalnego pliku ZIP dla `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

To polecenie wdraża pliki i katalogi z pliku ZIP do domyślnego folderu aplikacji App Service (`\home\site\wwwroot`) i ponownie uruchamia aplikację.

Domyślnie Aparat wdrażania zakłada, że plik ZIP jest gotowy do uruchomienia jako-is i nie uruchamia żadnej automatyzacji kompilacji. Aby włączyć tę samą automatyzację kompilacji jak w przypadku [wdrożenia usługi git](deploy-local-git.md), należy ustawić ustawienie aplikacji `SCM_DO_BUILD_DURING_DEPLOYMENT`, uruchamiając następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Wdróż plik WAR

Aby wdrożyć plik WAR do App Service, Wyślij żądanie POST do `https://<app-name>.scm.azurewebsites.net/api/wardeploy`. Żądanie POST musi zawierać plik WAR w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP.

Podczas wdrażania plików WAR zawsze używaj `/api/wardeploy`. Ten interfejs API rozszerza plik WAR i umieszcza go na udostępnionym dysku. Korzystanie z innych interfejsów API wdrażania może spowodować niespójne zachowanie. 

W przypadku uwierzytelniania podstawowego HTTP wymagane są poświadczenia wdrażania App Service. Aby dowiedzieć się, jak ustawić poświadczenia wdrożenia, zobacz [Ustawianie i resetowanie poświadczeń na poziomie użytkownika](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Z zwinięciem

W poniższym przykładzie za pomocą narzędzia zwinięcie można wdrożyć plik War. Zastąp symbole zastępcze `<username>`, `<war-file-path>`i `<app-name>`. Po wyświetleniu monitu przez zwinięcie wpisz hasło.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Z programem PowerShell

Poniższy przykład używa [publikowania-AzWebapp](/powershell/module/az.websites/publish-azwebapp) Przekaż plik War. Zastąp symbole zastępcze `<group-name>`, `<app-name>`i `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

W przypadku bardziej zaawansowanych scenariuszy wdrażania spróbuj [wdrożyć platformę Azure za pomocą narzędzia Git](deploy-local-git.md). Wdrożenie oparte na usłudze Git na platformie Azure umożliwia kontrolę wersji, przywracanie pakietów, MSBuild i nie tylko.

## <a name="more-resources"></a>Więcej zasobów

* [Kudu: wdrażanie z pliku zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Poświadczenia wdrażania Azure App Service](deploy-ftp.md)
