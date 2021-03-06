---
title: Grupuj maszyny do oceny przy użyciu Azure Migrate | Microsoft Docs
description: Opisuje sposób grupowania maszyn przed uruchomieniem oceny w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68301706"
---
# <a name="create-a-group-for-assessment"></a>Tworzenie grupy do oceny

W tym artykule opisano sposób tworzenia grup maszyn do oceny przy użyciu Azure Migrate: Ocena serwera.

[Azure Migrate](migrate-services-overview.md) ułatwia Migrowanie do platformy Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

## <a name="grouping-machines"></a>Grupowanie maszyn

Należy zebrać maszyny do grup, aby ocenić, czy są one odpowiednie do migracji na platformę Azure, i uzyskać dla nich szacunki dotyczące rozmiarów i kosztów platformy Azure. Istnieje kilka sposobów tworzenia grup:

- W przypadku znajomości maszyn, które muszą zostać zmigrowane wspólnie, można ręcznie utworzyć grupę w Azure Migrate.
- Jeśli nie masz pewności o maszynach, które muszą być zgrupowane razem, możesz użyć funkcji wizualizacji zależności w Azure Migrate, aby utworzyć grupy. 

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="create-a-group-manually"></a>Ręczne tworzenie grupy

Można utworzyć grupę w tym samym czasie, w którym można [utworzyć ocenę](how-to-create-assessment.md).

Jeśli chcesz utworzyć grupę ręcznie poza tworzeniem oceny, wykonaj następujące czynności:

1. W > projekcie Azure Migrate **Przegląd**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**. W **Azure Migrate: Ocena**serwera, kliknij przycisk **grupy**
    - Jeśli nie dodano jeszcze Azure Migrate: Narzędzie do oceny serwera, kliknij, aby je dodać. [Dowiedz się więcej](how-to-assess.md).
    - Jeśli projekt Azure Migrate nie został jeszcze utworzony, [Dowiedz się więcej](how-to-add-tool-first-time.md).

    ![Wybieranie grup](./media/how-to-create-a-group/select-groups.png)

2. Kliknij ikonę **grupy** .
3. W obszarze **Tworzenie grupy**Określ nazwę grupy, a w polu **Nazwa urządzenia**wybierz urządzenie Azure Migrate, którego używasz do odnajdywania maszyn.
1. Z listy maszyna wybierz maszyny, które chcesz dodać do grupy > **utworzyć**.

    ![Utwórz grupę](./media/how-to-create-a-group/create-group.png)

Tej grupy można teraz używać podczas [tworzenia oceny](how-to-create-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Uściślij grupę z mapowaniem zależności

Mapowanie zależności ułatwia wizualizację zależności między maszynami. Mapowanie zależności jest zazwyczaj stosowane, gdy chcesz ocenić grupy maszyn o wyższym poziomie zaufania.
- Ułatwia ona sprawdzanie zależności między maszynami, przed uruchomieniem oceny. 
- Ułatwia ona również efektywne planowanie migracji na platformę Azure, zapewniając, że nic nie pozostało w tle i w ten sposób unikając awarii podczas migracji.
- Można odkrywać systemy zależne, które muszą być migrowane razem, oraz sprawdzić, czy uruchomiony system nadal obsługuje użytkowników, czy też jest kandydatem do likwidowania zamiast migracji.

Jeśli skonfigurowano już [Mapowanie zależności](how-to-create-group-machine-dependencies.md)i chcesz udoskonalić istniejącą grupę, wykonaj następujące czynności:

1. Na karcie **serwery** w **Azure Migrate: Kafelek Ocena** serwera, kliknij przycisk **grupy**.
2. Kliknij grupę, którą chcesz udoskonalić.
    - Jeśli nie skonfigurowano jeszcze mapowania zależności, w kolumnie **zależności** zostanie wyświetlony stan **wymaga instalacji** . Dla każdej maszyny wirtualnej, dla której chcesz wizualizować zależności, kliknij pozycję **wymaga instalacji**. Przed zamapowaniem zależności maszyn można zainstalować kilku agentów na każdej maszynie wirtualnej. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md).

        ![Dodaj mapowanie zależności](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Jeśli skonfigurowano już mapowanie zależności, na stronie Grupa kliknij pozycję **Wyświetl zależności** , aby otworzyć mapę zależności grupy.

3. Po kliknięciu pozycji **Wyświetl zależności**Mapa zależności grupy pokazuje następujące elementy:

    - Ruch przychodzący (klientów) i ruch wychodzący (serwery) TCP do i ze wszystkich maszyn w grupie, w których zainstalowano agentów zależności.
    - Maszyny zależne, na których nie zainstalowano agentów zależności, są pogrupowane według numerów portów.
    - Komputery zależne z zainstalowanymi agentami zależności są wyświetlane jako oddzielne pola.
    - Procesy działające na maszynie. Rozwiń wszystkie pola maszyn, aby wyświetlić procesy.
    - Właściwości maszyny (w tym nazwa FQDN, system operacyjny, adres MAC). Kliknij każde pole komputera, aby wyświetlić szczegóły.

4. Aby wyświetlić zależności w przedziale czasu, należy zmodyfikować zakres czasu (domyślnie godzinę), określając daty rozpoczęcia i zakończenia lub czas trwania.

    > [!NOTE]
    > Zakres czasu może być maksymalnie godzinę. Jeśli potrzebujesz dłuższego zakresu, użyj [Azure monitor do wykonywania zapytań dotyczących danych zależnych](how-to-create-group-machine-dependencies.md) przez dłuższy czas.

5. Po zidentyfikowaniu zależności, które chcesz dodać do grupy lub usunąć z niej, można zmodyfikować grupę. Naciśnij klawisz Ctrl i kliknij, aby dodać lub usunąć maszyny z grupy.

    - Można dodawać tylko odnalezione maszyny.
    - Dodawanie i usuwanie maszyn unieważnia przeszłe oceny dla grupy.
    - Opcjonalnie można utworzyć nową ocenę podczas modyfikowania grupy.


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować [Mapowanie zależności](how-to-create-group-machine-dependencies.md) i używać ich do tworzenia grup o wysokim poziomie pewności.

