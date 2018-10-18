---
title: Travailler avec des clusters Azure Batch AI | Microsoft Docs
description: Comment choisir une configuration de cluster Batch AI, puis créer et gérer un cluster Batch AI
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61294d8b6b84b03b1e0c8d79b4d2855452c7f0e6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44056823"
---
# <a name="work-with-batch-ai-clusters"></a>Travailler avec des clusters Batch AI 

Cet article explique comment travailler avec des clusters dans Azure Batch AI. Il présente le concept de cluster, les types de configurations possibles et des exemples. La plupart des exemples de création et de gestion de cluster présentés dans cet article utilisent Azure CLI. Vous pouvez cependant vous servir d’autres outils, tels que le portail Azure et les kits de développement logiciel (SDK) Azure Batch AI, pour travailler avec des clusters.

Un cluster Batch AI est une ressource parmi d’autres dans le service. Pour comprendre l’étendue des clusters dans le service, voir la [vue d’ensemble des ressources Batch AI](resource-concepts.md).

## <a name="introduction-to-clusters"></a>Présentation des clusters

Un cluster dans Batch AI contient les ressources de calcul pour l’exécution de travaux d’apprentissage automatique (Machine Learning) et de formation d’intelligence artificielle. Tous les nœuds dans un cluster ont une taille de machine virtuelle et une image de système d’exploitation identiques. Batch AI offre plusieurs options pour la création de clusters adaptés à différents besoins. En règle générale, vous configurez un cluster distinct pour chaque catégorie de puissance de traitement nécessaire pour accomplir un projet. Vous pouvez mettre à l’échelle le nombre de nœuds en fonction de la demande et du budget. Des clusters peuvent être configurés et partagés pour une équipe, ou des utilisateurs individuels peuvent configurer leur propre cluster. 

Chaque cluster existe sous une ressource Batch AI appelée *espace de travail*. Avant d’approvisionner un cluster, vous devez disposer d’un espace de travail Batch AI configuré. Par exemple, si vous utilisez Azure CLI, servez-vous de la commande [az batchai workspace create](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) pour configurer un espace de travail. 

Après avoir créé un cluster, vous pouvez envoyer des travaux un par un à une file d’attente. Batch AI gère ensuite le processus d’allocation des ressources pour l’exécution des travaux sur le cluster. 

## <a name="cluster-configuration-options"></a>Options de configuration de cluster

Lorsque vous planifiez un cluster, commencez par déterminer vos besoins en calcul. Batch AI offrant des options de configuration flexibles, vous pouvez adapter un cluster à vos besoins. Les principales options à prendre en considération lors de l’approvisionnement d’un cluster sont les suivantes :

* **Taille de machine virtuelle** : choisissez une [taille de machine virtuelle](../virtual-machines/linux/sizes.md) disponible dans une [région prise en charge](https://azure.microsoft.com/global-infrastructure/services/) pour les nœuds de cluster. Chaque cluster ne pouvant contenir qu’une seule taille de machine virtuelle, si vos tâches requièrent plusieurs types de machines virtuelles, vous devez approvisionner un cluster distinct pour chaque type de besoin en calcul. Pour former des modèles de Machine Learning ou d’intelligence artificielle développés avec des infrastructures qui tirent parti de processeurs graphiques (GPU), voir les [tailles de machines virtuelles optimisées pour GPU](../virtual-machines/linux/sizes-gpu.md) dans Azure.
  
* **Priorité de machine virtuelle** : Batch AI offre des machines virtuelles dédiées ou basse priorité pour un cluster. Les machines virtuelles dédiées sont réservées à votre usage dans le cluster. L’option basse priorité alloue une capacité inutilisée de machine virtuelle Azure pour un coût sensiblement réduit en échange de la possibilité d’annuler des travaux si Azure doit récupérer les machines virtuelles. Les travaux dont l’exécution dure plus de 24 heures sur une machine virtuelle basse priorité sont également automatiquement annulés. Si le budget est une considération importante, envisagez d’utiliser des machines virtuelles basse priorité pour effectuer des travaux d’expérimentation de courte durée. Ensuite, lorsque le moment est venu d’exécuter des travaux plus longs, passez à des machines virtuelles dédiées.

* **Nombre de nœuds** : Batch AI offre des options de mise à l’échelle manuelle et automatique du nombre de nœuds dans le cluster. Avec l’option de mise à l’échelle manuelle, vous décidez quand augmenter ou réduire l’échelle d’un cluster. Vous gérez donc vos coûts de calcul. Avec l’option de mise à l’échelle automatique, l’échelle du cluster est toujours réduire lorsque vous l’utilisez pas. Vous minimisez donc vos coûts de calcul. 

  Si vous choisissez l’option de mise à l’échelle manuelle, vous définissez la valeur cible initiale lors de la création du cluster. La cible est le nombre de nœuds que Batch AI alloue initialement. Ensuite, vous pouvez redimensionner manuellement le nombre de nœuds.  

  Si vous choisissez l’option de mise à l’échelle automatique, vous définissez les nombres minimal et maximal de nœuds cibles lors de la création du cluster. La valeur cible peut aller de 0 au nombre maximal de nœuds pris en charge par votre [quota de cœurs Batch AI](quota-limits.md). La valeur cible 0 vous permet de conserver votre configuration de cluster sans que des coûts de calcul vous soient facturés. Si vous demandez une valeur cible supérieure à ce que votre limite de quota prend en charge, l’approvisionnement échoue. 

* **Image de machine virtuelle** : Batch AI approvisionne par défaut les machines virtuelles du cluster avec une image de Serveur Ubuntu par défaut, qui prend en charge les charges de travail de conteneur. Vous pouvez choisir une autre image Linux préconfigurée sur la Place de marché Microsoft Azure, par exemple [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md). 

* **Stockage** : Batch AI fournit une option de stockage automatique que vous pouvez choisir lorsque vous créez un cluster à l’aide d’Azure CLI. Cette option crée automatiquement un partage de fichiers et un conteneur d’objets blob Azure sous un nouveau compte de stockage. Ces ressources de stockage sont montées sur chaque nœud du cluster pendant l’exécution, ce qui rend les fichiers accessibles à partir d’un chemin d’accès local. Les noms des comptes de stockage, du partage de fichiers et du conteneur d’objets blob, ainsi que les chemins d’accès de montage ont tous des valeurs par défaut que vous pouvez personnaliser à l’aide de paramètres supplémentaires lors de la création du cluster. 

  Si aucune option de stockage n’est définie, vous devez créer les ressources de stockage séparément et les définir lors de l’envoi de travaux. Cette option est utile si votre cluster est managé au niveau de l’organisation, tandis que votre stockage est managé au niveau de l’utilisateur. Pour plus d’informations sur la façon de charger des fichiers sur Stockage Azure et y accéder en cours d’exécution, voir [Stocker les entrées et sorties de travaux Batch AI avec Stockage Azure](use-azure-storage.md).

* **Accès utilisateur** : Batch AI vous permet de générer des fichiers de clés SSH publiques et privées lors de la création d’un cluster, ou de fournir vos propres clés SSH de façon à pouvoir utiliser le protocole SSH dans des nœuds individuels. Vous pouvez également définir un nom d’utilisateur (par défaut, le nom est celui de l’utilisateur actuel) et un mot de passe. Ces informations d’identification permettent accéder aux nœuds lors de l’exécution afin d’afficher diverses métriques ou d’obtenir davantage d’informations sur vos travaux.

* **Tâche d’installation** : Batch AI vous permet de définir des arguments de ligne de commande à exécuter sur chaque nœud lors de l’allocation. Vous pouvez également définir le chemin d’accès de l’emplacement dans lequel le fichier de sortie doit être enregistré. Utilisez cette option lorsque vous avez des étapes d’approvisionnement supplémentaires en plus l’image de base.

* **Configuration supplémentaire** : quelques scénarios moins courants peuvent nécessiter des configurations plus spécialisées. Dans ce cas, un [fichier de configuration de cluster](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file) peut être associé à la commande Azure CLI pour créer un cluster. 

## <a name="create-the-cluster"></a>Création du cluster

Après avoir choisi les options de configuration du cluster, utilisez Azure CLI, le portail Azure ou des API Batch AI pour créer le cluster. Par exemple, pour créer un cluster à l’aide d’Azure CLI, vous pouvez suivre la documentation de la commande [az batchai cluster create](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) pour créer la commande exacte produisant les configurations dont vous avez besoin. 

Dans la configuration la plus basique, la commande suivante approvisionne un cluster Standard_NC6 avec un nœud et un accès SSH. Par défaut, ce cluster contient des machines virtuelles exécutant la dernière image de Serveur Ubuntu par défaut.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

L’exemple suivant approvisionne un cluster Standard_NC6 qui adapte automatiquement son échelle de 0 à 4 nœuds, et utilise des nœuds basse priorité et un compte de stockage automatique. Cette installation est une bonne configuration si vous avez besoin d’un cluster à faible coût et facile à gérer. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

L’exemple suivant approvisionne un cluster Standard_NC6 qui comprend une image de Data Science Virtual Machine, des options de stockage et de montage personnalisées, des informations d’identification SSH personnalisées, une tâche d’installation qui installe le package *unzip*, et un fichier de configuration de cluster pour une installation supplémentaire. Cette configuration est un exemple de cluster plus adapté à vos besoins.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>Analyser le cluster

Les commandes [az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show) et [az batchai cluster node list](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) permettent d’analyser diverses informations pour chaque cluster.

### <a name="list-all-clusters"></a>Répertorier tous les clusters

La commande suivante répertorie tous les clusters présents dans un espace de travail.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>Afficher des informations sur un cluster

La commande suivante affiche toutes les informations relatives à un cluster spécifique dans un format de tableau.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

Si votre cluster a été approvisionné à l’aide de l’option de stockage automatique, vous pouvez récupérer le nom du compte de stockage à utiliser lors du chargement des scripts et des travaux de formation. Utilisez la commande suivante :

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

Le résultat devrait être similaire à ce qui suit.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>Répertorier les nœuds de cluster

Si vous avez besoin de vous connecter aux nœuds de cluster, la commande suivante récupère la liste des nœuds et les informations de connexion.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

La sortie pour chaque nœud sera similaire à ce qui suit :

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
Vous pouvez utiliser ces informations pour établir une connexion SSH à un nœud à l’aide d’une commande similaire à la suivante.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>Envoyer des travaux au cluster

Une fois le cluster approvisionné, vous pouvez envoyer des travaux à exécuter sur les nœuds. Pour découvrir les différentes façons de préparer, d’envoyer et de surveiller des travaux à l’aide d’Azure CLI, voir la commande [az batchai job](/cli/azure/batchai/job?view=azure-cli-latest). 

## <a name="downscale-cluster-for-later-use"></a>Réduire l’échelle d’un cluster en vue d’une utilisation ultérieure

Une fois que vous avez fini d’exécuter vos tâches, vous pouvez réduire l’échelle de votre cluster. Il est recommandé de toujours réduire l’échelle des clusters inutilisés afin d’économiser des coûts de calcul. Réduire l’échelle d’un cluster à 0 nœud vous permet d’arrêter la facturation de frais, sans avoir à réapprovisionner les clusters par la suite, si vous avez besoin d’augmenter l’échelle à nouveau. Si vous avez sélectionné la mise à l’échelle automatique lors de la création du cluster, l’échelle de celui-ci doit être automatiquement réduite à la valeur cible minimale. Si vous avez choisi la mise à l’échelle manuelle, réduisez l’échelle du cluster à l’aide de la commande suivante.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>Suppression d'un cluster

Lorsque vous avez fini d’utiliser un cluster, servez-vous de la commande [suppression de cluster az batchai](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) pour le supprimer.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

La suppression de votre groupe de ressources entraîne aussi automatiquement la suppression de tous les clusters approvisionnés sous ce groupe de ressources.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples de création d’un cluster Batch AI, consultez le [Portail](quickstart-create-cluster-portal.md) ou [Azure CLI](quickstart-create-cluster-cli.md), ou le [les recettes Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes) sur GitHub.
