---
title: Konfigurowanie uwierzytelniania za pomocą Amazon Web Services
description: W tym artykule opisano sposób tworzenia i sprawdzania poprawności poświadczeń w usłudze AWS dla elementów Runbook w usłudze Azure Automation zarządzającej zasobami usługi AWS.
keywords: uwierzytelnianie aws, konfigurowanie aws
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 596dc334a412b3e0839d7661a23af771e5cd7394
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366944"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Uwierzytelnianie elementów Runbook w usłudze Amazon Web Services

Typowe zadania dotyczące zasobów w usłudze Amazon Web Services (AWS) można zautomatyzować za pomocą elementów Runbook usługi Azure Automation. Wiele zadań w usłudze AWS można zautomatyzować przy użyciu elementów Runbook usługi Automation, podobnie jak za pomocą zasobów platformy Azure. Wymagane są jedynie dwa elementy:

* Subskrypcja AWS i zestaw poświadczeń. W szczególności klucz dostępu AWS i klucz tajny. Więcej informacji można znaleźć w artykule [Using AWS Credentials (Korzystanie z poświadczeń usługi AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Subskrypcja platformy Azure i konto w usłudze Automation.

Aby wykonać uwierzytelnienie w usłudze AWS, należy określić zestaw poświadczeń usługi AWS do uwierzytelniania elementów Runbook uruchamianych z poziomu usługi Azure Automation. Jeśli masz już utworzone konto usługi Automation, którego chcesz użyć do uwierzytelniania za pomocą usługi AWS, możesz wykonać czynności opisane w poniższej sekcji: Jeśli chcesz przypisać konto dla elementów Runbook przeznaczonych dla zasobów AWS, najpierw utwórz nowe [konto usługi Automation](automation-offering-get-started.md) (Pomiń opcję tworzenia nazwy głównej usługi) i wykonaj następujące czynności:

## <a name="configure-automation-account"></a>Skonfiguruj konto usługi Automation

Aby usługa Azure Automation mogła się komunikować z usługą AWS, musisz najpierw pobrać poświadczenia usługi AWS i przechowywać je jako zasoby w usłudze Azure Automation. Wykonaj następujące czynności opisane w dokumencie usługi AWS [Zarządzanie kluczami dostępu konta usługi AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html), aby utworzyć klucz dostępu i skopiować **identyfikator klucza dostępu** i **tajny klucz dostępu** (ewentualnie pobierz plik klucza, aby go bezpiecznie przechować w innym miejscu).

Po utworzeniu i skopiowaniu kluczy zabezpieczeń usługi AWS musisz utworzyć zasób poświadczeń przy użyciu konta usługi Azure Automation, aby bezpiecznie je przechowywać i odwoływać się do nich z poziomu elementów Runbook. Wykonaj kroki opisane w sekcji: **Aby utworzyć nowe poświadczenie** w obszarze [zasoby poświadczeń w Azure Automation](shared-resources/credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) artykule i wprowadź następujące informacje:

1. W polu **Nazwa** wprowadź **AWScred** lub wartość odpowiadającą Twoim standardom nazewnictwa.
2. W polu **Nazwa użytkownika** wpisz swój **identyfikator dostępu** oraz **klucz tajny dostępu** w polach **Hasło** i **Potwierdź hasło**.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z artykułem [Automating deployment of a VM in Amazon Web Services (Zautomatyzowane wdrażanie maszyn wirtualnych w usłudze Amazon Web Services)](automation-scenario-aws-deployment.md), aby dowiedzieć się, jak utworzyć elementy runbook w celu automatyzacji zadań w usłudze AWS.
