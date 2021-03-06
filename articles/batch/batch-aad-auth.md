---
title: Uwierzytelnianie Azure Batch Services za pomocą Azure Active Directory
description: Usługa Batch obsługuje uwierzytelnianie z usługi Batch w usłudze Azure AD. Dowiedz się, jak uwierzytelniać się na jeden z dwóch sposobów.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: labrenne
ms.openlocfilehash: f56c05f64086ac2e98e69d6b21fae7a0a63b5006
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019523"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Uwierzytelnianie rozwiązań usługi Batch za pomocą Active Directory

Azure Batch obsługuje uwierzytelnianie z [Azure Active Directory][aad_about] (Azure AD). Azure AD to usługa zarządzania tożsamościami w chmurze z wieloma dzierżawcami firmy Microsoft. Sama platforma Azure korzysta z usługi Azure AD do uwierzytelniania klientów, administratorów usług i użytkowników w organizacji.

W przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą Azure Batch można uwierzytelnić się na jeden z dwóch sposobów:

- Przy użyciu **uwierzytelniania zintegrowanego** do uwierzytelniania użytkownika, który działa z aplikacją. Aplikacja używająca uwierzytelniania zintegrowanego zbiera poświadczenia użytkownika i używa tych poświadczeń do uwierzytelniania dostępu do zasobów usługi Batch.
- Za pomocą **nazwy głównej usługi** do uwierzytelniania aplikacji nienadzorowanej. Nazwa główna usługi definiuje zasady i uprawnienia dla aplikacji w celu reprezentowania aplikacji podczas uzyskiwania dostępu do zasobów w czasie wykonywania.

Aby dowiedzieć się więcej o usłudze Azure AD, zapoznaj się z [dokumentacją Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Punkty końcowe uwierzytelniania

Aby uwierzytelniać aplikacje wsadowe za pomocą usługi Azure AD, należy uwzględnić w kodzie niektóre dobrze znane punkty końcowe.

### <a name="azure-ad-endpoint"></a>Punkt końcowy usługi Azure AD

Podstawowy punkt końcowy urzędu usługi Azure AD to:

`https://login.microsoftonline.com/`

Aby uwierzytelnić się w usłudze Azure AD, użyj tego punktu końcowego wraz z IDENTYFIKATORem dzierżawy (identyfikator katalogu). Identyfikator dzierżawy określa dzierżawy usługi Azure AD do uwierzytelniania. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Punkt końcowy specyficzny dla dzierżawy jest wymagany podczas uwierzytelniania za pomocą nazwy głównej usługi. 
> 
> Punkt końcowy specyficzny dla dzierżawy jest opcjonalny w przypadku uwierzytelniania przy użyciu uwierzytelniania zintegrowanego, ale zalecane. Można jednak również użyć wspólnego punktu końcowego usługi Azure AD. Wspólny punkt końcowy zawiera interfejs zbierania poświadczeń ogólnych, gdy nie podano określonej dzierżawy. Wspólny punkt końcowy jest `https://login.microsoftonline.com/common`.
>
>

Aby uzyskać więcej informacji na temat punktów końcowych usługi Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Punkt końcowy zasobu partii

Użyj **punktu końcowego zasobów Azure Batch** , aby uzyskać token do uwierzytelniania żądań do usługi Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Rejestrowanie aplikacji w dzierżawie

Pierwszym krokiem korzystania z usługi Azure AD do uwierzytelniania jest rejestrowanie aplikacji w dzierżawie usługi Azure AD. Rejestrowanie aplikacji umożliwia wywoływanie Azure [Active Directory Authentication Library][aad_adal] (ADAL) w kodzie. Biblioteki ADAL zapewnia interfejs API do uwierzytelniania za pomocą usługi Azure AD z poziomu aplikacji. Rejestracja aplikacji jest wymagana niezależnie od tego, czy zamierzasz używać uwierzytelniania zintegrowanego, czy nazwy głównej usługi.

Możesz zarejestrować swoją aplikację, należy podać informacje o aplikacji do usługi Azure AD. Następnie usługa Azure AD udostępnia identyfikator aplikacji (nazywany także *identyfikatorem klienta*) używany do kojarzenia aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o IDENTYFIKATORze aplikacji, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować aplikację w usłudze Batch, wykonaj kroki opisane w sekcji [Dodawanie aplikacji](../active-directory/develop/quickstart-register-app.md) w temacie [Integrowanie aplikacji z Azure Active Directory][aad_integrate]. Jeśli aplikacja jest zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI dla **identyfikatora URI przekierowania**. Nie musi to być rzeczywisty punkt końcowy.

Po zarejestrowaniu aplikacji zobaczysz identyfikator aplikacji:

![Rejestrowanie aplikacji usługi Batch w usłudze Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Uzyskaj identyfikator dzierżawy dla Active Directory

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD, która udostępnia usługi uwierzytelniania aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz usługi Active Directory.
1. Wybierz pozycję **Właściwości**.
1. Skopiuj wartość identyfikatora GUID **identyfikator katalogu**. Ta wartość jest również określany jako identyfikator dzierżawy.

![Skopiuj identyfikator katalogu](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Użyj uwierzytelniania zintegrowanego

Aby uwierzytelnić się przy użyciu uwierzytelniania zintegrowanego, należy przyznać aplikacji uprawnienia do nawiązywania połączeń z interfejsem API usługi Batch. Ten krok umożliwia aplikacji uwierzytelnianie wywołań interfejsu API usługi Batch za pomocą usług Azure AD.

Po zarejestrowaniu aplikacji wykonaj następujące kroki w Azure Portal, aby udzielić dostępu do usługi Batch:

1. W okienku nawigacji po lewej stronie Azure Portal wybierz pozycję **wszystkie usługi**. Wybierz pozycję **rejestracje aplikacji**.
1. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę aplikacji](./media/batch-aad-auth/search-app-registration.png)

1. Wybierz aplikację i wybierz pozycję **uprawnienia interfejsu API**.
1. W sekcji **uprawnienia interfejsu API** wybierz pozycję **Dodaj uprawnienie**.
1. W obszarze **Wybierz interfejs API**Wyszukaj interfejs API usługi Batch. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch.
1. Po znalezieniu interfejsu API usługi Batch zaznacz go i wybierz pozycję **Wybierz**.
1. W obszarze **Wybierz uprawnienia**zaznacz pole wyboru obok pozycji **dostęp do usługi Azure Batch** , a następnie wybierz pozycję **Dodaj uprawnienia**.

Sekcja **uprawnienia interfejsu API** pokazuje teraz, że aplikacja usługi Azure AD ma dostęp do zarówno Microsoft Graph, jak i interfejsu API usługi Batch. Uprawnienia są udzielane Microsoft Graph automatycznie po pierwszym zarejestrowaniu aplikacji w usłudze Azure AD.

![Przyznawanie uprawnień interfejsu API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Korzystanie z nazwy głównej usługi

Aby uwierzytelnić aplikację, w której działa nienadzorowana, należy użyć nazwy głównej usługi. Po zarejestrowaniu aplikacji wykonaj następujące kroki w Azure Portal, aby skonfigurować jednostkę usługi:

1. Zażądaj wpisu tajnego dla aplikacji.
1. Przypisywanie kontroli dostępu opartej na rolach (RBAC) do aplikacji.

### <a name="request-a-secret-for-your-application"></a>Żądaj wpisu tajnego dla aplikacji

Gdy aplikacja jest uwierzytelniana za pomocą nazwy głównej usługi, wysyła zarówno identyfikator aplikacji, jak i klucz tajny do usługi Azure AD. Należy utworzyć i skopiować klucz tajny do użycia w kodzie.

Wykonaj następujące kroki w Azure Portal:

1. W okienku nawigacji po lewej stronie Azure Portal wybierz pozycję **wszystkie usługi**. Wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację z listy rejestracji aplikacji.
1. Wybierz aplikację, a następnie wybierz pozycję **certyfikaty & wpisy tajne**. W sekcji wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**.
1. Aby utworzyć wpis tajny, wprowadź opis wpisu tajnego. Następnie wybierz pozycję wygaśnięcia dla wpisu tajnego z jednego roku, dwóch lat lub bez wygaśnięcia.
1. Wybierz pozycję **Dodaj** , aby utworzyć i wyświetlić wpis tajny. Skopiuj wartość klucza tajnego do bezpiecznego miejsca, ponieważ nie będzie można uzyskać do niego dostępu po opuszczeniu strony.

    ![Tworzenie klucza tajnego](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Przypisywanie kontroli RBAC do aplikacji

Aby uwierzytelnić się za pomocą jednostki usługi, należy przypisać kontrolę RBAC do aplikacji. Wykonaj następujące kroki:

1. W Azure Portal przejdź do konta wsadowego używanego przez aplikację.
1. W sekcji **Ustawienia** konta Partia zadań wybierz pozycję **Access Control (IAM)** .
1. Wybierz kartę **przypisania ról** .
1. Wybierz **Dodaj przypisanie roli**.
1. Z listy rozwijanej **rola** wybierz rolę *współautor* lub *czytelnika* dla aplikacji. Aby uzyskać więcej informacji na temat tych ról, zobacz Wprowadzenie do [Access Control opartej na rolach w Azure Portal](../role-based-access-control/overview.md).  
1. W polu **Wybierz** wprowadź nazwę aplikacji. Wybierz aplikację z listy, a następnie wybierz pozycję **Zapisz**.

Aplikacja powinna teraz pojawić się w ustawieniach kontroli dostępu z przypisaną rolą RBAC.

![Przypisywanie roli RBAC do aplikacji](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Przypisywanie roli niestandardowej

Rola niestandardowa przyznaje użytkownikowi szczegółowe uprawnienia do przesyłania zadań, zadań i nie tylko. Dzięki temu użytkownicy mogą uniemożliwić wykonywanie operacji, które mają wpływ na koszt, na przykład tworząc pule lub modyfikując węzły.

Możesz użyć roli niestandardowej, aby przyznać uprawnienia użytkownikowi, grupie lub jednostce usługi usługi Azure AD dla następujących operacji RBAC:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft. Batch/batchAccounts/jobSchedules/Write
- Microsoft. Batch/batchAccounts/jobSchedules/Delete
- Microsoft. Batch/batchAccounts/jobSchedules/odczyt
- Microsoft. Batch/batchAccounts/Jobs/Write
- Microsoft. Batch/batchAccounts/Jobs/Delete
- Microsoft. Batch/batchAccounts/Jobs/Read
- Microsoft. Batch/batchAccounts/Certificates/Write
- Microsoft. Batch/batchAccounts/Certificates/Delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft. Batch/batchAccounts/Read (dla każdej operacji odczytu)
- Microsoft. Batch/batchAccounts/listKeys/Action (dla każdej operacji)

Role niestandardowe są przeznaczone dla użytkowników uwierzytelnionych przez usługę Azure AD, a nie poświadczenia konta usługi Batch (klucz współużytkowany). Zwróć uwagę, że poświadczenia konta w usłudze Batch dają pełne uprawnienia do konta w usłudze Batch. Należy również pamiętać, że zadania używające autopuli wymagają uprawnień na poziomie puli.

Oto przykład definicji roli niestandardowej:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Aby uzyskać ogólne informacje na temat tworzenia roli niestandardowej, zobacz [role niestandardowe dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Pobieranie Identyfikatora dzierżawy usługi Azure Active Directory

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD, która udostępnia usługi uwierzytelniania aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz usługi Active Directory.
1. Wybierz pozycję **Właściwości**.
1. Skopiuj wartość identyfikatora GUID **identyfikator katalogu**. Ta wartość jest również określany jako identyfikator dzierżawy.

![Skopiuj identyfikator katalogu](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Przykłady kodu

Przykłady kodu w tej sekcji przedstawiają sposób uwierzytelniania za pomocą usługi Azure AD przy użyciu uwierzytelniania zintegrowanego i nazwy głównej usługi. Większość z tych przykładów kodu korzysta z platformy .NET, ale koncepcje są podobne dla innych języków.

> [!NOTE]
> Token uwierzytelniania usługi Azure AD wygasa po upływie godziny. W przypadku korzystania z długotrwałego obiektu **BatchClient** zalecamy pobranie tokenu z biblioteki ADAL na każde żądanie, aby upewnić się, że zawsze masz prawidłowy token. 
>
>
> Aby to osiągnąć, należy napisać metodę pobierającą token z usługi Azure AD i przekazać tę metodę do obiektu **BatchTokenCredentials** jako delegata. Metoda Delegate jest wywoływana dla każdego żądania do usługi Batch, aby upewnić się, że podano prawidłowy token. Domyślnie tokeny pamięci podręcznej biblioteki ADAL, dlatego nowy token jest pobierany z usługi Azure AD tylko wtedy, gdy jest to konieczne. Aby uzyskać więcej informacji na temat tokenów w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Przykład kodu: używanie zintegrowanego uwierzytelniania usługi Azure AD z usługą Batch .NET

Aby uwierzytelnić się przy użyciu zintegrowanego uwierzytelniania z usługi Batch .NET, należy odwołać się do pakietu [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) i pakietu [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Uwzględnij następujące instrukcje `using` w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołuje się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikatora dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołuje się do punktu końcowego zasobu usługi Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołuje się do konta w usłudze Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) aplikacji. Identyfikator aplikacji jest dostępny w ramach rejestracji aplikacji w Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Skopiuj również określony identyfikator URI przekierowania, jeśli aplikacja została zarejestrowana jako aplikacja natywna. Identyfikator URI przekierowania określony w kodzie musi być zgodny z identyfikatorem URI przekierowania, który został podany podczas rejestracji aplikacji:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Napisz metodę wywołania zwrotnego, aby uzyskać token uwierzytelniania z usługi Azure AD. Metoda wywołania zwrotnego **GetAuthenticationTokenAsync** pokazana tutaj wywołuje bibliotekę ADAL w celu uwierzytelnienia użytkownika, który działa z aplikacją. Metoda **AcquireTokenAsync** udostępniona przez bibliotekę ADAL wyświetla użytkownikowi informacje o poświadczeniach, a aplikacja jest wykonywana, gdy użytkownik je udostępni (chyba że ma już poświadczenia w pamięci podręcznej):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Utwórz obiekt **BatchTokenCredentials** , który przyjmuje delegat jako parametr. Użyj tych poświadczeń, aby otworzyć obiekt **BatchClient** . Tego obiektu **BatchClient** można użyć do kolejnych operacji w usłudze Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Przykład kodu: używanie jednostki usługi Azure AD w usłudze Batch .NET

Aby uwierzytelnić się za pomocą jednostki usługi w usłudze Batch .NET, należy odwołać się do pakietu [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) i pakietu [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Uwzględnij następujące instrukcje `using` w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołuje się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikatora dzierżawy. W przypadku korzystania z jednostki usługi należy podać punkt końcowy specyficzny dla dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołuje się do punktu końcowego zasobu usługi Batch:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołuje się do konta w usłudze Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) aplikacji. Identyfikator aplikacji jest dostępny w ramach rejestracji aplikacji w Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Określ klucz tajny, który został skopiowany z Azure Portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Napisz metodę wywołania zwrotnego, aby uzyskać token uwierzytelniania z usługi Azure AD. Metoda wywołania zwrotnego **GetAuthenticationTokenAsync** pokazana tutaj wywołuje biblioteki ADAL do uwierzytelniania nienadzorowanego:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Utwórz obiekt **BatchTokenCredentials** , który przyjmuje delegat jako parametr. Użyj tych poświadczeń, aby otworzyć obiekt **BatchClient** . Następnie użyj tego obiektu **BatchClient** dla kolejnych operacji w usłudze Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Przykład kodu: używanie jednostki usługi Azure AD w usłudze Batch Python

Aby uwierzytelnić się za pomocą jednostki usługi w usłudze Batch Python, zainstaluj i Odwołuj się do modułów [Azure-Batch](https://pypi.org/project/azure-batch/) i [Azure-Common](https://pypi.org/project/azure-common/) .

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

W przypadku korzystania z jednostki usługi należy podać identyfikator dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Odwołuje się do punktu końcowego zasobu usługi Batch:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Odwołuje się do konta w usłudze Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Określ identyfikator aplikacji (identyfikator klienta) aplikacji. Identyfikator aplikacji jest dostępny w ramach rejestracji aplikacji w Azure Portal:

```python
CLIENT_ID = "<application-id>"
```

Określ klucz tajny, który został skopiowany z Azure Portal:

```python
SECRET = "<secret-key>"
```

Utwórz obiekt **ServicePrincipalCredentials** :

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Użyj poświadczeń jednostki usługi, aby otworzyć obiekt **BatchServiceClient** . Następnie użyj tego obiektu **BatchServiceClient** dla kolejnych operacji w usłudze Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o usłudze Azure AD, zapoznaj się z [dokumentacją Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Szczegółowe przykłady pokazujące, jak używać biblioteki ADAL, są dostępne w bibliotece [przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .

- Aby dowiedzieć się więcej na temat nazw głównych usług, zobacz temat [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Aby utworzyć nazwę główną usługi przy użyciu Azure Portal, zobacz temat [Używanie portalu do tworzenia Active Directory aplikacji i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md). Możesz również utworzyć jednostkę usługi przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

- Aby uwierzytelnić aplikacje zarządzania usługą Batch za pomocą usługi Azure AD, zobacz temat [uwierzytelnianie rozwiązań do zarządzania partiami przy użyciu Active Directory](batch-aad-auth-management.md).

- Przykładowy kod w języku Python dotyczący tworzenia klienta usługi Batch uwierzytelniany przy użyciu tokenu usługi Azure AD znajduje się w temacie [wdrażanie Azure Batch niestandardowego obrazu przy użyciu skryptu języka Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) .

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z Azure Active Directory"
[azure_portal]: https://portal.azure.com
