---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1729eabca75ed7c7a3a43ea2c0b1617efd337f2c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530110"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Pobieranie i Instalowanie konsolidatora dwuskładnikowego klienta konsolidatora

W przypadku powłoki opartej na bash w systemie Linux lub [Windows dla systemu Linux][install-wsl]Użyj `curl`, aby pobrać program z konsolidatorem w następujący sposób:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

@No__t_0 dane binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z konsolidatorem łączącej usługi. Użyj następujących poleceń, aby zainstalować `linkerd` dane binarne klienta w ramach powłoki opartej na bash w systemie Linux lub [Windows dla systemu Linux][install-wsl]. Te polecenia kopiują plik binarny klienta `linkerd` do lokalizacji standardowego programu użytkownika w `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Jeśli chcesz, aby uzupełniał wiersz polecenia dla konsolidatora `linkerd` dane binarne klienta, skonfiguruj go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10