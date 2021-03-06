---
title: Określ, który model klucza szyfrowania jest używany dla konta magazynu
titleSuffix: Azure Storage
description: Użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, aby sprawdzić, jak są zarządzane klucze szyfrowania dla konta magazynu. Klucze mogą być zarządzane przez firmę Microsoft (ustawienie domyślne) lub przez klienta. Klucze zarządzane przez klienta muszą być przechowywane w Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3806fead9226978c277e87f3d97b14ee38d9552d
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665409"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Ustal, który model klucza szyfrowania usługi Azure Storage jest używany dla konta magazynu

Dane na koncie magazynu są automatycznie szyfrowane przez usługę Azure Storage. Szyfrowanie usługi Azure Storage oferuje dwie opcje zarządzania kluczami szyfrowania na poziomie konta magazynu:

- **Klucze zarządzane przez firmę Microsoft.** Domyślnie firma Microsoft zarządza kluczami używanymi do szyfrowania konta magazynu.
- **Klucze zarządzane przez klienta.** Opcjonalnie możesz wybrać Zarządzanie kluczami szyfrowania dla konta magazynu. Klucze zarządzane przez klienta muszą być przechowywane w Azure Key Vault.

Dodatkowo można podać klucz szyfrowania na poziomie pojedynczego żądania dla niektórych operacji usługi BLOB Storage. Gdy w żądaniu zostanie określony klucz szyfrowania, ten klucz zastępuje klucz szyfrowania, który jest aktywny na koncie magazynu. Aby uzyskać więcej informacji, zobacz [Określanie klucza dostarczonego przez klienta w żądaniu do magazynu obiektów BLOB](../blobs/storage-blob-customer-provided-key.md).

Aby uzyskać więcej informacji na temat kluczy szyfrowania, zobacz [szyfrowanie usługi Azure Storage dla danych przechowywanych w stanie spoczynku](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Sprawdź model klucza szyfrowania dla konta magazynu

Aby określić, czy konto magazynu korzysta z kluczy zarządzanych przez firmę Microsoft czy kluczy zarządzanych przez klienta do szyfrowania, użyj jednego z poniższych metod.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

Aby sprawdzić model szyfrowania dla konta magazynu przy użyciu Azure Portal, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **szyfrowanie** i zanotuj ustawienie.

Na poniższej ilustracji przedstawiono konto magazynu, w którym klucze zarządzane przez klienta są używane do szyfrowania:

![Zrzut ekranu przedstawiający ustawienie klucza szyfrowania w Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić model szyfrowania dla konta magazynu za pomocą programu PowerShell, wywołaj polecenie [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) , a następnie sprawdź Właściwość **sourceType** dla konta.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Jeśli wartość właściwości **Źródło** klucza jest `Microsoft.Storage`, konto jest szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli wartość właściwości **Źródło** klucza jest `Microsoft.Keyvault`, konto jest szyfrowane przy użyciu kluczy zarządzanych przez klienta.

# <a name="azure-clitabcli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby sprawdzić model szyfrowania dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account show](/cli/azure/storage/account#az-storage-account-show) , a następnie sprawdź Właściwość **Source** dla konta.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Jeśli wartość właściwości **Źródło** klucza jest `Microsoft.Storage`, konto jest szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli wartość właściwości **Źródło** klucza jest `Microsoft.Keyvault`, konto jest szyfrowane przy użyciu kluczy zarządzanych przez klienta.

---

## <a name="next-steps"></a>Następne kroki

[Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md)
