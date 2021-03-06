---
title: Odnów certyfikat usługi Azure Application Gateway
description: Dowiedz się, jak odnowić certyfikat skojarzony z odbiornikiem bramy aplikacji.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278001"
---
# <a name="renew-application-gateway-certificates"></a>Odnów Application Gateway certyfikaty

W pewnym momencie należy odnowić certyfikaty, jeśli skonfigurowano bramę aplikacji do szyfrowania SSL.

Certyfikat skojarzony z odbiornikiem można odnowić przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure:

## <a name="azure-portal"></a>Portal Azure

Aby odnowić certyfikat odbiornika z portalu, przejdź do odbiornika usługi Application Gateway. Kliknij odbiornik z certyfikatem, który musi zostać odnowiony, a następnie kliknij pozycję **Odnów lub Edytuj wybrany certyfikat**.

![Odnów certyfikat](media/renew-certificate/ssl-cert.png)

Przekaż nowy certyfikat PFX, nadaj mu nazwę, wpisz hasło, a następnie kliknij przycisk **Zapisz**.

## <a name="azure-powershell"></a>Program Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby odnowić certyfikat przy użyciu Azure PowerShell, użyj następującego skryptu:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL za pomocą usługi Azure Application Gateway, zobacz [Konfigurowanie odciążania protokołu SSL](application-gateway-ssl-portal.md)
