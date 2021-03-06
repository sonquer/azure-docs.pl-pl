---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 0a4faad8bfe92a5389e0ee440aa3ccc404535955
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118104"
---
Przejdź do witryny Azure Portal i [Utwórz nowy zasób aparatu rozpoznawania formularzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer). W okienku **Tworzenie** podaj następujące informacje:

|    |    |
|--|--|
| **Nazwa** | Opisowa nazwa zasobu. Zalecamy użycie nazwy opisowej, na przykład *MyNameFormRecognizer*. |
| **Subskrypcja** | Wybierz subskrypcję platformy Azure, której udzielono dostępu. |
| **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
| **Warstwa cenowa** | Koszt zasobu zależy od wybranej warstwy cenowej i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
| **Grupa zasobów** | [Grupa zasobów platformy Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , która będzie zawierać zasób. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

> [!IMPORTANT]
> Zwykle podczas tworzenia zasobu usługi poznawczej w Azure Portal można utworzyć klucz subskrypcji dla wielu usług (używany przez wiele usług poznawczych) lub klucz subskrypcji jednej usługi (używany tylko w przypadku konkretnej usługi poznawczej). Jednak ponieważ aparat rozpoznawania formularzy jest wersją zapoznawczą, nie jest uwzględniony w subskrypcji usługi MFA i nie można utworzyć subskrypcji pojedynczej usługi, chyba że zostanie użyty link podany w powitalnej wiadomości e-mail.

Po zakończeniu wdrażania zasobu aparatu rozpoznawania formularzy Znajdź i wybierz go z listy **wszystkie zasoby** w portalu. Następnie wybierz kartę **Szybki Start** , aby wyświetlić dane dotyczące subskrypcji. Zapisz wartości **Klucz1** i **Endpoint** w tymczasowej lokalizacji. Zostaną one użyte w poniższych krokach.