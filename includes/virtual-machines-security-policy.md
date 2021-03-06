---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 53c9dea83fc6d413d7e82194696ffedabcc8cf7b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71830142"
---
Ważne jest, aby zachować bezpieczeństwo maszyny wirtualnej dla aplikacji, które są uruchamiane. Zabezpieczanie maszyn wirtualnych może obejmować jedną lub więcej usług i funkcji platformy Azure, które obejmują bezpieczny dostęp do maszyn wirtualnych i bezpieczny magazyn danych. Ten artykuł zawiera informacje, które umożliwiają bezpieczne zachowanie maszyny wirtualnej i aplikacji.

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Nowoczesne zagrożenie w środowiskach chmury jest dynamiczne, zwiększając nacisk na konserwację efektywnej ochrony w celu spełnienia wymagań dotyczących zgodności i zabezpieczeń. [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla systemu Azure](../articles/security/fundamentals/antimalware.md) to bezpłatna funkcja ochrony w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące oraz inne złośliwe oprogramowanie. Alerty można skonfigurować w taki sposób, aby powiadamiać użytkownika o próbie instalacji lub uruchomienia na maszynie wirtualnej znanego złośliwego lub niechcianego oprogramowania. Nie jest obsługiwana na maszynach wirtualnych z systemem Linux lub Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie na maszynach wirtualnych. Security Center zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne i współpracować z szeroką ekosystemem rozwiązań zabezpieczających.

Dostęp w czasie just in Time do Security Center można zastosować w ramach wdrożenia maszyny wirtualnej w celu zablokowania ruchu przychodzącego na maszynach wirtualnych platformy Azure, co pozwala ograniczyć narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Gdy jest włączona funkcja just in Time i użytkownik zażąda dostępu do maszyny wirtualnej, Security Center sprawdza, jakie uprawnienia użytkownik ma dla maszyny wirtualnej. Jeśli mają odpowiednie uprawnienia, żądanie zostanie zatwierdzone, a Security Center automatycznie skonfiguruje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń), aby zezwalały na ruch przychodzący do wybranych portów przez ograniczony czas. Po upływie tego czasu program Security Center Przywraca poprzedni stan sieciowych grup zabezpieczeń. 

## <a name="encryption"></a>Szyfrowanie

W przypadku ulepszonych zabezpieczeń i zgodności [maszyn](../articles/virtual-machines/linux/disk-encryption-overview.md) wirtualnych z [systemem Windows](../articles/virtual-machines/windows/encrypt-disks.md) można zaszyfrować dyski wirtualne na platformie Azure. Dyski wirtualne na maszynach wirtualnych z systemem Windows są szyfrowane przy użyciu funkcji BitLocker. Dyski wirtualne na maszynach wirtualnych z systemem Linux są szyfrowane przy użyciu DM-Crypt. 

Nie jest naliczana opłata za szyfrowanie dysków wirtualnych na platformie Azure. Klucze kryptograficzne są przechowywane w Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń) z certyfikatem standardu FIPS 140-2 Level 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i można przeprowadzić inspekcję ich użycia. Jednostka usługi Azure Active Directory zapewnia bezpieczny mechanizm wystawiania tych kluczy kryptograficznych w miarę włączania i wyłączania maszyn wirtualnych.

## <a name="key-vault-and-ssh-keys"></a>Klucze Key Vault i SSH

Wpisy tajne i certyfikaty mogą być modelowane jako zasoby i udostępniane przez [Key Vault](../articles/key-vault/key-vault-whatis.md). Za pomocą Azure PowerShell można tworzyć magazyny kluczy dla [maszyn wirtualnych z systemem Windows](../articles/virtual-machines/windows/key-vault-setup.md) i interfejsu wiersza polecenia platformy Azure dla [maszyn wirtualnych](../articles/virtual-machines/linux/key-vault-setup.md)z systemem Linux. Możesz również utworzyć klucze do szyfrowania.

Zasady dostępu magazynu kluczy przyznają oddzielnie uprawnienia do kluczy, wpisów tajnych i certyfikatów. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy, ale żadnych uprawnień do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych lub certyfikatów są jednak przyznawane na poziomie magazynu. Innymi słowy, [zasady dostępu magazynu kluczy](../articles/key-vault/key-vault-secure-your-key-vault.md) nie obsługują uprawnień na poziomie obiektu.

Łącząc się z maszynami wirtualnymi, należy użyć kryptografii klucza publicznego, aby zapewnić bezpieczniejsze metody logowania się do nich. Ten proces polega na wymianie klucza publicznego i prywatnego przy użyciu polecenia Secure Shell (SSH) w celu samodzielnego uwierzytelnienia, a nie nazwy użytkownika i hasła. Hasła są narażone na ataki z wymuszeniem, szczególnie w przypadku maszyn wirtualnych z Internetem, takich jak serwery sieci Web. Za pomocą pary kluczy Secure Shell (SSH) można utworzyć [maszynę wirtualną z systemem Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) , która używa kluczy SSH do uwierzytelniania, eliminując konieczność logowania przy użyciu haseł. Możesz również użyć kluczy SSH do nawiązania połączenia z [maszyną wirtualną z systemem Windows](../articles/virtual-machines/linux/ssh-from-windows.md) z MASZYNą wirtualną z systemem Linux.

## <a name="managed-identities-for-azure-resources"></a>Tożsamości zarządzane dla zasobów platformy Azure

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Zabezpieczanie poświadczeń to ważne zadanie. W idealnej sytuacji poświadczenia nie są nigdy wyświetlane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń, wpisów tajnych i innych kluczy, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. 

Funkcja tożsamości zarządzanych dla platformy Azure w usłudze Azure Active Directory (Azure AD) rozwiązuje ten problem. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie.  Kod uruchomiony na maszynie wirtualnej może zażądać tokenu z dwóch punktów końcowych, które są dostępne tylko z poziomu maszyny wirtualnej. Aby uzyskać szczegółowe informacje na temat tej usługi, zapoznaj się ze stroną przegląd [zarządzanych tożsamości dla zasobów platformy Azure](../articles/active-directory/managed-identities-azure-resources/overview.md) .   

## <a name="policies"></a>Zasady

[Zasady platformy Azure](../articles/azure-policy/azure-policy-introduction.md) mogą służyć do definiowania żądanego zachowania dla maszyn wirtualnych z [systemem Windows](../articles/virtual-machines/windows/policy.md) i maszyn wirtualnych systemu [Linux](../articles/virtual-machines/linux/policy.md)w organizacji. Korzystając z zasad, organizacja może wymusić różne konwencje i reguły w całym przedsiębiorstwie. Wymuszanie żądanego zachowania może pomóc w ograniczeniu ryzyka, a tym samym sukcesem organizacji.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Korzystając z [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md), można oddzielić cła w obrębie zespołu i przyznać dostęp tylko użytkownikom na maszynie wirtualnej, których potrzebują do wykonywania swoich zadań. Zamiast udzielać każdemu nieograniczonyemu uprawnienia do maszyny wirtualnej, można zezwolić tylko na niektóre akcje. Można skonfigurować kontrolę dostępu dla maszyny wirtualnej w [Azure Portal](../articles/role-based-access-control/role-assignments-portal.md)przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/role)lub[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z instrukcjami dotyczącymi monitorowania zabezpieczeń maszyn wirtualnych przy użyciu Azure Security Center dla [systemu](../articles/virtual-machines/windows/tutorial-azure-security.md) [Linux](../articles/security/fundamentals/overview.md) lub Windows.
