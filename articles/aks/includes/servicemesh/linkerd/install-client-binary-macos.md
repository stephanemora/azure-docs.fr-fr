---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: a4090172e926f21db01a8e374d8c4bb1c80402c7
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74197359"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Télécharger et installer le fichier binaire client Linkerd

Dans l’interpréteur de commandes bash sur MacOS, utilisez `curl` pour télécharger la version de Linkerd, comme suit :

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Le binaire client `linkerd` s’exécute sur votre machine cliente et vous permet d’interagir avec le maillage de services Linkerd. Utilisez les commandes suivantes pour installer le binaire client Linkerd `linkerd` dans un interpréteur de commandes bash sur MacOS. Ces commandes copient le binaire client `linkerd` à l’emplacement du programme utilisateur standard dans votre `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Si vous souhaitez utiliser la complétion de ligne de commande pour le binaire client Linkerd `linkerd`, configurez-la comme suit :

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
