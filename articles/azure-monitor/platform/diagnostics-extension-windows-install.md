---
title: Instalowanie i Konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (funkcji wad)
description: Dowiedz się, jak zbierać dane diagnostyczne platformy Azure na koncie usługi Azure Storage, aby można było wyświetlać je za pomocą jednego z kilku dostępnych narzędzi.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 5b3cc4cbaa663b7932609e85c544378a7cca69ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472689"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalowanie i Konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (funkcji wad)
Rozszerzenie diagnostyki Azure to Agent w Azure Monitor, który zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych platformy Azure i innych zasobów obliczeniowych. Ten artykuł zawiera szczegółowe informacje na temat instalowania i konfigurowania rozszerzenia Diagnostyka systemu Windows oraz opis sposobu przechowywania danych w usłudze i koncie usługi Azure Storage.

Rozszerzenie diagnostyki jest zaimplementowane jako [rozszerzenie maszyny wirtualnej](/virtual-machines/extensions/overview) na platformie Azure, dlatego obsługuje te same opcje instalacji przy użyciu szablonów Menedżer zasobów, programu PowerShell i interfejsu wiersza polecenia. Zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows,](/virtual-machines/extensions/features-windows) Aby uzyskać szczegółowe informacje na temat instalowania i konserwowania rozszerzeń maszyn wirtualnych.

## <a name="install-with-azure-portal"></a>Zainstaluj przy użyciu Azure Portal
Można zainstalować i skonfigurować rozszerzenie diagnostyki na pojedynczej maszynie wirtualnej w Azure Portal, która zapewnia interfejs, a nie działa bezpośrednio z konfiguracją. Włączenie rozszerzenia diagnostyki spowoduje automatyczne użycie konfiguracji domyślnej z najbardziej typowymi licznikami wydajności i zdarzeniami. Tę konfigurację domyślną można zmodyfikować zgodnie z określonymi wymaganiami.

> [!NOTE]
> Istnieją ustawienia rozszerzenia diagnostyki, których nie można skonfigurować przy użyciu Azure Portal, w tym wysyłania danych do usługi Azure Event Hubs. Należy użyć jednej z innych metod konfiguracji dla tych ustawień.

1. Otwórz menu dla maszyny wirtualnej w Azure Portal.
2. Kliknij pozycję **Ustawienia diagnostyczne** w sekcji **monitorowanie** w menu maszyny wirtualnej.
3. Kliknij pozycję **Włącz monitorowanie na poziomie gościa** , jeśli rozszerzenie diagnostyki nie zostało już włączone.
4. Nowe konto usługi Azure Storage zostanie utworzone dla maszyny wirtualnej o nazwie na podstawie nazwy grupy zasobów maszyny wirtualnej. Możesz dołączyć maszynę wirtualną do innego konta magazynu, wybierając kartę **Agent** .

![Ustawienia diagnostyczne](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Konfigurację domyślną można zmodyfikować po włączeniu rozszerzenia diagnostyki. W poniższej tabeli opisano opcje, które można zmodyfikować na różnych kartach. Niektóre opcje mają polecenie **niestandardowe** , które pozwala na określenie bardziej szczegółowej konfiguracji; Aby uzyskać szczegółowe informacje na temat różnych ustawień, zobacz [schemat rozszerzenia diagnostyki systemu Windows](diagnostics-extension-schema-windows.md) .

| Tab | Opis |
|:---|:---|
| Omówienie | Wyświetla bieżącą konfigurację z łączami do innych kart. |
| Liczniki wydajności | Wybierz liczniki wydajności do zebrania oraz częstotliwość próbkowania dla każdej z nich.  |
| Dzienniki | Wybierz dane dziennika do zebrania. Dotyczy to dzienników zdarzeń systemu Windows, dzienników usług IIS, dzienników aplikacji .NET i zdarzeń ETW.  |
| Zrzuty awaryjne | Włącz zrzut awaryjny dla różnych procesów. |
| Ujścia | Umożliwianie ujściam danych wysyłanie danych do miejsc docelowych oprócz usługi Azure Storage.<br>Azure Monitor — wysyła dane dotyczące wydajności do metryk Azure Monitor.<br>Application Insights — wysyłanie danych do aplikacji Application Insights. |
| Agent | Zmodyfikuj następującą konfigurację agenta:<br>— Zmień konto magazynu.<br>-Określ maksymalny dysk lokalny używany przez agenta.<br>— Skonfiguruj dzienniki kondycji samego agenta.|


> [!NOTE]
> Konfiguracja rozszerzenia diagnostyki można sformatować w formacie JSON lub XML, a każda konfiguracja wykonywana w Azure Portal będzie zawsze przechowywana jako plik JSON. Jeśli używasz XML z inną metodą konfiguracji, a następnie zmienisz konfigurację przy użyciu Azure Portal, ustawienia zostaną zmienione na format JSON.

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Zobacz [Używanie monitorowania i diagnostyki z maszyną wirtualną z systemem Windows i Azure Resource Manager szablonów](../../virtual-machines/extensions/diagnostics-template.md) na temat wdrażania rozszerzenia diagnostyki z szablonami Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure
Interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia Diagnostyka Azure istniejącej maszyny wirtualnej przy użyciu polecenia [AZ VM Extension Set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) jak w poniższym przykładzie. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Ustawienia chronione są definiowane w [elemencie PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) schematu konfiguracji. Poniżej znajduje się minimalny przykład pliku ustawień chronionych, który definiuje konto magazynu. Zobacz [przykładową konfigurację](diagnostics-extension-schema-windows.md#privateconfig-element) , aby uzyskać szczegółowe informacje na temat ustawień prywatnych.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Ustawienia publiczne są definiowane w [elemencie publicznym](diagnostics-extension-schema-windows.md#publicconfig-element) schematu konfiguracji. Poniżej znajduje się minimalny przykład pliku ustawień publicznych, który umożliwia zbieranie dzienników infrastruktury diagnostycznej, pojedynczy licznik wydajności i pojedynczy dziennik zdarzeń. Zobacz [przykładową konfigurację](diagnostics-extension-schema-windows.md#publicconfig-element) , aby uzyskać szczegółowe informacje o ustawieniach publicznych.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Wdrażanie programu PowerShell
Programu PowerShell można użyć do wdrożenia rozszerzenia Diagnostyka Azure do istniejącej maszyny wirtualnej przy użyciu polecenia [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) , jak w poniższym przykładzie. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Ustawienia prywatne są definiowane w [elemencie PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), podczas gdy ustawienia publiczne są zdefiniowane w [elemencie publicznym](diagnostics-extension-schema-windows.md#publicconfig-element) schematu konfiguracji. Możesz również określić szczegóły konta magazynu jako parametry polecenia cmdlet Set-AzVMDiagnosticsExtension zamiast uwzględniać je w ustawieniach prywatnych.

Poniżej znajduje się minimalny przykład pliku konfiguracji, który umożliwia zbieranie dzienników infrastruktury diagnostycznej, pojedynczy licznik wydajności i pojedynczy dziennik zdarzeń. Zobacz [przykładową konfigurację](diagnostics-extension-schema-windows.md#publicconfig-element) , aby uzyskać szczegółowe informacje dotyczące ustawień prywatnych i publicznych. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

[Aby włączyć Diagnostyka Azure w maszynie wirtualnej z systemem Windows, zobacz również korzystanie z programu PowerShell](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Magazyn danych
W poniższej tabeli wymieniono różne typy danych zbieranych z rozszerzenia diagnostyki oraz to, czy są one przechowywane jako tabela czy obiekt BLOB. Dane przechowywane w tabelach mogą być również przechowywane w obiektach Blob w zależności od [Ustawienia StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) w konfiguracji publicznej.


| Dane | Typ magazynu | Opis |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | Monitor diagnostyczny i zmiany konfiguracji. |
| WADDirectoriesTable | Tabela | Katalogi monitorowane przez Monitor diagnostyczny.  Dotyczy to również dzienników usług IIS, dzienników żądań zakończonych niepowodzeniem i katalogów niestandardowych.  Lokalizacja pliku dziennika obiektów BLOB jest określona w polu kontenera, a nazwa obiektu BLOB znajduje się w polu RelativePath.  Pole AbsolutePath wskazuje lokalizację i nazwę pliku, która istniała na maszynie wirtualnej platformy Azure. |
| WadLogsTable | Tabela | Dzienniki zapisane w kodzie przy użyciu odbiornika śledzenia. |
| WADPerformanceCountersTable | Tabela | Liczniki wydajności. |
| WADWindowsEventLogsTable | Tabela | Dzienniki zdarzeń systemu Windows. |
| funkcji wad-IIS-failedreqlogfiles | Obiekt blob | Zawiera informacje z dzienników żądań nieudanych usług IIS. |
| funkcji wad-IIS-LogFiles | Obiekt blob | Zawiera informacje o dziennikach usług IIS. |
| celnej | Obiekt blob | Niestandardowy kontener oparty na konfigurowaniu katalogów monitorowanych przez Monitor diagnostyczny.  Nazwa tego kontenera obiektów BLOB zostanie określona w WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Narzędzia do wyświetlania danych diagnostycznych
Dostępnych jest kilka narzędzi do wyświetlania danych po ich przeniesieniu do magazynu. Na przykład:

* Eksplorator serwera w programie Visual Studio — Jeśli zainstalowano narzędzia platformy Azure dla Microsoft Visual Studio, można użyć węzła usługi Azure Storage w Eksplorator serwera, aby wyświetlić dane obiektów BLOB tylko do odczytu i tabele z kont usługi Azure Storage. Możesz wyświetlić dane z lokalnego konta emulatora magazynu, a także z kont magazynu utworzonych dla platformy Azure. Aby uzyskać więcej informacji, zobacz [przeglądanie zasobów magazynu i zarządzanie nimi za pomocą Eksplorator serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Eksplorator usługi Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) jest aplikacją autonomiczną, która umożliwia łatwe współdziałanie z danymi usługi Azure Storage w systemach Windows, OSX i Linux.
* [Usługa Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) obejmuje Menedżera Diagnostyka Azure, który umożliwia wyświetlanie i pobieranie danych diagnostycznych zebranych przez aplikacje działające na platformie Azure oraz zarządzanie nimi.

## <a name="next-steps"></a>Następne kroki
- Zobacz [wysyłanie danych z rozszerzenia Diagnostyka systemu Windows Azure, Event Hubs aby](diagnostics-extension-stream-event-hubs.md) uzyskać szczegółowe informacje na temat przekazywania danych monitorowania do usługi Azure Event Hubs.
