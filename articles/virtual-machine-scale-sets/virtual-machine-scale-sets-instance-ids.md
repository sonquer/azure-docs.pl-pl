---
title: Opis identyfikatorów wystąpień maszyn wirtualnych zestawu skalowania maszyn wirtualnych platformy Azure
description: Informacje o identyfikatorach wystąpień dla maszyn wirtualnych usługi Azure VM Scale Sets i różne sposoby ich powierzchni.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: aa2b0013818f897f01945d394266a57016ecb0bb
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275850"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Opis identyfikatorów wystąpień maszyn wirtualnych zestawu skalowania maszyn wirtualnych platformy Azure
W tym artykule opisano identyfikatory wystąpień dla zestawów skalowania i różne sposoby ich powierzchni.

## <a name="scale-set-instance-ids"></a>Identyfikatory wystąpień zestawu skalowania

Każda maszyna wirtualna w zestawie skalowania Pobiera identyfikator wystąpienia, który jednoznacznie identyfikuje go. Ten identyfikator wystąpienia jest używany w interfejsach API zestawu skalowania do wykonywania operacji na określonej maszynie wirtualnej w zestawie skalowania. Na przykład można określić identyfikator określonego wystąpienia do odobrazowania podczas korzystania z interfejsu API odobrazu:

Interfejs API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)).

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Aby uzyskać więcej informacji, zobacz [dokumentację programu PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)).

Interfejs wiersza polecenia: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Listę identyfikatorów wystąpień można pobrać, wyświetlając listę wszystkich wystąpień w zestawie skalowania:

Interfejs API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list)).

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Aby uzyskać więcej informacji, zobacz [dokumentację programu PowerShell](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm)).

Interfejs wiersza polecenia: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Możesz również użyć [resources.Azure.com](https://resources.azure.com) lub [zestawów Azure SDK](https://azure.microsoft.com/downloads/) , aby wyświetlić listę maszyn wirtualnych w zestawie skalowania.

Dokładna prezentacja danych wyjściowych zależy od opcji podanych w poleceniu, ale Oto kilka przykładowych danych wyjściowych z interfejsu wiersza polecenia:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Jak widać, właściwość "instanceId" ma tylko liczbę dziesiętną. Identyfikatory wystąpień mogą być ponownie używane dla nowych wystąpień po usunięciu starych wystąpień.

>[!NOTE]
> Nie ma **żadnej gwarancji** dotyczącej sposobu, w jaki identyfikatory wystąpień są przypisane do maszyn wirtualnych w zestawie skalowania. Mogą wydawać się sekwencyjnie zwiększać, ale nie zawsze jest to przypadek. Nie należy podejmować zależności od konkretnego sposobu, w jaki identyfikatory wystąpień są przypisane do maszyn wirtualnych.

## <a name="scale-set-vm-names"></a>Nazwy maszyn wirtualnych zestawu skalowania

W przykładowych danych wyjściowych powyżej istnieje również "nazwa" dla maszyny wirtualnej. Ta nazwa przyjmuje postać "{Scale-Set-Name} _ {instance-ID}". Ta nazwa jest wyświetlana w Azure Portal podczas wyświetlania listy wystąpień w zestawie skalowania:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Część nazwy {instance-ID} jest tą samą liczbą dziesiętną, która została omówiona wcześniej przez właściwość "instanceId".

## <a name="instance-metadata-vm-name"></a>Nazwa maszyny wirtualnej metadanych wystąpienia

W przypadku wykonywania zapytań dotyczących [metadanych wystąpienia](../virtual-machines/windows/instance-metadata-service.md) z poziomu maszyny wirtualnej zestawu skalowania zobaczysz w danych wyjściowych ciąg "name":

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Ta nazwa jest taka sama jak nazwa omówiona wcześniej.

## <a name="scale-set-vm-computer-name"></a>Nazwa komputera maszyny wirtualnej zestawu skalowania

Każda maszyna wirtualna w zestawie skalowania również pobiera przypisaną do niej nazwę komputera. Ta nazwa komputera jest nazwą hosta maszyny wirtualnej w [ramach rozpoznawania nazw DNS udostępnianej przez platformę Azure w sieci wirtualnej](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Ta nazwa komputera ma postać "{Computer-name-prefix} {Base-36-instance-ID}".

{Base-36-instance-ID} ma wartość [base 36](https://en.wikipedia.org/wiki/Base36) i ma zawsze sześć cyfr. Jeśli reprezentacja podstawowa 36 liczby ma mniej niż sześć cyfr, wartość {Base-36-instance-ID} jest uzupełniona zerami, aby zapewnić jej sześć cyfr. Na przykład wystąpienie z {Computer-name-prefix} "nsgvmss" i IDENTYFIKATORem wystąpienia 85 będzie mieć nazwę komputera "nsgvmss00002D".

>[!NOTE]
> Prefiks nazwy komputera jest właściwością modelu zestawu skalowania, który można ustawić, aby można go było różnić od samej nazwy zestawu skalowania.
