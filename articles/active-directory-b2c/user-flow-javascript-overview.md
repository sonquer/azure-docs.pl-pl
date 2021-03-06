---
title: Wersje języka JavaScript i układu strony
titleSuffix: Azure AD B2C
description: Informacje o sposobie włączania języka JavaScript i używania wersji układu strony w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 78fc3af10bde5e9dd25d02f7a21d77e958b15190
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149528"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Wersje języka JavaScript i układu strony w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C zawiera zestaw spakowanych zawartości zawierający kod HTML, CSS i JavaScript dla elementów interfejsu użytkownika w przepływach użytkownika i zasadach niestandardowych.

Aby włączyć język JavaScript dla aplikacji:

* Włącz ją w przepływie użytkownika przy użyciu Azure Portal
* Wybierz [Układ strony](page-layout.md)
* Używanie [b2clogin.com](b2clogin.md) w żądaniach

Jeśli zamierzasz włączyć kod po stronie klienta [JavaScript](javascript-samples.md) , elementy, które opierają się na języku JavaScript, muszą być niezmienne. Jeśli nie są one niezmienne, wszelkie zmiany mogą spowodować nieoczekiwane zachowanie na stronach użytkownika. Aby uniknąć tych problemów, Wymuś użycie układu strony i określ wersję układu strony, aby upewnić się, że definicje zawartości opartej na języku JavaScript są niezmienne. Nawet jeśli nie zamierzasz włączać języka JavaScript, możesz określić wersję układu strony dla stron.

## <a name="enable-javascript"></a>Włączanie języka JavaScript

We **właściwościach**przepływu użytkownika można włączyć język JavaScript. Włączenie języka JavaScript wymusza również użycie układu strony. Następnie można ustawić wersję układu strony dla przepływu użytkownika zgodnie z opisem w następnej sekcji.

![Strona właściwości przepływu użytkownika z wyróżnionym ustawieniem Włącz język JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Wybierz wersję układu strony

Niezależnie od tego, czy w obszarze właściwości przepływu użytkownika jest włączone JavaScript, możesz określić wersję układu strony dla stron przepływu użytkownika. Otwórz przepływ użytkownika i wybierz pozycję **układy strony**. W obszarze **Nazwa układu**wybierz stronę przepływ użytkownika i wybierz **wersję układu strony**.

Informacje o różnych wersjach układów stron można znaleźć w [dzienniku zmian wersji układu strony](page-layout.md).

![Ustawienia układu strony w portalu pokazujące listę rozwijaną wersji układu strony](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Następne kroki

Przykłady użycia języka JavaScript można znaleźć w przykładach [języka JavaScript do użycia w Azure Active Directory B2C](javascript-samples.md).
