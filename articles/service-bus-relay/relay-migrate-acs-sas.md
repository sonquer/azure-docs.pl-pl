---
title: Azure Relay — migracja do autoryzacji sygnatury dostępu współdzielonego
description: Opisuje sposób migrowania aplikacji Azure Relay z używania Azure Active Directory Access Control Service do autoryzacji sygnatury dostępu współdzielonego.
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 59b9e734526c56016e2ddf59c2afb5b8f7b4ad09
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514905"
---
# <a name="azure-relay---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Relay — migracja z Azure Active Directory Access Control Service do autoryzacji sygnatury dostępu współdzielonego

Aplikacje Azure Relay w historyczny sposób mogli korzystać z dwóch różnych modeli autoryzacji: modelu tokenu [sygnatury dostępu współdzielonego (SAS)](../service-bus-messaging/service-bus-sas.md) dostarczonego bezpośrednio przez usługę przekaźnika oraz modelem federacyjnym, w którym zarządzanie regułami autoryzacji jest zarządzane w ramach [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) i tokeny uzyskane z usługi ACS są przekazywane do przekaźnika w celu autoryzowania dostępu do żądanych funkcji.

Model autoryzacji usług ACS został zastąpiony przez [autoryzację SAS](../service-bus-messaging/service-bus-authentication-and-authorization.md) jako preferowany model, a cała dokumentacja, wskazówki i przykłady używają wyłącznie sygnatury dostępu współdzielonego. Ponadto nie można już tworzyć nowych przestrzeni nazw przekaźnika, które są sparowane z usługą ACS.

Użycie sygnatury dostępu współdzielonego nie jest od razu zależne od innej usługi, ale może być używane bezpośrednio od klienta bez pośredników przez udzielenie klientowi dostępu do nazwy reguły SAS i klucza reguły. SAS można również łatwo zintegrować z podejściem, w którym klient musi najpierw przekazać kontrolę autoryzacji z inną usługą, a następnie wystawić token. Drugie podejście jest podobne do wzorca użycia usług ACS, ale umożliwia wystawianie tokenów dostępu na podstawie warunków specyficznych dla aplikacji, które są trudne do wyrażania w usłudze ACS.

W przypadku wszystkich istniejących aplikacji, które są zależne od usługi ACS, zachęcamy klientów do migrowania aplikacji w celu zalogowania się do nich.

## <a name="migration-scenarios"></a>Scenariusze migracji

Usługi ACS i Relay są zintegrowane za pomocą udostępnionej wiedzy o *kluczu podpisywania*. Klucz podpisywania jest używany przez przestrzeń nazw ACS do podpisywania tokenów autoryzacji i jest używany przez Azure Relay do sprawdzenia, czy token został wystawiony przez sparowaną przestrzeń nazw usługi ACS. Przestrzeń nazw ACS zawiera tożsamości usługi i reguły autoryzacji. Reguły autoryzacji definiują tożsamość usługi lub token wystawiony przez zewnętrzny dostawca tożsamości, który umożliwia uzyskiwanie typu dostępu do części wykresu przestrzeni nazw przekaźnika w postaci dopasowania najdłuższego prefiksu.

Na przykład reguła usług ACS może udzielić żądania **send** na prefiksie ścieżki `/` do tożsamości usługi, co oznacza, że token wystawiony przez usługę ACS na podstawie tej reguły przyznaje prawa klienta do wysyłania do wszystkich jednostek w przestrzeni nazw. Jeśli prefiks ścieżki jest `/abc`, tożsamość jest ograniczona do wysyłania do jednostek o nazwie `abc` lub zorganizowanych poniżej tego prefiksu. Przyjęto założenie, że czytelnicy tej wskazówki dotyczącej migracji już znają te koncepcje.

Scenariusze migracji należą do trzech szerokich kategorii:

1.  **Niezmienione wartości domyślne**. Niektórzy klienci używają obiektu [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) , przekazując automatycznie wygenerowaną tożsamość usługi **właściciela** i jej klucz tajny dla przestrzeni nazw ACS, sparowany z przestrzenią nazw przekaźnika i nie dodają nowych reguł.

2.  **Niestandardowe tożsamości usług z prostymi regułami**. Niektórzy klienci dodają nowe tożsamości usługi i przydzielą każdemu nowemu identyfikatorowi uprawnienia **wysyłanie**, **nasłuchiwanie**i **Zarządzanie** dla jednej konkretnej jednostki.

3.  **Niestandardowe tożsamości usługi ze złożonymi regułami**. Bardzo nieliczni klienci mają złożone zestawy reguł, w których zewnętrznie wystawione tokeny są mapowane na prawa w ramach przekaźnika lub w przypadku, gdy jedna tożsamość usługi ma przypisane zróżnicowane prawa do kilku ścieżek przestrzeni nazw za pomocą wielu reguł.

Aby uzyskać pomoc dotyczącą migracji złożonych zestawów reguł, możesz skontaktować się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). Pozostałe dwa scenariusze umożliwiają prostą migrację.

### <a name="unchanged-defaults"></a>Niezmienione wartości domyślne

Jeśli aplikacja nie zmieniła wartości domyślnych usługi ACS, można zastąpić wszystkie [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) użycie za pomocą obiektu [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) i użyć wstępnie skonfigurowanej przestrzeni nazw **RootManageSharedAccessKey** zamiast konta **właściciela** ACS. Należy pamiętać, że nawet w przypadku konta **właściciela** ACS ta konfiguracja była (i nadal nie jest zalecana), ponieważ to konto/reguła zapewnia pełny urząd zarządzania względem przestrzeni nazw, w tym uprawnienia do usuwania wszystkich jednostek.

### <a name="simple-rules"></a>Reguły proste

Jeśli aplikacja używa niestandardowych tożsamości usług z prostymi regułami, migracja jest prosta w przypadku, gdy utworzona została tożsamość usługi ACS w celu zapewnienia kontroli dostępu w określonym przekaźniku. W tym scenariuszu często zdarza się, że w przypadku rozwiązań w stylu SaaS Każdy przekaźnik jest używany jako most do witryny dzierżawy lub biura oddziału, a tożsamość usługi jest tworzona dla danej lokacji. W takim przypadku można migrować odpowiednią tożsamość usługi do reguły sygnatury dostępu współdzielonego bezpośrednio w ramach przekaźnika. Nazwa tożsamości usługi może być nazwą reguły sygnatury dostępu współdzielonego, a klucz tożsamości usługi może być kluczem reguły sygnatury dostępu współdzielonego. Prawa reguły dostępu współdzielonego są następnie konfigurowane jako odpowiednik odpowiedniej reguły ACS dla jednostki.

Nową i dodatkową konfigurację sygnatury dostępu współdzielonego można utworzyć w dowolnym istniejącym obszarze nazw federacyjnym z usługą ACS, a migracja z usługi ACS jest następnie wykonywana przy użyciu [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) zamiast [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Przestrzeń nazw nie musi być odłączona od usługi ACS.

### <a name="complex-rules"></a>Reguły złożone

Reguły sygnatury dostępu współdzielonego nie powinny być kontami, ale są nazwanymi kluczami podpisywania skojarzonymi z prawami. W związku z tym scenariusze, w których aplikacja tworzy wiele tożsamości usługi i przyznaje im prawa dostępu dla kilku jednostek lub cała przestrzeń nazw nadal wymaga pośrednika wystawiającego tokeny. Możesz uzyskać wskazówki dotyczące takiego pośrednika, [kontaktując się z pomocą techniczną](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o uwierzytelnianiu Azure Relay, zobacz następujące tematy:

* [Azure Relay uwierzytelnianie i autoryzacja](relay-authentication-and-authorization.md)
* [Uwierzytelnianie Service Bus przy użyciu sygnatur dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md)


