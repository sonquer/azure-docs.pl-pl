---
title: Utwórz nową ofertę SaaS w portalu komercyjnym
description: Jak utworzyć nową ofertę SaaS (Software as a Service) w celu uzyskania listy lub sprzedaży w witrynie Azure Marketplace, AppSource lub za pośrednictwem programu Cloud Solution Provider (CSP) za pomocą portalu Marketplace w witrynie Microsoft Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/22/2020
ms.openlocfilehash: 4cac2fcd16f779a6b31e36ca175801eedaa31d5d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705823"
---
# <a name="create-a-new-saas-offer"></a>Utwórz nową ofertę SaaS

Aby rozpocząć tworzenie ofert oprogramowania jako usługi (SaaS), upewnij się, że najpierw [utworzysz konto Centrum partnerskiego](./create-account.md) i otworzysz [komercyjny pulpit nawigacyjny Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z wybraną kartą **Przegląd** .

![Komercyjny pulpit nawigacyjny portalu Marketplace w centrum partnerskim](./media/new-offer-overview.png)

>[!Note]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim zostaną zaktualizowane w systemie i przechowane przed ponownym opublikowaniem. Pamiętaj o przesłaniu oferty publikacji po wprowadzeniu zmian.

Wybierz pozycję + **Nowa oferta...** , a następnie wybierz element menu **oprogramowanie jako usługa** .

W przypadku wybrania innego typu oferty nastąpi przekierowanie do starszej [Portal Cloud partner](https://cloudpartner.azure.com/). Tylko oferty SaaS i Dynamics 365 są dostępne w portalu komercyjnej witryny Marketplace w centrum partnerskim.

![Utwórz okno oferty w centrum partnerskim](./media/new-offer-click.png)

Zostanie wyświetlone okno dialogowe **Nowa oferta** .

![Okno dialogowe Nowa oferta](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

- **Identyfikator oferty**: unikatowy identyfikator dla każdej oferty na Twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL dla oferty witryny Marketplace i szablonów Azure Resource Manager (jeśli dotyczy). Identyfikator oferty musi zawierać małe litery, znaki alfanumeryczne (w tym łączniki i podkreślenia, ale nie odstępy). **Identyfikator oferty** jest ograniczony do 50 znaków i nie można go zmienić po wybraniu pozycji *Utwórz*.  
Przykład: test-oferta-1
<br>Wynikiem jest adres URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias oferty**: nazwa używana do odwoływania się do oferty w portalu Centrum partnerskiego. Ta nazwa nie będzie używana w portalu Marketplace i różni się od *nazwy oferty* i innych wartości, które będą widoczne dla klientów. Tej wartości nie można zmienić po wybraniu opcji *Utwórz*.

<br>Przykład: Oferta testu 1&#8482;

Wybierz pozycję **Utwórz**.  Dla tej oferty została utworzona strona **przeglądu oferty** .  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** znajdują się następujące informacje:

- **Stan publikowania** przedstawia wizualną reprezentację kroków wymaganych do opublikowania oferty oraz czas wykonania poszczególnych kroków. Niekompletne ikony kroków publikowania zostaną wyszarzone.

- Menu **Przegląd oferty** zawiera listę linków do wykonywania operacji na tej ofercie. Ta lista operacji zmienia się w zależności od wyboru dokonanego dla oferty.  
    - Jeśli oferta jest wersją roboczą, Usuń wersję roboczą
    - Jeśli oferta jest oferowana na żywo, Zatrzymaj sprzedawanie
    - Jeśli oferta jest dostępna w wersji zapoznawczej — przejdź na żywo
    - Jeśli nie ukończono wylogowania wydawcy — Anuluj publikowanie

## <a name="offer-setup"></a>Konfiguracja oferty

Na karcie **Konfiguracja oferty** są wyświetlane poniższe informacje. Po zakończeniu tych pól wybierz pozycję **Zapisz** .

- **Czy chcesz sprzedawać w firmie Microsoft?** (Tak/nie)
    - **Tak**, chcemy sprzedawać swoją ofertę w firmie Microsoft, dzięki czemu firma Microsoft udostępnia transakcje portalu Marketplace w Twoim imieniu. oraz 
    - **Nie**, wolisz po prostu wystawić ofertę za pomocą rynków Marketplace, przetwarzać wszelkie transakcje pieniężne niezależnie od firmy Microsoft.

### <a name="sell-through-microsoft"></a>Sprzedawanie przez firmę Microsoft

Sprzedawanie przez firmę Microsoft zapewnia lepsze odnajdywanie i pozyskiwanie klientów, pozwala firmie Microsoft na hostowanie transakcji w witrynie Marketplace w Twoim imieniu i korzystanie z dostępnych globalnie możliwości handlowych firmy Microsoft.

#### <a name="saas-offer-requirements"></a>Wymagania oferty SaaS

Aby wyświetlić listę ofert oprogramowania jako usługi (SaaS) z komercyjnym rynkiem Marketplace w centrum partnerskim, należy spełnić następujące kryteria:

- Twoja oferta musi używać [usługi Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) do zarządzania tożsamościami i uwierzytelniania.
- Twoja oferta musi używać [interfejsów API realizacji SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) do integracji z portalem Azure Marketplace.
- Aby uzyskać bardziej szczegółowe wymagania, zobacz [Przewodnik po publikowaniu oferty SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Opcje cen i rozliczeń SaaS
Dzięki rozwiązaniom SaaS działającym w ramach subskrypcji platformy Azure wydawcy opłaty licencyjne są naliczane przez klientów obejmują koszt infrastruktury, w której wdrażane jest oprogramowanie. Użycie infrastruktury platformy Azure jest zarządzane i rozliczane dla Ciebie bezpośrednio przez partnera. Klient nie widzi rzeczywistych opłat za użycie infrastruktury. Wydawcy powinni uzyskać opłaty za użycie infrastruktury platformy Azure do ich cen licencji na oprogramowanie. 

Usługa SaaS oferuje comiesięczne lub roczne rozliczanie w oparciu o stałą opłatę za użytkownika lub opłaty za użycie przy użyciu taryfowej usługi rozliczania. Komercyjny Portal firmy Microsoft działa na modelu agencji, zgodnie z którym wydawcy ustawili ceny, klienci Microsoft Bills i Microsoft płaciją za wydawcę, przy jednoczesnym potrąceniu Opłaty za Agencję.

W poniższej tabeli przedstawiono przykład podziału kosztów i wypłatów w celu przedstawienia modelu agencji.

|**Koszt licencji**|**$100 miesięcznie**|
|:---|:---|
|Koszt użycia platformy Azure (D1/1-rdzeniowe)|Rozliczana bezpośrednio z wydawcą, a nie klientem|
|Klient jest rozliczany przez firmę Microsoft|$100,00 miesięcznie (w przypadku wydawców należy uwzględnić koszty związane z założeniami lub przekazaniem infrastruktury w ramach opłaty licencyjnej)|

|**Rachunki firmy Microsoft**|**$100 miesięcznie**|
|:---|:---|
|Firma Microsoft płaci 80% kosztu licencji <br>**dla zakwalifikowanych aplikacji SaaS, firma Microsoft płaci 90% kosztu licencji*|$80,00 miesięcznie <br>*$* 90,00 miesięcznie *|

- W tym przykładzie firma Microsoft rozlicza $100,00 od klienta na potrzeby licencji na oprogramowanie i płaci do wydawcy $80,00.
- Partnerzy, którzy zakwalifikowani do **obniżonej opłaty za usługę Marketplace** , będą widzieć obniżoną opłatę za transakcje w ofertach SaaS od maja 2019 do 2020 czerwca. W tym scenariuszu firma Microsoft Bills $100,00 na potrzeby licencji na oprogramowanie i płaci wydawcę $90,00.

> [!NOTE]
> **Zmniejszona opłata za usługę Marketplace**: w przypadku niektórych ofert SaaS opublikowanych na naszym rynku komercyjnym firma Microsoft obniży opłatę za usługę Marketplace z 20% (zgodnie z opisem w umowie Microsoft Publisher) do 10%. Aby oferta mogła zakwalifikować się, co najmniej jedna oferta musi zostać wystawiona przez firmę Microsoft, ponieważ jest to adres IP do współsprzedaży lub co do sprzedaży.  Przed końcem każdego miesiąca kalendarzowego należy spełnić co najmniej pięć (5) dni roboczych, aby uzyskać tę obniżoną opłatę za usługę Marketplace w danym miesiącu.  Zmniejszona opłata za usługę Marketplace nie dotyczy maszyn wirtualnych, zarządzanych aplikacji ani innych produktów udostępnianych za pośrednictwem komercyjnej witryny Marketplace.  Zmniejszona opłata za usługę Marketplace będzie dostępna tylko w przypadku ofert z tytułu licencji uzyskanych przez firmę Microsoft od 1 maja 2019 do 30 czerwca 2020.  Po tym czasie opłata za usługę Marketplace powróci do normalnej kwoty.

### <a name="list-through-microsoft"></a>Wystaw za poorednictwem firmy Microsoft

Promuj swoją firmę firmie Microsoft, tworząc listę Marketplace. Wybranie do wyświetlania listy tylko oferty, a nie w firmie Microsoft oznacza, że firma Microsoft nie uczestniczy bezpośrednio w transakcjach licencji na oprogramowanie. Nie ma żadnej powiązanej opłaty za transakcje, a wydawca utrzymuje 100% wszelkich opłat za Licencjonowanie oprogramowania zebranych od klienta. Jednak Wydawca jest odpowiedzialny za obsługę wszystkich aspektów transakcji związanych z licencją na oprogramowanie, w tym między innymi w kolejności realizacji, pomiaru, rozliczeń, fakturowania, płatności i kolekcji.

- **Jak chcesz, aby potencjalni klienci mogli korzystać z tej oferty z licytacją?**

##### <a name="get-it-now-free"></a>Pobierz teraz (bezpłatnie)
Wystaw swoją ofertę bezpłatnie klientom, podając prawidłowy adres URL (począwszy od *protokołu HTTP* lub *https*), w którym można uzyskać próbę [uwierzytelniania przy użyciu jednego kliknięcia, korzystając z Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Na przykład: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Bezpłatna wersja próbna (lista)
Utwórz listę ofert klientom z linkiem do bezpłatnej wersji próbnej, podając prawidłowy adres URL (począwszy od *protokołu HTTP* lub *https*), w którym można uzyskać próbkę uwierzytelniania za pomocą [jednego kliknięcia przy użyciu Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Na przykład: `https://contoso.com/trial/saas-app`. Oferta z listą bezpłatnych wersji próbnych jest tworzona, zarządzana i konfigurowana przez usługę i nie ma subskrypcji zarządzanych przez firmę Microsoft.

> [!NOTE]
> Tokeny wysyłane przez aplikację za pomocą linku do wersji próbnej mogą być używane tylko w celu uzyskania informacji o użytkowniku za pomocą usługi Azure AD w celu zautomatyzowania tworzenia kont w aplikacji. Konta Microsoft (MSA) nie są obsługiwane na potrzeby uwierzytelniania przy użyciu tego tokenu.

##### <a name="contact-me"></a>Kontakt ze mną
Zbierz informacje kontaktowe klienta, łącząc system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i źródłem witryny Marketplace, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Aby uzyskać więcej informacji o konfigurowaniu programu CRM, zobacz [łączenie z usługą Zarządzanie potencjalnymi klientami](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Przykładowa lista ofert w witrynie Marketplace

![Przykładowa lista ofert w witrynie Marketplace z notatkami](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Włącz stację testową

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, dając im możliwość "Wypróbuj przed zakupem", co spowodowało zwiększenie konwersji i generowanie wysoce wykwalifikowanych klientów. [Dowiedz się więcej o testowaniu dysków.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Włącz stację testową** (CheckBox)

Po włączeniu dysku testowego zostanie wyświetlony monit o skonfigurowanie środowiska demonstracyjnego dla klientów w celu wypróbowania oferty przez określony czas. 

### <a name="type-of-test-drive"></a>Typ dysku testowego

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : szablon wdrożenia zawierający wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, korzystają tylko z zasobów platformy Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Firma Microsoft obsługuje i utrzymuje usługę Test Drive (w tym aprowizacji i wdrożenia) dla systemu planowania zasobów przedsiębiorstwa w przedsiębiorstwie  
- **[Dynamics 365 dla zaangażowania klienta](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Firma Microsoft obsługuje i utrzymuje usługę Test Drive (w tym Provisioning and Deployment) dla systemu zaangażowania klientów (sprzedaż, usługa, usługa projektu, usługa pola itp.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Firma Microsoft hostuje i utrzymuje usługę Test Drive (łącznie z obsługą i wdrażaniem) dla systemu planowania zasobów w ramach finansów i operacji (finansów, operacji, produkcji, łańcucha dostaw itp.). 
- **[Aplikacja logiki](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : szablon wdrożenia obejmujący wszystkie złożone architektury rozwiązań. Wszystkie produkty niestandardowe powinny używać tego typu dysku testowego.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : osadzony link do pulpitu nawigacyjnego skompilowanego niestandardowo. Produkty, które chcą zaprezentować interaktywną wizualizację Power BI, powinny używać tego typu dysku testowego. Wszystko, co musisz przekazać, to osadzony adres URL Power BI.

#### <a name="additional-test-drive-resources"></a>Dodatkowe zasoby dotyczące dysku testowego
- [Testowanie najlepszych rozwiązań technicznych](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Testowanie najlepszych rozwiązań marketingowych](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Testowanie dysku — Omówienie jednego modułu stronicowania](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Zarządzanie potencjalnymi klientami

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Dodatkowe zasoby zarządzania potencjalnymi klientami
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Przegląd zarządzania potencjalnymi klientami — Omówienie jednego modułu stronicowania](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji.

## <a name="properties"></a>Właściwości

Na karcie **Właściwości** zostanie wyświetlona prośba o zdefiniowanie kategorii i branż używanych do grupowania oferty na rynkach Marketplace, umów prawnych wspierających Twoją ofertę i wersję aplikacji.

Po zakończeniu tych pól wybierz pozycję **Zapisz** .

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną (1) i maksymalnie trzy kategorie (3) używane do grupowania oferty w odpowiednie obszary wyszukiwania w portalu Marketplace. Dowiedz się, w jaki sposób oferta obsługuje te kategorie w opisie oferty.

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Wersja aplikacji

To pole jest opcjonalne i używane w witrynie AppSource Marketplace do identyfikowania numeru wersji oferty.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardowa umowa dla komercyjnego portalu Microsoft Marketplace

Firma Microsoft oferuje standardowy szablon kontraktu.

- **Używasz standardowej umowy dotyczącej komercyjnej witryny Microsoft Marketplace?**

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardową umowę dotyczącą komercyjnego portalu Microsoft Marketplace, aby ułatwić obsługę transakcji w portalu Marketplace. Zamiast przedstawiać niestandardowe warunki i postanowienia, Komercyjni wydawcy portalu Marketplace mogą zaoferować swoje oprogramowanie zgodnie z umową standardową, której klienci muszą jedynie Zweryfikuj i akceptować. Kontrakt standardowy można znaleźć tutaj: https://go.microsoft.com/fwlink/?linkid=2041178.

Możesz wybrać opcję użycia kontraktu standardowego zamiast podawania własnych warunków i postanowień, wybierając opcję "Użyj standardowej umowy dla portalu komercyjnego".

![Korzystanie z pola wyboru kontraktu standardowego](./media/use-standard-contract.png)

> [!NOTE]
> Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej portalu komercyjnego firmy Microsoft nie można korzystać ze swoich niestandardowych warunków i postanowień. Jest to scenariusz "lub". Możesz zaoferować swoje rozwiązanie w ramach standardowej umowy **lub** własnych warunków i postanowień. Jeśli chcesz zmodyfikować warunki kontraktu standardowego, możesz to zrobić za pomocą standardowych poprawek kontraktu.

#### <a name="standard-contract-amendments"></a>Standardowe zmiany kontraktu

Standardowe zmiany umów umożliwiają wydawcom wybranie standardowych warunków umowy dla uproszczenia i dostosowanie warunków dla ich produktu lub firmy. Klienci muszą jedynie przejrzeć zmiany w umowie, jeśli zostali już zrecenzowani i zaakceptowali umowę standardową firmy Microsoft.

Istnieją dwa rodzaje zmian dostępnych dla komercyjnych wydawców portalu Marketplace:

- Uniwersalne zmiany: te zmiany są powszechnie stosowane do standardowej umowy dla wszystkich klientów. Uniwersalne zmiany są wyświetlane dla każdego klienta oferty w przepływie zakupu. Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki.
- Zmiany niestandardowe: te zmiany są specjalnymi zmianami w standardowej umowie, które są przeznaczone dla określonych klientów tylko za pośrednictwem identyfikatorów dzierżaw platformy Azure. Wydawcy mogą wybrać dzierżawcę, do którego chce się kierować. Tylko klienci z dzierżawy będą widzieć niestandardowe warunki zmiany w przepływie zakupu oferty.  Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki.

>[!NOTE]
> Te dwa typy zmian stosują się między sobą. Klienci z niestandardowymi zmianami otrzymają także uniwersalną zmianę do standardowej umowy podczas zakupu.

**Zasady uniwersalnej zmiany do standardowej umowy dotyczącej komercyjnego rynku firmy Microsoft**: w tym polu Wprowadź postanowienia dotyczące uniwersalnej poprawki. Możesz wprowadzić pojedynczą zmianę uniwersalną dla każdej oferty. W tym polu można wprowadzić nieograniczoną liczbę znaków. Te warunki są wyświetlane klientom w AppSource, w witrynie Azure Marketplace i/lub Azure Portal podczas odnajdywania i przepływu zakupów.

**Niestandardowe warunki zmiany do standardowej umowy dotyczącej komercyjnego rynku firmy Microsoft**: Zacznij od wybrania pozycji **Dodaj niestandardowe warunki zmiany**. Dla każdej oferty możesz podać maksymalnie 10 niestandardowych postanowień.

- **Niestandardowe warunki zmiany**: wprowadź niestandardowe warunki zmiany w polu niestandardowe warunki zmiany. W tym polu można wprowadzić nieograniczoną liczbę znaków. Tylko klienci z identyfikatorów dzierżawców, którzy określisz dla tych terminów niestandardowych, będą przedstawiani niestandardowe warunki zmiany w przepływie zakupu oferty w Azure Portal.  
- **Identyfikatory dzierżawców** (wymagane): Każda Poprawka niestandardowa może być przeznaczona do maksymalnie 20 identyfikatorów dzierżawy. W przypadku dodania niestandardowej poprawki należy podać co najmniej jeden identyfikator dzierżawy. Identyfikator dzierżawy identyfikuje Twojego klienta na platformie Azure. Możesz polecić klienta dla tego identyfikatora i znaleźć go, przechodząc do portal.azure.com > Azure Active Directory > właściwości. Wartość identyfikatora katalogu to identyfikator dzierżawy (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy organizacji klienta przy użyciu adresu URL nazwy domeny, co [to jest mój Microsoft Azure i identyfikator dzierżawy pakietu Office 365?](https://www.whatismytenantid.com).
- **Opis** (opcjonalnie): Opcjonalnie podaj przyjazny opis identyfikatora dzierżawy, który pomaga zidentyfikować klienta, którego celem jest zmiana.

#### <a name="terms-and-conditions"></a>Postanowienia

Jeśli chcesz podać własne niestandardowe warunki i postanowienia, możesz je wprowadzić w polu warunki i postanowienia. W tym polu można wprowadzić do 10 000 znaków tekstu. Jeśli warunki i postanowienia wymagają dłuższego opisu, wprowadź w tym polu link pojedynczego adresu URL, w którym można znaleźć warunki i postanowienia. Będzie ona wyświetlana klientom jako aktywne łącze.

Aby móc wypróbować ofertę, klienci muszą zaakceptować te warunki.

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji.

## <a name="offer-listing"></a>Lista oferty

Na karcie Lista ofert są wyświetlane języki (i rynki), w których oferta jest dostępna. obecnie w języku angielskim (Stany Zjednoczone) jest jedyną dostępną lokalizacją. Ponadto na tej stronie jest wyświetlany stan listy charakterystycznej dla języka oraz Data/godzina dodania. Musisz zdefiniować szczegóły witryny Marketplace (nazwę oferty, opis, wyszukiwane terminy itp.) dla każdego języka/rynku.

> [!NOTE]
> Oferta zawartości oferty (na przykład opis oferty, dokumenty, zrzuty ekranu, warunki użytkowania i zasady zachowania poufności informacji) nie muszą być w języku angielskim, o ile opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

### <a name="offer-listings"></a>Oferty ofert

Podaj szczegóły, które mają być wyświetlane w witrynie Marketplace, w tym opisy oferty i zasobów marketingowych.

- **Nazwa** (wymagana): Nazwa zdefiniowana w tym miejscu zostanie wyświetlona jako tytuł oferty w wybranym portalu Marketplace. Nazwa jest wstępnie wypełniana na podstawie poprzedniej nowej pozycji **oferty** . Nazwa może być znakiem towarowym. Nie może zawierać znaków emoji (chyba że są to symbole towarowe i praw autorskich) i muszą być ograniczone do 50 znaków.
- **Podsumowanie** (wymagane): Podaj krótki opis oferty, który ma być używany na potrzeby wyników wyszukiwania na liście w portalu Marketplace. W tym polu można wprowadzić do 100 znaków tekstu.
- **Opis** (wymagane): Podaj opis oferty, która ma zostać wyświetlona na liście przeglądów portalu Marketplace. Rozważ uwzględnienie propozycji wartości, najważniejszych korzyści, wszelkich kategorii lub związków branżowych, możliwości zakupu w aplikacji, wszelkich wymaganych informacji oraz linku, aby dowiedzieć się więcej.
W tym polu można wprowadzić do 3 000 znaków tekstu. Aby uzyskać dodatkowe wskazówki, zobacz artykuł [Napisz doskonały opis aplikacji](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Słowa kluczowe wyszukiwania**: Wprowadź do trzech słów kluczowych wyszukiwania, których klienci mogą używać w celu znalezienia oferty w portalu Marketplace.
- **Instrukcje dotyczące** rozpoczynania pracy (wymagane): Wyjaśnij, jak skonfigurować i rozpocząć korzystanie z aplikacji dla potencjalnych klientów.  Ten przewodnik Szybki Start może zawierać linki do bardziej szczegółowej dokumentacji online. W tym polu można wprowadzić do 3 000 znaków tekstu.

#### <a name="description"></a>**Opis**

To pole jest wymagane. Elementy do uwzględnienia w **opisie**:

* Jasno opisz swoją wartość oferty w pierwszych kilku zdaniach opisu.  
* Należy pamiętać, że pierwsze niektóre zdania mogą być wyświetlane w wynikach wyszukiwania.  
* Nie należy polegać na funkcjach i funkcjach, aby sprzedawać produkt. Zamiast tego należy skoncentrować się na dostarczanej wartości.  
* Korzystaj z specyficznych dla branży słownictwa lub takich słów, jak to możliwe.

Podstawowe składniki Twojej pozycji wartości powinny zawierać następujące informacje:

* Opis produktu.
* Typ użytkownika, który korzysta z produktu.
* Klienci muszą lub cierpili adresy produktów.

Aby zwiększyć atrakcyjność **opisu** oferty, użyj edytora tekstu sformatowanego do formatowania opisu.

![Korzystanie z edytora tekstu sformatowanego](./media/text-editor2.png)

Skorzystaj z poniższych instrukcji, aby użyć edytora tekstu sformatowanego:

- Aby zmienić format zawartości, zaznacz tekst, który chcesz sformatować, i wybierz styl tekstu, jak pokazano poniżej:

     ![Zmienianie formatu tekstu przy użyciu edytora tekstu sformatowanego](./media/text-editor3.png)

- Aby dodać listę punktowaną lub numerowaną do tekstu, Użyj poniższych opcji:

     ![Używanie edytora tekstu sformatowanego do dodawania list](./media/text-editor4.png)

- Aby dodać lub usunąć wcięcie do tekstu, Użyj poniższych opcji:

     ![Używanie edytora tekstu sformatowanego do wcięcia](./media/text-editor5.png)

#### <a name="links"></a>Linki

- **Zasady ochrony prywatności** (wymagane): link do zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz podawanie prawidłowych zasad zachowania poufności informacji.
- **Materiały marketingowe programu CSP** (opcjonalnie): Podaj link do materiałów marketingowych, jeśli wybierzesz opcję rozszerania oferty do programu w programie [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . Dostawca usług kryptograficznych rozszerza ofertę na szerszego grona wykwalifikowanych klientów, umożliwiając partnerom programu CSP łączenie się z ofertą, rynku i sprzedaży. Tacy odsprzedawcy będą musieli uzyskać dostęp do materiałów marketingowych oferty. Aby uzyskać więcej informacji, zobacz [usługi przejdź do rynku](https://partner.microsoft.com/reach-customers/gtm).
- **Użyteczne linki** (opcjonalnie): opcjonalne uzupełniające dokumenty online dotyczące aplikacji lub powiązanych usług wymienionych przez podanie **tytułu** i **adresu URL**. Dodaj dodatkowe przydatne linki, klikając pozycję **+ Dodaj adres URL**.

#### <a name="contact-information"></a>Informacje kontaktowe

- **Kontakty**: dla każdego kontaktu z klientem Podaj **nazwisko**pracownika, **numer telefonu**i adres **e-mail** .  ( *Nie będą* one wyświetlane publicznie). **Adres URL pomocy** technicznej jest również wymagany dla grupy **kontaktów pomoc techniczna** .  (Te informacje *będą* wyświetlane publicznie).

**Kontakt z pomocą techniczną** (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.

**Kontakt inżynieryjny** (wymagany): w przypadku pytań technicznych.

**Kontakt z menedżerem kanału** (wymagany): w przypadku pytań odsprzedawców związanych z programem CSP.

#### <a name="files-and-images"></a>Pliki i obrazy

- **Dokumenty** (wymagane): Dodaj powiązane dokumenty marketingowe dla oferty w formacie PDF, co daje co najmniej jedną (1) i trzy (3) dokumenty na ofertę.
- **Obrazy** (opcjonalnie): istnieje wiele miejsc, w których obrazy logo oferty mogą pojawić się w portalu Marketplace, co wymaga następujących rozmiarów — małe: 48 x 48 pikseli _(wymagane),_ średni: 90 x 90 pikseli, duże: 216 x 216 pikseli _(wymagane),_ szerokie: 255 x 115 pikseli i Hero: 815 x 290 pikseli. Wszystkie obrazy muszą znajdować się w. Format PNG.
- **Zrzuty ekranu** (wymagane): Dodaj zrzuty ekranu pokazujące Twoją ofertę. Można dodać maksymalnie pięć (5) zrzutów ekranu i mieć rozmiar o 1280 x 720 pikseli. Wszystkie obrazy muszą znajdować się w. Format PNG.
- **Wideo** (opcjonalnie): Dodaj linki do filmów wideo pokazujących Twoją ofertę. Możesz używać linków do filmów wideo w usłudze YouTube i/lub Vimeo, które są wyświetlane wraz z ofertą dla klientów. Należy również wprowadzić obraz miniatury wideo o rozmiarach do 1280 x 720 pikseli w formacie PNG. Możesz wyświetlić maksymalnie cztery wideo dla każdej oferty.

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

- [Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Wersja zapoznawcza

Karta **Podgląd** umożliwia zdefiniowanie ograniczonej liczby odbiorców w **wersji zapoznawczej** w celu udostępnienia oferty przed opublikowaniem oferty w szerszym gronie odbiorców w portalu Marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w wersji zapoznawczej wybierz pozycję **Przejdź na żywo** , aby Twoja oferta mogła zostać opublikowana na żywo dla odbiorców publicznych w portalu Marketplace.

- **Zdefiniuj odbiorców wersji zapoznawczej: Dodaj pojedynczą wiadomość e-mail w usłudze AAD/MSA na wiersz wraz z opcjonalnym opisem.**

Dodaj do dziesięciu (10) adresy e-mail ręcznie lub dwadzieścia (20) w przypadku przekazywania pliku CSV dla istniejących kont Microsoft (MSA) lub kont Azure Active Directory, aby pomóc w weryfikacji oferty przed opublikowaniem na żywo. Dodając te konta, można zdefiniować odbiorców, którzy będą mogli uzyskać dostęp do wersji zapoznawczej do oferty przed opublikowaniem jej w portalu Marketplace. Jeśli Twoja oferta jest już aktywna, możesz nadal zdefiniować odbiorcę w wersji zapoznawczej do testowania wszelkich zmian lub aktualizacji oferty.

> [!NOTE]
> Odbiorcy wersji zapoznawczej różnią się od odbiorców prywatnych. Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty _przed_ opublikowaniem jej na żywo w portalu Marketplace. Możesz również utworzyć plan i udostępnić go tylko dla odbiorców prywatnych. Na karcie **Lista planowania** możesz zdefiniować odbiorców prywatnych z tym pole wyboru to **jest plan prywatny** . Następnie można zdefiniować odbiorców prywatnych do 20 000 klientów korzystających z identyfikatorów dzierżaw platformy Azure.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Karta **konfiguracja techniczna** zawiera szczegółowe informacje techniczne (ścieżkę URL, element webhook, identyfikator dzierżawy i identyfikator aplikacji) używane do nawiązania połączenia z ofertą. To połączenie umożliwia nam zainicjowanie oferty dla klienta końcowego, jeśli zdecyduje się ją nabyć. Diagramy opisujące użycie zebranych pól są dostępne w dokumentacji dotyczącej [interfejsów API realizacji SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **Adres URL strony docelowej** (wymagane): Zdefiniuj adres URL witryny, po którym klienci będą mogli po uzyskaniu oferty z portalu Marketplace. Ten adres URL będzie punktem końcowym, który odbiera token, gdy klient jest kierowany do strony. Ten token może być wymieniany w celu uzyskania szczegółowych informacji o aprowizacji za pomocą rozwiązania w interfejsach API realizacji. Te szczegóły i wszelkie inne zbierane informacje mogą być używane jako część strony sieci Web interaktywnej klienta wbudowanej w środowisko użytkownika w celu ukończenia rejestracji i aktywowania zakupu.

- **Element webhook połączenia** (wymagany): dla wszystkich zdarzeń asynchronicznych wymaganych przez firmę Microsoft w imieniu klienta (przykład: subskrypcja SaaS stała się nieprawidłowa) wymagane jest podanie elementu webhook połączenia. Jeśli nie masz jeszcze systemu elementu webhook, najprostszą konfiguracją jest posiadanie aplikacji logiki punktu końcowego protokołu HTTP, która będzie nasłuchiwać wszystkich zdarzeń, które są w nim ogłaszane, a następnie odpowiednio obsługiwać je (na przykład https:\//prod-1westus.logic.azure.com:443/work). Aby uzyskać więcej informacji, zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych HTTP w usłudze Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): wewnątrz Azure Portal wymagamy [utworzenia aplikacji Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , aby umożliwić nam zweryfikowanie połączenia między naszymi dwiema usługami w ramach komunikacji uwierzytelnionej. Aby znaleźć [Identyfikator dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), przejdź do Azure Active Directory i wybierz pozycję **Właściwości**, a następnie wyszukaj numer **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).

- **Identyfikator aplikacji usługi Azure AD** (wymagane): wymagany jest również [Identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) i klucz uwierzytelniania. Aby uzyskać te wartości, przejdź do Azure Active Directory i wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Aby znaleźć klucz uwierzytelniania, przejdź do pozycji **Ustawienia** i wybierz pozycję **klucze**. Należy podać opis i czas trwania, a następnie podać wartość liczbową.

>[!Note]
>Identyfikator aplikacji platformy Azure jest skojarzony z IDENTYFIKATORem wydawcy, dlatego upewnij się, że ten sam identyfikator aplikacji jest używany we wszystkich ofertach.

## <a name="plan-overview"></a>Przegląd planu

Karta **Przegląd planu** umożliwia udostępnianie różnych opcji planu w ramach tej samej oferty. Plany te (czasami określane jako jednostki SKU) mogą różnić się w odniesieniu do wersji, zysków lub warstw usługi. Musisz skonfigurować co najmniej jeden plan, aby sprzedawać swoją ofertę w portalu Marketplace.

Po utworzeniu zobaczysz nazwy planu, identyfikatory, modele cen, dostępność (publiczna lub prywatna), bieżący stan publikowania i wszystkie dostępne akcje.

**Akcje** dostępne w **przeglądzie planu** różnią się w zależności od bieżącego stanu planu i mogą obejmować:

- Jeśli plan ma stan **wersja robocza** , Usuń wersję roboczą
- Jeśli plan ma stan **Live** -Stop Sprzedaj lub zsynchronizuj odbiorców prywatnych

**Utwórz nowy plan** (co najmniej jeden plan dla tych, którzy wybierają sprzedaż w firmie Microsoft)

- **Identyfikator planu:** Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie URL produktu i szablonach Azure Resource Manager (jeśli dotyczy). Używaj tylko małych liter, znaków alfanumerycznych, łączników lub podkreśleń. Dla tego identyfikatora planu można używać maksymalnie 50 znaków. Nie można zmodyfikować identyfikatora po wybraniu pozycji Utwórz.
- **Nazwa planu:** Klienci będą widzieli tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę oferty dla każdego planu w tej ofercie. Nazwa planu służy do rozróżniania planów oprogramowania, które mogą być częścią tej samej oferty (na przykład nazwa oferty: system Windows Server; plany: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Zaplanuj listę

Na karcie **lista planów** są wyświetlane języki (i rynki), w których plan jest dostępny. obecnie jest dostępna tylko wersja angielskojęzyczna (Stany Zjednoczone). Ponadto na tej stronie jest wyświetlany stan listy charakterystycznej dla języka oraz Data/godzina dodania. Musisz zdefiniować szczegóły witryny Marketplace (nazwę oferty, opis, wyszukiwane terminy itp.) dla każdego języka/rynku.

#### <a name="plan-listing-details"></a>Szczegóły planu

Wybranie jednego z języków planu spowoduje wyświetlenie informacji o **planie** , takich jak **Nazwa** i **opis.**

- **Nazwa**: Wstępnie wypełniona w oparciu o **Nowy** wpis w wersji zapoznawczej i zostanie wyświetlona jako tytuł oferty "plan oprogramowania" wyświetlany w portalu Marketplace.
- **Opis:** Ten opis umożliwia wyjaśnienie, co sprawia, że ten plan oprogramowania jest unikatowy i wszelkie różnice między innymi planami oprogramowania w ramach oferty. Może zawierać do 500 znaków.

Po zakończeniu tych pól wybierz pozycję **Zapisz** .

#### <a name="plan-pricing-and-availability"></a>Planowanie cen i dostępności

Karta **ceny i dostępność** umożliwia skonfigurowanie rynków, w których ten plan będzie dostępny w programie, odpowiedniego modelu zysków, ceny i okresu rozliczeniowego. Ponadto możesz wskazać, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (odbiorców prywatnych).

##### <a name="enabling-free-trials"></a>Włączanie bezpłatnych wersji próbnych

Oferty SaaSymi za pomocą komercyjnej witryny Marketplace umożliwiają zapewnienie miesięcznej bezpłatnej wersji próbnej podczas sprzedaży przez firmę Microsoft. W przypadku wszystkich modeli rozliczeń i terminów z wyjątkiem planów taryfowych są obsługiwane bezpłatne wersje próbne. Ta opcja pozwala klientom z niską barierą na wprowadzanie przez jeden miesiąc bezpłatnego dostępu.  Jeśli zdecydujesz się na włączenie bezpłatnej wersji próbnej dla planów w ramach oferty, klient nie będzie mógł dokonać konwersji na płatną subskrypcję przed końcem początkowego okresu pierwszego miesiąca.  W tym czasie klienci kupujący ofertę mogą wypróbować dowolne z obsługiwanych planów, w przypadku których bezpłatna wersja próbna jest włączona i konwertowana między nimi.  Konwersja na płatną subskrypcję odbywa się automatycznie na koniec okresu obowiązywania.

>[!Note]
>Jeśli klient zdecyduje się na konwersję do planu bez bezpłatnych wersji próbnych, zostanie wykonana konwersja, ale bezpłatna wersja próbna zostanie utracona natychmiast.  Ponadto po rozpoczęciu płacenia przez klienta planu nie można już ponownie uzyskać bezpłatnej wersji próbnej tej subskrypcji, nawet jeśli przekonwertujesz ją na jednostkę SKU, która obsługuje bezpłatne wersje próbne.

Możliwość skonfigurowania bezpłatnej wersji próbnej jest dostępna dla każdego planu oferty. Po prostu przejdź do cennika i dostępności dla każdej oferty i zaznacz pole wyboru, aby zezwolić na miesięczną wersję próbną.

![Pole wyboru miesięcznej bezpłatnej wersji próbnej](./media/free-trial-enable.png)

>[!Note]
>Po opublikowaniu oferty transakcyjnej z bezpłatną wersją próbną nie można jej wyłączyć dla tego planu. Upewnij się, że to ustawienie jest poprawne podczas pierwszego publikowania, aby uniknąć konieczności ponownego tworzenia planu.

Aby uzyskać informacje o subskrypcjach klientów, które aktualnie uczestniczą w bezpłatnej wersji próbnej, użyj nowej właściwości interfejsu API `isFreeTrial`, która zostanie oznaczona jako true lub false. Aby uzyskać więcej informacji, zobacz [SaaS Get Subscription API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Bezpłatne wersje próbne nie są obsługiwane w przypadku planów, które wykorzystują usługę pomiaru Marketplace.

#### <a name="markets"></a>Wprowadza

- **Edytowanie rynków** (opcjonalnie)

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla dowolnej lokalizacji rynkowej, w której chcesz udostępnić ten plan. Pole wyszukiwania i przycisk służący do wybierania krajów "z przekazaniem podatku", w których firma Microsoft dokonuje zakupu sprzedaży i używania podatków w Twoim imieniu, są dołączane do pomocy.

Jeśli już ustawisz ceny dla planu w Stany Zjednoczone dolarów (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku zostanie obliczona zgodnie z bieżącymi stawkami za wymianę. Przed opublikowaniem należy zawsze sprawdzić cenę poszczególnych rynków. Cennik można przejrzeć przy użyciu linku "Eksport cen (xlsx)" po zapisaniu zmian.

#### <a name="pricing"></a>Cennik

- **Model cen**: stawka ryczałtowa lub oparta na miejscu

Stała **stawka:** Zapewnij dostęp do oferty przy użyciu jednej ceny miesięcznej lub rocznej ceny ryczałtowej. Jest to czasami określane jako Cennik oparty na witrynie. Korzystając z tego modelu cen, można opcjonalnie zdefiniować plany taryfowe używające interfejsu API usługi pomiarowej Marketplace do naliczania opłat klientom zgodnie z jednostkami niestandardowymi.  Aby uzyskać więcej informacji na temat rozliczeń naliczanych, zobacz opłaty [naliczane przy użyciu usługi pomiaru Marketplace](./saas-metered-billing.md).

**Na użytkownika:** Zapewnij dostęp do oferty przy użyciu ceny na podstawie liczby użytkowników, którzy uzyskują dostęp do oferty lub zajmują stanowiska. Ten model oparty na użytkownikach umożliwia ustawienie minimalnej i maksymalnej liczby użytkowników dozwolonych na podstawie ceny. W ten sposób można skonfigurować różne punkty cenowe na podstawie liczby użytkowników, konfigurując wiele planów.  Te pola są opcjonalne. Jeśli pole pozostanie puste, liczba użytkowników będzie interpretowana jako nie ma limitu (minimum 1 i maksimum, tak jak system może obsługiwać). Te pola mogą być edytowane w ramach aktualizacji planu.

Po opublikowaniu wyboru modelu cen rozliczeniowych nie można zmienić. Ponadto wszystkie plany dla tej samej oferty muszą mieć ten sam model cen.

- **Okres rozliczeniowy**: miesięczny lub roczny

Wybierz częstotliwość, z jaką klienci muszą uiścić cenę. Należy podać co najmniej jedną cenę miesięczną lub roczną lub obie opcje można udostępnić klientom.

- **Cena**: USD miesięcznie lub USD rocznie

Ceny ustawione w walucie lokalnej (USD = Stany Zjednoczone dolara) są konwertowane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących stawek wymiany dostępnych podczas instalacji. Sprawdź te ceny przed opublikowaniem, eksportując arkusz kalkulacyjny z cennikiem i sprawdzając cenę na każdym rynku. Jeśli chcesz ustawić ceny niestandardowe na indywidualnym rynku, zmodyfikuj i zaimportuj arkusz cen. Użytkownik jest odpowiedzialny za sprawdzanie poprawności cen i ich ustawienia.
*\*Najpierw musisz zapisać zmiany cen, aby włączyć eksportowanie danych cen.*

Uważnie Przejrzyj ceny przed opublikowaniem, ponieważ istnieją pewne ograniczenia dotyczące tego, co można zmienić po opublikowaniu planu:

- Po opublikowaniu planu nie można zmienić modelu cen.
- Po opublikowaniu okresu rozliczeniowego dla planu nie można go później usunąć.
- Po opublikowaniu ceny na rynku planu nie można jej później zmienić.

### <a name="plan-audience"></a>Planowanie odbiorców

Istnieje możliwość skonfigurowania każdego planu, który będzie widoczny dla wszystkich użytkowników lub tylko do określonych odbiorców. Członkostwo w tych ograniczonych odbiorcach można przypisywać przy użyciu identyfikatorów dzierżaw usługi Azure AD.

#### <a name="privacy"></a>Ochrona prywatności

- **To jest plan prywatny** (opcjonalne pole wyboru)

Zaznacz to pole wyboru, aby plan był prywatny i widoczny tylko dla wybranych odbiorców z ograniczeniami. Po opublikowaniu jako planu prywatnego możesz zaktualizować odbiorców lub wybrać opcję udostępnienia planu wszystkim użytkownikom. Gdy plan zostanie opublikowany jako widoczny dla wszystkich użytkowników, musi pozostać widoczny dla wszystkich. (Nie można ponownie skonfigurować planu jako planu prywatnego).

- **Odbiorcy z ograniczeniami (identyfikatory dzierżawców)**

Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego. Dostęp jest przypisywany przy użyciu identyfikatorów dzierżawców z opcją dołączenia opisu każdego przypisanego identyfikatora dzierżawy. W przypadku importowania pliku arkusza kalkulacyjnego CSV można dodać maksymalnie 10 identyfikatorów dzierżaw lub 20 000 klientów.

Dzierżawca jest reprezentacją organizacji, identyfikator reprezentowany jako identyfikator GUID (unikatowy identyfikator globalny, 128-bitowy numer liczbowy używany do identyfikowania zasobów). Jest to dedykowane wystąpienie usługi Azure AD, którą organizacja lub deweloper aplikacji otrzymuje po utworzeniu relacji z firmą Microsoft, na przykład zarejestrowaniu się na platformie Azure, w usłudze Microsoft Intune lub Microsoft 365. Każda dzierżawa usługi Azure AD jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD. Aby sprawdzić dzierżawę, zaloguj się do Azure Portal przy użyciu konta, którego chcesz używać do zarządzania aplikacją. Jeśli masz dzierżawę, nastąpi automatyczne zalogowanie do niej, a nazwa dzierżawy zostanie wyświetlona bezpośrednio pod nazwą Twojego konta. Umieść wskaźnik myszy na nazwie konta w prawym górnym rogu witryny Azure Portal, aby wyświetlić swoją nazwę, adres e-mail, identyfikator katalogu/dzierżawy (GUID) oraz domenę. Jeśli Twoje konto jest skojarzone z wieloma dzierżawami, możesz wybrać nazwę swojego konta, aby otworzyć menu, w którym można przełączać się między dzierżawami. Każda dzierżawa ma własny identyfikator dzierżawy. Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu URL nazwy domeny w: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Mimo że usługa SaaS oferuje identyfikatory dzierżawców do definiowania prywatnych odbiorców, inne typy ofert mogą korzystać z identyfikatorów subskrypcji platformy Azure (które są również reprezentowane jako identyfikatory GUID).

> [!NOTE]
> Prywatni odbiorcy (lub odbiorcy z ograniczeniami) różnią się od odbiorców w wersji zapoznawczej. Na karcie **[Podgląd](#preview)** można zdefiniować odbiorców w wersji zapoznawczej. Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty *przed* opublikowaniem jej w portalu Marketplace. Gdy oznaczenie odbiorców prywatnych odnosi się tylko do określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie), ale tylko w okresie ograniczonej wersji zapoznawczej, gdy plan jest testowany i sprawdzony.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Przykładowa lista planów w ramach oferty portalu Marketplace

![Przykład aukcji planu Marketplace z notatkami](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Wersja testowa

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Odbiorcy dostawcy rozwiązań w chmurze (CSP)

Wybranie oferty w programie CSP pozwala dostawcom rozwiązań w chmurze sprzedawać produkt w ramach rozwiązania do swoich klientów. Aby uzyskać więcej informacji, zobacz [dostawcy rozwiązań w chmurze](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publikuj

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Publikuj** w prawym górnym rogu portalu. Nastąpi przekierowanie do strony **Recenzja i publikowanie** .

#### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Jeśli ta oferta jest publikowana po raz pierwszy, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
    - *Nie uruchomiono* — oznacza, że sekcja nie została dotknięcia i należy ją ukończyć.
    - *Niekompletne* — oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania więcej informacji. Musisz wrócić do sekcji i zaktualizować ją.
    - *Gotowe* — oznacza, że sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także wszelkich dodatkowych notatek przydatnych w zrozumieniu swojej aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail z prośbą o udostępnienie wersji zapoznawczej oferty, którą można przejrzeć i zatwierdzić. Musisz wrócić do Centrum partnerskiego i wybrać pozycję **Przejdź na żywo** , aby uzyskać ofertę opublikowania oferty na publiczną (lub w przypadku prywatnej oferty dla odbiorców prywatnych).

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
