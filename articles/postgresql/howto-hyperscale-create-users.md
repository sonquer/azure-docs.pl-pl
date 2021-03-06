---
title: Tworzenie użytkowników-skalowanie (Citus) — Azure Database for PostgreSQL
description: W tym artykule opisano, jak można tworzyć nowe konta użytkowników w celu współdziałania z Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484931"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie użytkowników w Azure Database for PostgreSQL-Citus

> [!NOTE]
> Termin "Użytkownicy" odnosi się do użytkowników w grupie serwerów wieloskalowania (Citus). Aby dowiedzieć się więcej o użytkownikach subskrypcji platformy Azure i ich uprawnieniach, odwiedź [artykuł kontroli dostępu opartej na rolach (RBAC) platformy Azure](../role-based-access-control/built-in-roles.md) lub zapoznaj [się z tematem Dostosowywanie ról](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Konto administratora serwera

Aparat PostgreSQL używa [ról](https://www.postgresql.org/docs/current/sql-createrole.html) w celu kontrolowania dostępu do obiektów bazy danych, a nowo utworzona grupa serwerów w postaci Citusej zawiera kilka wstępnie zdefiniowanych ról:

* [Domyślne role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Ze względu na to, że jest to zarządzana usługa PaaS, tylko firma Microsoft może zalogować się przy użyciu roli administratora `postgres`. W przypadku ograniczonego dostępu administracyjnego funkcja przedskalowania zapewnia rolę `citus`.

Uprawnienia dla roli `citus`:

* Odczytaj wszystkie zmienne konfiguracyjne, nawet zmienne zwykle widoczne tylko dla użytkowników.
* Przeczytaj wszystkie PG\_stat\_\* widoki i Używaj różnych rozszerzeń związanych z statystyką — nawet widoki lub rozszerzenia są zwykle widoczne tylko dla użytkowników.
* Wykonywanie funkcji monitorowania, które mogą mieć dostęp do blokad udostępniania w tabelach, potencjalnie przez długi czas.
* [Utwórz rozszerzenia PostgreSQL](concepts-hyperscale-extensions.md) (ponieważ rola jest członkiem `azure_pg_admin`).

W szczególności rola `citus` ma pewne ograniczenia:

* Nie można utworzyć ról
* Nie można utworzyć baz danych

## <a name="how-to-create-additional-user-roles"></a>Jak utworzyć dodatkowe role użytkowników

Jak wspomniano wcześniej, konto administratora `citus` nie ma uprawnień do tworzenia dodatkowych użytkowników. Aby dodać użytkownika, użyj interfejsu Azure Portal.

1. Przejdź do strony **role** dla swojej grupy serwerów w ramach skalowania i kliknij pozycję **+ Dodaj**:

   ![Strona role](media/howto-hyperscale-create-users/1-role-page.png)

2. Wprowadź nazwę i hasło roli. Kliknij przycisk **Save** (Zapisz).

   ![Dodaj rolę](media/howto-hyperscale-create-users/2-add-user-fields.png)

Użytkownik zostanie utworzony w węźle koordynatora grupy serwerów i rozpropagowany do wszystkich węzłów procesu roboczego. Role utworzone za pośrednictwem Azure Portal mają atrybut `LOGIN`, co oznacza, że są one prawdziwe użytkownikom, którzy mogą logować się do bazy danych.

## <a name="how-to-modify-privileges-for-user-role"></a>Jak zmodyfikować uprawnienia dla roli użytkownika

Nowe role użytkowników są często używane do zapewniania dostępu do bazy danych z ograniczonymi uprawnieniami. Aby zmodyfikować uprawnienia użytkownika, użyj standardowych poleceń PostgreSQL przy użyciu narzędzia, takiego jak PgAdmin lub PSQL. (Zobacz [nawiązywanie połączenia z usługą PSQL](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) na stronie Szybki Start skalowania (Citus).

Na przykład, aby zezwolić `db_user` na odczytywanie `mytable`, Udziel uprawnienia:

```sql
GRANT SELECT ON mytable TO db_user;
```

Funkcja wieloskal (Citus) propaguje instrukcje GRANTu jednej tabeli w całym klastrze, stosując je we wszystkich węzłach procesu roboczego. Jednak dotacje dla całego systemu (na przykład dla wszystkich tabel w schemacie) muszą być uruchamiane w każdym węźle daty.  Użyj `run_command_on_workers()` funkcji pomocnika:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Jak usunąć rolę użytkownika lub zmienić hasło

Aby zaktualizować użytkownika, odwiedź stronę **role** dla swojej grupy serwerów w skali i kliknij wielokropek **...** obok użytkownika. Wielokropek spowoduje otwarcie menu w celu usunięcia użytkownika lub zresetowania hasła.

   ![Edytowanie roli](media/howto-hyperscale-create-users/edit-role.png)

Rola `citus` jest uprzywilejowana i nie można jej usunąć.

## <a name="next-steps"></a>Następne kroki

Otwórz Zaporę dla adresów IP nowych maszyn użytkowników, aby umożliwić im łączenie: [Utwórz reguły zapory Citus (i zarządzaj nimi) przy użyciu Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników bazy danych, zobacz dokumentację produktu PostgreSQL:

* [Role i uprawnienia bazy danych](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Składnia GRANTu](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html)
