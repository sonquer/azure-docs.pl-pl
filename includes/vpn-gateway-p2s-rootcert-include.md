---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "67183091"
---
Użyj certyfikatu głównego wygenerowanego za pomocą rozwiązania przedsiębiorstwa (zalecane) albo wygeneruj certyfikat z podpisem własnym. Po utworzeniu certyfikatu głównego wyeksportuj dane certyfikatu publicznego (nie klucz prywatny) jako plik cer X.509 z kodowaniem Base64. Następnie przekaż dane certyfikatu publicznego na serwer platformy Azure.

* **Certyfikat przedsiębiorstwa:** Jeśli używasz rozwiązania Enterprise, możesz użyć istniejącego łańcucha certyfikatów. Uzyskaj plik cer dla certyfikatu głównego, którego chcesz użyć.
* **Certyfikat główny z podpisem własnym:** Jeśli nie używasz rozwiązania z certyfikatem przedsiębiorstwa, Utwórz certyfikat główny z podpisem własnym. W przeciwnym razie utworzone przez Ciebie certyfikaty nie będą zgodne z połączeniami punkt-lokacja i będzie wyświetlany błąd połączenia podczas próby nawiązania połączenia. Możesz użyć programu Azure PowerShell, MakeCert lub protokołu OpenSSL. W poniższych artykułach opisano sposób generowania zgodnego certyfikatu głównego z podpisem własnym:

  * [Instrukcje programu Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): w przypadku tych instrukcji do generowania certyfikatów wymagany jest system Windows 10 i program PowerShell. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): użyj programu MakeCert, jeśli nie masz dostępu do komputera z systemem Windows 10 w celu wygenerowania certyfikatów. Mimo iż narzędzie MakeCert jest przestarzałe, przy jego użyciu można nadal generować certyfikaty. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje dotyczące systemu Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
