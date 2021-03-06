---
title: Dodawanie niestandardowego certyfikatu urzędu certyfikacji — Azure API Management | Microsoft Docs
description: Dowiedz się, jak dodać niestandardowy certyfikat urzędu certyfikacji w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073600"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Jak dodać niestandardowy certyfikat urzędu certyfikacji na platformie Azure API Management

Usługa Azure API Management umożliwia instalowanie certyfikatów urzędu certyfikacji na komputerze wewnątrz zaufanych głównych i pośrednich magazynów certyfikatów. Ta funkcja powinna być używana, jeśli usługi wymagają niestandardowego certyfikatu urzędu certyfikacji.

W tym artykule przedstawiono sposób zarządzania certyfikatami urzędu certyfikacji wystąpienia usługi Azure API Management w Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Przekazywanie certyfikatu urzędu certyfikacji

![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/00.png)

Postępuj zgodnie z poniższymi instrukcjami, aby przekazać nowy certyfikat urzędu certyfikacji. Jeśli jeszcze nie utworzono wystąpienia usługi API Management, zobacz samouczek [Tworzenie wystąpienia usługi API Management](get-started-create-service-instance.md).

1. Przejdź do wystąpienia usługi Azure API Management w Azure Portal.

2. Z menu wybierz opcję **certyfikaty urzędu certyfikacji** .

3. Kliknij przycisk **+ Dodaj**.  

    ![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/01.png)  

4. Wyszukaj certyfikat i wybierz magazyn certyfikatów. Wymagany jest tylko klucz publiczny, dlatego hasło nie jest wymagane.

    ![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/02.png)  

5. Kliknij polecenie **Zapisz**. Ta operacja może potrwać kilka minut.

    ![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Certyfikat urzędu certyfikacji można przekazać za pomocą `New-AzApiManagementSystemCertificate` polecenia programu PowerShell.

## <a name="step1a"> </a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, kliknij menu kontekstowe , a następnie wybierz pozycję **Usuń** obok certyfikatu.

![Usuwanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
