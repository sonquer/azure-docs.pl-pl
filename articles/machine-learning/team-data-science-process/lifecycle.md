---
title: Cykl życia zespołowego danych dla celów naukowych
description: Team Data Science naukowych oferuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720456"
---
# <a name="the-team-data-science-process-lifecycle"></a>Cykl życia zespołowego danych dla celów naukowych

Team Data Science naukowych oferuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia pełne kroki, które są wykonywane przez pomyślne projekty. W przypadku korzystania z innego cyklu życia nauki dotyczącego danych, takiego jak standardowy proces produkcji na potrzeby wyszukiwania danych [("wyrazisty DM")](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), odnajdywania wiedzy w bazach danych [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)lub własnego procesu niestandardowego w organizacji, można nadal używać przetwarzania TDSP opartego na zadaniach. 

Ten cykl życia jest przeznaczona dla projektów do nauki o danych, które są przeznaczone do wysłania jako część inteligentnych aplikacji. Te aplikacje wdrażania modeli uczenia i sztucznej inteligencji maszynowego do analizy predykcyjnej. Projekty naukowo-badawcze i projekty analizy Improvised mogą również korzystać z tego procesu. Jednak w tych projektach, niektóre kroki opisane w tym miejscu może być niepotrzebne. 

## <a name="five-lifecycle-stages"></a>Pięć etapów cyklu życia

Cykl życia przetwarzania TDSP składa się z pięć etapów głównych, które są wykonywane wielokrotnie. Etapy te obejmują:

   1. [Zrozumienie biznesowe](lifecycle-business-understanding.md)
   2. [Pozyskiwanie i zrozumienie danych](lifecycle-data.md)
   3. [Modelu](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Cykl życia przetwarzania TDSP w modelu za pomocą sekwencji iterowanej czynności, które zawiera wskazówek na temat zadań potrzebnych do korzystania z modeli predykcyjnych. Możesz wdrożyć modele predykcyjne w środowisku produkcyjnym, który ma być używany do tworzenia inteligentnych aplikacji. Celem tego cyklu życia procesu jest nadal można przenieść projektu do nauki o danych do punktu końcowego wyczyść zaangażowania. Do nauki o danych jest zadaniem w zakresie badań i odnajdywania. Możliwość komunikowania się zadania do Twojego zespołu i klientów za pomocą dobrze zdefiniowanych zestaw artefaktów, korzystających z szablonów standardowych pomaga uniknąć nieporozumień. Za pomocą tych szablonów również zwiększa prawdopodobieństwo pomyślnego ukończenia projektu nauki o danych złożonych.

Na każdym etapie firma Microsoft zapewnia następujące informacje:

   * **Cele**: określone cele.
   * **Jak to zrobić**: zarys określonych zadań i wskazówki dotyczące ich kończenia.
   * **Artefakty**: elementy dostarczane i pomoc techniczna do ich tworzenia.

## <a name="next-steps"></a>Następne kroki

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
