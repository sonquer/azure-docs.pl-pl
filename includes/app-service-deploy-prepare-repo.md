---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836786"
---
## <a name="prepare-your-repository"></a>Przygotowanie repozytorium

Aby uzyskać automatyczne kompilacje z serwer kompilacji Kudu usługi Azure App Service, upewnij się, że katalogu głównym repozytorium ma poprawne pliki w projekcie.

| Środowisko uruchomieniowe | Plików w katalogu głównym |
|-|-|
| ASP.NET (tylko Windows) | _*.sln_, _*.csproj_, lub _default.aspx_ |
| ASP.NET Core | _*.sln_ lub _*.csproj_ |
| PHP | _index.php_ |
| Ruby (tylko system Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, lub _package.json_ przy użyciu skryptu uruchamiania |
| Python | _\*PY_, _requirements.txt_, lub _pliku runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, lub  _iisstart.htm_ |
| Zadania WebJob | _\<Parametr job_name > / run. \<rozszerzenia >_ w obszarze _aplikacji\_danych/zadania/continuous_ dla ciągłych zadań Webjob, lub _aplikacji\_danych/zadania/triggered_ dla wyzwolone Zadania Webjob. Aby uzyskać więcej informacji, zobacz [dokumentacji Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funkcje | Zobacz [ciągłe wdrażanie dla usługi Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Aby dostosować swoje wdrożenia, należy dołączyć *.deployment* plik w folderze głównym repozytorium. Aby uzyskać więcej informacji, zobacz [Dostosowywanie wdrożeń](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) i [skrypt wdrożenia niestandardowego](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> W przypadku tworzenia w programie Visual Studio umożliwiają [programu Visual Studio Utwórz repozytorium dla Ciebie](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projekt jest natychmiast gotowy do wdrożenia przy użyciu narzędzia Git.
>

