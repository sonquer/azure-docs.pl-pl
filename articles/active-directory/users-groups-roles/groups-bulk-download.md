---
title: Pobierz listę grup w portalu Azure Active Directory | Microsoft Docs
description: Pobierz zbiorczo właściwości grupy w centrum administracyjnym platformy Azure w Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517138"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Pobierz zbiorczo listę grup (wersja zapoznawcza) w Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można zbiorczo pobrać listę wszystkich grup w organizacji do pliku wartości rozdzielanych przecinkami (CSV).

## <a name="to-download-a-list-of-groups"></a>Aby pobrać listę grup

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora w organizacji.
1. W usłudze Azure AD wybierz pozycję **grupy**  > **grupy pobierania**.
1. Na stronie **pobieranie grup** wybierz pozycję **Rozpocznij** , aby otrzymać plik CSV z listą Twoich grup.

   ![Polecenie Pobierz grupy znajduje się na stronie wszystkie grupy](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Sprawdź stan pobierania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Na stronie wyniki operacji zbiorczych jest wyświetlany stan zbiorczego żądania](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze do pobrania listy grup można uruchomić przez maksymalnie godzinę. Pozwala to na pobranie listy co najmniej 300 000 grup.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
