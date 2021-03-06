---
title: Microsoft Azure Data Box Gateway dostępu do urządzenia, możliwości i trybu łączności | Microsoft Docs
description: Zawiera opis sposobu zarządzania dostępem, możliwością i trybem łączności dla urządzenia Azure Data Box Gateway, które ułatwia transfer danych do platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 38662fc8dda935d5f000aee6609fd9b2e42de17f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253176"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Zarządzanie dostępem, możliwością i trybem łączności dla Azure Data Box Gateway

W tym artykule opisano sposób zarządzania dostępem, możliwością i trybem łączności dla Azure Data Box Gateway. Te operacje są wykonywane za pośrednictwem lokalnego interfejsu użytkownika sieci Web lub Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do urządzeń
> * Zarządzanie trybem łączności
> * Zarządzanie mocą

## <a name="manage-device-access"></a>Zarządzanie dostępem do urządzeń

Dostęp do urządzenia Data Box Gateway jest kontrolowany przy użyciu hasła urządzenia. Hasło można zmienić za pomocą lokalnego interfejsu użytkownika sieci Web. Możesz również zresetować hasło urządzenia w Azure Portal.

### <a name="change-device-password"></a>Zmienianie hasła urządzenia

Wykonaj następujące kroki w lokalnym interfejsie użytkownika, aby zmienić hasło urządzenia.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja, > zmienić hasła**.
2. Wprowadź bieżące hasło, a następnie nowe hasło. Podane hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Potwierdź nowe hasło.

    ![Zmień hasło](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kliknij pozycję **Zmień hasło**.
 
### <a name="reset-device-password"></a>Resetowanie hasła urządzenia

Resetowanie przepływu pracy nie wymaga od użytkownika odwoływania starego hasła i jest przydatne, gdy hasło zostanie utracone. Ten przepływ pracy jest wykonywany w Azure Portal.

1. W Azure Portal przejdź do **omówienia > Resetowanie hasła administratora**.

    ![Resetowanie hasła](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Wprowadź nowe hasło, a następnie potwierdź je. Podane hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Kliknij przycisk **Resetuj**.

    ![Resetowanie hasła](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Zarządzanie dostępem do zasobów

Aby utworzyć zasób Data Box Edge/Data Box Gateway, IoT Hub i zasobów usługi Azure Storage, musisz mieć uprawnienia jako współautor lub wyższy na poziomie grupy zasobów. Wymagane są także odpowiednie dostawcy zasobów do zarejestrowania. W przypadku wszelkich operacji, które obejmują klucz aktywacji i poświadczenia, wymagane są również uprawnienia do Azure Active Directory interfejs API programu Graph. Opisano je w poniższych sekcjach.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Zarządzanie uprawnieniami interfejsu API Microsoft Azure Active Directory Graph

Podczas generowania klucza aktywacji dla urządzenia Data Box Edge lub wykonywania żadnych operacji wymagających poświadczeń wymagane są uprawnienia do Azure Active Directory interfejs API programu Graph. Operacje, które wymagają poświadczeń, mogą być następujące:

-  Tworzenie udziału ze skojarzonym kontem magazynu.
-  Tworzenie użytkownika, który może uzyskiwać dostęp do udziałów na urządzeniu.

Musisz mieć `User` dostęp do Active Directory dzierżawy, ponieważ musisz mieć `Read all directory objects`możliwość. Nie możesz być użytkownikiem-gościem, ponieważ nie ma uprawnień `Read all directory objects`do programu. Jeśli jesteś gościem, niektóre operacje, takie jak generowanie klucza aktywacji, tworzenie udziału na urządzeniu Data Box Edge i tworzenie użytkownika, zakończą się niepowodzeniem.

Aby uzyskać więcej informacji na temat zapewniania dostępu użytkownikom w celu Azure Active Directory interfejs API programu Graph, zobacz [domyślny dostęp dla administratorów, użytkowników i użytkowników-Gości](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Rejestrowanie dostawcy zasobów

Aby udostępnić zasób na platformie Azure (w modelu Azure Resource Manager), potrzebny jest dostawca zasobów, który obsługuje tworzenie tego zasobu. Na przykład, aby zainicjować obsługę administracyjną maszyny wirtualnej, w subskrypcji powinien być dostępny dostawca zasobów Microsoft. COMPUTE.
 
Dostawcy zasobów są rejestrowani na poziomie subskrypcji. Domyślnie każda nowa subskrypcja platformy Azure ma wstępnie zarejestrowaną listę najczęściej stosowanych dostawców zasobów. Dostawca zasobów dla elementu "Microsoft. DataBoxEdge" nie jest uwzględniony na tej liście.

Nie musisz przyznawać uprawnień dostępu dla poziomu subskrypcji, aby użytkownicy mogli tworzyć zasoby takie jak "Microsoft. DataBoxEdge" w grupach zasobów, do których mają prawa właściciela, o ile dostawcy zasobów dla tych zasobów są już użytkownikiem.

Przed podjęciem próby utworzenia dowolnego zasobu upewnij się, że dostawca zasobów został zarejestrowany w ramach subskrypcji. Jeśli dostawca zasobów nie jest zarejestrowany, należy się upewnić, że użytkownik tworzący nowy zasób ma wystarczającą ilość uprawnień do zarejestrowania wymaganego dostawcy zasobów na poziomie subskrypcji. Jeśli nie zostało to jeszcze zrobione, zobaczysz następujący komunikat o błędzie:

*Nazwa subskrypcji \<subskrypcji > nie ma uprawnień do rejestrowania dostawców zasobów: Microsoft.DataBoxEdge.*


Aby uzyskać listę zarejestrowanych dostawców zasobów w bieżącej subskrypcji, uruchom następujące polecenie:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

W przypadku urządzenia `Microsoft.DataBoxEdge` Data Box Edge należy zarejestrować. Aby zarejestrować `Microsoft.DataBoxEdge`się, administrator subskrypcji powinien uruchomić następujące polecenie:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Aby uzyskać więcej informacji na temat rejestrowania dostawcy zasobów, zobacz [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Zarządzanie trybem łączności

Niezależnie od domyślnego trybu normalnego, urządzenie można również uruchomić w trybie częściowo rozłączonym lub rozłączonym. Każdy z tych trybów jest opisany poniżej:

- **Częściowo Rozłączono** — w tym trybie urządzenie nie może przekazać żadnych danych do udziałów, jednak można nimi zarządzać za pośrednictwem Azure Portal.

    Ten tryb jest zazwyczaj używany w przypadku taryfowej sieci satelitarnej i celem jest Minimalizacja użycia przepustowości sieci. W przypadku operacji monitorowania urządzeń może nadal występować minimalne zużycie sieci.

- **Rozłączono** — w tym trybie urządzenie jest w pełni odłączone od chmury, a operacje przekazywania i pobierania w chmurze są wyłączone. Urządzenie może być zarządzane tylko za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

    Ten tryb jest zazwyczaj używany, gdy chcesz przełączyć urządzenie do trybu offline.

Aby zmienić tryb urządzenia, wykonaj następujące kroki:

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź do pozycji **konfiguracja > Ustawienia chmury**.
2. Wyłącz **przekazywanie i pobieranie w chmurze**.
3. Aby uruchomić urządzenie w trybie częściowo rozłączona, Włącz **zarządzanie Azure Portal**.

    ![Tryb łączności](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Aby uruchomić urządzenie w trybie rozłączonym, wyłącz **zarządzanie Azure Portal**. Teraz urządzenie można zarządzać tylko za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

    ![Tryb łączności](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Zarządzanie mocą

Możesz zamknąć lub ponownie uruchomić urządzenie wirtualne przy użyciu lokalnego interfejsu użytkownika sieci Web. Zaleca się, aby przed ponownym uruchomieniem przełączyć udziały w tryb offline na hoście, a następnie na urządzeniu. Ta akcja minimalizuje wszelką możliwość uszkodzenia danych.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja > ustawienia zarządzania**.
2. Kliknij przycisk **Zamknij** lub **Uruchom ponownie** w zależności od tego, co chcesz zrobić.

    ![Ustawienia zasilacza](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby wykonać operację.

> [!NOTE]
> Jeśli wyłączysz urządzenie wirtualne, musisz uruchomić urządzenie za pomocą funkcji zarządzania funkcją hypervisor.
