---
title: Uwierzytelnianie, żądania i odpowiedzi
description: Uwierzytelnianie w usłudze AD przy użyciu Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b023b49955f642f1cafcb5f26ae67e657718bcd6
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148236"
---
# <a name="authentication-requests-and-responses"></a>Uwierzytelnianie, żądania i odpowiedzi

Azure Key Vault obsługuje żądania i odpowiedzi sformatowane w formacie JSON. Żądania do Azure Key Vault są kierowane do prawidłowego adresu URL Azure Key Vault przy użyciu protokołu HTTPS z niektórymi parametrami adresu URL i treściami żądania i odpowiedzi zakodowanych w formacie JSON.

W tym temacie omówiono specyficzne dla usługi Azure Key Vault. Aby uzyskać ogólne informacje na temat korzystania z interfejsów REST platformy Azure, w tym uwierzytelniania/autoryzacji i uzyskiwania tokenu dostępu, zobacz [Dokumentacja interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>Adres URL żądania  
 Operacje zarządzania kluczami używają operacji DELETE, GET, PATCH, PUT i https protokołu HTTP dla istniejących obiektów kluczy. Użyj protokołu HTTP POST. Klienci, którzy nie mogą obsługiwać określonych czasowników HTTP, mogą również używać protokołu HTTP POST przy użyciu nagłówka X-HTTP-REQUEST do określenia zamierzonego zlecenia; żądania, które zwykle nie wymagają treści, powinny zawierać pustą treść przy użyciu polecenia POST protokołu HTTP, na przykład podczas korzystania z polecenia POST zamiast DELETE.  

 Aby można było korzystać z obiektów w Azure Key Vault, przykładowe adresy URL:  

- Aby utworzyć klucz o nazwie TESTKEY w Key Vault użyciu `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Aby ZAIMPORTOWAĆ klucz o nazwie IMPORTEDKEY do Key Vault Użyj `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Aby uzyskać wpis tajny o nazwie "dbsecret" w Key Vault użyciu `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Aby PODPISać skrót przy użyciu klucza o nazwie TESTKEY w Key Vault użyciu `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Urząd żądania do Key Vault jest zawsze następujący, `https://{keyvault-name}.vault.azure.net/`  

  Klucze są zawsze przechowywane pod ścieżką/Keys, wpisy tajne są zawsze przechowywane pod ścieżką/Secrets.  

## <a name="api-version"></a>Wersja interfejsu API  
 Usługa Azure Key Vault obsługuje przechowywanie wersji protokołów w celu zapewnienia zgodności z klientami niskiego poziomu, chociaż nie wszystkie możliwości będą dostępne dla tych klientów. Klienci muszą używać parametru ciągu zapytania `api-version`, aby określić wersję protokołu, którą obsługują, ponieważ nie ma wartości domyślnej.  

 Wersje protokołów Azure Key Vault są zgodne ze schematem numerowania dat przy użyciu funkcji {rrrr}. {MM}. Format {DD}.  

## <a name="request-body"></a>Treść żądania  
 Zgodnie ze specyfikacją protokołu HTTP operacje GET nie mogą zawierać treści żądania, a operacje POST i PUT muszą mieć treść żądania. Treść w operacjach usuwania jest opcjonalna w protokole HTTP.  

 O ile nie wskazano inaczej w opisie operacji, typem zawartości treści żądania musi być Application/JSON i musi zawierać serializowany obiekt JSON zgodny z typem zawartości.  

 O ile nie wskazano inaczej w opisie operacji, nagłówek żądania Accept musi zawierać typ nośnika Application/JSON.  

## <a name="response-body"></a>Treść odpowiedzi  
 O ile nie wskazano inaczej w opisie operacji, typ zawartości treści odpowiedzi dla operacji zakończonych powodzeniem i zakończonych niepowodzeniem będzie miał wartość Application/JSON i zawiera szczegółowe informacje o błędzie.  

## <a name="using-http-post"></a>Korzystanie z polecenia POST protokołu HTTP  
 Niektórzy klienci mogą nie być w stanie używać niektórych zleceń HTTP, takich jak PATCH lub DELETE. Azure Key Vault obsługuje POST protokołu HTTP jako alternatywę dla tych klientów pod warunkiem, że klient zawiera również nagłówek "X-HTTP-METHOD" do określonego pierwotnego zlecenia HTTP. Obsługa protokołu HTTP POST jest zapisywana dla każdego interfejsu API zdefiniowanego w tym dokumencie.  

## <a name="error-responses"></a>Odpowiedzi na błędy  
 Obsługa błędów będzie używać kodów stanu HTTP. Typowe wyniki to:  

- 2xx — sukces: używany do normalnego działania. Treść odpowiedzi będzie zawierać oczekiwany wynik  

- 3xx — przekierowanie: 304 "nie zmodyfikowano" może zostać zwrócony do spełnienia warunkowego GET. Inne kody 3xx mogą być używane w przyszłości, aby wskazać zmiany w systemie DNS i ścieżce.  

- 4xx — Błąd klienta: używany dla nieprawidłowych żądań, brakujące klucze, błędy składniowe, nieprawidłowe parametry, błędy uwierzytelniania itd. Treść odpowiedzi będzie zawierać szczegółowe wyjaśnienie błędu.  

- 5xx — błąd serwera: używany do wewnętrznego błędu serwera. Treść odpowiedzi będzie zawierać podsumowanie informacji o błędzie.  

  System został zaprojektowany tak, aby działał za serwerem proxy lub zaporą. W związku z tym klient może otrzymać inne kody błędów.  

  Azure Key Vault zwraca również informacje o błędzie w treści odpowiedzi w przypadku wystąpienia problemu. Treść odpowiedzi jest sformatowana w formacie JSON i ma postać:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Uwierzytelnianie  
 Wszystkie żądania do Azure Key Vault muszą być uwierzytelnione. Azure Key Vault obsługuje tokeny dostępu Azure Active Directory, które można uzyskać za pomocą OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Aby uzyskać więcej informacji na temat rejestrowania aplikacji i uwierzytelniania w celu korzystania z Azure Key Vault, zobacz [Rejestrowanie aplikacji klienckiej w usłudze Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Tokeny dostępu należy wysyłać do usługi przy użyciu nagłówka autoryzacji HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Gdy nie dostarczono tokenu dostępu lub gdy usługa nie akceptuje tokenu, do klienta zostanie zwrócony błąd HTTP 401 i zostanie uwzględniony nagłówek WWW-Authenticate, na przykład:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry w nagłówku WWW-Authenticate są następujące:  

-   Autoryzacja: adres usługi autoryzacji OAuth2, która może zostać użyta w celu uzyskania tokenu dostępu dla żądania.  

-   zasób: nazwa zasobu (https://vault.azure.net) do użycia w żądaniu autoryzacji).  

## <a name="see-also"></a>Zobacz też  
 [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
