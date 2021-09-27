---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2021
ms.author: glenga
ms.openlocfilehash: 162cfe73e57c314ed1b8200d5ecacebc92cfc517
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669705"
---
Les étapes suivantes utilisent [APT](https://wiki.debian.org/Apt) pour installer les Outils Core sur votre distribution Ubuntu/Debian Linux. Pour les autres distributions Linux, consultez le [fichier Lisez-moi Outils Core](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installez la clé GPG du référentiel de packages Microsoft pour valider l’intégrité du package :

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Configurez la liste de sources APT avant d’effectuer une mise à jour d’APT.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Vérifiez la présence de l’une des chaînes de version Linux appropriées ci-dessous dans le fichier `/etc/apt/sources.list.d/dotnetdev.list` :

    | Distribution Linux | Version |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Démarrez la mise à jour des sources APT :

    ```bash
    sudo apt-get update
    ```