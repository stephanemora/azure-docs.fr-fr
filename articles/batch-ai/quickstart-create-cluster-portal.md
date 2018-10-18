---
title: Démarrage rapide Azure - Créer un cluster Batch AI | Microsoft Docs
description: Démarrage rapide - Créer un cluster Batch AI pour l’apprentissage des modèles d’intelligence artificielle et Machine Learning - portail Azure
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
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 8b9daa0fbbf84e0f602498a0847c9e120f709b17
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057556"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Démarrage rapide : créer un cluster pour les travaux de formation Batch AI à l’aide du portail Azure

Ce démarrage rapide montre comment utiliser le portail Azure pour créer un cluster Batch AI que vous pouvez utiliser pour l’apprentissage des modèles d’intelligence artificielle et Machine Learning. Batch AI est un service managé qui permet aux scientifiques des données et aux chercheurs en IA d’effectuer l’apprentissage des modèles d’IA et de Machine Learning à l’échelle sur des clusters de machines virtuelles Azure.

Initialement, le cluster a un seul nœud GPU et un serveur de fichiers joint. À l’issue de ce démarrage rapide, vous disposerez d’un cluster capable de monter en puissance que vous pourrez utiliser pour effectuer l’apprentissage des modèles d’apprentissage profond. Envoyez des travaux de formation au cluster à l’aide de Batch AI, des outils [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ou de [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Créer la paire de clés SSH

Vous aurez besoin d’une paire de clés SSH pour suivre ce guide de démarrage rapide. Si vous disposez déjà d’une paire de clés SSH, vous pouvez ignorer cette étape.

Pour créer une paire de clés SSH, exécutez la commande suivante à partir d’un interpréteur de commandes Bash et suivez les instructions qui s’affichent à l’écran. Vous pouvez par exemple utiliser [Azure Cloud Shell](../cloud-shell/overview.md) ou, sur Windows, le [sous-système Windows pour Linux](/windows/wsl/install-win10). La sortie de la commande inclut le nom du fichier de clé publique. Copiez le contenu du fichier de clé publique (`cat ~/.ssh/id_rsa.pub`) dans le Presse-papiers ou un autre emplacement, afin d’y accéder dans une étape ultérieure.

```bash
ssh-keygen -t rsa -b 2048
```

Pour plus d’informations sur la création de paires de clés SSH, consultez [Créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-batch-ai-workspace"></a>Créer un espace de travail Batch AI

Commencez par créer un espace de travail Batch AI pour organiser vos ressources Batch AI. Un espace de travail peut contenir un ou plusieurs clusters ou autres ressources Batch AI.

1. Sélectionnez **Tous les services** et filtrez sur **Batch AI**.

2. Sélectionnez **Ajouter un espace de travail**.

3. Entrez les valeurs **Nom de l’espace de travail** et **Groupe de ressources**. Si vous le souhaitez, sélectionnez des options **Abonnement** et **Emplacement** différentes pour l’espace de travail. Sélectionnez **Créer un espace de travail**.

  ![Créer un espace de travail Batch AI](./media/quickstart-create-cluster-portal/create-workspace.png)

Lorsque le message **Déploiement réussi** s’affiche, accédez à la ressource que vous avez créée et sélectionnez l’espace de travail.

## <a name="create-a-file-server"></a>Créer un serveur de fichiers

Un serveur de fichiers Batch AI est un NFS à nœud unique, qui peut être monté automatiquement sur les nœuds de cluster. C’est l’une des différentes façons dont vous pouvez fournir un stockage pour les données d’entrée et de sortie à partir de vos travaux de formation.

1. Dans l’espace de travail, sélectionnez **Serveur de fichiers** > **Ajouter un serveur de fichiers batch ai**.

2. Entrez les valeurs **Nom du serveur de fichiers** et **Taille de la machine virtuelle**. Pour ce démarrage rapide, une taille de machine virtuelle de *Standard D1_v2* est suggérée pour le serveur de fichiers. Choisissez une taille différente si vous avez besoin stocker de plus grandes quantités de données d’entrée ou de sortie pour les travaux de formation.

3. Entrez un **Nom d’utilisateur administrateur** et copiez le contenu de votre fichier de clé publique SSH dans **Clé SSH**. Acceptez les valeurs par défaut pour les autres valeurs et sélectionnez **Créer le serveur de fichiers**.

  ![Créer le serveur de fichiers Batch AI](./media/quickstart-create-cluster-portal/create-file-server.png)

Le déploiement du serveur de fichiers ne nécessite que quelques minutes.

Une fois que le serveur est créé, cliquez sur **Propriétés** et prenez note des **Paramètres de montage**. Vous pouvez exécuter SSH à l’adresse IP publique du serveur pour charger et télécharger les données d’apprentissage et les fichiers de sortie dans le répertoire indiqué (*/data*).

![Propriétés du serveur de fichiers](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Créer un cluster

Les étapes suivantes créent un cluster avec un seul nœud GPU. Le nœud de cluster exécute une image de serveur Ubuntu par défaut conçue pour héberger les applications basées sur conteneur, que vous pouvez utiliser pour la plupart des charges de travail de formation. Le nœud de cluster monte le serveur de fichiers à son point de montage. 

1. Dans votre espace de travail Batch AI, sélectionnez **Cluster** > **Ajouter un cluster batch ai**.

2. Entrez les valeurs pour **Nom du cluster** et les paramètres suivants. La taille de machine virtuelle suggérée a un GPU NVIDIA Tesla K80.
  
   |Paramètre  |Valeur  |
   |---------|---------|
   |**Taille de la machine virtuelle**     |Standard NC6|
   |**Nombre cible de nœuds**     |1|

3. Entrez un **Nom d’utilisateur administrateur** et copiez le contenu de votre fichier de clé publique SSH dans **Clé SSH**. Acceptez les valeurs par défaut pour les autres valeurs sur cette page, puis sélectionnez **Suivant : configuration du nœud**.

   ![Entrer les informations de base sur le cluster](./media/quickstart-create-cluster-portal/create-cluster.png)

4. Sous **Monter les volumes**, sélectionnez **Références du serveur de fichiers** > **Ajouter**. Sélectionnez le serveur de fichiers que vous avez créé précédemment. Entrez un **chemin de montage relatif** où le serveur de fichiers est monté sur chaque nœud du cluster. Sélectionnez **Enregistrer et continuer**.

   ![Ajouter les références du serveur de fichiers](./media/quickstart-create-cluster-portal/file-server-reference.png)

Enregistrez la configuration du nœud et sélectionnez **Créer un cluster**.

Batch AI prend quelques minutes pour allouer le nœud. Pendant ce temps, **l’état de l’allocation** du cluster est **Redimensionnement**. Après quelques minutes, l’état du cluster est **Stable** et le nœud démarre.

![Démarrage du cluster](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Sélectionnez le nom du cluster pour vérifier l’état du nœud. Lorsque l’état d’un nœud est **Inactif**, il est prêt à exécuter des travaux de formation.

### <a name="list-cluster-nodes"></a>Répertorier les nœuds du cluster

Si vous avez besoin de vous connecter aux nœuds du cluster (dans ce cas, un seul nœud) pour installer des applications ou effectuer la maintenance, obtenez les informations de connexion dans le portail. Une fois le cluster créé, cliquez sur **Nœuds** et prenez note des paramètres de **connexion** SSH (adresse IP et numéro de port).

![Nœuds de cluster](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Ces informations permettent d’établir une connexion SSH au nœud. Par exemple, entrez l’adresse IP et le numéro de port corrects de votre nœud dans la commande suivante :

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>Redimensionner le cluster

Lorsque vous utilisez votre cluster pour effectuer l’apprentissage d’un modèle, vous aurez peut-être besoin de ressources de calcul supplémentaires. Par exemple, pour augmenter la taille à 2 nœuds pour un travail de formation distribué, sélectionnez **Mise à l’échelle** et définissez le **Nombre cible de nœuds** sur 2. Enregistrez la configuration.

![Mettre à l’échelle le cluster](./media/quickstart-create-cluster-portal/scale-cluster.png)

Le redimensionnement du cluster nécessite quelques minutes.

## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous souhaitez poursuivre les exemples et tutoriels Batch AI, utilisez l’espace de travail, le serveur de fichiers et le cluster Batch AI créés dans ce démarrage rapide.

Vous êtes facturé pour le cluster et le serveur de fichiers Batch AI tandis que les machines virtuelles sous-jacentes sont en cours d’exécution, même si aucun travail n’est planifié. Si vous souhaitez maintenir la configuration du cluster lorsque vous ne disposez d’aucun travail à exécuter, redimensionnez le cluster sur 0 nœud. Ensuite, redimensionnez-le sur 1 ou plusieurs nœuds pour exécuter vos travaux. 

Lorsque vous n’en avez plus besoin, supprimez l’espace de travail Batch AI contenant le cluster et le serveur de fichiers. Pour ce faire, sélectionnez l’espace de travail Batch AI puis **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un cluster Batch AI et un serveur de fichiers joint, à l’aide du portail Azure. Pour en savoir plus sur l’utilisation d’un cluster Batch AI pour effectuer l’apprentissage d’un modèle, poursuivez le démarrage rapide pour effectuer l’apprentissage d’un modèle d’apprentissage profond.

> [!div class="nextstepaction"]
> [Effectuer l’apprentissage d’un modèle d’apprentissage profond](./quickstart-tensorflow-training-cli.md)
