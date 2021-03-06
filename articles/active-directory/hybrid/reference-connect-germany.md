---
title: Program Azure AD Connect w usłudze Microsoft Cloud w Niemczech
description: Program Azure AD Connect umożliwia integrowanie katalogów lokalnych z usługą Azure Active Directory. Dzięki temu można posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD.
keywords: wprowadzenie do programu Azure AD Connect, omówienie programu Azure AD Connect, co to jest program Azure AD Connect, instalowanie usługi Active Directory, Niemcy, Black Forest
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62f0d82a543c0ae4e629eda3bca18b0a06322f2c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381255"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Program Azure AD Connect w usłudze Microsoft Cloud w Niemczech — publiczna wersja zapoznawcza
## <a name="introduction"></a>Wprowadzenie
Azure AD Connect zapewnia synchronizację między lokalną usługą Active Directory a usługą Azure Active Directory.
Obecnie wiele scenariuszy w usłudze [Microsoft Cloud w Niemczech](https://azure.microsoft.com/global-infrastructure/germany/
) musi być wykonywanych przez operatora. Korzystając z usługi Microsoft Cloud w Niemczech, należy pamiętać o następujących kwestiach:

* Następujące adresy URL muszą zostać otwarte na serwerze proxy, aby synchronizacja zakończyła się pomyślnie:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Listy odwołania certyfikatów
* Po zalogowaniu do katalogu usługi Azure AD należy używać konta w domenie onmicrosoft.de.

 
## <a name="download"></a>Do pobrania
Usługę Azure AD Connect można pobrać z bloku programu Azure AD Connect w portalu.  Znajdź blok programu Azure AD Connect, korzystając z poniższych instrukcji.

### <a name="the-azure-ad-connect-blade"></a>Blok programu Azure AD Connect
Po zalogowaniu do witryny Azure Portal:

1. Przejdź do pozycji Przeglądaj
2. Wybierz pozycję Azure Active Directory
3. Następnie wybierz pozycję Azure AD Connect

Zostaną wyświetlone następujące szczegóły:

![Blok programu Azure AD Connect](./media/reference-connect-germany/germany1.png)

W poniższej tabeli opisano funkcje wyświetlane w bloku.

| Tytuł | Opis |
| --- | --- |
| STAN SYNCHRONIZACJI |Informuje, czy synchronizacja jest włączona, czy wyłączona. |
| OSTATNIA SYNCHRONIZACJA |Godzina zakończenia ostatniej pomyślnej synchronizacji. |
| DOMENY FEDERACYJNE |Wyświetla liczbę obecnie skonfigurowanych domen federacyjnych. |

## <a name="installation"></a>Instalacja
Możesz zainstalować program Azure AD Connect, korzystając z dokumentacji znajdującej się [tutaj](how-to-connect-install-roadmap.md).

## <a name="advanced-features-and-additional-information"></a>Funkcje zaawansowane i dodatkowe informacje
Aby uzyskać dodatkowe informacje dotyczące ustawień niestandardowych lub zaawansowanych konfiguracji, przejdź do artykułu [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md). Ta strona zawiera informacje i linki do dodatkowych wskazówek.

