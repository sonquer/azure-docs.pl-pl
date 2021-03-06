---
title: Warunki użytkowania-Azure Active Directory | Microsoft Docs
description: Zacznij korzystać z Azure Active Directory warunki użytkowania, aby przedstawić informacje pracownikom lub Gościom przed uzyskaniem dostępu.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31d84d5bf43bac55769a6479917794a51c1ccd0c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999117"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory warunki użytkowania

Warunki użytkowania usługi Azure AD zapewniają prostą metodę, która może być używana przez organizacje do prezentowania informacji użytkownikom końcowym. Dzięki tej prezentacji użytkownicy mogą zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności. W tym artykule opisano, jak zacząć korzystać z warunków użytkowania.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Filmy wideo z omówieniem

Poniższy klip wideo zawiera krótkie omówienie warunków użytkowania.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Aby obejrzeć dodatkowe filmy wideo zobacz:
- [Jak wdrożyć warunki użytkowania w programie Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Jak wdrożyć warunki użytkowania w Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Co mogę zrobić z warunkami użytkowania?

Warunki użytkowania usługi Azure AD mają następujące możliwości:

- Przed uzyskaniem dostępu należy wymagać od pracowników lub Gości zaakceptowania warunków użytkowania.
- Przed uzyskaniem dostępu należy wymagać od pracowników lub Gości akceptowania warunków użytkowania na każdym urządzeniu.
- Wymaganie od pracowników lub Gości akceptacji warunków użytkowania według harmonogramu cyklicznego.
- Przed zarejestrowaniem informacji o zabezpieczeniach w usłudze Azure Multi-Factor Authentication (MFA) wymagane jest, aby pracownicy lub Goście zaakceptowali warunki użytkowania.
- Wymaganie od pracowników akceptacji warunków użytkowania przed zarejestrowaniem informacji o zabezpieczeniach w usłudze Azure AD Samoobsługowe resetowanie hasła (SSPR).
- Zaprezentowanie ogólnych warunków użytkowania dla wszystkich użytkowników w organizacji.
- Zaprezentowanie określonych warunków użytkowania na podstawie atrybutów użytkownika (np. warunki dla lekarzy różnią się od warunków dla pielęgniarek, a pracownicy krajowi mają inne warunki niż pracownicy międzynarodowi) za pomocą [grup dynamicznych](../users-groups-roles/groups-dynamic-membership.md).
- Obecne warunki użytkowania podczas uzyskiwania dostępu do aplikacji o wysokiej wpływ na działalność biznesową, takich jak Salesforce.
- Prezentowanie warunków użytkowania w różnych językach.
- Lista użytkowników, którzy mają lub nie zaakceptowali warunków użytkowania.
- Pomaga w osiągnięciu przepisów dotyczących zachowania poufności.
- Wyświetl dziennik działania warunków użytkowania na potrzeby zgodności i inspekcji.
- Twórz warunki użytkowania i zarządzaj nimi za pomocą [Microsoft Graph interfejsów API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (obecnie w wersji zapoznawczej).

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z i konfigurować warunki użytkowania usługi Azure AD, musisz dysponować:

- Subskrypcja usługi Azure AD w wersji Premium P1, Premium P2, EMS E3 lub EMS E5.
   - Jeśli nie masz żadnej z tych subskrypcji, możesz [uzyskać dostęp do usługi Azure AD w wersji Premium](../fundamentals/active-directory-get-started-premium.md) lub [włączyć wersję próbnej usługi Azure AD w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jedno z następujących kont administratora katalogu, który chcesz skonfigurować:
   - Administrator globalny
   - Administrator zabezpieczeń
   - Administrator dostępu warunkowego

## <a name="terms-of-use-document"></a>Dokument z warunkami użytkowania

Warunki użytkowania usługi Azure AD używają formatu PDF do prezentowania zawartości. Zawartość ta może być dowolna i obejmować na przykład istniejące dokumenty kontraktowe, umożliwiając gromadzenie umów użytkowników końcowych podczas logowania. Aby zapewnić obsługę użytkowników na urządzeniach przenośnych, zalecany rozmiar czcionki w pliku PDF jest punktem 24.

## <a name="add-terms-of-use"></a>Dodawanie warunków użytkowania

Po sfinalizowaniu dokumentu z użyciem warunków użytkowania Użyj następującej procedury, aby ją dodać.

1. Logowanie do platformy Azure jako globalny, Administrator, Administrator zabezpieczeń lub Administrator dostępu warunkowego.
1. Przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

   ![Dostęp warunkowy — blok Warunki użytkowania](./media/terms-of-use/tou-blade.png)

1. Kliknij pozycję **Nowe warunki**.

   ![Okienko nowe warunki użytkowania umożliwiające określenie warunków użytkowania](./media/terms-of-use/new-tou.png)

1. W polu **Nazwa** wprowadź nazwę warunków użytkowania, które będą używane w Azure Portal.
1. W **nazwę wyświetlaną** wprowadź tytuł, którą użytkownicy widzą podczas logowania.
1. W przypadku **warunki użytkowania dokumentu**przejdź do pliku PDF z końcowymi warunkami użytkowania i wybierz go.
1. Wybierz język dla dokumentu warunków użytkowania. Opcja wyboru języka umożliwia przekazanie wielu wersji językowych warunków użytkowania. Wersja warunków użytkowania widoczna dla użytkownika końcowego będzie zależała od preferencji jego przeglądarki.
1. Aby wymagać od użytkowników końcowych wyświetlania warunków użytkowania przed ich zaakceptowaniem, ustaw opcję **Wymagaj, aby użytkownicy mogli rozwijać warunki użytkowania** **.**
1. Aby wymagać od użytkowników końcowych akceptacji warunków użytkowania na każdym urządzeniu, z którego uzyskują dostęp, ustaw opcję **Wymagaj od użytkowników zgody na każde urządzenie** **na.** Aby uzyskać więcej informacji, zobacz [warunki użytkowania poszczególnych urządzeń](#per-device-terms-of-use).
1. Jeśli chcesz wycofać warunki użytkowania, które zostały wysłane zgodnie z harmonogramem, ustaw **wygasanie** z **dniem**. Jeśli włączona, są wyświetlane dwa ustawienia dodatkowe harmonogramu.

   ![Ustawienia wygasania, aby ustawić datę początkową, częstotliwość i czas trwania](./media/terms-of-use/expire-consents.png)

1. Użyj ustawień **Data wygaśnięcia** i **częstotliwość** , aby określić harmonogram wygaśnięcia warunków użytkowania. W poniższej tabeli przedstawiono wyniki dla kilku przykładowych ustawień:

   | Wygaśnięcie zaczyna się | Częstotliwość | Wynik |
   | --- | --- | --- |
   | Dzisiejsza data  | Miesięczne | Rozpoczynając od dzisiaj, użytkownicy muszą zaakceptować warunki użytkowania, a następnie zaakceptować je ponownie co miesiąc. |
   | W przyszłości  | Miesięczne | Rozpoczynając od dzisiaj, użytkownicy muszą zaakceptować warunki użytkowania. W przypadku przyszłych Data wygaśnie zgody i następnie użytkownicy muszą ponownie zaakceptowali co miesiąc.  |

   Na przykład jeśli ustawisz wygasania, począwszy od daty **1 stycznia** i częstotliwość **miesięczne**, Oto jak wygasanie ważności poświadczeń mogą wystąpić w przypadku dwóch użytkowników:

   | Użytkownik | Najpierw zaakceptować daty | Najpierw wygasa dnia | Po drugie wygasa dnia | Trzeci wygasa dnia |
   | --- | --- | --- | --- | --- |
   | Alicja | 1 stycznia | 1 lutego | 1 marca | 1 kwietnia |
   | Bob | 15 stycznia | 1 lutego | 1 marca | 1 kwietnia |

1. Użyj ustawienia **czas trwania przed ponownym zatwierdzeniem (w dniach)** , aby określić liczbę dni, po których użytkownik musi ponownie zaakceptować warunki użytkowania. Dzięki temu użytkownicy mogą postępuj zgodnie z własnym harmonogramem. Na przykład, jeśli zostanie ustawiona na czas trwania **30** dni, Oto jak wygasanie ważności poświadczeń mogą wystąpić w przypadku dwóch użytkowników:

   | Użytkownik | Najpierw zaakceptować daty | Najpierw wygasa dnia | Po drugie wygasa dnia | Trzeci wygasa dnia |
   | --- | --- | --- | --- | --- |
   | Alicja | 1 stycznia | Do 31 stycznia | 2 marca | 1 kwietnia |
   | Bob | 15 stycznia | 14 lutego | 16 marca | Kwi 15 |

   Istnieje możliwość użycia **wygaśnie zgody** i **czas trwania zanim ponownej akceptacji wymaga (w dniach)** ustawienia, ale zazwyczaj można użyć jednej z nich.

1. W obszarze **dostęp warunkowy**Użyj listy **szablon zasady wymuszania dostępu warunkowego** , aby wybrać szablon, aby wymusić warunki użytkowania.

   ![Lista rozwijana dostęp warunkowy do wybierania szablonu zasad](./media/terms-of-use/conditional-access-templates.png)

   | Szablon | Opis |
   | --- | --- |
   | **Dostęp do aplikacji w chmurze dla wszystkich gości** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich Gości i wszystkich aplikacji w chmurze. Te zasady mają wpływ witryny Azure portal. Po utworzeniu, może być wymagane wylogowanie się i zaloguj się. |
   | **Dostęp do aplikacji dla wszystkich użytkowników w chmurze** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich użytkowników i aplikacji w chmurze. Te zasady mają wpływ witryny Azure portal. Po utworzeniu, konieczna będzie wylogowanie się i zaloguj się. |
   | **Zasady niestandardowe** | Wybierz użytkowników, grupy i aplikacje, do których zostaną zastosowane te warunki użytkowania. |
   | **Utwórz zasady dostępu warunkowego później** | W przypadku tworzenia zasad dostępu warunkowego te warunki użytkowania będą widoczne na liście kontrolek Grant. |

   >[!IMPORTANT]
   >Kontrolki zasad dostępu warunkowego (w tym warunki użytkowania) nie obsługują wymuszania na kontach usług. Zalecamy wykluczenie wszystkich kont usług z zasad dostępu warunkowego.

    Niestandardowe zasady dostępu warunkowego umożliwiają szczegółowe warunki użytkowania, w dół do określonej aplikacji w chmurze lub grupy użytkowników. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Wymagaj zaakceptowania warunków użytkowania przed uzyskaniem dostępu do](require-tou.md)aplikacji w chmurze.

1. Kliknij przycisk **Utwórz**.

   W przypadku wybrania niestandardowego szablonu dostępu warunkowego zostanie wyświetlony nowy ekran, który umożliwia utworzenie niestandardowych zasad dostępu warunkowego.

   ![Nowe okienko dostępu warunkowego w przypadku wybrania szablonu zasad dostępu warunkowego](./media/terms-of-use/custom-policy.png)

   Powinny teraz być widoczne nowe warunki użytkowania.

   ![Nowe warunki użytkowania wymienione w bloku warunki użytkowania](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Wyświetl raport, kto został zaakceptowany i odrzucone

W bloku Warunki użytkowania znajduje się liczba użytkowników, którzy je zaakceptowali i odrzucili. Te liczniki i osoby, które zaakceptowały/odrzucają, są przechowywane w okresie użytkowania warunków użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

   ![W bloku Warunki użytkowania wyświetlana jest liczba zaakceptowanych i odrzuconych elementów przez użytkownika](./media/terms-of-use/view-tou.png)

1. W przypadku warunków użytkowania kliknij numery w obszarze **zaakceptowane** lub **odrzucone** , aby wyświetlić bieżący stan dla użytkowników.

   ![Okienko zaakceptowanych Warunki użytkowania zawiera listę użytkowników, którzy zaakceptowali](./media/terms-of-use/accepted-tou.png)

1. Aby wyświetlić historię dla poszczególnych użytkowników, kliknij przycisk wielokropka ( **...** ) i następnie **wyświetlić historię**.

   ![Wyświetl menu kontekstowe dla użytkownika](./media/terms-of-use/view-history-menu.png)

   W okienku widoku Historia Zobacz historię wszystkich akceptuje odmówi i wygasanie ważności poświadczeń.

   ![Okienko historia wyświetlania wyświetla listę akceptowanych, odrzuconych i wygasających użytkowników](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Wyświetl usługi Azure AD dzienniki inspekcji

Jeśli chcesz wyświetlić dodatkowe działanie, warunki użytkowania usługi Azure AD obejmują dzienniki inspekcji. Każdy zgody użytkownika wyzwala zdarzenie w dziennikach inspekcji, które są przechowywane przez **30 dni**. Te dzienniki możesz wyświetlić w portalu lub pobrać jako plik CSV.

Aby rozpocząć pracę z usługą Azure AD dzienniki inspekcji, użyj następującej procedury:

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania.
1. Kliknij pozycję **Wyświetl dzienniki inspekcji**.

   ![Warunki użytkowania blok z wyróżnioną opcją Wyświetl dzienniki inspekcji](./media/terms-of-use/audit-tou.png)

1. Ekranie dzienników inspekcji w usłudze Azure AD, można filtrować informacje przy użyciu podanej listy do informacji z dzienników inspekcji określonego docelowego.

   Można również kliknąć pozycję **Pobierz**, aby pobrać informacje w pliku CSV do użytku lokalnego.

   ![Ekran dzienników inspekcji usługi Azure AD — Data, zasady docelowe, zainicjowane przez i działanie](./media/terms-of-use/audit-logs-tou.png)

   Po kliknięciu przycisku dziennika okienko pojawia się ze szczegółami dodatkowe działania.

   ![Szczegóły działania dziennika przedstawiającego działanie, stan działania, zainicjowane przez, zasady docelowe](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Jakie warunki użytkowania wyglądają dla użytkowników

Po utworzeniu i wymuszeniu warunków użytkowania użytkownicy, którzy znajdują się w zakresie, zobaczą następujący ekran podczas logowania.

![Przykładowe warunki użytkowania, które pojawiają się po zalogowaniu się użytkownika](./media/terms-of-use/user-tou.png)

Użytkownicy mogą wyświetlać warunki użytkowania i, w razie potrzeby, powiększać i pomniejszać.

![Widok warunków użytkowania z przyciskami powiększenia](./media/terms-of-use/zoom-buttons.png)

Na poniższym ekranie pokazano, jak warunki użytkowania wyglądają na urządzeniach przenośnych.

![Przykładowe warunki użytkowania, które pojawiają się, gdy użytkownik loguje się na urządzeniu przenośnym](./media/terms-of-use/mobile-tou.png)

Użytkownicy muszą zaakceptować warunki użytkowania tylko raz i nie będą widzieć ponownie warunków użytkowania przy kolejnych logowaniach.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak użytkownicy mogą przeglądać swoje warunki użytkowania

Użytkownicy mogą przeglądać i przeglądać Warunki użytkowania, które zaakceptowali, wykonując poniższą procedurę.

1. Zaloguj się do witryny [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. W prawym górnym rogu kliknij swoją nazwę i wybierz **profilu**.

   ![Witryna moje aplikacje z otwartym okienkiem użytkownika](./media/terms-of-use/tou14.png)

1. Na stronie Profil kliknij pozycję **Przejrzyj warunki użytkowania**.

   ![Strona profilu dla użytkownika pokazująca link warunki użytkowania](./media/terms-of-use/tou13a.png)

1. Następnie możesz przejrzeć zaakceptowane warunki użytkowania.

## <a name="edit-terms-of-use-details"></a>Edytuj szczegóły warunków użytkowania

Można edytować niektóre szczegóły warunków użytkowania, ale nie można modyfikować istniejącego dokumentu. Poniższa procedura opisuje sposób edytowania szczegółów.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania, które chcesz edytować.
1. Kliknij przycisk **Edycja warunków**.
1. W postanowieniach edycji okienko użycia Zmień nazwę, nazwę wyświetlaną lub Wymagaj od użytkowników rozwinięcia wartości.

   Jeśli istnieją inne ustawienia, które chcesz zmienić, takie jak dokument PDF, użytkownicy muszą wyrazić zgodę na każde urządzenie, wygasnąć, czas trwania przed ponowną akceptacją lub zasady dostępu warunkowego, należy utworzyć nowe warunki użytkowania.

   ![Okienko Edycja warunków użytkowania z pokazywaniem opcji Nazwa i rozwiń](./media/terms-of-use/edit-tou.png)

1. Kliknij przycisk **Zapisz** Aby zapisać zmiany.

   Gdy zapiszesz zmiany, użytkownicy nie będą musieli ponownie akceptować tych zmian.

## <a name="add-a-terms-of-use-language"></a>Dodawanie języka warunków użytkowania

Poniższa procedura opisuje sposób dodawania języka warunków użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania, które chcesz edytować.
1. W okienku szczegółów kliknij **języków** kartę.

   ![Warunki użytkowania wybrane i pokazywanie karty Języki w okienku szczegółów](./media/terms-of-use/languages-tou.png)

1. Kliknij przycisk **Dodaj język**.
1. W słowach Dodaj użycie języka okienka przekazać zlokalizowane dokument PDF i wybierz język.

   ![Dodaj okienko języka warunków użytkowania z opcjami przekazywania zlokalizowanych plików PDF](./media/terms-of-use/language-add-tou.png)

1. Kliknij przycisk **Dodaj** można dodać języka.

## <a name="per-device-terms-of-use"></a>Warunki użytkowania poszczególnych urządzeń

Ustawienie **Wymagaj od użytkowników zgody na każde urządzenie** umożliwia użytkownikom końcowym zaakceptowanie warunków użytkowania na każdym urządzeniu, z którego uzyskują dostęp. Użytkownicy będą musieli proces dołączania urządzenia w usłudze Azure AD. Gdy urządzenie jest przyłączone, identyfikator urządzenia służy do wymuszania warunków użytkowania na poszczególnych urządzeniach.

Poniżej przedstawiono listę obsługiwanych platform i oprogramowania.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Inne |
> | --- | --- | --- | --- | --- |
> | **Aplikacja natywna** | Yes | Yes | Tak |  |
> | **Microsoft Edge** | Tak | Yes | Yes |  |
> | **Program Internet Explorer** | Yes | Yes | Yes |  |
> | **Chrome (z rozszerzeniem)** | Yes | Yes | Tak |  |

Warunki użytkowania poszczególnych urządzeń mają następujące ograniczenia:

- Urządzenie może być częścią tylko jednej dzierżawy.
- Użytkownik musi mieć uprawnienia do przyłączania urządzenia.
- Aplikacja do rejestracji w usłudze Intune nie jest obsługiwana.
- Użytkownicy B2B usługi Azure AD nie są obsługiwani.

Jeśli urządzenie użytkownika nie jest połączony, otrzymają komunikat, który potrzebują w celu przyłączenia urządzenia. Ich środowisko pracy będzie zależny od platformy i oprogramowania.

### <a name="join-a-windows-10-device"></a>Dołączanie urządzenia z systemem Windows 10

Jeśli użytkownik korzysta z systemu Windows 10 i Microsoft Edge, otrzymają komunikat podobny do następującego [proces dołączania urządzenia](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![System Windows 10 i Microsoft Edge — komunikat wskazujący, że urządzenie musi być zarejestrowane](./media/terms-of-use/per-device-win10-edge.png)

W przypadku korzystania z przeglądarki Chrome, zostanie wyświetlony monit do zainstalowania [rozszerzenie systemu Windows 10 kont](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Przeglądarki

Jeśli użytkownik korzysta z przeglądarki, która nie jest obsługiwana, poprosimy do korzystania z innej przeglądarki.

![Komunikat wskazujący, że urządzenie musi być zarejestrowane, ale przeglądarka nie jest obsługiwana](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Usuń warunki użytkowania

Stare warunki użytkowania można usunąć, wykonując czynności opisane w poniższej procedurze.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania, które chcesz usunąć.
1. Kliknij pozycję **Usuń**.
1. W wyświetlonym komunikacie z pytaniem o kontynuowanie kliknij pozycję **Tak**.

   ![Komunikat z prośbą o potwierdzenie usunięcia warunków użytkowania](./media/terms-of-use/delete-tou.png)

   Nie powinno już być widoczne warunki użytkowania.

## <a name="deleted-users-and-active-terms-of-use"></a>Usunięci Użytkownicy i aktywne warunki użytkowania

Domyślnie usunięty użytkownik jest w stanie usunięcia w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby. Po 30 dniach użytkownik jest trwale usuwany. Ponadto przy użyciu portalu usługi Azure Active Directory administrator globalny może jawnie [trwale usunąć niedawno usuniętego użytkownika](../fundamentals/active-directory-users-restore.md) przed upłynięciem tego okresu. Jeden użytkownik został trwale usunięty. kolejne dane dotyczące tego użytkownika zostaną usunięte z aktywnych warunków użytkowania. Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="policy-changes"></a>Zmiany zasad

Zasady dostępu warunkowego zaczynają obowiązywać natychmiast. W takim przypadku administrator będzie wyświetlane "smutne chmury" lub "Problemy z tokenem usługi Azure AD". Administrator musi się wylogować i zaloguj się ponownie, aby spełnić wymagania nowych zasad.

> [!IMPORTANT]
> W następujących przypadkach uprawnieni użytkownicy muszą się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad:
>
> - zasady dostępu warunkowego są włączone w warunkach użytkowania
> - Utworzono drugą wersję warunków użytkowania

## <a name="b2b-guests-preview"></a>Goście B2B (wersja zapoznawcza)

W większości organizacji istnieje proces, w którym pracownicy mogą wyrazić zgodę na warunki użytkowania i zasady zachowania poufności informacji w organizacji. Jak można wymusić ten sam zgody dla usługi Azure AD business-to-business, ale goście (B2B), kiedy są dodawane przy użyciu programu SharePoint lub zespoły? Korzystając z dostępu warunkowego i warunków użytkowania, można wymusić zasady bezpośrednio do użytkowników Gości B2B. W trakcie przepływu wykupu zaproszenia użytkownik otrzymuje warunki użytkowania. Ta funkcja jest obecnie w wersji zapoznawczej.

Warunki użytkowania będą wyświetlane tylko wtedy, gdy użytkownik ma konto gościa w usłudze Azure AD. Usługa SharePoint Online aktualnie ma [środowisko odbiorcy udostępniania zewnętrznego ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) umożliwiające udostępnianie dokumentu lub folderu, który nie wymaga, aby użytkownik miał konto gościa. W takim przypadku warunki użytkowania nie są wyświetlane.

![Okienko Użytkownicy i grupy — karta Dołącz z opcją wszyscy użytkownicy-Goście zaznaczona](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Obsługa aplikacji w chmurze (wersja zapoznawcza)

Warunki użytkowania może służyć do aplikacji w chmurze innej, takich jak usługi Azure Information Protection i Microsoft Intune. Ta funkcja jest obecnie w wersji zapoznawczej.

### <a name="azure-information-protection"></a>Azure Information Protection

Można skonfigurować zasady dostępu warunkowego dla aplikacji Azure Information Protection i wymagać warunków użytkowania, gdy użytkownik uzyskuje dostęp do chronionego dokumentu. Spowoduje to wyzwolenie warunków użytkowania przed uzyskaniem dostępu do chronionego dokumentu po raz pierwszy.

![Okienko aplikacje w chmurze z wybraną aplikacją Microsoft Azure Information Protection](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Rejestracja w usłudze Microsoft Intune

Można skonfigurować zasady dostępu warunkowego dla aplikacji do rejestracji Microsoft Intune i wymagać warunków użytkowania przed rejestracją urządzenia w usłudze Intune. Aby uzyskać więcej informacji, zobacz odczytu [Wybieranie odpowiedniego warunki rozwiązanie dla Twojej organizacji wpis w blogu](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Okienko aplikacje w chmurze z wybraną aplikacją Microsoft Intune](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Aplikacja do rejestracji w usłudze Intune nie jest obsługiwana w przypadku [warunków użytkowania poszczególnych urządzeń](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Jak mogę zobaczyć, kiedy użytkownik zaakceptuje warunki użytkowania?**<br />
Odp.: W bloku Warunki użytkowania kliknij liczbę w obszarze **zaakceptowane**. Można również wyświetlić lub zaakceptuj działań związanych z wyszukiwaniem w usłudze Azure AD dzienniki inspekcji. Aby uzyskać więcej informacji, zobacz Wyświetlanie raportu dla użytkowników, którzy zaakceptowali i odrzucili i [wyświetlania dzienników inspekcji usługi Azure AD](#view-azure-ad-audit-logs).

**Pyt.: Jak długo są przechowywane informacje?**<br />
Odp.: Użytkownik liczy w raporcie warunki użytkowania i kto zaakceptował/odrzucony przez cały czas użytkowania warunków użytkowania. Inspekcja usługi Azure AD, dzienniki są przechowywane przez 30 dni.

**Pyt.: Dlaczego widzę inną liczbę przesłanych elementów w raporcie warunki użytkowania a dzienniki inspekcji usługi Azure AD?**<br />
Odp.: Raport warunki użytkowania jest przechowywany przez okres istnienia tych warunków użytkowania, podczas gdy dzienniki inspekcji usługi Azure AD są przechowywane przez 30 dni. Ponadto w raporcie warunki użytkowania są wyświetlane tylko bieżące Stany zgody użytkownika. Na przykład jeśli użytkownik odrzuci, a następnie zaakceptuje, raport warunki użytkowania będzie zawierać tylko ten użytkownik. Jeśli zachodzi potrzeba wyświetlenia historii, możesz użyć usługi Azure AD dzienniki inspekcji.

**Pyt.: Czy jeśli edytuję szczegóły warunków użytkowania, czy wymagane jest ponowne zaakceptowanie przez użytkowników?**<br />
Odp.: Nie, jeśli administrator edytuje szczegóły warunków użytkowania (nazwa, nazwa wyświetlana, wymaganie, aby użytkownicy mogli rozwinąć lub dodać język), nie wymaga od użytkowników ponownej akceptacji nowych warunków.

**Pyt.: Czy mogę zaktualizować istniejące warunki użytkowania dokumentu?**<br />
Odp.: Obecnie nie można zaktualizować istniejących warunków użytkowania dokumentu. Aby zmienić dokument warunków użytkowania, konieczne będzie utworzenie nowego wystąpienia warunków użytkowania.

**Pyt.: Jeśli hiperłącza znajdują się w dokumencie warunki użytkowania dokumentu PDF, użytkownicy końcowi będą mogli je klikać?**<br />
Odp.: Tak, użytkownicy końcowi mogą wybierać hiperlinki do dodatkowych stron, ale linki do sekcji w dokumencie nie są obsługiwane.

**Pyt.: Czy warunki użytkowania obsługują wiele języków?**<br />
Odp.: Tak. Obecnie istnieją 108 różne języki, które administrator może skonfigurować dla jednego warunku użytkowania. Administrator może przekazać wiele dokumentów PDF i oznaczyć je za pomocą odpowiedniego języka (do 108). Gdy użytkownicy końcowi zalogują się, zobaczą preferencje językowe przeglądarki i wyświetlają pasujący dokument. Jeśli nie ma dopasowania, zostanie wyświetlony dokument domyślny, który jest pierwszym przekazaniem dokumentu.

**Pyt.: Kiedy są wyzwalane warunki użytkowania?**<br />
Odp.: Warunki użytkowania są wyzwalane podczas logowania.

**Pyt.: Do jakich aplikacji można wybrać warunki użytkowania?**<br />
Odp.: Zasady dostępu warunkowego można utworzyć w aplikacjach dla przedsiębiorstw korzystających z nowoczesnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [aplikacje przedsiębiorstwa](./../manage-apps/view-applications-portal.md).

**Pyt.: Czy mogę dodać wiele warunków użytkowania do danego użytkownika lub aplikacji?**<br />
Odp.: Tak, tworząc wiele zasad dostępu warunkowego przeznaczonych dla tych grup lub aplikacji. Jeśli użytkownik mieści się w zakresie wielu warunków użytkowania, zaakceptuje w danym momencie jedno warunki użytkowania.

**Pyt.: Co się stanie, jeśli użytkownik odrzuci warunki użytkowania?**<br />
Odp.: Użytkownik ma zablokowany dostęp do aplikacji. Użytkownik będzie musiał zalogować się ponownie i zaakceptuj warunki w celu uzyskania dostępu.

**Pyt.: Czy jest możliwe nieakceptowanie warunków użytkowania, które zostały wcześniej zaakceptowane?**<br />
Odp.: Możesz [przejrzeć wcześniej zaakceptowane warunki użytkowania](#how-users-can-review-their-terms-of-use), ale obecnie nie można jej zaakceptować.

**Pyt.: Co się stanie, jeśli będę również korzystać z warunków i postanowień usługi Intune?**<br />
Odp.: W przypadku skonfigurowania warunków użytkowania usługi Azure AD oraz warunków [i postanowień w usłudze Intune](/intune/terms-and-conditions-create)użytkownik będzie musiał zaakceptować oba te czynności. Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniego warunki rozwiązanie dla Twojej organizacji wpis w blogu](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**Pyt.: Które punkty końcowe są używane przez usługę do uwierzytelniania?**<br />
Odp.: Warunki użytkowania wykorzystuje następujące punkty końcowe do uwierzytelniania: https://tokenprovider.termsofuse.identitygovernance.azure.com i. https://account.activedirectory.windowsazure.com Jeśli Twoja organizacja ma listę adresów URL do rejestracji, konieczne będzie dodanie tych punktów końcowych do listy dozwolonych oraz punktów końcowych usługi Azure AD w celu zalogowania.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: Wymagaj akceptacji warunków użytkowania przed uzyskaniem dostępu do aplikacji w chmurze](require-tou.md)
- [Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md)
