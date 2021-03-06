---
title: Korzystanie z zaawansowanej ochrony przed zagrożeniami — Azure Database for PostgreSQL — pojedynczy serwer
description: Ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8b7f52ea318432e97a450a54526f6481b14139c9
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776147"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Zaawansowana ochrona przed zagrożeniami dla Azure Database for PostgreSQL-pojedynczego serwera

Usługa Advanced Threat Protection dla usługi Azure Database for PostgreSQL wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

Zaawansowana ochrona przed zagrożeniami jest częścią zaawansowanej oferty zabezpieczeń danych, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem [Azure Portal](https://portal.azure.com) i jest obecnie dostępny w wersji zapoznawczej.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach na platformie Azure dla instytucji rządowych i w ramach suwerennej chmury: US Gov Teksas, US Gov Arizona, US gov Iowa, US, gov wirginia, US DoD (region wschodni), US dod (region środkowy), Niemcy środkowe, Niemcy północne, Chiny Wschodnie, Chiny Wschodnie 2. Odwiedź stronę [dostępne produkty według regionów,](https://azure.microsoft.com/global-infrastructure/services/) Aby uzyskać ogólną dostępność produktu.
>

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w których wdrożono Azure Database for PostgreSQL dla serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="set-up-threat-detection"></a>Konfigurowanie wykrywania zagrożeń
1. Uruchom Azure Portal w [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera Azure Database for PostgreSQL, który ma być chroniony. W obszarze Ustawienia zabezpieczeń wybierz pozycję **Zaawansowana ochrona przed zagrożeniami (wersja zapoznawcza)** .
3. Na stronie Konfiguracja **zaawansowanej ochrony przed zagrożeniami (wersja zapoznawcza)** :

   - Włącz zaawansowaną ochronę przed zagrożeniami na serwerze.
   - W obszarze **Ustawienia zaawansowanej ochrony przed zagrożeniami**w polu tekstowym **Wyślij alerty do** wprowadź listę wiadomości e-mail, które mają otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Zbadaj nietypowe działania bazy danych

Po wykryciu nietypowych działań bazy danych otrzymasz powiadomienie e-mail. Wiadomość e-mail zawiera informacje dotyczące podejrzanego zdarzenia zabezpieczeń, w tym charakter nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia dla bazy danych.
    
1. Kliknij link **Wyświetl Ostatnie alerty** w wiadomości e-mail, aby uruchomić Azure Portal i wyświetlić stronę Azure Security Center alertów, która zawiera przegląd aktywnych zagrożeń wykrytych w bazie danych SQL.
    
    ![Raport o nietypowych działaniach](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Wyświetl aktywne zagrożenia:

    ![Aktywne zagrożenia](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknij konkretny alert, aby uzyskać dodatkowe szczegóły i akcje związane z badaniem tego zagrożenia i korygowaniem przyszłe zagrożenia.
    
    ![Konkretny alert](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Eksploruj alerty wykrywania zagrożeń

Zaawansowana ochrona przed zagrożeniami integruje swoje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Kliknij pozycję **alerty zabezpieczeń** w obszarze **Ochrona przed zagrożeniami** , aby uruchomić stronę alerty Azure Security Center i zapoznać się z omówieniem aktywnych zagrożeń SQL wykrytych w bazie danych.

  ![Ochrona przed zagrożeniami ASC](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) .  
