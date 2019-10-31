---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8b1a4a8a6c808348ca17a9eebac17c5821ceefe5
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529733"
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
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
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