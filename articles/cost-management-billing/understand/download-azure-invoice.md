---
title: Wyświetlanie i pobieranie faktury platformy Microsoft Azure
description: Opis sposobu wyświetlania i pobierania faktury za korzystanie z platformy Microsoft Azure.
keywords: faktura rozliczeniowa, pobieranie faktury, faktura platformy azure, dane użycia platformy azure
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 691d27acebf238e84265870e8c01976bfc2412b2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200269"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Wyświetlanie i pobieranie faktury platformy Microsoft Azure

W przypadku większości subskrypcji możesz pobrać fakturę z [witryny Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub zażądać jej wysłania w wiadomości e-mail. Jeśli jesteś klientem platformy Azure z umową Enterprise Agreement (umową EA), nie możesz pobrać faktur swojej organizacji. Faktury są wysyłane do osoby skonfigurowanej jako odbiorca faktur dla rejestracji.

Tylko niektóre role mają uprawnienia do wyświetlania faktur. Są to na przykład role administratora konta lub administratora przedsiębiorstwa. Aby uzyskać więcej szczegółów na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról](../manage/manage-billing-access.md).

Jeśli masz umowę klienta firmy Microsoft, musisz mieć jedną z następujących ról, aby otrzymywać faktury:

- Właściciel profilu rozliczeniowego
- Współautor
- Czytelnik
- Menedżer faktur

Jeśli masz umowę partnerską firmy Microsoft, musisz być administratorem globalnym lub agentem administracyjnym w organizacji partnerskiej, aby wyświetlać i pobierać faktury platformy Azure. Zobacz temat [Sprawdzanie typu konta rozliczeniowego](#check-your-billing-account-type), aby dowiedzieć się, jakich uprawnień potrzebujesz.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Dlaczego nie możesz wyświetlić faktury

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.

- Na platformie Azure opłaty są naliczane na koniec okresu fakturowania. W związku z tym faktura mogła jeszcze nie zostać wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.

- Nie masz uprawnień do wyświetlania faktur. Jeśli masz umowę klienta firmy Microsoft lub umowę partnerską firmy Microsoft, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur w profilu rozliczeniowym. W przypadku innych subskrypcji stare faktury mogą nie być widoczne, jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](../manage/manage-billing-access.md).

- Jeśli masz bezpłatną wersję próbną lub miesięczną kwotę środków w ramach subskrypcji, otrzymasz fakturę tylko w przypadku przekroczenia kwoty miesięcznych środków. Użytkownicy z umową klienta firmy Microsoft lub umową partnerską firmy Microsoft zawsze otrzymują fakturę.

## <a name="download-invoices-in-the-azure-portal"></a>Pobieranie faktur w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.
1. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego.
1. W menu po lewej stronie wybierz pozycję **Faktury** w obszarze **Rozliczenia**.
1. W siatce faktur znajdź wiersz faktury, którą chcesz pobrać.
1. Kliknij ikonę pobierania lub symbol wielokropka (`...`) na końcu wiersza.
1. W menu pobierania wybierz pozycję **Faktura**.

Aby uzyskać więcej informacji na temat faktury, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](review-individual-bill.md). Aby uzyskać pomoc dotyczącą zarządzania kosztami, zobacz [Zapobieganie powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzanie kosztami](../manage/getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Otrzymywanie faktur dla subskrypcji w wiadomości e-mail

Możesz wyrazić zgodę i skonfigurować dodatkowych adresatów, aby otrzymywać fakturę platformy Azure w wiadomości e-mail. Ta funkcja może być niedostępna dla niektórych subskrypcji, takich jak oferty pomocy technicznej, umowy Enterprise Agreement lub platforma Azure w ramach programu licencjonowania Open.

1. Wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wyraź zgodę dla każdej posiadanej subskrypcji. Kliknij pozycję **Faktury**, a następnie pozycję **Wyślij do mnie wiadomość e-mail z fakturą**.

    ![Zrzut ekranu pokazujący przepływ z wyrażeniem zgody](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Kliknij pozycję **Wyraź zgodę** i zaakceptuj warunki.

    ![Zrzut ekranu pokazujący 2. krok przepływu z wyrażeniem zgody](./media/download-azure-invoice/invoicearticlestep02.png)

3. Po zaakceptowaniu umowy można skonfigurować dodatkowych adresatów. Po usunięciu adresata adres e-mail nie jest dłużej przechowywany. Jeśli zmienisz zdanie, musisz dodać go ponownie.

    ![Zrzut ekranu pokazujący 3. krok przepływu z wyrażeniem zgody](./media/download-azure-invoice/invoicearticlestep03.png)

Jeśli nie otrzymasz wiadomości e-mail po wykonaniu tych kroków, upewnij się, że Twój adres e-mail w [preferencjach komunikacji w Twoim profilu](https://account.windowsazure.com/profile) jest poprawny.

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rezygnacja z otrzymywania faktur dla subskrypcji w wiadomości e-mail

Aby zrezygnować z otrzymywania faktur za pośrednictwem poczty e-mail, wykonaj powyższe kroki, a następnie kliknij pozycję **Zrezygnuj z otrzymywania faktur pocztą e-mail**. Ta opcja usuwa wszystkie adresy e-mail ustawione w celu otrzymywania faktur pocztą e-mail. Możesz ponownie skonfigurować adresatów, jeśli ponownie wyrazisz zgodę.

 ![Zrzut ekranu przedstawiający przepływ rezygnacji](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Otrzymywanie faktur dla umowy klienta firmy Microsoft w wiadomości e-mail

Jeśli masz konto rozliczeniowe dla Umowy z Klientem Microsoft, możesz wyrazić zgodę na otrzymywanie faktur w wiadomości e-mail. Wszyscy użytkownicy z rolą właściciela, współautora, czytelnika lub menedżera faktur w profilu rozliczeniowym otrzymują fakturę w wiadomości e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/download-azure-invoice/search-cmb.png)

1. Po lewej stronie wybierz pozycję **Profile rozliczeniowe**. Z listy profilów rozliczeniowych wybierz profil rozliczeniowy, który będzie otrzymywać faktury w wiadomości e-mail.

   [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Wybierz pozycję **Właściwości** z lewej strony, a następnie wybierz pozycję **Aktualizuj preferencje dotyczące wysyłania faktur pocztą e-mail**.

   [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Wybierz pozycję **Wyraź zgodę**, a następnie kliknij pozycję **Aktualizuj**.

   [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Rezygnacja z otrzymywania faktur dla umowy klienta firmy Microsoft w wiadomości e-mail

Aby zrezygnować z otrzymywania faktur za pośrednictwem poczty e-mail, wykonaj powyższe kroki, a następnie kliknij pozycję **Zrezygnuj**. Rezygnacja z otrzymywania faktur w wiadomości e-mail obejmuje wszystkich użytkowników z rolą właściciela, współautora, czytelnika lub menedżera faktur.

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Przyznawanie innym osobom dostępu do faktur dla Umowy z Klientem Microsoft

Możesz dać innym osobom dostęp do wyświetlania, pobierania i opłacania faktur, przypisując im rolę menedżera faktury dla profilu rozliczeniowego. Jeśli wybrano opcję otrzymywania faktury w wiadomości e-mail, użytkownicy ci również otrzymują faktury w wiadomości e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/download-azure-invoice/search-cmb.png)

1. Po lewej stronie wybierz pozycję **Profile rozliczeniowe**. Z listy profilów rozliczeniowych wybierz profil rozliczeniowy, dla którego chcesz przypisać rolę menedżera faktur.

   [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Wybierz pozycję **Kontrola dostępu (IAM)** po lewej stronie, a następnie wybierz pozycję **Dodaj** w górnej części strony.

   [![Zrzut ekranu przedstawiający stronę kontroli dostępu](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. Z listy rozwijanej Rola wybierz pozycję **Menedżer faktur**. Wprowadź adres e-mail użytkownika, któremu chcesz przyznać dostęp. Wybierz przycisk **Zapisz**, aby przypisać rolę.

   [![Zrzut ekranu pokazujący dodawanie użytkownika jako menedżera faktur](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>Sprawdzanie typu konta rozliczeniowego
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktur i opłat, zobacz:

- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](download-azure-daily-usage.md)
- [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](review-individual-bill.md)
- [Omówienie terminów na fakturze za korzystanie z platformy Azure](understand-invoice.md)
- [Omówienie terminów dotyczących szczegółów użycia platformy Microsoft Azure](understand-usage.md)
- [Wyświetlanie cennika platformy Azure obowiązującego w organizacji](../manage/ea-pricing.md)

Jeśli masz umowę klienta firmy Microsoft, zobacz:

- [Omówienie opłat na fakturze dla profilu rozliczeniowego](review-customer-agreement-bill.md)
- [Omówienie terminów na fakturze dla profilu rozliczeniowego](mca-understand-your-invoice.md)
- [Omówienie pliku użycia i opłat dotyczących platformy Azure dla profilu rozliczeniowego](mca-understand-your-usage.md)
- [Wyświetlanie i pobieranie dokumentów podatkowych dla profilu rozliczeniowego](mca-download-tax-document.md)
- [Wyświetlanie cennika platformy Azure obowiązującego w organizacji](../manage/ea-pricing.md)
