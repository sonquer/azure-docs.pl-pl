---
title: 'Błąd rozwiązywania problemów: środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny'
description: Dowiedz się, jak rozwiązywać problemy z nieprawidłowym kontem magazynu.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063785"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Błąd rozwiązywania problemów: "środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny"

Ten artykuł pomaga w rozwiązywaniu problemów z następującym ciągiem błędu, który pojawia się w Azure Portal:

> "Błąd: środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny. Kliknij tutaj, aby uzyskać szczegółowe informacje na temat konfiguracji magazynu ".

Ten problem występuje, gdy nie można uruchomić środowisko uruchomieniowe usługi Azure Functions. Najbardziej typową przyczyną problemu jest to, że aplikacja funkcji utraciła dostęp do konta magazynu. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące konta magazynu](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

Pozostała część tego artykułu pomaga rozwiązywać następujące przyczyny tego błędu, w tym informacje dotyczące identyfikowania i rozwiązywania każdego przypadku.

## <a name="storage-account-was-deleted"></a>Konto magazynu zostało usunięte

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, funkcja nie będzie działać.

Zacznij od przejrzenia nazwy konta magazynu w ustawieniach aplikacji. `AzureWebJobsStorage` lub `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` zawiera nazwę konta magazynu opakowaną w ciągu połączenia. Aby uzyskać więcej informacji, zobacz temat informacje o [ustawieniach aplikacji dla Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Wyszukaj swoje konto magazynu w Azure Portal, aby sprawdzić, czy nadal istnieje. Jeśli został usunięty, Utwórz ponownie konto magazynu i Zastąp parametry połączenia magazynu. Kod funkcji zostanie utracony i konieczne będzie jego ponowne wdrożenie.

## <a name="storage-account-application-settings-were-deleted"></a>Ustawienia aplikacji konta magazynu zostały usunięte

W poprzednim kroku, jeśli nie możesz znaleźć parametrów połączenia konta magazynu, prawdopodobnie został on usunięty lub nadpisany. Najczęściej trwa usuwanie ustawień aplikacji w przypadku używania miejsc wdrożenia lub skryptów Azure Resource Manager do ustawiania ustawień aplikacji.

### <a name="required-application-settings"></a>Wymagane ustawienia aplikacji

* Wymagane:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Wymagane dla funkcji planu zużycia:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Aby uzyskać więcej informacji, zobacz temat informacje o [ustawieniach aplikacji dla Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Wskazówki

* Nie sprawdzaj ustawień gniazda dla żadnego z tych ustawień. W przypadku wymiany miejsc wdrożenia funkcja jest przerywana.
* Nie należy modyfikować tych ustawień w ramach zautomatyzowanych wdrożeń.
* Te ustawienia muszą być podane i ważne podczas tworzenia. Automatyczne wdrożenie, które nie zawiera tych ustawień, powoduje, że aplikacja funkcji nie zostanie uruchomiona, nawet jeśli ustawienia zostaną dodane później.

## <a name="storage-account-credentials-are-invalid"></a>Poświadczenia konta magazynu są nieprawidłowe

W przypadku ponownego wygenerowania kluczy magazynu należy zaktualizować wcześniej omówione parametry połączenia konta magazynu. Aby uzyskać więcej informacji na temat zarządzania kluczami magazynu, zobacz [Tworzenie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>Konto magazynu jest niedostępne

Aplikacja funkcji musi mieć dostęp do konta magazynu. Typowe problemy z zablokowaniem dostępu aplikacji funkcji do konta magazynu są następujące:

* Aplikacja funkcji jest wdrażana w App Service Environment bez poprawnych reguł sieciowych w celu zezwolenia na ruch do i z konta magazynu.

* Zapora konta magazynu jest włączona i nie jest skonfigurowana w taki sposób, aby zezwalała na ruch do i z funkcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>Dzienny limit przydziału wykonywania jest pełny

W przypadku skonfigurowania dziennego przydziału wykonywania aplikacja funkcji jest tymczasowo wyłączona, co spowoduje, że wiele kontrolek portalu stanie się niedostępna. 

Aby sprawdzić limit przydziału w [Azure Portal](https://portal.azure.com), wybierz pozycję **funkcje platformy** > **aplikacja funkcji ustawienia** w aplikacji funkcji. Jeśli osiągnięto **dzienny limit przydziału użycia** , zostanie wyświetlony następujący komunikat:

  > "Aplikacja funkcji osiągnął dzienny limit przydziału użycia i został zatrzymany do następnego 24-godzinnego okresu czasu".

Aby rozwiązać ten problem, Usuń lub Zwiększ dzienny limit przydziału, a następnie uruchom ponownie aplikację. W przeciwnym razie wykonywanie aplikacji jest blokowane do następnego dnia.

## <a name="app-is-behind-a-firewall"></a>Aplikacja znajduje się za zaporą

Środowisko uruchomieniowe funkcji może być nieosiągalne z jednego z następujących powodów:

* Aplikacja funkcji jest hostowana w ramach [wewnętrznego równoważenia obciążenia App Service Environment](../app-service/environment/create-ilb-ase.md) i jest skonfigurowana do blokowania przychodzącego ruchu internetowego.

* Aplikacja funkcji ma [Ograniczenia adresów IP dla ruchu przychodzącego](functions-networking-options.md#inbound-ip-restrictions) , które są skonfigurowane do blokowania dostępu do Internetu. 

Azure Portal wykonuje wywołania bezpośrednio do uruchomionej aplikacji w celu pobrania listy funkcji i wysyła wywołania HTTP do punktu końcowego kudu. Ustawienia na poziomie platformy na karcie **funkcje platformy** są nadal dostępne.

Aby sprawdzić konfigurację App Service Environment:
1. Przejdź do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) podsieci, w której znajduje się App Service Environment.
1. Sprawdź poprawność reguł ruchu przychodzącego, aby zezwolić na ruch pochodzący z publicznego adresu IP komputera, na którym uzyskujesz dostęp do aplikacji. 
   
Portalu można także użyć z komputera, który jest połączony z siecią wirtualną, na której uruchomiona jest aplikacja lub do maszyny wirtualnej działającej w sieci wirtualnej. 

Aby uzyskać więcej informacji na temat konfiguracji reguł ruchu przychodzącego, zobacz sekcję "sieciowe grupy zabezpieczeń" [zagadnienia dotyczące sieci App Service Environment](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o monitorowaniu aplikacji funkcji:

> [!div class="nextstepaction"]
> [Monitoruj Azure Functions](functions-monitoring.md)
