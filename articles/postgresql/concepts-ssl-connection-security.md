---
title: SSL-Azure Database for PostgreSQL — pojedynczy serwer
description: Instrukcje i informacje dotyczące sposobu konfigurowania łączności SSL dla Azure Database for PostgreSQL-pojedynczego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5c5e1a8cee8cdad0659ae00829d170bf3fa7bf87
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941426"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurowanie łączności SSL w Azure Database for PostgreSQL — pojedynczy serwer

Azure Database for PostgreSQL preferuje łączenie aplikacji klienckich z usługą PostgreSQL przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją.

Domyślnie usługa bazy danych PostgreSQL jest skonfigurowana tak, aby wymagała połączenia SSL. Można wyłączyć wymaganie protokołu SSL, jeśli aplikacja kliencka nie obsługuje łączności SSL.

## <a name="enforcing-ssl-connections"></a>Wymuszanie połączeń SSL

W przypadku wszystkich serwerów Azure Database for PostgreSQL, które są obsługiwane za pośrednictwem Azure Portal i interfejsu wiersza polecenia, wymuszanie połączeń SSL jest domyślnie włączone. 

Analogicznie, parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "parametry połączenia" w ramach serwera w Azure Portal zawierają wymagane parametry dla wspólnych języków do łączenia się z serwerem bazy danych przy użyciu protokołu SSL. Parametr SSL zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

## <a name="configure-enforcement-of-ssl"></a>Skonfiguruj Wymuszanie protokołu SSL

Opcjonalnie można wyłączyć wymuszanie łączności SSL. Zaleca się, aby zawsze włączyć ustawienie **Wymuszaj połączenie SSL** dla zwiększonych zabezpieczeń. Microsoft Azure

> [!NOTE]
> Obecnie wersja protokołu TLS obsługiwana przez Azure Database for PostgreSQL to TLS 1,0, TLS 1,1, TLS 1,2.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Odwiedź serwer Azure Database for PostgreSQL i kliknij pozycję **zabezpieczenia połączeń**. Użyj przycisku przełącznika, aby włączyć lub wyłączyć ustawienie **Wymuszaj połączenie SSL** . Następnie kliknij przycisk **Zapisz**.

![Zabezpieczenia połączeń — wyłączanie protokołu Wymuś protokół SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Ustawienie można potwierdzić, wyświetlając stronę **Przegląd** , aby wyświetlić wskaźnik **stanu wymuszania protokołu SSL** .

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Można włączyć lub wyłączyć parametr **wymuszania protokołu SSL** przy użyciu wartości `Enabled` lub `Disabled` odpowiednio w interfejsie wiersza polecenia platformy Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Upewnij się, że aplikacja lub struktura obsługuje połączenia SSL

Niektóre struktury aplikacji korzystające z PostgreSQL dla usług baz danych nie domyślnie włączają protokół SSL podczas instalacji. Jeśli serwer PostgreSQL wymusza połączenia SSL, ale aplikacja nie jest skonfigurowana dla protokołu SSL, nawiązanie połączenia z serwerem bazy danych przez aplikację może zakończyć się niepowodzeniem. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikacje, które wymagają weryfikacji certyfikatu na potrzeby łączności SSL

W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji (. cer), aby bezpiecznie nawiązać połączenie. Certyfikat do połączenia z serwerem Azure Database for PostgreSQL znajduje się w https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Pobierz plik certyfikatu i Zapisz go w preferowanej lokalizacji.

### <a name="connect-using-psql"></a>Nawiązywanie połączenia przy użyciu PSQL

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem PostgreSQL za pomocą narzędzia wiersza polecenia PSQL. Użyj ustawienia parametrów połączenia `sslmode=verify-full`, aby wymusić weryfikację certyfikatu SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do parametru `sslrootcert`.

Następujące polecenie jest przykładem parametrów połączenia PSQL:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Upewnij się, że wartość przeniesiona do `sslrootcert` jest zgodna ze ścieżką pliku dla zapisywanych certyfikatów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z różnymi opcjami łączności aplikacji w [bibliotekach połączeń dla Azure Database for PostgreSQL](concepts-connection-libraries.md).
