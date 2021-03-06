---
title: Dane konfiguracji w skali — Azure Automation
description: Dowiedz się, jak skonfigurować dane w odpowiedniej skali dla konfiguracji stanu w Azure Automation.
keywords: DSC, PowerShell, konfiguracja, instalacja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028288"
---
# <a name="configuration-data-at-scale"></a>Dane konfiguracji w dużej skali

> Dotyczy: Windows PowerShell 5,1

Zarządzanie setkami lub tysiącami serwerów może być wyzwaniem.
Klienci otrzymali opinię, że najbardziej trudnym aspektem jest zarządzanie [danymi konfiguracji](/powershell/scripting/dsc/configurations/configdata).
Organizowanie informacji w ramach konstrukcji logicznych, takich jak lokalizacja, typ i środowisko.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność typu open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy GitHub, a nie od firmy Microsoft.

## <a name="community-project-datum"></a>Projekt społeczności: Datum

Utworzono rozwiązanie obsługiwane przez społeczność o nazwie [Datum](https://github.com/gaelcolas/Datum) , aby rozwiązać ten problem.
Datum tworzy doskonałe pomysły dotyczące innych platform zarządzania konfiguracją i implementuje ten sam typ rozwiązania dla DSC programu PowerShell.
Informacje są [zorganizowane w plikach tekstowych](https://github.com/gaelcolas/Datum#3-intended-usage) w oparciu o koncepcje logiczne.
Oto przykłady:

- Ustawienia, które powinny być stosowane globalnie
- Ustawienia, które powinny być stosowane do wszystkich serwerów w lokalizacji
- Ustawienia, które powinny być stosowane dla wszystkich serwerów baz danych
- Ustawienia poszczególnych serwerów

Te informacje są zorganizowane w preferowany format pliku (JSON, YAML lub PSD1).
Polecenia cmdlet są udostępniane do generowania plików danych konfiguracji przez [konsolidowanie informacji](https://github.com/gaelcolas/Datum#datum-tree) z każdego pliku w programie do pojedynczego widoku roli serwera lub serwera.

Po wygenerowaniu plików danych można użyć ich ze [skryptami konfiguracji DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) w celu wygenerowania plików MOF i [przekazania plików MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) lub na [platformie Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) , aby przeprowadzić konfigurację ściągania.

Aby wypróbować podstawę, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/datum/) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Następne kroki

- [Przegląd konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Zasoby DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurowanie Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaconfig)
