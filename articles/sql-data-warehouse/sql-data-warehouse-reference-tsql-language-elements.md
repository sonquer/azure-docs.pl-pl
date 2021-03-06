---
title: Elementy języka T-SQL
description: Linki do dokumentacji dotyczącej instrukcji języka T-SQL obsługiwanych w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 06/13/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 02f463e12547ba64a05e04988d9c192bba4f6a27
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692531"
---
# <a name="t-sql-language-elements-supported-in-azure-sql-data-warehouse"></a>Elementy języka T-SQL obsługiwane w Azure SQL Data Warehouse
Linki do dokumentacji dla elementów języka T-SQL obsługiwanych w Azure SQL Data Warehouse.

## <a name="core-elements"></a>Podstawowe elementy
* [Konwencje składni](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)
* [Reguły nazewnictwa obiektów](https://msdn.microsoft.com/library/ms175874.aspx)
* [zastrzeżone słowa kluczowe](https://msdn.microsoft.com/library/ms189822.aspx)
* [sortowania](https://msdn.microsoft.com/library/ff848763.aspx)
* [komentarz](https://msdn.microsoft.com/library/ms181627.aspx)
* [stałe](https://msdn.microsoft.com/library/ms179899.aspx)
* [typy danych](https://msdn.microsoft.com/library/ms187752.aspx)
* [WYKONANA](https://msdn.microsoft.com/library/ms188332.aspx)
* [wyrażeń](https://msdn.microsoft.com/library/ms190286.aspx)
* [KILLBIT](https://msdn.microsoft.com/library/ms173730.aspx)
* [Obejście właściwości tożsamości](https://msdn.microsoft.com/library/ms186775.aspx)
* [DRUKOWANY](https://msdn.microsoft.com/library/ms176047.aspx)
* [UŻYWANYCH](https://msdn.microsoft.com/library/ms188366.aspx)

## <a name="batches-control-of-flow-and-variables"></a>Partie, sterowanie przepływem i zmienne
* [Rozpocznij... PUNKTÓW](https://msdn.microsoft.com/library/ms190487.aspx)
* [Przerwij](https://msdn.microsoft.com/library/ms181271.aspx)
* [Zadeklaruj @local_variable](https://msdn.microsoft.com/library/ms188927.aspx)
* [IF... PRZEJMI](https://msdn.microsoft.com/library/ms182717.aspx)
* [INSTRUKCJI](https://msdn.microsoft.com/library/ms178592.aspx)
* [SET@local_variable](https://msdn.microsoft.com/library/ms189484.aspx)
* [GENEROWAĆ](https://msdn.microsoft.com/library/ee677615.aspx)
* [Spróbuj... EFEKTYWN](https://msdn.microsoft.com/library/ms175976.aspx)
* [CZEKAĆ](https://msdn.microsoft.com/library/ms178642.aspx)

## <a name="operators"></a>Operatory
* [+ (Dodaj)](https://msdn.microsoft.com/library/ms178565.aspx)
* [+ (Łączenie ciągów)](https://msdn.microsoft.com/library/ms177561.aspx)
* [-(Wartość ujemna)](https://msdn.microsoft.com/library/ms189480.aspx)
* [-(Odejmij)](https://msdn.microsoft.com/library/ms189518.aspx)
* [* (Pomnóż)](https://msdn.microsoft.com/library/ms176019.aspx)
* [/(Dzielenie)](https://msdn.microsoft.com/library/ms175009.aspx)
* [Operator](https://msdn.microsoft.com/library/ms190279.aspx)

## <a name="wildcard-characters-to-match"></a>Symbole wieloznaczne do dopasowania
* [= (Równa się)](https://msdn.microsoft.com/library/ms175118.aspx)
* [> (Większe niż)](https://msdn.microsoft.com/library/ms178590.aspx)
* [< (Mniejsze niż)](https://msdn.microsoft.com/library/ms179873.aspx)
* [> = (większe niż lub równe)](https://msdn.microsoft.com/library/ms181567.aspx)
* [< = (mniejsze lub równe)](https://msdn.microsoft.com/library/ms174978.aspx)
* [> < (nie równa się)](https://msdn.microsoft.com/library/ms176020.aspx)
* [! = (Nie równa się)](https://msdn.microsoft.com/library/ms190296.aspx)
* [LUB](https://msdn.microsoft.com/library/ms188372.aspx)
* [ZAKRESU](https://msdn.microsoft.com/library/ms187922.aspx)
* [ISTNIEJĄCY](https://msdn.microsoft.com/library/ms188336.aspx)
* [PODCZAS](https://msdn.microsoft.com/library/ms177682.aspx)
* [IS [NOT] NULL](https://msdn.microsoft.com/library/ms188795.aspx)
* [TYPU](https://msdn.microsoft.com/library/ms179859.aspx)
* [NIEMOŻLIWE](https://msdn.microsoft.com/library/ms189455.aspx)
* [ORAZ](https://msdn.microsoft.com/library/ms188361.aspx)

### <a name="bitwise-operators"></a>Operatory bitowe
* [& (Bitowe i)](https://msdn.microsoft.com/library/ms174965.aspx)
* [| (Bitowe lub)](https://msdn.microsoft.com/library/ms186714.aspx)
* [^ (Bitowe wykluczające lub)](https://msdn.microsoft.com/library/ms190277.aspx)
* [~ (Nie bitowe)](https://msdn.microsoft.com/library/ms173468.aspx)
* [^ = (Bitowe wykluczające lub równe)](https://msdn.microsoft.com/library/cc627413.aspx)
* [| = (Bitowe lub równe)](https://msdn.microsoft.com/library/cc627409.aspx)
* [& = (bitowe i równe)](https://msdn.microsoft.com/library/cc627427.aspx)

## <a name="functions"></a>Funkcje
* [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
* [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
* [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
* [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
* [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
* [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
* [ABS](https://msdn.microsoft.com/library/ms189800.aspx)
* [ACOS](https://msdn.microsoft.com/library/ms178627.aspx)
* [ASCII](https://msdn.microsoft.com/library/ms177545.aspx)
* [ASIN](https://msdn.microsoft.com/library/ms181581.aspx)
* [ATAN](https://msdn.microsoft.com/library/ms181746.aspx)
* [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
* [BINARY_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
* [SPRAW](https://msdn.microsoft.com/library/ms181765.aspx)
* [CAST i CONVERT](https://msdn.microsoft.com/library/ms187928.aspx)
* [Montaż](https://msdn.microsoft.com/library/ms189818.aspx)
* [DELIKATN](https://msdn.microsoft.com/library/ms187323.aspx)
* [Funkcja](https://msdn.microsoft.com/library/ms186323.aspx)
* [SUMA](https://msdn.microsoft.com/library/ms189788.aspx)
* [ŁĄCZONYCH](https://msdn.microsoft.com/library/ms190349.aspx)
* [COL_NAME](https://msdn.microsoft.com/library/ms174974.aspx)
* [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
* [CONCAT](https://msdn.microsoft.com/library/hh231515.aspx)
* [COSINUS](https://msdn.microsoft.com/library/ms188919.aspx)
* [COT](https://msdn.microsoft.com/library/ms188921.aspx)
* [LICZBĄ](https://msdn.microsoft.com/library/ms175997.aspx)
* [COUNT_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
* [CUME_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
* [CURRENT_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
* [CURRENT_USER](https://msdn.microsoft.com/library/ms176050.aspx)
* [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
* [Długość](https://msdn.microsoft.com/library/ms173486.aspx)
* [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
* [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
* [DATEFROMPARTS](https://msdn.microsoft.com/library/hh213228.aspx)
* [DATAname](https://msdn.microsoft.com/library/ms174395.aspx)
* [Funkcja](https://msdn.microsoft.com/library/ms174420.aspx)
* [DATETIME2FROMPARTS](https://msdn.microsoft.com/library/hh213312.aspx)
* [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
* [DATETIMEOFFSETFROMPARTS](https://msdn.microsoft.com/library/hh231077.aspx)
* [DZIEŃ](https://msdn.microsoft.com/library/ms176052.aspx)
* [DB_ID](https://msdn.microsoft.com/library/ms186274.aspx)
* [DB_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
* [KĄT](https://msdn.microsoft.com/library/ms178566.aspx)
* [DENSE_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
* [WYSTĘPUJĄ](https://msdn.microsoft.com/library/ms188753.aspx)
* [EOMONTH](https://msdn.microsoft.com/library/hh213020.aspx)
* [ERROR_MESSAGE](https://msdn.microsoft.com/library/ms190358.aspx)
* [ERROR_NUMBER](https://msdn.microsoft.com/library/ms175069.aspx)
* [ERROR_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
* [ERROR_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
* [ERROR_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
* [EXP](https://msdn.microsoft.com/library/ms179857.aspx)
* [FIRST_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
* [WYKŁADZIN](https://msdn.microsoft.com/library/ms178531.aspx)
* [GETDATE](https://msdn.microsoft.com/library/ms188383.aspx)
* [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
* [HAS_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
* [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
* [INDEXPROPERTY](https://msdn.microsoft.com/library/ms187729.aspx)
* [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
* [ISNULL](https://msdn.microsoft.com/library/ms184325.aspx)
* [ISNUMERIC](https://msdn.microsoft.com/library/ms186272.aspx)
* [OPÓŹNIENIA](https://msdn.microsoft.com/library/hh231256.aspx)
* [LAST_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
* [PROWADZIĆ](https://msdn.microsoft.com/library/hh213125.aspx)
* [LEWYM](https://msdn.microsoft.com/library/ms177601.aspx)
* [Funkcja](https://msdn.microsoft.com/library/ms190329.aspx)
* [REJESTROWANE](https://msdn.microsoft.com/library/ms190319.aspx)
* [Log10 —](https://msdn.microsoft.com/library/ms175121.aspx)
* [DOŁU](https://msdn.microsoft.com/library/ms174400.aspx)
* [DAWAJ](https://msdn.microsoft.com/library/ms177827.aspx)
* [Maksymalny](https://msdn.microsoft.com/library/ms187751.aspx)
* [DŁUGOŚCI](https://msdn.microsoft.com/library/ms179916.aspx)
* [BIEŻĄCYM](https://msdn.microsoft.com/library/ms187813.aspx)
* [NCHAR](https://msdn.microsoft.com/library/ms182673.aspx)
* [NTILE](https://msdn.microsoft.com/library/ms175126.aspx)
* [NULLIF](https://msdn.microsoft.com/library/ms177562.aspx)
* [OBJECT_ID](https://msdn.microsoft.com/library/ms190328.aspx)
* [OBJECT_NAME](https://msdn.microsoft.com/library/ms186301.aspx)
* [OBJECTPROPERTY](https://msdn.microsoft.com/library/ms176105.aspx)
* [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
* [Funkcje skalarne ODBC](https://msdn.microsoft.com/library/bb630290.aspx)
* [Klauzula OVER](https://msdn.microsoft.com/library/ms189461.aspx)
* [Analiza składni](https://msdn.microsoft.com/library/ms188006.aspx)
* [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
* [PERCENTILE_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
* [PERCENTILE_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
* [PERCENT_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
* [PRZETWARZANIA](https://msdn.microsoft.com/library/ms189512.aspx)
* [AWANSOWAN](https://msdn.microsoft.com/library/ms174276.aspx)
* [CUDZYSŁÓWname](https://msdn.microsoft.com/library/ms176114.aspx)
* [RADIANACH](https://msdn.microsoft.com/library/ms189742.aspx)
* [RAND](https://msdn.microsoft.com/library/ms177610.aspx)
* [STOPNI](https://msdn.microsoft.com/library/ms176102.aspx)
* [STĘPOWAĆ](https://msdn.microsoft.com/library/ms186862.aspx)
* [REPLIKUJ](https://msdn.microsoft.com/library/ms174383.aspx)
* [COFNIĘCI](https://msdn.microsoft.com/library/ms180040.aspx)
* [Kliknij](https://msdn.microsoft.com/library/ms177532.aspx)
* [Nit](https://msdn.microsoft.com/library/ms175003.aspx)
* [ROW_NUMBER](https://msdn.microsoft.com/library/ms186734.aspx)
* [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
* [SCHEMA_ID](https://msdn.microsoft.com/library/ms188797.aspx)
* [SCHEMA_NAME](https://msdn.microsoft.com/library/ms175068.aspx)
* [SERVERPROPERTY](https://msdn.microsoft.com/library/ms174396.aspx)
* [SESSION_USER](https://msdn.microsoft.com/library/ms177587.aspx)
* [ZAPIS](https://msdn.microsoft.com/library/ms188420.aspx)
* [SINUS](https://msdn.microsoft.com/library/ms188377.aspx)
* [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
* [SOUNDEX](https://msdn.microsoft.com/library/ms187384.aspx)
* [ODSTĘP](https://msdn.microsoft.com/library/ms187950.aspx)
* [SQL_VARIANT_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
* [SQRT](https://msdn.microsoft.com/library/ms176108.aspx)
* [KWADRATOWE](https://msdn.microsoft.com/library/ms173569.aspx)
* [STATS_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
* [STDEV](https://msdn.microsoft.com/library/ms190474.aspx)
* [ODCH](https://msdn.microsoft.com/library/ms176080.aspx)
* [STR](https://msdn.microsoft.com/library/ms189527.aspx)
* [RZECZY](https://msdn.microsoft.com/library/ms188043.aspx)
* [PODCIĄG](https://msdn.microsoft.com/library/ms187748.aspx)
* [NALEŻNOŚCI](https://msdn.microsoft.com/library/ms187810.aspx)
* [SUSER_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
* [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
* [SYSDATETIME](https://msdn.microsoft.com/library/bb630353.aspx)
* [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
* [SYSTEM_USER](https://msdn.microsoft.com/library/ms179930.aspx)
* [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
* [TAN](https://msdn.microsoft.com/library/ms190338.aspx)
* [TERTIARY_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
* [TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
* [TODATETIMEOFFSET](https://msdn.microsoft.com/library/bb630335.aspx)
* [TYPE_ID](https://msdn.microsoft.com/library/ms181628.aspx)
* [TYPE_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
* [TYPEPROPERTY](https://msdn.microsoft.com/library/ms188419.aspx)
* [UNICODE](https://msdn.microsoft.com/library/ms180059.aspx)
* [PRAWYM górnym](https://msdn.microsoft.com/library/ms180055.aspx)
* [Użytkownicy](https://msdn.microsoft.com/library/ms186738.aspx)
* [NAZWA_UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms188014.aspx)
* [FUNKCJĘ](https://msdn.microsoft.com/library/ms186290.aspx)
* [VARP](https://msdn.microsoft.com/library/ms188735.aspx)
* [CZTEROLETNIEGO](https://msdn.microsoft.com/library/ms186313.aspx)
* [XACT_STATE](https://msdn.microsoft.com/library/ms189797.aspx)

## <a name="transactions"></a>Transakcje
* [Akcja](https://msdn.microsoft.com/library/mt204031.aspx)

## <a name="diagnostic-sessions"></a>Sesje diagnostyczne
* [TWORZENIE SESJI DIAGNOSTYCZNEJ](https://msdn.microsoft.com/library/mt204029.aspx)

## <a name="procedures"></a>Postępowanie
* [sp_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
* [sp_columns](https://msdn.microsoft.com/library/ms176077.aspx)
* [sp_configure](https://msdn.microsoft.com/library/ms188787.aspx)
* [sp_datatype_info_90](https://msdn.microsoft.com/library/mt204014.aspx)
* [sp_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
* [sp_execute](https://msdn.microsoft.com/library/ff848746.aspx)
* [sp_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
* [sp_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
* [sp_pdw_add_network_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
* [sp_pdw_database_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
* [sp_pdw_database_encryption_regenerate_system_keys](https://msdn.microsoft.com/library/mt204033.aspx)
* [sp_pdw_log_user_data_masking](https://msdn.microsoft.com/library/mt204023.aspx)
* [sp_pdw_remove_network_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
* [sp_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
* [sp_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
* [sp_spaceused](https://msdn.microsoft.com/library/ms188776.aspx)
* [sp_special_columns_100](https://msdn.microsoft.com/library/mt204025.aspx)
* [sp_sproc_columns](https://msdn.microsoft.com/library/ms182705.aspx)
* [sp_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
* [sp_tables](https://msdn.microsoft.com/library/ms186250.aspx)
* [sp_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)

## <a name="set-statements"></a>Ustawianie instrukcji
* [USTAW ANSI_DEFAULTS](https://msdn.microsoft.com/library/ms188340.aspx)
* [USTAW ANSI_NULL_DFLT_OFF](https://msdn.microsoft.com/library/ms187356.aspx)
* [USTAW ANSI_NULL_DFLT_ON](https://msdn.microsoft.com/library/ms187375.aspx)
* [USTAW ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx)
* [USTAW WARTOŚĆ ANSI_PADDING](https://msdn.microsoft.com/library/ms187403.aspx)
* [USTAW ANSI_WARNINGS](https://msdn.microsoft.com/library/ms190368.aspx)
* [USTAW WARTOŚĆ ARITHABORT](https://msdn.microsoft.com/library/ms190306.aspx)
* [USTAW ARITHIGNORE](https://msdn.microsoft.com/library/ms184341.aspx)
* [USTAW CONCAT_NULL_YIELDS_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
* [USTAW DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx)
* [USTAW DATEFORMAT](https://msdn.microsoft.com/library/ms189491.aspx)
* [USTAW FMTONLY](https://msdn.microsoft.com/library/ms173839.aspx)
* [USTAW IMPLICIT_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
* [USTAW LOCK_TIMEOUT](https://msdn.microsoft.com/library/ms189470.aspx)
* [USTAW NUMBERIC_ROUNDABORT](https://msdn.microsoft.com/library/ms188791.aspx)
* [USTAW QUOTED_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
* [USTAW WARTOŚĆ ROWCOUNT](https://msdn.microsoft.com/library/ms188774.aspx)
* [USTAW WARTOŚĆ PARAMETRU TEXTSIZE](https://msdn.microsoft.com/library/ms186238.aspx)
* [USTAW POZIOM IZOLACJI TRANSAKCJI](https://msdn.microsoft.com/library/ms173763.aspx)
* [USTAW XACT_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [instrukcje języka T-SQL w Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-statements.md)i [widoki systemowe w programie Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-system-views.md).

