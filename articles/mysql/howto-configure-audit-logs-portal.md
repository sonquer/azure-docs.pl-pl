---
title: Dzienniki inspekcji dostępu — Azure Portal — Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników inspekcji w programie Azure Database for MySQL z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: ff1a6c63b6eb99acdef955806a138e3e22b8902a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773714"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Skonfiguruj i uzyskaj dostęp do dzienników inspekcji dla Azure Database for MySQL w Azure Portal

Można skonfigurować [Azure Database for MySQL dzienników inspekcji](concepts-audit-logs.md) i ustawień diagnostycznych z Azure Portal.

> [!IMPORTANT]
> Funkcje dziennika inspekcji są obecnie dostępne w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby krokowo poprowadzić ten przewodnik, musisz:

- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

Włącz i skonfiguruj rejestrowanie inspekcji.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

1. Wybierz serwer Azure Database for MySQL.

1. W sekcji **Ustawienia** na pasku bocznym wybierz opcję **parametry serwera**.
    ![Parametry serwera](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Zaktualizuj parametr **audit_log_enabled** na wartość on.
    ![włączyć dzienników inspekcji](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Wybierz [typy zdarzeń](concepts-audit-logs.md#configure-audit-logging) , które mają być rejestrowane, aktualizując parametr **audit_log_events** .
    ![](./media/howto-configure-audit-logs-portal/audit-log-events.png) zdarzeń dziennika inspekcji

1. Dodaj wszystkich użytkowników programu MySQL, które mają zostać wykluczone z rejestrowania przez zaktualizowanie parametru **audit_log_exclude_users** . Określ użytkowników, podając ich nazwę użytkownika MySQL.
    Dziennik inspekcji ![Wyklucz użytkowników](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Po zmianie parametrów możesz kliknąć przycisk **Zapisz**. Możesz też **odrzucić** zmiany.
    ![Zapisz](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**.

1. Kliknij pozycję "+ Dodaj ustawienie diagnostyczne" ![Dodaj ustawienia diagnostyczne](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które ujścia danych mają wysyłać dzienniki inspekcji (konto magazynu, centrum zdarzeń i/lub Log Analytics obszar roboczy).

1. Wybierz pozycję "MySqlAuditLogs" jako typ dziennika.
![skonfigurować ustawienia diagnostyczne](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Po skonfigurowaniu ujścia danych do potoków dzienników inspekcji do programu można kliknąć przycisk **Zapisz**.
![zapisać ustawienia diagnostycznego](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Uzyskaj dostęp do dzienników inspekcji, przepoznając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md) w Azure Database for MySQL.