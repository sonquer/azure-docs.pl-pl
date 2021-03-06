---
title: Rozwiązywanie problemów z instalacją Azure AD Connect | Microsoft Docs "
description: Ten temat zawiera instrukcje dotyczące rozwiązywania problemów z instalacją Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211841"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Rozwiązywanie problemów: Problemy z instalacją programu Azure AD Connect

## <a name="recommended-steps"></a>**Zalecane czynności**
Sprawdź, który [Azure AD Connect typ instalacji](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) jest odpowiedni dla Ciebie. Jeśli spełniasz kryteria instalacji ekspresowej, zdecydowanie zalecamy przeprowadzenie instalacji ekspresowej. Instalacja ekspresowa zapewnia minimalne opcje potrzebne do zakończenia instalacji, dlatego istnieje mniejsze prawdopodobieństwo wystąpienia problemów. 

Jeśli jednak nie spełnisz kryteriów instalacji ekspresowej i musisz przeprowadzić instalację niestandardową, możesz wykonać kilka najlepszych rozwiązań, aby uniknąć typowych problemów. Tylko w przypadku uproszczenia są wymienione tutaj tylko opcje selektywne:

* Upewnij się, że jesteś administratorem na komputerze, na którym instalujesz aplikację AAD Connect. Zaloguj się na komputerze przy użyciu tych samych poświadczeń administratora.

* Zezwalaj na ustawienie domyślne na poniższej stronie, z wyjątkiem opcji "Użyj istniejącej SQL Server", jeśli chcesz użyć istniejących SQL Server. Poniżej przedstawiono [więcej informacji](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) na temat używania niestandardowych opcji instalacji. 

    ![Użyj istniejących SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na poniższej stronie wybierz opcję "Utwórz nowe konto usługi AD", aby uniknąć problemów z uprawnieniami istniejącego konta.

    ![Konto lasu usługi AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Typowe problemy**

* [Problemy z łącznością z Active Directory lokalnymi](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemy z łącznością z Azure Active Directoryami online](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemy z uprawnieniami Active Directory lokalnych](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Prerequisites for Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites) (Wymagania wstępne programu Azure AD Connect)
* [Select which installation type to use for Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) (Wybieranie typu instalacji do użycia dla programu Azure AD Connect)
* [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Niestandardowa instalacja programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Co to jest serwer przejściowy?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Co to jest moduł ADConnectivityTool programu PowerShell?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect synchronizację](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md)





