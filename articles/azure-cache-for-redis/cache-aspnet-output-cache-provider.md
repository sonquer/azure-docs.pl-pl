---
title: Dostawca wyjściowej pamięci podręcznej ASP.NET dla usługi Azure cache for Redis
description: Dowiedz się, jak buforować dane wyjściowe ASP.NET strony za pomocą usługi Azure cache for Redis. Dostawca wyjściowej pamięci podręcznej Redis jest mechanizmem magazynu poza procesem dla danych wyjściowej pamięci podręcznej.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 1a375f063d398c19ed86a0a401e2a41c696ef4e2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412986"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Dostawca wyjściowej pamięci podręcznej ASP.NET dla usługi Azure cache for Redis

Dostawca wyjściowej pamięci podręcznej Redis jest mechanizmem magazynu poza procesem dla danych wyjściowej pamięci podręcznej. Te dane są przeznaczone wyłącznie dla pełnych odpowiedzi HTTP (buforowanie danych wyjściowych strony). Dostawca podłącza się do nowego punktu rozszerzalności dostawcy pamięci podręcznej, który został wprowadzony w ASP.NET 4.

Aby użyć dostawcy wyjściowej pamięci podręcznej Redis, najpierw Skonfiguruj pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET przy użyciu pakietu NuGet wyjściowego dostawcy pamięci podręcznej Redis. Ten temat zawiera wskazówki dotyczące konfigurowania aplikacji do korzystania z dostawcy wyjściowej pamięci podręcznej Redis. Aby uzyskać więcej informacji na temat tworzenia i konfigurowania wystąpienia usługi Azure cache for Redis, zobacz [Tworzenie pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Przechowuj dane wyjściowe strony ASP.NET w pamięci podręcznej

Aby skonfigurować aplikację kliencką w programie Visual Studio przy użyciu pakietu NuGet usługi Azure cache for Redis, kliknij pozycję **Menedżer pakietów NuGet**, **konsola Menedżera pakietów** z menu **Narzędzia** .

W oknie `Package Manager Console` uruchom następujące polecenie.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Pakiet NuGet Redis wyjściowego dostawcy pamięci podręcznej ma zależność od pakietu StackExchange. Redis. StrongName. Jeśli pakiet StackExchange. Redis. StrongName nie występuje w projekcie, jest on zainstalowany. Aby uzyskać więcej informacji na temat pakietu NuGet Redis wyjściowego dostawcy pamięci podręcznej, zobacz stronę NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) .

>[!NOTE]
>Oprócz silnej nazwy pakietu StackExchange. Redis. StrongName istnieje również StackExchange. Redis, która nie ma silnej nazwy. Jeśli projekt używa niesilnej nazwy StackExchange. Redis, należy ją odinstalować. w przeciwnym razie w projekcie wystąpią konflikty nazw. Aby uzyskać więcej informacji o tych pakietach, zobacz [Konfigurowanie klientów pamięci podręcznej platformy .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu i dodaje następującą sekcję do pliku Web. config. Ta sekcja zawiera konfigurację wymaganą przez aplikację ASP.NET do użycia dostawcy wyjściowej pamięci podręcznej Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Skonfiguruj atrybuty przy użyciu wartości z bloku pamięci podręcznej w Microsoft Azure Portal i skonfiguruj inne wartości zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Konfigurowanie usługi Azure cache for Redis Settings](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atrybut | Typ | Domyślne | Opis |
| --------- | ---- | ------- | ----------- |
| *host* | string | lokalnym | Adres IP lub nazwa hosta serwera Redis |
| *przewożąc* | dodatnia liczba całkowita | 6379 (non-SSL)<br/>6380 (SSL) | Port serwera Redis |
| *accessKey* | string | "" | Hasło serwera Redis, gdy autoryzacja Redis jest włączona. Wartość jest domyślnie pustym ciągiem, co oznacza, że dostawca stanu sesji nie będzie używać żadnego hasła podczas nawiązywania połączenia z serwerem Redis. **Jeśli Twój serwer Redis znajduje się w publicznie dostępnej sieci, takiej jak Azure Redis Cache, upewnij się, że włączono autoryzację Redis, aby zwiększyć bezpieczeństwo i zapewnić bezpieczne hasło.** |
| *ssl* | wartość logiczna | **false** | Czy nawiązać połączenie z serwerem Redis za pośrednictwem protokołu SSL. Ta wartość jest domyślnie **fałszywa** , ponieważ Redis nie obsługuje protokołu SSL z pola. **Jeśli używasz Azure Redis Cache, który obsługuje protokół SSL z pola, pamiętaj, aby ustawić wartość true, aby zwiększyć bezpieczeństwo.**<br/><br/>Port bez obsługi protokołu SSL jest domyślnie wyłączony w przypadku nowych pamięci podręcznych. Określ **wartość true** dla tego ustawienia, aby używać portu SSL. Aby uzyskać więcej informacji na temat włączania portu bez obsługi protokołu SSL, zobacz sekcję [porty dostępu](cache-configure.md#access-ports) w temacie [Konfigurowanie pamięci podręcznej](cache-configure.md) . |
| *databaseIdNumber* | dodatnia liczba całkowita | 0 | *Ten atrybut może być określony tylko za pomocą pliku Web. config lub AppSettings.*<br/><br/>Określ, która baza danych Redis ma być używana. |
| *connectionTimeoutInMilliseconds* | dodatnia liczba całkowita | Dostarczone przez StackExchange. Redis | Służy do ustawiania *ConnectTimeout* podczas tworzenia stackexchange. Redis. ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | dodatnia liczba całkowita | Dostarczone przez StackExchange. Redis | Służy do ustawiania *SyncTimeout* podczas tworzenia stackexchange. Redis. ConnectionMultiplexer. |
| *ConnectionString* (prawidłowe parametry połączenia stackexchange. Redis) | string | *nie dotyczy* | Odwołanie do parametru AppSettings lub Web. config lub inne prawidłowe parametry połączenia StackExchange. Redis. Ten atrybut może podawać wartości parametrów *host*, *port*, *AccessKey*, *SSL*i inne atrybuty stackexchange. Redis. Aby bliżej zapoznać się z parametrem *ConnectionString*, zobacz [ustawienie ConnectionString](#setting-connectionstring) w sekcji [uwagi dotyczące atrybutu](#attribute-notes) . |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *nie dotyczy* | *Te atrybuty można określić tylko za pomocą pliku Web. config lub AppSettings.*<br/><br/>Użyj tych atrybutów, aby podać parametry połączenia. *settingsClassName* powinna być kwalifikowana nazwa klasy zestawu, która zawiera metodę określoną przez *settingsMethodName*.<br/><br/>Metoda określona przez *settingsMethodName* powinna być publiczna, statyczna i void (nie przyjmować żadnych parametrów) z typem zwracanym **ciągu**. Ta metoda zwraca rzeczywiste parametry połączenia. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *nie dotyczy* | *Te atrybuty można określić tylko za pomocą pliku Web. config lub AppSettings.*<br/><br/>Te atrybuty umożliwiają debugowanie aplikacji przez dostarczanie dzienników z pamięci podręcznej stanu sesji/wyjściowej oraz dzienników z StackExchange. Redis. *loggingClassName* powinna być kwalifikowana nazwa klasy zestawu, która zawiera metodę określoną przez *loggingMethodName*.<br/><br/>Metoda określona przez *loggingMethodName* powinna być publiczna, statyczna i void (nie przyjmować żadnych parametrów) z typem zwracanym **System. IO. TextWriter**. |
| *applicationName* | string | Nazwa modułu bieżącego procesu lub "/" | *Tylko SessionStateProvider*<br/>*Ten atrybut może być określony tylko za pomocą pliku Web. config lub AppSettings.*<br/><br/>Prefiks nazwy aplikacji do użycia w pamięci podręcznej Redis. Klient może używać tej samej pamięci podręcznej Redis do różnych celów. Aby upewnić się, że klucze sesji nie kolidują, można je prefiksować przy użyciu nazwy aplikacji. |
| *Parametr throwOnError* | wartość logiczna | true | *Tylko SessionStateProvider*<br/>*Ten atrybut może być określony tylko za pomocą pliku Web. config lub AppSettings.*<br/><br/>Określa, czy zgłaszać wyjątek w przypadku wystąpienia błędu.<br/><br/>Aby uzyskać więcej informacji na temat *parametr throwOnError*, zobacz [uwagi dotyczące *parametr throwOnError* ](#notes-on-throwonerror) w sekcji [uwagi dotyczące atrybutów](#attribute-notes) . |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | dodatnia liczba całkowita | 5000 | *Tylko SessionStateProvider*<br/>*Ten atrybut może być określony tylko za pomocą pliku Web. config lub AppSettings.*<br/><br/>Czas ponowienia próby w przypadku niepowodzenia operacji. Jeśli ta wartość jest mniejsza niż *operationTimeoutInMilliseconds*, dostawca nie będzie ponawiać próby.<br/><br/>Aby uzyskać więcej informacji na temat *retryTimeoutInMilliseconds*, zobacz [uwagi dotyczące *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) w sekcji [uwagi dotyczące atrybutów](#attribute-notes) . |
| *redisSerializerType* | string | *nie dotyczy* | Określa nazwę typu kwalifikowanego zestawu klasy implementującej Microsoft. Web. Redis. ISerializer i zawierający logikę niestandardową do serializacji i deserializacji wartości. Aby uzyskać więcej informacji, zobacz [Informacje o *redisSerializerType* ](#about-redisserializertype) w sekcji [uwagi dotyczące atrybutów](#attribute-notes) . |
|

## <a name="attribute-notes"></a>Uwagi dotyczące atrybutów

### <a name="setting-connectionstring"></a>Ustawianie *ConnectionString*

Wartość parametru *ConnectionString* jest używana jako klucz do pobierania rzeczywistych parametrów połączenia z AppSettings, jeśli taki ciąg istnieje w appSettings. Jeśli nie można odnaleźć wewnątrz AppSettings, wartość *ConnectionString* zostanie użyta jako klucz do pobrania rzeczywistych parametrów połączenia z sekcji **ConnectionString** pliku Web. config (Jeśli ta sekcja istnieje). Jeśli parametry połączenia nie istnieją w sekcji AppSettings lub nie można użyć parametru Web. config **ConnectionString** , wartość literału *ConnectionString* będzie używana jako parametry połączenia podczas tworzenia stackexchange. Redis. ConnectionMultiplexer.

W poniższych przykładach pokazano, jak używać *ConnectionString* .

#### <a name="example-1"></a>Przykład 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

W `web.config`Użyj powyższego klucza jako wartości parametru, a nie wartości rzeczywistej.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Przykład 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

W `web.config`Użyj powyższego klucza jako wartości parametru, a nie wartości rzeczywistej.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Przykład 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Uwagi dotyczące *parametr throwOnError*

Obecnie w przypadku wystąpienia błędu podczas operacji sesji dostawca stanu sesji zgłosi wyjątek. Spowoduje to zamknięcie aplikacji.

To zachowanie zostało zmodyfikowane w sposób, który obsługuje oczekiwania istniejących użytkowników dostawcy stanu sesji ASP.NET, a także zapewnia możliwość działania na wyjątkach, w razie potrzeby. Zachowanie domyślne nadal zgłasza wyjątek w przypadku wystąpienia błędu spójnego z innymi dostawcami stanu sesji ASP.NET. istniejący kod powinien działać tak samo jak poprzednio.

Jeśli ustawisz *parametr throwOnError* na **false**, zamiast zgłaszać wyjątek w przypadku wystąpienia błędu, będzie on działać w trybie dyskretnym. Aby sprawdzić, czy wystąpił błąd i, jeśli tak, sprawdzić, jaki był wyjątek, sprawdź Właściwość statyczną *Microsoft. Web. Redis. pakietu redissessionstateprovider. LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Uwagi dotyczące *retryTimeoutInMilliseconds*

Zapewnia to kilka logiki ponawiania, aby uprościć przypadek, w którym niektóre operacje na sesji powinny być ponawiane z powodu błędu sieci, a jednocześnie pozwala kontrolować limit czasu ponawiania prób lub zrezygnować całkowicie z ponowień.

Jeśli ustawisz *retryTimeoutInMilliseconds* na liczbę, na przykład 2000, wtedy, gdy operacja sesji nie powiedzie się, zostanie ponowiona próba dla 2000 milisekund przed potraktowaniem jej jako błędu. Aby Dostawca stanu sesji stosował tę logikę ponowień, po prostu Skonfiguruj limit czasu. Pierwsza ponowna próba nastąpi po 20 milisekundach, co jest wystarczające w większości przypadków, gdy wystąpi błąd sieci. Następnie będzie ponawiać próbę co sekundę do czasu przełączenia. Po upływie limitu czasu program ponowi próbę jeszcze raz, aby upewnić się, że nie wytnie limitu czasu przez (maksymalnie) 1 sekundy.

Jeśli nie sądzisz, że potrzebujesz ponowienia próby (na przykład w przypadku uruchamiania serwera Redis na tym samym komputerze, na którym znajduje się aplikacja), lub jeśli chcesz samodzielnie obsłużyć logikę ponowień, ustaw wartość *retryTimeoutInMilliseconds* na 0.

### <a name="about-redisserializertype"></a>Informacje o *redisSerializerType*

Domyślnie Serializacja do przechowywania wartości w Redis jest wykonywana w formacie binarnym dostarczonym przez klasę **BinaryFormatter** . Użyj *redisSerializerType* , aby określić nazwę typu kwalifikowanego zestawu klasy implementującej **Microsoft. Web. Redis. ISerializer** i która ma logikę niestandardową do serializacji i deserializacji wartości. Na przykład poniżej przedstawiono klasę serializatorów JSON używającą JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Zakładając, że ta klasa jest zdefiniowana w zestawie o nazwie **MyCompanyDll**, można ustawić parametr *redisSerializerType* , aby go użyć:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Dyrektywa wyjściowej pamięci podręcznej

Dodaj dyrektywę OutputCache do każdej strony, dla której chcesz buforować dane wyjściowe.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

W poprzednim przykładzie dane buforowanej strony pozostają w pamięci podręcznej przez 60 sekund, a inna wersja strony jest buforowana dla każdej kombinacji parametrów. Aby uzyskać więcej informacji na temat dyrektywy OutputCache, zobacz [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Po wykonaniu tych kroków aplikacja zostanie skonfigurowana do korzystania z dostawcy wyjściowej pamięci podręcznej Redis.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [dostawcą stanu sesji ASP.NET dla usługi Azure cache for Redis](cache-aspnet-session-state-provider.md).
