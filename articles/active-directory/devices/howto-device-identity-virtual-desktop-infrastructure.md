---
title: Tożsamość urządzenia i Wirtualizacja pulpitu — Azure Active Directory
description: Dowiedz się, w jaki sposób można razem używać tożsamości infrastruktury VDI i urządzeń usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900368"
---
# <a name="device-identity-and-desktop-virtualization"></a>Tożsamość urządzenia i Wirtualizacja pulpitu

Administratorzy często wdrażają platformy infrastruktury pulpitu wirtualnego (VDI), które obsługują systemy operacyjne Windows w swoich organizacjach. Administratorzy wdrażają infrastrukturę VDI w programie:

- Usprawnianie zarządzania.
- Obniż koszty dzięki konsolidacji i scentralizowaniu zasobów.
- Zapewnianie mobilności użytkowników końcowych i swobody uzyskiwania dostępu do pulpitów wirtualnych w dowolnym miejscu, z dowolnego miejsca i na dowolnym urządzeniu.

Istnieją dwa podstawowe typy pulpitów wirtualnych:

- Stale
- Nietrwałe

Wersje trwałe wykorzystują unikatowy obraz stacjonarny dla każdego użytkownika lub puli użytkowników. Te unikatowe pulpity mogą być dostosowane i zapisywane do użytku w przyszłości. 

Wersje nietrwałe używają kolekcji pulpitów, do których użytkownicy mogą uzyskać dostęp w zależności od wymaganej bazy. Te nietrwałe komputery stacjonarne są przywracane do oryginalnego stanu po wylogowaniu się użytkownika.

W tym artykule omówiono wskazówki firmy Microsoft dla administratorów dotyczące obsługi tożsamości urządzeń i infrastruktury VDI. Aby uzyskać więcej informacji na temat tożsamości urządzeń, zobacz artykuł [co to jest tożsamość urządzenia](overview.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Przed skonfigurowaniem tożsamości urządzeń w usłudze Azure AD dla środowiska VDI zapoznaj się z obsługiwanymi scenariuszami. W poniższej tabeli przedstawiono, które scenariusze aprowizacji są obsługiwane. Inicjowanie obsługi w tym kontekście oznacza, że administrator może skonfigurować tożsamość urządzeń na dużą skalę, bez konieczności interakcji z użytkownikiem końcowym.

| Typ tożsamości urządzenia | Infrastruktura tożsamości | Urządzenia Windows | Wersja platformy VDI | Obsługiwane |
| --- | --- | --- | --- | --- |
| Dołączona hybrydowa usługa Azure AD | Federacyjny | Windows Current * * * i Windows Down-Level * * * * | Stale | Tak |
|   |   | Bieżące dla systemu Windows | Nietrwałe | Nie |
|   |   | System Windows niższego poziomu | Nietrwałe | Tak |
|   | Zarządzany * * | System Windows — bieżące i Windows niższe poziomy | Stale | Tak |
|   |   | Bieżące dla systemu Windows | Nietrwałe | Nie |
|   |   | System Windows niższego poziomu | Nietrwałe | Tak |
| Przyłączone do usługi Azure AD | Federacyjni | Bieżące dla systemu Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
|   | Zarządzane | Bieżące dla systemu Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
| Zarejestrowane w usłudze Azure AD | Federacyjni | Bieżące dla systemu Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
|   | Zarządzane | Bieżące dla systemu Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |

\* środowisko infrastruktury tożsamości **federacyjnych** reprezentuje środowisko z dostawcą tożsamości, takim jak AD FS lub inne dostawcy tożsamości innych firm.

\*\* **zarządzanym** środowiskiem infrastruktury tożsamości jest środowisko z usługą Azure AD jako dostawca tożsamości wdrożony za pomocą dowolnej [synchronizacji skrótów haseł (PHS)](../hybrid/whatis-phs.md) lub [uwierzytelniania przekazującego (PTA)](../hybrid/how-to-connect-pta.md) z [bezproblemowym logowaniem jednokrotnym](../hybrid/how-to-connect-sso.md).

\*\*\* **aktualne urządzenia z systemem Windows** 10, windows Server 2016 i windows Server 2019.

\*\*\*\* urządzenia **niskiego poziomu systemu Windows** reprezentują systemy Windows 7, Windows 8.1, windows Server 2008 R2, windows Server 2012 i windows Server 2012 R2. Aby uzyskać informacje o pomocy technicznej w systemie Windows 7, zobacz temat [Obsługa systemu Windows 7 kończy się](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Aby uzyskać informacje na temat pomocy technicznej dotyczącej systemu Windows Server 2008 R2, zobacz [przygotowanie do systemu Windows server 2008 koniec wsparcia](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Wskazówki firmy Microsoft

Administratorzy powinni zapoznać się z następującymi artykułami, na podstawie ich infrastruktury tożsamości, aby dowiedzieć się, jak skonfigurować sprzężenie hybrydowe usługi Azure AD.

- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

Jeśli korzystasz z narzędzia przygotowywania systemu (Sysprep. exe) i używasz obrazu sprzed systemu Windows 10 1809 do instalacji, upewnij się, że obraz nie pochodzi z urządzenia, które jest już zarejestrowane w usłudze Azure AD jako dołączona hybrydowa usługa Azure AD.

Jeśli korzystasz z migawki maszyny wirtualnej w celu utworzenia dodatkowych maszyn wirtualnych, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która jest już zarejestrowana w usłudze Azure AD jako sprzężenie hybrydowe usługi Azure AD.

W przypadku wdrażania nietrwałego infrastruktury VDI Administratorzy IT powinni zwrócić szczególną uwagę na zarządzanie przestarzałymi urządzeniami w usłudze Azure AD. Firma Microsoft zaleca, aby administratorzy IT implementują poniższe wskazówki. Niewykonanie tej czynności spowoduje, że katalog będzie miał wiele starych urządzeń przyłączonych do hybrydowej usługi Azure AD zarejestrowanych z nietrwałej platformy VDI.

- Utwórz i użyj prefiksu dla nazwy wyświetlanej komputera, który wskazuje pulpit jako oparty na infrastrukturze VDI.
- Zaimplementuj poniższe polecenie jako część skryptu wylogowywania. To polecenie spowoduje wyzwolenie najlepszego wywołania usługi Azure AD w celu usunięcia urządzenia.
   - W przypadku urządzeń niższego poziomu systemu Windows — automiejscem pracy. exe/Leave
- Zdefiniuj i Implementuj proces [zarządzania przestarzałymi urządzeniami](manage-stale-devices.md).
   - Gdy masz strategię do identyfikowania nietrwałych hybrydowych urządzeń z usługą Azure AD, możesz bardziej agresywnie czyścić te urządzenia, aby upewnić się, że katalog nie jest używany z wieloma przestarzałymi urządzeniami.
 
## <a name="next-steps"></a>Następne kroki

[Konfigurowanie dołączania hybrydowego Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
