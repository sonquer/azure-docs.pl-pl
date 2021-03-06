---
title: Oferta publikowania Azure IoT Edge modułu | Portal Azure Marketplace
description: Jak opublikować ofertę modułu IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813831"
---
# <a name="publish-iot-edge-module-offer"></a>Publikowanie oferty modułu usługi IoT Edge

 Po utworzeniu nowej oferty, podając informacje na **nowej stronie oferty** , można opublikować ofertę. Wybierz pozycję **Publikuj** , aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania dla oferty "go Live".

![Kroki publikowania dla oferty modułu IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikowania, z oszacowaniem czasu (maksimum) w celu ukończenia każdego kroku.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etap publikowania**           | **Pierwszym**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie wymagań wstępnych         | 15 minut   | Informacje o ofercie i ustawienia oferty są weryfikowane.                        |
| Certyfikacja                  | 2 tygodnie | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Ten krok spowoduje wykonanie skanowania w poszukiwaniu wirusów, złośliwego oprogramowania, zgodności z bezpieczeństwem i problemów z zabezpieczeniami. Zostanie również zweryfikowana, że ta oferta modułu IoT Edge spełnia wszystkie kryteria kwalifikujące (zobacz [wymagania wstępne](./cpp-prerequisites.md) i [przygotowania zasobów technicznych](./cpp-create-technical-assets.md)). Opinie są podawane w przypadku znalezienia problemu. |
| Tworzenie pakietów | 1 godzina  | Zasoby techniczne oferty są pakowane do użytku przez klienta, a systemy liderów są skonfigurowane i konfigurowane. |
|  Wylogowanie wydawcy             |  -        | Końcowe przeglądy i potwierdzenie wydawcy przed rozpoczęciem oferty. Ofertę można wdrożyć w wybranych subskrypcjach (w krokach dotyczących informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie wymagania.  Wybierz pozycję **Przejdź na żywo** , aby Twoja oferta mogła przejść do następnego kroku. |
| Tworzenie pakietów                 | 1 godzina | Końcowa oferta jest replikowana w systemach i regionach produkcyjnych w portalu Marketplace. | 
| Na żywo                           | 4 dni |Oferta jest wydana, replikowana do wymaganych regionów i udostępniona publicznie. |

Zezwalaj na zakończenie procesu publikowania przez maksymalnie 10 dni roboczych i oferowana jest oferta. Po zakończeniu procesu publikowania w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)zostanie wyświetlona oferta modułu IoT Edge.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty modułu IoT Edge w portalu Azure Marketplace](./cpp-update-existing-offer.md)
