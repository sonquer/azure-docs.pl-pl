---
title: Przykładowe typy klas w Azure Lab Services | Microsoft Docs
description: Zawiera typy klas, dla których można skonfigurować laboratoria przy użyciu Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 80204b6f156981ab3ecb8f348f3ce7ea077a6836
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443537"
---
# <a name="class-types-overview---azure-lab-services"></a>Przegląd typów klas — Azure Lab Services

Azure Lab Services umożliwia szybkie Konfigurowanie środowisk laboratoryjnych w chmurze. Artykuły w tej sekcji zawierają wskazówki dotyczące konfigurowania kilku typów laboratoriów zajęć przy użyciu Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Uczenie głębokie w przetwarzaniu języka naturalnego

Można skonfigurować laboratorium ukierunkowane na głębokie uczenie w ramach przetwarzania języka naturalnego (NLP) przy użyciu Azure Lab Services. Przetwarzanie języka naturalnego (NLP) jest formą sztucznej analizy (AI), która umożliwia komputerom z translacją, rozpoznawanie mowy i innymi funkcjami interpretacji języka. Studenci tworzący klasę NLPą mogą uzyskać maszynę wirtualną z systemem Linux, aby dowiedzieć się, jak zastosować algorytmy sieciowe neuronowych w celu opracowania modeli uczenia głębokiego, które są używane do analizowania zapisanych języków ludzkich.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium ukierunkowanego na głębokie uczenie w zakresie przetwarzania w języku naturalnym przy użyciu Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Skrypty powłoki w systemie Linux

Można skonfigurować laboratorium do uczenia skryptów powłoki w systemie Linux. Obsługa skryptów jest przydatną częścią administrowania systemem, która umożliwia administratorom uniknięcie powtarzających się zadań. W tym przykładowym scenariuszu Klasa obejmuje tradycyjne skrypty bash oraz ulepszone skrypty. Ulepszone skrypty to skrypty, które łączą bash polecenia i Ruby. Takie podejście umożliwia używanie języka Ruby do przekazywania danych wokół i bash poleceń w celu współdziałania z powłoką.

Studenci korzystający z tych klas skryptów otrzymują maszynę wirtualną z systemem Linux, aby poznać podstawy systemu Linux, a także zapoznać się z obsługą skryptów powłoki bash. Na maszynie wirtualnej z systemem Linux jest włączona funkcja dostępu do pulpitu zdalnego z zainstalowanym edytorem tekstu w języku [regedit](https://help.gnome.org/users/gedit/stable/) i [Visual Studio Code](https://code.visualstudio.com/) .

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etyczne działania hakerskie

Można skonfigurować laboratorium dla klasy, która koncentruje się na dowodowych etycznej działanie hakerskie. Testowanie penetracji, metoda stosowana przez społeczność etyczną działanie hakerskie, występuje, gdy ktoś próbuje uzyskać dostęp do systemu lub sieci w celu zademonstrowania luk w zabezpieczeniach.

W klasie etycznej działanie hakerskie studenci mogą uczyć się nowoczesnych technik obrony przed lukami w zabezpieczeniach. Każdy student otrzymuje maszynę wirtualną hosta systemu Windows Server, która ma dwie zagnieżdżone maszyny wirtualne — jedną maszynę wirtualną z obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) i inną maszynę z obrazem [Kali Linux](https://www.kali.org/) . Maszyna wirtualna Metasploitable jest używana do wykorzystania w celach.  Maszyna wirtualna z systemem Kali Linux zapewnia dostęp do narzędzi wymaganych do wykonywania zadań śledczej.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia klasy etycznej działanie hakerskie](class-type-ethical-hacking.md).

## <a name="database-management"></a>Zarządzanie bazami danych
Pojęcia dotyczące baz danych są jednym z kursów wprowadzających w większości działów nauki komputerowych w szkole. W Azure Lab Services można skonfigurować laboratorium dla podstawowej klasy zarządzania bazami danych. Na przykład można skonfigurować szablon maszyny wirtualnej w laboratorium z serwerem bazy danych [MySQL](https://www.mysql.com/) lub serwerem [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) .

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia zarządzania bazami danych dla relacyjnych baz danych](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Notesy Python i Jupyter
Można skonfigurować maszynę szablonu w Azure Lab Services z narzędziami wymaganymi do nauki uczniów, jak korzystać z [notesów Jupyter](http://jupyter-notebook.readthedocs.io). Notesy Jupyter to projekt typu "open source", który umożliwia łatwe łączenie tekstu sformatowanego i wykonywalnego kodu źródłowego języka [Python](https://www.python.org/) na jednej kanwie o nazwie Notes. Uruchamianie notesu skutkuje liniowym rekordem danych wejściowych i wyjściowych.  Mogą one obejmować tekst, tabele informacji, wykresy punktowe i wiele innych.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia danych przy użyciu notesów Python i Jupyter](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Tworzenie aplikacji mobilnych za pomocą programu Android Studio
Można skonfigurować laboratorium w Azure Lab Services, aby nauczyć się wprowadzenia klasy opracowywania aplikacji mobilnych. Ta klasa koncentruje się na aplikacjach mobilnych systemu Android, które można opublikować w [sklep Google Play](https://play.google.com/store/apps).  Studenci mogą dowiedzieć się, jak używać [Android Studio](https://developer.android.com/studio) do kompilowania aplikacji.  [Emulator programu Visual Studio dla systemu Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) służy do lokalnego testowania aplikacji.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia opracowywania aplikacji mobilnych za pomocą Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Skonfiguruj laboratorium ukierunkowane na głębokie uczenie się w przetwarzaniu języka naturalnego przy użyciu Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md)
- [Działanie hakerskie etyczny](class-type-ethical-hacking.md)
