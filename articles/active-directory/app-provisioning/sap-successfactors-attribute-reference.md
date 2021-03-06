---
title: Odwołanie do atrybutu SAP SuccessFactors | Microsoft Docs
description: Dowiedz się, jakie atrybuty z SuccessFactors są obsługiwane przez inicjowanie obsługi SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522360"
---
# <a name="sap-successfactors-attribute-reference"></a>Odwołanie do atrybutu SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Obsługiwane jednostki i atrybuty SuccessFactors

W poniższej tabeli przechwycono listę atrybutów SuccessFactors obsługiwanych przez następujące dwie aplikacje aprowizacji: 
* [SuccessFactors Active Directory aprowizacji użytkowników](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors do aprowizacji użytkowników usługi Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Jednostka SuccessFactors                  | SuccessFactors — atrybut     | Typ operacji |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Odczyt           |
| 2  | PerPerson                              | personId                     | Odczyt           |
| 3  | PerPerson                              | perPersonUuid                | Odczyt           |
| 4  | PerPersonal                            | displayName                  | Odczyt           |
| 5  | PerPersonal                            | firstName                    | Odczyt           |
| 6  | PerPersonal                            | płeć                       | Odczyt           |
| 7  | PerPersonal                            | lastName                     | Odczyt           |
| 8  | PerPersonal                            | MiddleName                   | Odczyt           |
| 9  | PerPersonal                            | preferowany                | Odczyt           |
| 10 | Użytkownik                                   | addressLine1                 | Odczyt           |
| 11 | Użytkownik                                   | addressLine2                 | Odczyt           |
| 12 | Użytkownik                                   | addressLIne3                 | Odczyt           |
| 13 | Użytkownik                                   | businessPhone                | Odczyt           |
| 14 | Użytkownik                                   | cellPhone                    | Odczyt           |
| 15 | Użytkownik                                   | city                         | Odczyt           |
| 16 | Użytkownik                                   | trzeciego                      | Odczyt           |
| 17 | Użytkownik                                   | custom01                     | Odczyt           |
| 18 | Użytkownik                                   | custom02                     | Odczyt           |
| 19 | Użytkownik                                   | custom03                     | Odczyt           |
| 20 | Użytkownik                                   | custom04                     | Odczyt           |
| 21 | Użytkownik                                   | custom05                     | Odczyt           |
| 22 | Użytkownik                                   | custom06                     | Odczyt           |
| 23 | Użytkownik                                   | custom07                     | Odczyt           |
| 24 | Użytkownik                                   | custom08                     | Odczyt           |
| 25 | Użytkownik                                   | custom09                     | Odczyt           |
| 26 | Użytkownik                                   | custom10                     | Odczyt           |
| 27 | Użytkownik                                   | custom11                     | Odczyt           |
| 28 | Użytkownik                                   | custom12                     | Odczyt           |
| 29 | Użytkownik                                   | custom13                     | Odczyt           |
| 30 | Użytkownik                                   | custom14                     | Odczyt           |
| 31 | Użytkownik                                   | empId                        | Odczyt           |
| 32 | Użytkownik                                   | homePhone                    | Odczyt           |
| 33 | Użytkownik                                   | jobFamily                    | Odczyt           |
| 34 | Użytkownik                                   | pseudonim                     | Odczyt           |
| 35 | Użytkownik                                   | state                        | Odczyt           |
| 36 | Użytkownik                                   | timeZone                     | Odczyt           |
| 37 | Użytkownik                                   | nazwa użytkownika                     | Odczyt           |
| 38 | Użytkownik                                   | Kod pocztowy                      | Odczyt           |
| 39 | PerPhone                               | areaCode                     | Odczyt           |
| 40 | PerPhone                               | countryCode                  | Odczyt           |
| 41 | PerPhone                               | rozszerzenia                    | Odczyt           |
| 42 | PerPhone                               | phoneNumber                  | Odczyt           |
| 43 | PerPhone                               | numer telefonu                    | Odczyt           |
| 44 | PerEmail                               | emailAddress                 | Odczyt, zapis    |
| 45 | PerEmail                               | adres e-mail                    | Odczyt           |
| 46 | EmpEmployment                          | firstDateWorked              | Odczyt           |
| 47 | EmpEmployment                          | lastDateWorked               | Odczyt           |
| 48 | EmpEmployment                          | userId                       | Odczyt           |
| 49 | EmpEmployment                          | isContingentWorker           | Odczyt           |
| 50 | EmpJob                                 | countryOfCompany             | Odczyt           |
| 51 | EmpJob                                 | emplStatus                   | Odczyt           |
| 52 | EmpJob                                 | endDate                      | Odczyt           |
| 53 | EmpJob                                 | startDate                    | Odczyt           |
| 54 | EmpJob                                 | Stanowisko                     | Odczyt           |
| 55 | EmpJob                                 | położenie                     | Odczyt           |
| 65 | EmpJob                                 | customString13               | Odczyt           |
| 56 | EmpJob                                 | managerId                    | Odczyt           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Odczyt           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Odczyt           |
| 59 | EmpJob\.firmy                        | company                      | Odczyt           |
| 60 | EmpJob\.firmy                        | companyId                    | Odczyt           |
| 61 | EmpJob\.firma\.CountryOfRegistration | twoCharCountryCode           | Odczyt           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Odczyt           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Odczyt           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Odczyt           |
| 65 | EmpJob\.dział                     | department                   | Odczyt           |
| 66 | EmpJob\.dział                     | departmentId                 | Odczyt           |
| 67 | EmpJob\.                       | dzielenie                     | Odczyt           |
| 68 | EmpJob\.                       | divisionId                   | Odczyt           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Odczyt           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Odczyt           |
| 71 | EmpJob\.lokalizacji                       | Lokalizacja                 | Odczyt           |
| 72 | EmpJob\.lokalizacji                       | officeLocationAddress        | Odczyt           |
| 73 | EmpJob\.lokalizacji                       | officeLocationCity           | Odczyt           |
| 74 | EmpJob\.lokalizacji                       | officeLocationCustomString4  | Odczyt           |
| 75 | EmpJob\.lokalizacji                       | officeLocationZipCode        | Odczyt           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Odczyt           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Odczyt           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Odczyt           |


## <a name="default-attribute-mapping"></a>Domyślne mapowanie atrybutów

Poniższa tabela zawiera domyślne mapowanie atrybutów między SuccessFactorsmi wymienionymi powyżej i atrybutami AD/Azure AD. W bloku "mapowanie" aplikacji aprowizacji usługi Azure AD można zmodyfikować to mapowanie domyślne w celu uwzględnienia atrybutów z powyższej listy. 

| \# | Jednostka SuccessFactors                  | SuccessFactors — atrybut | Domyślne mapowanie atrybutów AD/Azure AD   | Przetwarzanie uwagi                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Używane jako pasujący atrybut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | Nie \[zamapowane \- używane jako zakotwiczenie źródła\] | Podczas synchronizacji początkowej usługa aprowizacji łączy personUuid z istniejącym objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Nie dotyczy                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Nie dotyczy                                                                                           |
| 5  | PerPersonal                            | lastName                 | numery seryjne                                      | Nie dotyczy                                                                                           |
| 6  | Użytkownik                                   | addressLine1             | Adres                           | Nie dotyczy                                                                                           |
| 7  | Użytkownik                                   | city                     | l                                       | Nie dotyczy                                                                                           |
| 8  | Użytkownik                                   | trzeciego                  | co                                      | Nie dotyczy                                                                                           |
| 9  | Użytkownik                                   | state                    | St                                      | Nie dotyczy                                                                                           |
| 10 | Użytkownik                                   | nazwa użytkownika                 | samAccountName                          | Nie dotyczy                                                                                           |
| 11 | Użytkownik                                   | Kod pocztowy                  | postalCode                              | Nie dotyczy                                                                                           |
| 12 | PerEmail                               | emailAddress             | poczta                                    | Nie dotyczy                                                                                           |
| 13 | EmpJob                                 | Stanowisko                 | title                                   | Nie dotyczy                                                                                           |
| 14 | EmpJob                                 | managerId                | Menedżer                                 | Nie dotyczy                                                                                           |
| 15 | EmpJob\.firma\.CountryOfRegistration | twoCharCountryCode       | c                                       | Nie dotyczy                                                                                           |
| 16 | EmpJob\.dział                     | department               | department                              | Nie dotyczy                                                                                           |
| 17 | EmpJob\.                       | dzielenie                 | company                                 | Nie dotyczy                                                                                           |
| 18 | EmpJob\.lokalizacji                       | officeLocationAddress    | Adres                           | Nie dotyczy                                                                                           |
| 19 | EmpJob\.lokalizacji                       | officeLocationZipCode    | postalCode                              | Nie dotyczy                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Jeśli activeEmploymentsCount = 0, wyłącz account\.                                           |

