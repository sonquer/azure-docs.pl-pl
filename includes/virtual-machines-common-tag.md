---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182257"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Tagowanie maszyny wirtualnej za pomocą szablonów
Najpierw przyjrzyjmy się znakowaniu za poorednictwem szablonów. [Ten szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umieszcza znaczniki w następujących zasobach: COMPUTE (maszyna wirtualna), Storage (konto magazynu) i sieć (publiczny adres IP, Virtual Network i interfejs sieciowy). Ten szablon jest przeznaczony dla maszyny wirtualnej z systemem Windows, ale można go dostosować do maszyn wirtualnych z systemem Linux.

Kliknij przycisk **Wdróż na platformie Azure** w [linku szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Spowoduje to przejście do [Azure Portal](https://portal.azure.com/) , w którym można wdrożyć ten szablon.

![Proste wdrożenie ze znacznikami](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ten szablon zawiera następujące znaczniki: *dział*, *aplikacja*i *utworzone przez*. Możesz dodawać lub edytować te Tagi bezpośrednio w szablonie, jeśli chcesz zmienić nazwy tagów.

![Tagi platformy Azure w szablonie](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Jak widać, znaczniki są definiowane jako pary klucz/wartość, oddzielone dwukropkiem (:). Tagi muszą być zdefiniowane w tym formacie:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Zapisz plik szablonu po zakończeniu edycji przy użyciu wybranych przez Ciebie tagów.

Następnie w sekcji **Edytuj parametry** można wypełnić wartości dla tagów.

![Edytuj Tagi w Azure Portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kliknij przycisk **Utwórz** , aby wdrożyć ten szablon z wartościami tagów.

## <a name="tagging-through-the-portal"></a>Tagowanie za pomocą portalu
Po utworzeniu zasobów przy użyciu tagów można wyświetlać, dodawać i usuwać Tagi w portalu.

Wybierz ikonę tagów, aby wyświetlić Tagi:

![Ikona tagów w Azure Portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Dodaj nowy tag za pomocą portalu, definiując własną parę klucz/wartość i Zapisz go.

![Dodaj nowy tag w Azure Portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Nowy tag powinien teraz pojawić się na liście tagów dla zasobu.

![Nowy tag zapisany w Azure Portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

