---
title: Rozwiązywanie problemów ze starszymi przyłączonymi urządzeniami hybrydowymi Azure Active Directory
description: Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379120"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu 

Ten artykuł dotyczy tylko następujących urządzeń: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

W przypadku systemu Windows 10 lub Windows Server 2016 zobacz [Rozwiązywanie problemów hybrydowych Azure Active Directory dołączone do urządzeń z systemem Windows 10 i Windows server 2016](troubleshoot-hybrid-join-windows-current.md).

W tym artykule przyjęto założenie, że [skonfigurowano urządzenia hybrydowe Azure Active Directory dołączone](hybrid-azuread-join-plan.md) do obsługi następujących scenariuszy:

- Dostęp warunkowy oparty na urządzeniach

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, które rozwiązują potencjalne problemy.  

**Co należy wiedzieć:** 

- Hybrydowe dołączanie usługi Azure AD dla urządzeń z systemem Windows o niższych działaniach działa nieco inaczej niż w systemie Windows 10. Wielu klientów nie zakłada, że potrzebują AD FS (dla domen federacyjnych) lub bezproblemowe skonfigurowanie logowania jednokrotnego (dla domen zarządzanych).
- W przypadku klientów z domenami federacyjnymi, jeśli punkt połączenia usługi został skonfigurowany w taki sposób, aby wskazywał nazwę domeny zarządzanej (na przykład contoso.onmicrosoft.com, a nie contoso.com), to sprzężenie hybrydowe usługi Azure AD dla urządzeń z systemem Windows o niższych wartościach będzie nie działają.
- Maksymalna liczba urządzeń dla każdego użytkownika dotyczy obecnie również urządzeń przyłączonych do hybrydowego poziomu usługi Azure AD. 
- To samo urządzenie fizyczne pojawia się wiele razy w usłudze Azure AD, gdy wielu użytkowników domeny loguje się do hybrydowych urządzeń przyłączonych do usługi Azure AD.  Na przykład jeśli *JKowalski* i *jharnett* logowanie do urządzenia, dla każdego z nich zostanie utworzony osobna rejestracja (identyfikator urządzenia) na karcie Informacje o **użytkowniku** . 
- Możesz również uzyskać wiele wpisów dla urządzenia na karcie Informacje o użytkowniku z powodu ponownej instalacji systemu operacyjnego lub ręcznego ponownego rejestrowania.
- Początkowa Rejestracja/przyłączanie urządzeń jest skonfigurowany do wykonania próby zalogowania się lub zablokowania/odblokowania. Zadanie harmonogramu zadań może wystąpić z 5-minutowego opóźnienia. 
- Upewnij się, że zainstalowano [KB4284842](https://support.microsoft.com/help/4284842) , w przypadku systemu Windows 7 z dodatkiem SP1 lub windows Server 2008 R2 z dodatkiem SP1. Ta aktualizacja zapobiega występowaniu w przyszłości niepowodzeń uwierzytelniania z powodu utraty dostępu klienta do kluczy chronionych po zmianie hasła.

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1. Pobieranie stanu rejestracji 

**Aby sprawdzić stan rejestracji:**  

1. Zaloguj się przy użyciu konta użytkownika, które wykonał sprzężenie hybrydowe usługi Azure AD.
1. Otwórz wiersz polecenia 
1. Wpisz polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

To polecenie wyświetla okno dialogowe, które zawiera szczegółowe informacje o stanie sprzężenia.

![Workplace Join dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2. oszacowanie stanu hybrydowego przyłączenia do usługi Azure AD 

Jeśli urządzenie nie zostało dołączone do hybrydowej usługi Azure AD, możesz spróbować wykonać sprzężenie hybrydowe usługi Azure AD, klikając przycisk "Przyłączanie". Jeśli próba wykonania hybrydowego sprzężenia usługi Azure AD zakończy się niepowodzeniem, zostaną wyświetlone szczegółowe informacje o błędzie.

**Najczęstsze problemy są następujące:**

- Nieprawidłowo skonfigurowane AD FS lub problemy z usługą Azure AD lub sieci

    ![Workplace Join dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Nie można w trybie dyskretnym przeprowadzić uwierzytelniania za pomocą usługi Azure AD lub AD FS. Może to być spowodowane brakiem lub nieprawidłowym skonfigurowaniem AD FS (dla domen federacyjnych) lub braku lub nieprawidłowym skonfigurowaniu logowania jednokrotnego w usłudze Azure AD (w przypadku domen zarządzanych) lub problemów z siecią. 
   - Może to być, że uwierzytelnianie wieloskładnikowe (MFA) jest włączone/skonfigurowane dla użytkownika, a WIAORMULTIAUTHN nie jest skonfigurowany na serwerze AD FS. 
   - Kolejną możliwością jest to, że strona odnajdowania obszaru macierzystego (HRD) oczekuje na interakcję użytkownika, co uniemożliwia automatyczne zażądanie tokenu przez program **. exe.**
   - Może to być AD FS i brak adresów URL usługi Azure AD w strefie intranetowej programu IE na kliencie.
   - Problemy z łącznością sieciową mogą uniemożliwiać automatyczne przejście do AD FS lub adresów URL usługi Azure **AD.** 
   - Usługa autosite **. exe** wymaga, aby klient miał bezpośredni wgląd z klienta do lokalnego kontrolera domeny usługi AD w organizacji, co oznacza, że sprzężenie hybrydowe usługi Azure AD powiedzie się tylko wtedy, gdy klient jest połączony z intranetem firmy.
   - Twoja organizacja korzysta z bezproblemowego logowania jednokrotnego usługi Azure AD, `https://autologon.microsoftazuread-sso.com` lub `https://aadg.windows.net.nsatc.net` nie są obecne w ustawieniach sieci intranet dla programu IE urządzenia i nie włączono **aktualizacji na pasku stanu za pomocą skryptu** dla strefy intranet.
- Użytkownik nie jest zalogowany jako użytkownik domeny

   ![Workplace Join dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Istnieje kilka różnych powodów, dla których może to być spowodowane:

   - Zalogowany użytkownik nie jest użytkownikiem domeny (na przykład użytkownik lokalny). Hybrydowe dołączanie usługi Azure AD na urządzeniach niskiego poziomu jest obsługiwane tylko dla użytkowników domeny.
   - Klient nie może nawiązać połączenia z kontrolerem domeny.    
- Osiągnięto limit przydziału

    ![Workplace Join dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Usługa nie odpowiada 

    ![Workplace Join dla systemu Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Informacje o stanie można także znaleźć w dzienniku zdarzeń w obszarze: **aplikacje i usługi Log\Microsoft-Workplace Join**
  
**Najczęstszymi przyczynami niepowodzenia hybrydowego przyłączenia do usługi Azure AD są:** 

- Komputer nie jest podłączony do sieci wewnętrznej organizacji ani do sieci VPN z połączeniem z lokalnym kontrolerem domeny usługi AD.
- Użytkownik jest zalogowany na komputerze przy użyciu konta komputera lokalnego. 
- Problemy z konfiguracją usługi: 
   - Serwer AD FS nie został skonfigurowany do obsługi **WIAORMULTIAUTHN**. 
   - Las komputera nie ma obiektu punktu połączenia z usługą, który wskazuje zweryfikowaną nazwę domeny w usłudze Azure AD 
   - Lub jeśli domena jest zarządzana, bezproblemowe logowanie jednokrotne nie zostało skonfigurowane ani nie działa.
   - Użytkownik osiągnął limit urządzeń. 

## <a name="next-steps"></a>Następne kroki

Pytania można znaleźć w temacie [często zadawane pytania dotyczące zarządzania urządzeniami](faq.md)  
