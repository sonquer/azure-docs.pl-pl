---
title: Azure Database for MySQL szyfrowanie danych za pomocą klucza zarządzanego przez klienta
description: Azure Database for MySQL szyfrowanie danych za pomocą klucza zarządzanego przez klienta umożliwia Bring Your Own Key (BYOK) na potrzeby ochrony danych przechowywanych w czasie spoczynku. Pozwala to również organizacjom na wdrażanie rozdzieleń obowiązków związanych z zarządzaniem kluczami i danymi.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 552c6b5176babfc1a66f74f1bb6daa9370744b8f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210435"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL szyfrowanie danych za pomocą klucza zarządzanego przez klienta

> [!NOTE]
> W tej chwili należy zażądać dostępu do korzystania z tej funkcji. Aby to zrobić, skontaktuj się z AskAzureDBforMySQL@service.microsoft.com.

Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta Azure Database for MySQL umożliwia korzystanie z własnego klucza (BYOK) na potrzeby ochrony danych w spoczynku. Pozwala to również organizacjom na wdrażanie rozdzieleń obowiązków związanych z zarządzaniem kluczami i danymi. W przypadku szyfrowania zarządzanego przez klienta użytkownik jest odpowiedzialny za oraz w pełni kontrolujący cykl życia klucza, uprawnienia do użycia klucza i inspekcje operacji na kluczach.

Szyfrowanie danych z kluczami zarządzanymi przez klienta dla Azure Database for MySQL jest ustawiany na poziomie serwera. Dla danego serwera klucz zarządzany przez klienta o nazwie klucz szyfrowania klucza (KEK) jest używany do szyfrowania klucza szyfrowania danych używanego przez usługę. KEK jest kluczem asymetrycznym przechowywanym w wystąpieniu [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) klienta i zarządzanym przez klienta. Klucz szyfrowania klucza (KEK) i klucz szyfrowania danych (w tym artykule) opisano szczegółowo w dalszej części tego artykułu.

Key Vault to oparty na chmurze zewnętrzny system zarządzania kluczami. Jest ona wysoce dostępna i zapewnia skalowalny, bezpieczny magazyn dla kluczy kryptograficznych RSA, opcjonalnie obsługiwane przez program FIPS 140-2 Level 2 sprawdzone sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale zapewnia usługi szyfrowania i odszyfrowywania do autoryzowanych jednostek. Key Vault może wygenerować klucz, zaimportować go lub [przetransferować z lokalnego urządzenia HSM](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w których Azure Database for MySQL obsługuje warstwy cenowe "Ogólnego przeznaczenia" i "zoptymalizowane pod kątem pamięci".

## <a name="benefits"></a>Korzyści

Szyfrowanie danych dla Azure Database for MySQL zapewnia następujące korzyści:

* Dostęp do danych jest w pełni kontrolowany przez użytkownika przez możliwość usuwania klucza i uzyskiwania dostępu do bazy danych 
* Pełna kontrola nad cyklem życia klucza, włącznie z rotacją klucza do dopasowania do zasad firmowych
* Centralne zarządzanie i organizacja kluczy w Azure Key Vault
* Możliwość wdrożenia separacji obowiązków między biurami zabezpieczeń i administratorami systemów i administratorów systemu


## <a name="terminology-and-description"></a>Terminologia i opis

**Klucz szyfrowania danych (unencryption)** : symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych. Szyfrowanie każdego bloku danych przy użyciu innego klucza sprawia, że ataki analizy kryptograficznej są trudniejsze. Dostęp do DEKs jest wymagany przez dostawcę zasobów lub wystąpienie aplikacji, które szyfruje i odszyfrowuje określony blok. Gdy zastąpisz klucz szyfrowania danych za pomocą nowego klucza, tylko dane w skojarzonym bloku muszą zostać ponownie zaszyfrowane przy użyciu nowego klucza.

**Klucz szyfrowania klucza (KEK)** : klucz szyfrowania używany do szyfrowania DEKs. Element KEK, który nigdy nie pozostawia Key Vault umożliwia zaszyfrowanie i kontrola DEKs. Jednostka, która ma dostęp do KEK, może różnić się od jednostki, która wymaga tego klucza. Ponieważ KEK jest wymagany do odszyfrowania DEKs, KEK jest efektywnie jednym punktem, przez który DEKs może być skutecznie usunięty przez usunięcie KEK.

DEKs szyfrowany za pomocą KEKs są przechowywane oddzielnie. Tylko jednostka z dostępem do KEK może odszyfrować te DEKs. Aby uzyskać więcej informacji, zobacz [zabezpieczenia w szyfrowaniu](../security/fundamentals/encryption-atrest.md)w stanie spoczynku.

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Jak działa szyfrowanie danych z kluczem zarządzanym przez klienta

![Diagram przedstawiający przegląd Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Aby serwer MySQL mógł używać kluczy zarządzanych przez klienta przechowywanych w Key Vault na potrzeby szyfrowania danych w programie, administrator Key Vault ma następujące prawa dostępu do serwera:

* **Pobierz**: w celu pobrania publicznej części i właściwości klucza w magazynie kluczy.
* **wrapKey**: aby można było zaszyfrować klucz szyfrowania danych.
* **unwrapKey**: aby można było odszyfrować klucz szyfrowania danych.

Administrator magazynu kluczy może również [włączyć rejestrowanie Key Vault zdarzeń inspekcji](../azure-monitor/insights/azure-key-vault.md), aby mogły być później poddane inspekcji.

Gdy serwer jest skonfigurowany do korzystania z klucza zarządzanego przez klienta przechowywanego w magazynie kluczy, serwer wysyła do magazynu kluczy, w którym są szyfrowane. Key Vault zwraca zaszyfrowany klucz szyfrowania danych, który jest przechowywany w bazie. Analogicznie, jeśli jest to konieczne, serwer wysyła chronione szyfrowanie szyfrowania danych do magazynu kluczy w celu odszyfrowania. Audytorzy mogą używać Azure Monitor do przeglądania dzienników zdarzeń inspekcji Key Vault, jeśli rejestrowanie jest włączone.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Wymagania dotyczące konfigurowania szyfrowania danych dla Azure Database for MySQL

Poniżej przedstawiono wymagania dotyczące konfigurowania Key Vault:

* Key Vault i Azure Database for MySQL muszą należeć do tej samej dzierżawy Azure Active Directory (Azure AD). Między dzierżawcami Key Vault i interakcje serwera nie są obsługiwane. Późniejsze przeniesienie zasobów wymaga ponownego skonfigurowania szyfrowania danych.
* Należy włączyć funkcję usuwania nietrwałego w magazynie kluczy, aby chronić przed utratą danych w przypadku, gdy zostanie usunięte przypadkowe wystąpienie (lub Key Vault). Zasoby usunięte nietrwale są przechowywane przez 90 dni, chyba że użytkownik odzyskuje lub przeczyści je w międzyczasie. Akcje odzyskania i przeczyszczania mają własne uprawnienia skojarzone z zasadami dostępu Key Vault. Funkcja usuwania nietrwałego jest domyślnie wyłączona, ale można ją włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure (należy zauważyć, że nie można włączyć jej za pomocą Azure Portal).
* Przyznaj Azure Database for MySQL dostęp do magazynu kluczy za pomocą uprawnień Get, wrapKey i unwrapKey przy użyciu unikatowej tożsamości zarządzanej. W Azure Portal unikatowa tożsamość jest tworzona automatycznie po włączeniu szyfrowania danych w programie MySQL. Szczegółowe instrukcje krok po kroku znajdują Azure Portal się w temacie [Konfigurowanie szyfrowania danych dla programu MySQL](howto-data-encryption-portal.md) .

* W przypadku korzystania z zapory z Key Vault należy włączyć opcję **Zezwalaj zaufanym usługom firmy Microsoft na pomijanie zapory**.

Poniżej przedstawiono wymagania dotyczące konfigurowania klucza zarządzanego przez klienta:

* Klucz zarządzany przez klienta, który ma być używany na potrzeby szyfrowania, może być tylko asymetryczny, RSA 2028.
* Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia (jeśli jest ustawiona) musi być przyszłą datą i godziną.
* Klucz musi być w stanie *włączony* .
* W przypadku importowania istniejącego klucza do magazynu kluczy upewnij się, że jest on udostępniany w obsługiwanych formatach plików (`.pfx`, `.byok`, `.backup`).

## <a name="recommendations"></a>Zalecenia

W przypadku korzystania z szyfrowania danych przy użyciu klucza zarządzanego przez klienta poniżej przedstawiono zalecenia dotyczące konfigurowania Key Vault:

* Ustaw blokadę zasobów Key Vault, aby określić, kto może usunąć ten krytyczny zasób i zapobiegać przypadkowemu lub nieautoryzowanemu usuwaniu.
* Włącz inspekcję i raportowanie dla wszystkich kluczy szyfrowania. Key Vault udostępnia dzienniki, które można łatwo wstrzyknąć do innych informacji o zabezpieczeniach i narzędzi do zarządzania zdarzeniami. Azure Monitor Log Analytics to jeden przykład usługi, która jest już zintegrowana.

* Upewnij się, że Key Vault i Azure Database for MySQL znajdują się w tym samym regionie, aby zapewnić szybszy dostęp do operacji zawijania i odpakowania danych.

Poniżej przedstawiono zalecenia dotyczące konfigurowania klucza zarządzanego przez klienta:

* Przechowuj kopię klucza zarządzanego przez klienta w bezpiecznym miejscu lub wykorzystaj ją w usłudze Escrow.

* Jeśli Key Vault generuje klucz, Utwórz kopię zapasową klucza przed użyciem klucza po raz pierwszy. Możesz przywrócić kopię zapasową tylko do Key Vault. Aby uzyskać więcej informacji na temat polecenia Backup, zobacz [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Niedostępny warunek klucza zarządzanego przez klienta

W przypadku konfigurowania szyfrowania danych przy użyciu klucza zarządzanego przez klienta w programie Key Vault dostęp do tego klucza jest wymagany, aby serwer mógł pozostać w trybie online. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w Key Vault, serwer rozpoczyna odrzucanie wszystkich połączeń w ciągu 10 minut. Serwer wystawia odpowiedni komunikat o błędzie i zmieni stan serwera na *niedostępny*. Jedyną akcją dozwoloną dla bazy danych w tym stanie jest usunięcie jej.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Przypadkowe odwoływanie dostępu do klucza z Key Vault

Może się zdarzyć, że ktoś mający wystarczające prawa dostępu do Key Vault przypadkowo wyłącza dostęp serwera do klucza przez:

* Odwoływanie uprawnień Get, wrapKey i unwrapKey magazynu kluczy z serwera.
* Usuwanie klucza.
* Usuwanie magazynu kluczy.
* Zmiana reguł zapory magazynu kluczy.

* Usuwanie tożsamości zarządzanej serwera w usłudze Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitoruj klucz zarządzany przez klienta w Key Vault

Aby monitorować stan bazy danych i włączyć alerty na potrzeby utraty dostępu do funkcji ochrony za pomocą szyfrowania danych, skonfiguruj następujące funkcje platformy Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): niedostępna baza danych, która utraciła dostęp do klucza klienta, jest wyświetlana jako "niedostępna" po odmowie pierwszego połączenia z bazą danych.
* [Dziennik aktywności](../service-health/alerts-activity-log-service-notifications.md): Jeśli dostęp do klucza klienta w Key Vault zarządzanym przez klienta nie powiedzie się, wpisy zostaną dodane do dziennika aktywności. Można przywrócić dostęp najszybciej, jak to możliwe, w przypadku tworzenia alertów dla tych zdarzeń.

* [Grupy akcji](../azure-monitor/platform/action-groups.md): Zdefiniuj te, aby wysyłać powiadomienia i alerty na podstawie Twoich preferencji.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Przywracanie i replikowanie z kluczem zarządzanym klienta w Key Vault

Gdy Azure Database for MySQL jest szyfrowany przy użyciu klucza zarządzanego przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera zostanie również zaszyfrowana. Tę nową kopię można wykonać za pomocą operacji w trybie lokalnym lub z możliwością przywracania geograficznego albo za pomocą replik odczytu. Jednak kopię można zmienić w celu odzwierciedlenia klucza zarządzanego nowego klienta na potrzeby szyfrowania. Gdy klucz zarządzany przez klienta zostanie zmieniony, stare kopie zapasowe serwera zaczynają korzystać z najnowszego klucza.

Aby uniknąć problemów podczas konfigurowania szyfrowania danych zarządzanych przez klienta podczas przywracania lub tworzenia repliki odczytu, należy wykonać następujące kroki na serwerach głównych i przywróconych/replice:

* Zainicjuj proces przywracania lub odczytywania repliki z głównego Azure Database for MySQL.
* Zachowaj nowo utworzony serwer (przywrócony/Replica) w stanie niedostępnym, ponieważ jego unikatowa tożsamość nie otrzymała jeszcze uprawnień do Key Vault.
* Na serwerze przywrócenia/repliki ponownie sprawdź poprawność klucza zarządzanego przez klienta w ustawieniach szyfrowania danych. Gwarantuje to, że nowo utworzony serwer ma uprawnienia zawijania i odwinięcia klucza przechowywanego w Key Vault.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak skonfigurować szyfrowanie danych za pomocą klucza zarządzanego przez klienta dla usługi Azure Database for MySQL przy użyciu Azure Portal](howto-data-encryption-portal.md).
