---
title: Microsoft Defender Advanced Threat Protection — Azure Security Center
description: W tym dokumencie przedstawiono integrację między programem Azure Security Center i zaawansowaną ochroną przed zagrożeniami w usłudze Microsoft Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 46b9fe5c6a038aa98cf8df64c40bf8ea1747efec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663610"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender przy użyciu Azure Security Center

Azure Security Center rozszerza oferty platformy ochrony obciążeń w chmurze przez integrację z usługą [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Ta zmiana zapewnia kompleksowe możliwości dotyczące wykrywania i odpowiedzi punktu końcowego (EDR, Endpoint Detection and Response). Dzięki integracji z usługą Microsoft Defender ATP można wykasować nietypowe. Możesz także wykryć zaawansowane ataki i odpowiedzieć na nie w punktach końcowych serwera monitorowanych przez Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Funkcje ATP dotyczące programu Microsoft Defender w Security Center

W przypadku korzystania z programu Microsoft Defender ATP uzyskasz następujące korzyści:

- **Zaawansowane czujniki wykrywania po naruszeniu**oprogramowania: czujniki usługi Microsoft Defender ATP dla serwerów z systemem Windows zbierają ogromną gamę sygnałów z zachowaniem.

- **Wykrywanie naruszeń oprogramowania oparte na analizie w chmurze**: Usługa Microsoft Defender ATP szybko dostosowuje się do zmieniających się zagrożeń. Używa zaawansowanej analizy i danych Big Data. Usługa Microsoft Defender ATP jest wzmocniona możliwościami Intelligent Security Graph z sygnałami w systemach Windows, Azure i Office w celu wykrywania nieznanych zagrożeń. Zapewnia alerty funkcjonalne i umożliwia szybkie reagowanie na nie.

- **Analiza zagrożeń**: Usługa Microsoft Defender ATP generuje alerty po zidentyfikowaniu narzędzi, technik i procedur osoby atakującej. Używa on danych wygenerowanych przez program Microsoft Threat myśliwych i zespoły ds. zabezpieczeń, uzupełnione o analizy udostępniane przez partnerów.

Następujące funkcje są teraz dostępne w Azure Security Center:

- **Automatyczne**dołączanie: czujnik usługi Microsoft Defender ATP jest automatycznie włączany dla serwerów z systemem Windows, które są dołączane do Azure Security Center.

- **Pojedyncze okienko szkła**: w konsoli Azure Security Center są wyświetlane alerty programu Microsoft Defender ATP.

- **Szczegółowe badanie maszynowe**: Azure Security Center klienci mogą korzystać z konsoli programu Microsoft Defender ATP w celu przeprowadzenia szczegółowej analizy w celu odzyskania zakresu naruszenia.

![Azure Security Center, wyświetlanie listy alertów i ogólnych informacji o każdym alercie](media/security-center-wdatp/image1.png)

Aby przeprowadzić dalsze badanie, użyj programu Microsoft Defender ATP. Usługa Microsoft Defender ATP udostępnia dodatkowe informacje, takie jak drzewo procesu alertu i wykres incydentu. Możesz również wyświetlić szczegółową oś czasu komputera, która pokazuje każde zachowanie w okresie historycznym przez maksymalnie sześć miesięcy.

![Strona Microsoft Defender ATP ze szczegółowymi informacjami o alercie](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Obsługa platform

Usługa Microsoft Defender ATP w Security Center obsługuje wykrywanie w systemach Windows Server 2016, 2012 R2 i 2008 R2 SP1 w przypadku maszyn wirtualnych platformy Azure potrzebna jest subskrypcja warstwy Standardowa i dla maszyn wirtualnych z systemem innym niż Azure potrzebna jest warstwa standardowa tylko na poziomie obszaru roboczego.

> [!NOTE]
> W przypadku korzystania z Azure Security Center do monitorowania serwerów usługa Microsoft Defender ATP zostanie automatycznie utworzona, a dane z witryny Microsoft Defender ATP są domyślnie przechowywane w Europie. Jeśli musisz przenieść dane do innej lokalizacji, musisz skontaktować się z pomoc techniczna firmy Microsoft w celu zresetowania dzierżawy. Monitorowanie punktu końcowego serwera korzystające z tej integracji zostało wyłączone dla klientów korzystających z pakietu Office 365 w zatoce.

## <a name="onboarding-servers-to-security-center"></a>Dołączanie serwerów do Security Center 

Aby dołączać serwery do Security Center, kliknij przycisk **Przejdź do Azure Security Center do** dołączania serwerów z usługi Microsoft Defender ATP Server dołączanie.

1. W obszarze dołączania wybierz lub Utwórz obszar roboczy, w którym mają zostać zapisane dane. <br>
2. Jeśli nie widzisz wszystkich obszarów roboczych, może to być spowodowane brakiem uprawnień, upewnij się, że obszar roboczy jest ustawiony na warstwę Standardowa usługi Azure Security. Aby uzyskać więcej informacji, zobacz [uaktualnianie do warstwy standardowa Security Center w celu zwiększenia bezpieczeństwa](security-center-pricing.md).
    
3. Wybierz pozycję **Dodaj serwery** , aby wyświetlić instrukcje dotyczące sposobu instalowania Microsoft Monitoring Agent. 

4. Po dołączeniu można monitorować maszyny w obszarze **obliczenia i aplikacje**.

   ![Dołączanie komputerów](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Włącz integrację z programem Microsoft Defender ATP

Aby sprawdzić, czy integracja z usługą Microsoft Defender ATP jest włączona, wybierz pozycję **Security center** > **cennik & Ustawienia** > kliknij subskrypcję.
W tym miejscu można zobaczyć aktualnie włączone integracje.

  ![Strona ustawień wykrywania zagrożeń Azure Security Center z włączoną integracją z usługą Microsoft Defender ATP](media/security-center-wdatp/enable-integrations.png)

- Jeśli serwery zostały już dołączone do Azure Security Center warstwy Standardowa, nie musisz podejmować żadnych dalszych działań. Azure Security Center automatycznie dołączać serwery do programu Microsoft Defender ATP. Dołączanie może potrwać do 24 godzin.

- Jeśli nie dołączysz serwerów do Azure Security Center warstwy Standardowa, Dołącz je do Azure Security Center jak zwykle.

- W przypadku dołączania serwerów za pomocą programu Microsoft Defender ATP:
  - Zapoznaj się z dokumentacją, aby uzyskać wskazówki dotyczące [odłączania maszyn serwera](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Dołącz te serwery do Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Dostęp do portalu Microsoft Defender ATP

Postępuj zgodnie z instrukcjami w temacie [Przypisywanie dostępu użytkowników do portalu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Ustawianie konfiguracji zapory

Jeśli masz serwer proxy lub zaporę blokującą ruch anonimowy, ponieważ czujnik usługi Microsoft Defender ATP nawiązuje połączenie z kontekstu systemu, upewnij się, że ruch anonimowy jest dozwolony. Postępuj zgodnie z instrukcjami w temacie [Włączanie dostępu do adresów URL usługi Microsoft Defender ATP na serwerze proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testowanie funkcji

Aby wygenerować niegroźny alert dotyczący testu Microsoft Defender ATP:

1. Użyj Pulpit zdalny, aby uzyskać dostęp do maszyny wirtualnej z systemem Windows Server 2012 R2 lub z maszyną wirtualną z systemem Windows Server 2016. Otwórz okno wiersza polecenia.

2. W wierszu polecenia skopiuj i uruchom następujące polecenie. Okno wiersza polecenia zostanie zamknięte automatycznie.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Okno wiersza polecenia z powyższym poleceniem](media/security-center-wdatp/image4.jpeg)

3. Jeśli polecenie zakończy się pomyślnie, zobaczysz nowy Alert na pulpicie nawigacyjnym Azure Security Center i portalu Microsoft Defender ATP. Ten alert może potrwać kilka minut.

4. Aby sprawdzić alert w Security Center, przejdź do **alertów zabezpieczeń** > **podejrzanego wiersza polecenia programu PowerShell**.

5. W oknie badanie wybierz link, aby przejść do portalu usługi Microsoft Defender ATP.

## <a name="next-steps"></a>Następne kroki

- [Platformy i funkcje obsługiwane przez usługę Azure Security Center](security-center-os-coverage.md)
- [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md): informacje na temat sposobu, w jaki zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
