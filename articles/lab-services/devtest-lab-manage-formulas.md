---
title: Zarządzanie formułami w Azure DevTest Labs tworzenia maszyn wirtualnych | Microsoft Docs
description: W tym artykule przedstawiono sposób tworzenia formuły z poziomu bazy (obrazu niestandardowego, obrazu witryny Marketplace lub innej formuły) lub istniejącej maszyny wirtualnej.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a668c1f7cf401c109c1041232d7f28dd2accd750
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760406"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Zarządzanie formułami Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

W tym artykule przedstawiono sposób tworzenia formuły z poziomu bazy (obrazu niestandardowego, obrazu witryny Marketplace lub innej formuły) lub istniejącej maszyny wirtualnej. Ten artykuł prowadzi także przez użytkownika przez zarządzanie istniejącymi formułami.

## <a name="create-a-formula"></a>Utwórz formułę
Każda osoba mająca uprawnienia *użytkowników* DevTest Labs jest w stanie tworzyć maszyny wirtualne przy użyciu formuły jako podstawy. Istnieją dwa sposoby tworzenia formuł: 

* Z użycia podstawowego, gdy chcesz zdefiniować wszystkie cechy formuły.
* Z istniejącej maszyny wirtualnej laboratorium — Użyj, jeśli chcesz utworzyć formułę opartą na ustawieniach istniejącej maszyny wirtualnej.

Aby uzyskać więcej informacji na temat dodawania użytkowników i uprawnień, zobacz [Dodawanie właścicieli i użytkowników w Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Utwórz formułę z bazy
Poniższe kroki przeprowadzą Cię przez proces tworzenia formuły z obrazu niestandardowego, obrazu z witryny Marketplace lub innej formuły.

1. Zaloguj się do [portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .

3. Z listy laboratoriów wybierz odpowiednie laboratorium.  

4. Na stronie laboratorium wybierz pozycję **formuły (baza wielokrotnego użytku)** .
   
    ![Menu formuły](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na stronie **formuły** wybierz pozycję **+ Dodaj**.
   
    ![Dodawanie formuły](./media/devtest-lab-create-formulas/add-formula.png)

6. Na stronie **Wybierz bazę** wybierz podstawową (obraz niestandardowy, obraz witryny Marketplace lub formułę), z której ma zostać utworzona formuła.
   
    ![Lista podstawowa](./media/devtest-lab-create-formulas/base-list.png)

7. Na karcie **Ustawienia podstawowe** na stronie **Tworzenie formuły** określ następujące wartości:
   
    * **Nazwa formuły** — wprowadź nazwę formuły. Ta wartość jest wyświetlana na liście obrazów podstawowych podczas tworzenia maszyny wirtualnej. Nazwa jest sprawdzana podczas wpisywania, a jeśli nie jest prawidłowa, komunikat wskazuje wymagania dotyczące prawidłowej nazwy.
    * **Nazwa użytkownika** — wprowadź nazwę użytkownika, który ma przyznane uprawnienia administratora.
    * **Hasło** — wprowadź lub wybierz pozycję z listy rozwijanej — wartość skojarzona z wpisem tajnym (hasło), która ma być używana dla określonego użytkownika. Aby dowiedzieć się więcej na temat zapisywania wpisów tajnych w magazynie kluczy i korzystania z nich podczas tworzenia zasobów laboratorium, zobacz [zapisywanie wpisów tajnych w Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **Rozmiar maszyny wirtualnej** — wybierz **Zmień rozmiar** , aby zmienić rozmiar maszyny wirtualnej. 
    * **Artefakty** — wybierz opcję **Dodaj lub Usuń artefakty** , w której wybierasz i skonfigurujesz artefakty, które chcesz dodać do obrazu podstawowego. Aby uzyskać więcej informacji na temat artefaktów, zobacz [Tworzenie niestandardowych artefaktów dla Azure DevTest Labs maszyny wirtualnej](devtest-lab-artifact-author.md).
8. Przejdź do karty **Ustawienia zaawansowane** i określ następujące wartości:
    - **Sieć wirtualna** — aby zmienić sieć wirtualną, wybierz pozycję **Zmień wirtualną**. 
    - **Podsieć** — aby zmienić podsieć, wybierz pozycję **Zmień podsieć**. 
    - **Konfiguracja adresu IP** — Określ, czy chcesz, aby publiczne, prywatne lub udostępnione adresy IP. Aby uzyskać więcej informacji na temat udostępnionych adresów IP, zobacz [Omówienie udostępnionych adresów IP w Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Data i godzina wygaśnięcia** — Określ datę i godzinę wygaśnięcia maszyny wirtualnej, aby maszyna wirtualna została automatycznie usunięta. 
    - **Ustaw tę maszynę** jako dodające się do dołączenia, co oznacza, że ta maszyna nie zostanie przypisana do momentu utworzenia. W przypadku, gdy użytkownicy w laboratorium będą mogli przejmować na stronie laboratorium na własność ("Claim") maszynę.     
    - **Liczba wystąpień z** możliwością zgłoszenia — Określ, ile wystąpień, które chcesz utworzyć. 
8. Wybierz pozycję **Prześlij** , aby utworzyć formułę.

9. Gdy formuła została utworzona, zostanie wyświetlona na liście na stronie **formuły** .

### <a name="create-a-formula-from-a-vm"></a>Tworzenie formuły na podstawie maszyny wirtualnej
Poniższe kroki przeprowadzą Cię przez proces tworzenia formuły opartej na istniejącej maszynie wirtualnej. 

> [!NOTE]
> Aby można było utworzyć formułę z maszyny wirtualnej, maszyna wirtualna musi zostać utworzona po 30 marca 2016. 
> 
> 

1. Zaloguj się do [portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz odpowiednie laboratorium.  
4. Na stronie **Przegląd** laboratorium wybierz maszynę wirtualną, z której chcesz utworzyć formułę.
   
    ![Maszyny wirtualne laboratoria](./media/devtest-lab-create-formulas/my-vms.png)
5. Na stronie maszyny wirtualnej wybierz pozycję **Utwórz formułę (baza wielokrotnego użytku)** .
   
    ![Utwórz formułę](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na stronie **Utwórz formułę** wprowadź **nazwę** i **Opis** nowej formuły.
   
    ![Utwórz stronę formuły](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Wybierz **przycisk OK** , aby utworzyć formułę.

## <a name="modify-a-formula"></a>Modyfikowanie formuły
Aby zmodyfikować formułę, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz odpowiednie laboratorium.  
4. Na stronie laboratorium wybierz pozycję **formuły (baza wielokrotnego użytku)** .
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stronie **formuły laboratoryjne** wybierz formułę, którą chcesz zmodyfikować.
6. Na stronie **Aktualizuj formułę** wprowadź wymagane zmiany, a następnie wybierz pozycję **Aktualizuj**.

## <a name="delete-a-formula"></a>Usuwanie formuły
Aby usunąć formułę, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz odpowiednie laboratorium.  
4. Na stronie **Ustawienia** laboratorium wybierz pozycję **formuły**.
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stronie **formuły laboratoryjne** wybierz wielokropek z prawej strony formuły, którą chcesz usunąć.
   
    ![Menu formuły](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. W menu kontekstowym formuły wybierz pozycję **Usuń**.
   
    ![Menu kontekstowe formuły](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Wybierz pozycję **tak** w oknie dialogowym potwierdzenia usunięcia.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Powiązane wpisy w blogu
* [Obrazy niestandardowe lub formuły?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Następne kroki
Po utworzeniu formuły do użycia podczas tworzenia maszyny wirtualnej następnym krokiem jest [dodanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md).

