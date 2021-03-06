---
title: Głębokie uczenie & platformy AI
titleSuffix: Azure Data Science Virtual Machine
description: Dostępne platformy i narzędzia uczenia głębokiego na platformie Azure Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 3dfb2c201138a65379aa509ce1bf10894ab6819b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984714"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Głębokie uczenie i platformy AI dla Data Science VM platformy Azure
Poniżej wymieniono platformy uczenia głębokiego na DSVM.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Caffe jest zainstalowany w `/opt/caffe`.   Przykłady znajdują się w `/opt/caffe/examples`.|
| Jak uruchomić go      | Użyj X2Go, aby zalogować się do maszyny wirtualnej, a następnie uruchomić nowy terminal, a następnie wprowadź następujące polecenie:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Otwiera nowe okno przeglądarki z notesami próbki. Pliki binarne są instalowane w /opt/caffe/build/install/bin.<br/><br/>Zainstalowana wersja programu Caffe wymaga języka Python 2,7 i nie będzie współdziałać z językiem Python 3,5, który jest domyślnie aktywowany. Aby przełączyć się do języka Python 2,7, uruchom `source activate root`, aby przełączyć się do środowiska Anaconda.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Caffe2 jest zainstalowany w środowisku [Python 2,7 (root) Conda. |
| Jak uruchomić go      | Terminal: uruchamianie języka Python i importowanie Caffe2. <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu Caffe2, aby znaleźć przykładowe notesy. Niektóre notesów wymagają głównego Caffe2 w kod Python; Wprowadź /opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 5.2 |
| Obsługiwane wersje DSVM      | Linux (Ubuntu)     |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Moduł łańcucha jest instalowany w języku Python 3,5. |
| Jak uruchomić go      | Terminal: Aktywuj środowisko Python 3,5, uruchom `python`, a następnie `import chainer`. <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu łańcucha, aby znaleźć przykładowe notesy.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, sterownik NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 10.0.130|
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  |_procesory GPU NVIDIA smi_ jest dostępny w ścieżce systemowej.  |
| Jak uruchomić go      | Otwórz wiersz polecenia (w systemie Windows) lub terminal (na komputerze z systemem Linux), a następnie uruchom polecenie _NVIDIA-SMI_. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 0.16.1|
| Obsługiwane wersje DSVM      | Linux (Ubuntu)   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Horovod jest zainstalowany w języku Python 3,5 |
| Jak uruchomić go      | Aktywuj poprawne środowisko w terminalu, a następnie uruchom Język Python. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 2.2.4 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Keras jest zainstalowany w języku Python 3,6 w systemie Windows i w języku Python 3,5 w Linux |
| Jak uruchomić go      | Aktywuj poprawne środowisko w terminalu, a następnie uruchom Język Python. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 2.5.1 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | CNTK jest zainstalowany w języku Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i w języku Python 3,5 w systemie [Linux](./dsvm-tools-languages.md#python-linux-edition) |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko i uruchom Język Python. <br/>Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog CNTK dla przykładów. |

## <a name="mxnethttpsmxnetapacheorg"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.3.0 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | MXNet jest instalowany w `C:\dsvm\tools\mxnet` w systemie Windows i `/dsvm/tools/mxnet` na Ubuntu. Powiązania języka Python są instalowane w języku Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i w języku Python 3,5 on [Linux](./dsvm-tools-languages.md#python-linux-edition)) powiązania R są również zawarte w Ubuntu DSVM. |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko Conda, a następnie uruchom `import mxnet`. <br/>Jupyter: Połącz się z [Jupyter](provision-vm.md#access-the-dsvm) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog `mxnet` dla przykładów. |

## <a name="mxnet-model-serverhttpsgithubcomawslabsmxnet-model-serverquick-start"></a>[Serwer modelu MXNet](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.0.1 |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Serwer modelu MXNet jest zainstalowany w języku Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i w języku Python 3,5 w systemie [Linux](./dsvm-tools-languages.md#python-linux-edition). |
| Jak uruchomić go      | Terminal: Uruchom `sudo systemctl stop jupyterhub`, aby najpierw zatrzymać usługę JupyterHub, ponieważ obie nasłuchują na tym samym porcie. Następnie aktywuj poprawne środowisko Conda i uruchom `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smihttpsdevelopernvidiacomnvidia-system-management-interface"></a>[Interfejs zarządzania systemem NVidia (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje |  |
| Obsługiwane wersje DSVM      | System Windows i Linux   |
| Co to jest? | Procesory GPU NVIDIA narzędzie do wykonywania zapytań aktywność procesora GPU |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | `nvidia-smi` znajduje się na ścieżce systemowej. |
| Jak uruchomić go      | Na maszynie wirtualnej **z procesorem GPU**Otwórz wiersz polecenia (w systemie Windows) lub terminal (na komputerze z systemem Linux), a następnie uruchom `nvidia-smi`. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.2.0 (Ubuntu 16,04, Windows 2016, Windows 2019), 1.4.0 (Ubuntu 18,04) |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Zainstalowane w języku [Python 3,5](dsvm-tools-languages.md#python-linux-edition). Przykładowe notesy Jupyter są dołączone, a przykłady znajdują się w/dsvm/Samples/pytorch. |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Connect, a następnie otwórz katalog PyTorch dla przykładów.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1,13 |
| Obsługiwane wersje DSVM      | Windows, Linux |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | Zainstalowane w języku Python 3,5 w systemach [Linux](dsvm-tools-languages.md#python-linux-edition) i Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> * Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog TensorFlow dla przykładów.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow obsługujące](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1,12 |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  | tensorflow_model_server jest dostępny w terminalu. |
| Jak uruchomić go      |  Przykłady są dostępne [online](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje | 1.0.3 |
| Obsługiwane wersje DSVM      | Linux |
| Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM?  |Theano jest zainstalowany w języku Python 2,7 (_root_) i w środowisku Python 3,5 (_py35_). |
| Jak uruchomić go      |  Terminal: Aktywuj żądaną wersję języka Python (root lub py35), uruchom środowisko Python, a następnie zaimportuj Theano.<br/>* Jupyter: Wybierz jądro Python 2,7 lub 3,5, a następnie zaimportuj Theano.  <br/>Aby obejść ostatnią usterkę biblioteki jądra matematycznego (MKL), musisz najpierw ustawić warstwę wątku MKL w następujący sposób:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |