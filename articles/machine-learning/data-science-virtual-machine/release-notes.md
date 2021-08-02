---
title: Nouveautés relatives à Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Notes de publication pour Azure Data Science Virtual Machine
author: timoklimmer
ms.service: data-science-vm
ms.author: tklimmer
ms.date: 05/25/2021
ms.topic: reference
ms.openlocfilehash: a3bb8f3c7174fe301a8c81396bb46b6762e70796
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111409844"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Notes de publication Azure Data Science Virtual Machine

Dans cet article, découvrez les versions d’Azure Data Science Virtual Machine. Pour obtenir la liste complète des outils inclus, ainsi que les numéros de version, consultez [cette page](./tools-included.md).

Consultez la [liste des problèmes connus](reference-known-issues.md) pour en savoir plus sur les bogues connus et les solutions de contournement.

## <a name="2021-06-01"></a>2021-06-01

Nouvelle image pour [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Version : 21.06.01

Les principaux changements sont les suivants :

- Docker est activé par défaut
- JupyterHub utilise JupyterLab par défaut
- Mise à jour des versions de Python pour corriger [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523)
- Mise à jour d’IntelliJ IDEA à la version 2021.1 pour corriger [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758)
- Mise à jour de PyCharm Community à la version 2021.1
- Mise à jour de TensorFlow à la version 2.5.0

<br/>
Suppression de plusieurs icônes du bureau

## <a name="2021-05-22"></a>2021-05-22

Nouvelle image pour [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Les mises à jour de version sélectionnées sont les suivantes :

Version : 21.05.22

Les mises à jour de version sélectionnées sont les suivantes :
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Visual Studio Code 1.56.2, y compris l’extension Azure ML
- PyCharm Community édition 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Studio Community édition 2019 (version 16.9.6)
- Azure CLI 2.23.0
- Explorateur Stockage 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 64 bits (mai 2021)
- Azure Data Studio 1.28.0
- Navigateur Microsoft Edge

<br/>
Suppression de Firefox, d’Apache Drill et de Microsoft Integration Runtime.

<br/>
Mode sombre, modification des icônes sur le bureau, modification du papier peint en arrière-plan.

<br/>
Activation de l’image à utiliser dans une machine virtuelle Gen2.

## <a name="2021-05-12"></a>2021-05-12

Nouvelle image pour [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Les mises à jour de version sélectionnées sont les suivantes :
- CUDA 11.3, cuDNN 8, NCCL2
- Python 3.8
- R 4.0.5
- Spark 3.1, y compris mmlspark et les connecteurs vers Stockage Blob, Data Lake et CosmosDB
- Java 11 (OpenJDK)
- Jupyter Lab 3.0.14
- PyTorch 1.8.1, y compris torchaudio torchtext torchvision, torch-tb-profiler
- TensorFlow 2.4.1, y compris TensorBoard
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- Azure CLI 2.23.0
- Explorateur Stockage Azure 1.19.1
- azcopy 10.10
- Navigateur Microsoft Edge (bêta)

<br/>
Ajout de Docker. Pour économiser des ressources, le service Docker n’est pas démarré par défaut. Pour démarrer le service Docker, exécutez les commandes de ligne de commande suivantes :

```
sudo systemctl start docker
```

> [!NOTE]
> Si votre machine est équipée de GPU, vous pouvez les utiliser à l’intérieur des conteneurs en ajoutant un paramètre `--gpus` à votre commande Docker.
>
> Par exemple, l’exécution de 
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> exécutera un conteneur Ubuntu 18.04 avec PyTorch préinstallé et tous les GPU activés. Elle rendra également un dossier local *local_dir* disponible dans le conteneur sous *container_dir*.
>


## <a name="2020-02-24"></a>24/02/2020

Images Data Science Virtual Machine désormais disponibles pour les images [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) et [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

## <a name="2019-10-08"></a>2019-10-08

### <a name="updates-to-software-on-the-windows-dsvm"></a>Mises à jour des logiciels sur la machine virtuelle DSVM Windows

- Explorateur Stockage Azure 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Mise à jour du navigateur par défaut pour Windows

Précédemment, le navigateur par défaut était défini sur Internet Explorer. Les utilisateurs sont désormais invités à choisir un navigateur par défaut lorsqu’ils se connectent pour la première fois.
