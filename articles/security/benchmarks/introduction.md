---
title: Wprowadzenie do usługi Azure Security test
description: Wprowadzenie do testu porównawczego zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945331"
---
# <a name="azure-security-benchmark-introduction"></a>Wprowadzenie do usługi Azure Security test

Użytkownik może mieć kilka lat lub nawet dekadę doświadczenia z lokalnym przetwarzaniem danych. Wiesz już, jak zabezpieczyć te wdrożenia; jednak chmura jest inna. Jak wiadomo, czy wdrożenia w chmurze są bezpieczne? Jakie są różnice między praktykami zabezpieczeń dla systemów lokalnych i wdrożeń w chmurze?

Istnieje obszerna kolekcja oficjalnych dokumentów, najlepszych rozwiązań, architektury referencyjnej, wskazówek dotyczących sieci Web, narzędzi typu "open source", roztworów komercyjnych, źródeł danych analizy i innych, które mogą pomóc w zabezpieczeniu chmury. Której opcji użyć? Co możesz zrobić, aby uzyskać akceptowalny poziom zabezpieczeń w chmurze? 

Jednym z najlepszych sposobów zabezpieczania wdrożeń w chmurze jest skoncentrowanie się na zaleceniach dotyczących usługi Cloud Security test. Zalecenia dotyczące testów porównawczych, na potrzeby zabezpieczania każdej usługi, należy zacząć od podstawowej wiedzy o ryzyku cyberbezpieczeństwa i sposobach zarządzania nimi. Następnie można użyć tego rozwiązania, przyjmując zalecenia dotyczące zabezpieczeń porównawczych od dostawcy usług w chmurze, aby ułatwić wybór określonych ustawień konfiguracji zabezpieczeń w danym środowisku. 

Test porównawczy zabezpieczeń platformy Azure obejmuje zbiór zaleceń dotyczących zabezpieczeń o dużym wpływie, których można użyć w celu zabezpieczenia większości usług używanych na platformie Azure. Te zalecenia można traktować jako "ogólne" lub "organizacyjne", które mają zastosowanie do większości usług platformy Azure. Zalecenia dotyczące testów porównawczych zabezpieczeń platformy Azure są następnie dostosowywane dla każdej usługi platformy Azure i dostosowane wskazówki są zawarte w artykułach zalecenia dotyczące usługi. 

W dokumentacji usługi Azure Security Tests określono mechanizmy kontroli zabezpieczeń i zalecenia dotyczące usług.

- **Kontrolki zabezpieczeń**: zalecenia dotyczące usługi Azure Security test zostały podzielone na kategorie według kontroli zabezpieczeń. Mechanizmy kontroli zabezpieczeń reprezentują wymagania dotyczące zabezpieczeń niezależny od wysokiego poziomu, takie jak zabezpieczenia sieci i ochrona danych. Każda kontrola zabezpieczeń ma zestaw zaleceń dotyczących zabezpieczeń i instrukcje, które ułatwiają zaimplementowanie tych zaleceń. 
- **Zalecenia dotyczące usługi**: Jeśli są dostępne, zalecenia dotyczące testów porównawczych dla usług platformy Azure obejmują zalecenia dotyczące zabezpieczeń platformy Azure, które są dostosowane specjalnie do tej usługi. 

Warunki "kontrola", "wzorzec" i "linia bazowa" są często używane w dokumentacji usługi Azure Security test i ważne jest, aby zrozumieć, w jaki sposób platforma Azure korzysta z tych warunków. 

| Okres obowiązywania Umowy | Opis | Przykład |
|--|--|--|
| Kontrola | **Kontrolka** to ogólny opis funkcji lub działania, które muszą zostać rozkierowane i nie jest specyficzny dla technologii lub implementacji. | Ochrona danych jest jedną z formantów zabezpieczeń. Ta kontrolka zawiera określone akcje, które należy rozwiązać, aby zapewnić ochronę danych. |
| Punkt odniesienia | **Wzorzec porównawczy** zawiera zalecenia dotyczące zabezpieczeń dla określonej technologii, takich jak Azure. Zalecenia są podzielone według kontrolki, do której należą. | Test porównawczy zabezpieczeń platformy Azure obejmuje zalecenia dotyczące zabezpieczeń specyficzne dla platformy Azure  |
| Punkt odniesienia | **Podstawą** dla organizacji są wymagania dotyczące zabezpieczeń. Wymagania dotyczące zabezpieczeń są oparte na zaleceniach dotyczących testów porównawczych. Każda organizacja decyduje, które zalecenia porównawcze mają być uwzględnione w ich linii bazowej. | Firma Contoso tworzy swoją linię bazową zabezpieczeń, wybierając, aby wymagać określonych zaleceń w teście zabezpieczeń Azure. |

Prosimy o opinię na temat testu porównawczego zabezpieczeń platformy Azure! Zachęcamy do podania komentarzy w obszarze opinii poniżej. Jeśli wolisz udostępniać dane własne więcej prywatnie z zespołem usługi Azure Security test, możesz uzupełnić formularz w https://aka.ms/AzSecBenchmark 
