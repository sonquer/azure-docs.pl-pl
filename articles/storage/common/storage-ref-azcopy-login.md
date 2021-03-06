---
title: AzCopy logowanie | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia logowania AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d6b2fbe28aae8e8233aaeb75bc9b43a35a9ab588
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905287"
---
# <a name="azcopy-login"></a>azcopy login

Loguje się do Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

Zaloguj się do Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Storage.

Aby można było autoryzować konto usługi Azure Storage, musisz przypisać rolę **współautor danych obiektów blob magazynu** do konta użytkownika w kontekście konta magazynu, nadrzędnej grupy zasobów lub subskrypcji nadrzędnej.

To polecenie spowoduje buforowanie zaszyfrowanych informacji logowania dla bieżącego użytkownika przy użyciu wbudowanych mechanizmów systemu operacyjnego.

Aby uzyskać więcej informacji, zapoznaj się z przykładami.

> [!IMPORTANT]
> W przypadku ustawienia zmiennej środowiskowej przy użyciu wiersza polecenia ta zmienna zostanie odczytana w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne w historii, można użyć skryptu w celu wyświetlenia monitu o podanie poświadczeń użytkownika i ustawienia zmiennej środowiskowej.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

Zaloguj się interaktywnie przy użyciu domyślnego identyfikatora dzierżawy usługi AAD ustawionego na wartość common:

```azcopy
azcopy login
```

Zaloguj się interaktywnie przy użyciu określonego identyfikatora dzierżawy:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Zaloguj się przy użyciu tożsamości przypisanej do systemu maszyny wirtualnej (VM):

```azcopy
azcopy login --identity
```

Zaloguj się przy użyciu tożsamości przypisanej do użytkownika maszyny wirtualnej i identyfikatora klienta tożsamości usługi:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Zaloguj się przy użyciu tożsamości przypisanej do użytkownika maszyny wirtualnej i identyfikatora obiektu tożsamości usługi:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Zaloguj się przy użyciu tożsamości przypisanej do użytkownika maszyny wirtualnej i identyfikatora zasobu tożsamości usługi:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Zaloguj się jako nazwa główna usługi przy użyciu klucza tajnego klienta. Ustaw zmienną środowiskową AZCOPY_SPA_CLIENT_SECRET na wpis tajny klienta dla uwierzytelniania podstawowego usługi opartego na wpisie tajnym.

```azcopy
azcopy login --service-principal
```

Zaloguj się jako nazwa główna usługi przy użyciu certyfikatu i hasła. Ustaw zmienną środowiskową AZCOPY_SPA_CERT_PASSWORD na hasło certyfikatu dla autoryzacji głównej usługi opartej na certyfikatach.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Pamiętaj, aby traktować/Path/to/my/CERT jako ścieżkę do pliku PEM lub PKCS12. Usługa AzCopy nie dociera do magazynu certyfikatów systemu w celu uzyskania certyfikatu.

--ścieżka certyfikatu jest obowiązkowa podczas uwierzytelniania jednostki usługi opartej na certyfikatach.

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--AAD — punkt końcowy|Punkt końcowy Azure Active Directory, który ma być używany. Wartość domyślna (https://login.microsoftonline.com) jest poprawna dla publicznej chmury platformy Azure. Ustaw ten parametr podczas uwierzytelniania w chmurze krajowej. Zobacz [punkty końcowe uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Ta flaga nie jest wymagana w przypadku tożsamość usługi zarządzanej.|
|--ciąg identyfikatora aplikacji|Identyfikator aplikacji tożsamości przypisanej do użytkownika. Wymagane dla uwierzytelniania nazwy głównej usługi.|
|--ciąg ścieżki certyfikatu|Ścieżka do certyfikatu na potrzeby uwierzytelniania SPN. Wymagane dla uwierzytelniania nazwy głównej usługi opartej na certyfikatach.|
|-h,--pomoc|Pokaż zawartość pomocy dla polecenia login.|
|--tożsamość|Zaloguj się przy użyciu tożsamości maszyny wirtualnej, znanej również jako tożsamość usługi zarządzanej (MSI).|
|--Identity-Client-ID|Identyfikator klienta tożsamości przypisanej do użytkownika.|
|--ciąg identyfikatora obiektu|Identyfikator obiektu tożsamości przypisanej do użytkownika.|
|--ciąg tożsamości zasobu|Identyfikator zasobu tożsamości przypisanej przez użytkownika.|
|--Service-Principal|Zaloguj się za pomocą nazwy SPN (główna nazwa usługi) za pomocą certyfikatu lub klucza tajnego. Wpis tajny klienta lub hasło certyfikatu muszą być umieszczone w odpowiedniej zmiennej środowiskowej. Wpisz `AzCopy env`, aby wyświetlić nazwy i opisy zmiennych środowiskowych.|
|--ciąg identyfikatora dzierżawy| Identyfikator dzierżawy usługi Azure Active Directory, który ma być używany na potrzeby logowania interaktywnego urządzenia OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
