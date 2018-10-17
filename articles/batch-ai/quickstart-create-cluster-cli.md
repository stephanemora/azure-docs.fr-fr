---
title: Démarrage rapide Azure - Créer un cluster Batch AI - Azure CLI | Microsoft Docs
description: Démarrage rapide - Créer un cluster Batch AI pour l’apprentissage des modèles d’intelligence artificielle et Machine Learning - Interface de ligne de commande Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ms.openlocfilehash: 0d4ba7edfb22a6710222c854ceb2bf86284d2d77
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057590"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Démarrage rapide : créer un cluster pour les travaux de formation Batch AI à l’aide de l’interface de ligne de commande Azure

Ce démarrage rapide montre comment utiliser l’interface de ligne de commande Azure pour créer un cluster Batch AI que vous pouvez utiliser pour l’apprentissage des modèles d’intelligence artificielle et Machine Learning. Batch AI est un service managé qui permet aux scientifiques des données et aux chercheurs en IA d’effectuer l’apprentissage des modèles d’IA et de Machine Learning à l’échelle sur des clusters de machines virtuelles Azure.

Au départ, le cluster comporte un seul nœud GPU. À l’issue de ce démarrage rapide, vous disposerez d’un cluster capable de monter en puissance que vous pourrez utiliser pour effectuer l’apprentissage de vos modèles. Envoyez des travaux de formation au cluster à l’aide de Batch AI, des outils [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ou de [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite que vous exécutiez la version 2.0.38 minimum d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

Ce démarrage rapide suppose l’exécution des commandes dans un interpréteur de commandes Bash, dans Cloud Shell ou sur votre ordinateur local.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande `az group create`. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus2*. Veillez à choisir un emplacement comme Est des États-Unis 2, dans lequel le service Batch AI est disponible.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Créer un cluster Batch AI

Tout d’abord, utilisez la commande `az batchai workspace create` pour créer un *espace de travail* Batch AI. Vous avez besoin d’un espace de travail pour organiser vos clusters Batch AI et autres ressources.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup 
```

Pour créer un cluster Batch AI, utilisez la commande `az batchai cluster create`. L’exemple suivant crée un cluster avec les propriétés suivantes :

* Contient un seul nœud dans la machine virtuelle NC6 comportant un GPU NVIDIA Tesla K80. 
* Exécute une image de serveur Ubuntu par défaut conçue pour héberger les applications basées sur conteneur, que vous pouvez utiliser pour la plupart des charges de travail de formation. 
* Ajoute un compte d’utilisateur nommé *myusername* et génère des clés SSH si elles n’existent pas déjà dans l’emplacement de clé par défaut (*~/.ssh*) de votre environnement local. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

La sortie de la commande affiche les propriétés du cluster. La création et le démarrage du nœud prennent quelques minutes. Pour afficher l’état du cluster, exécutez la commande `az batchai cluster show`. 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

Au début de la création du cluster, la sortie est similaire à ce qui suit, montrant le cluster à l’état `resizing` :

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
Le cluster est prêt à l’emploi lorsque l’état indique `steady` et que le seul nœud est `Idle`.

## <a name="list-cluster-nodes"></a>Répertorier les nœuds du cluster 

Si vous avez besoin de vous connecter aux nœuds du cluster (dans ce cas, un seul nœud) pour installer des applications ou effectuer la maintenance, obtenez les informations de connexion en exécutant la commande `az batchai cluster node list` :


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

Le résultat JSON ressemble à ce qui suit :

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Ces informations permettent d’établir une connexion SSH au nœud. Par exemple, entrez l’adresse IP correcte de votre nœud dans la commande suivante :

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
Quittez la session SSH pour continuer.

## <a name="resize-the-cluster"></a>Redimensionner le cluster

Lorsque vous utilisez votre cluster pour effectuer l’apprentissage d’un travail, vous aurez peut-être besoin de ressources de calcul supplémentaires. Par exemple, pour augmenter la taille à 2 nœuds pour un travail d’apprentissage distribué, exécutez la commande [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) :

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

Le redimensionnement du cluster nécessite quelques minutes.

## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous souhaitez poursuivre les exemples et tutoriels Batch AI, utilisez l’espace de travail Batch AI créé dans ce démarrage rapide. 

Vous êtes facturé pour le cluster Batch AI lorsque les nœuds sont en cours d’exécution. Si vous souhaitez conserver la configuration du cluster lorsque vous ne disposez d’aucun travail à exécuter, redimensionnez le cluster sur 0 nœud. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Ensuite, redimensionnez-le sur 1 ou plusieurs nœuds pour exécuter vos travaux. Lorsque vous n’avez plus besoin d’un cluster, supprimez-le avec la commande `az batchai cluster delete` :

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
```

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande `az group delete` afin de supprimer le groupe de ressources pour les ressources Batch AI. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris comment créer un cluster Batch AI à l’aide d’Azure CLI. Pour en savoir plus sur l’utilisation d’un cluster Batch AI pour effectuer l’apprentissage d’un modèle, poursuivez le démarrage rapide pour effectuer l’apprentissage d’un modèle d’apprentissage profond.

> [!div class="nextstepaction"]
> [Effectuer l’apprentissage d’un modèle d’apprentissage profond](./quickstart-tensorflow-training-cli.md)
