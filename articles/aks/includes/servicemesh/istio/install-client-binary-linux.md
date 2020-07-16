---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 34a393e06d91e5f60e622667a47e833e910e7228
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244160"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Télécharger et installer le binaire client istioctl d’Istio

Dans un interpréteur de commandes bash sur Linux ou sur [Sous-système Windows pour Linux][install-wsl], utilisez `curl` pour télécharger la version d’Istio, puis extrayez-la avec `tar`, de la manière suivante :

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Le binaire client `istioctl` s’exécute sur votre machine cliente et vous permet d’interagir avec le maillage de services Istio. Utilisez les commandes suivantes pour installer le binaire client Istio `istioctl` dans un interpréteur de commandes bash sur Linux ou le [sous-système Windows pour Linux][install-wsl]. Ces commandes copient le binaire client `istioctl` à l’emplacement du programme utilisateur standard dans votre `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Si vous souhaitez utiliser la complétion de ligne de commande pour le binaire client `istioctl` Istio, configurez-la comme suit :

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
