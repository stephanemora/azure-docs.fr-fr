---
title: Utiliser des nœuds de périphérie vides sur des clusters Apache Hadoop dans HDInsight - Azure
description: Découvrez comment ajouter un nœud de périphérie vide à un cluster HDInsight, qui peut être utilisé en tant que client, et comment tester / héberger vos applications HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: df35ee9791dc1090385e2d2aed5966a1292ddc64
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708183"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Utiliser des nœuds de périphérie vides sur des clusters Apache Hadoop dans HDInsight

Découvrez comment ajouter un nœud de périphérie vide à un cluster HDInsight. Un nœud de périphérie vide est une machine virtuelle Linux sur laquelle sont installés et configurés les mêmes outils clients que dans les nœuds principaux, sans que des services [Apache Hadoop](https://hadoop.apache.org/) s’exécutent. Vous pouvez utiliser le nœud de périmètre pour accéder au cluster, tester vos applications clientes et héberger vos applications clientes. 

Vous pouvez ajouter un nœud de périmètre vide à un cluster HDInsight existant et à un nouveau cluster, lorsque vous créez le cluster. L’ajout d’un nœud de périmètre vide est effectué à l’aide du modèle Azure Resource Manager.  L’exemple suivant montre comment procéder à l’aide d’un modèle :

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Comme indiqué dans l’exemple, vous pouvez éventuellement appeler une [action de script](hdinsight-hadoop-customize-cluster-linux.md) pour effectuer une configuration supplémentaire, telle que l’installation [d’Apache Hue](hdinsight-hadoop-hue-linux.md) dans le nœud de périmètre. Le script d’action de script doit être publiquement accessible sur Internet.  Par exemple, si le script est stocké dans le stockage Azure, utilisez des conteneurs publics ou blobs publics.

La taille de la machine virtuelle de nœud de périmètre doit respecter les exigences de taille de machine virtuelle du nœud Worker du cluster HDInsight. Pour connaître les tailles de machine virtuelle de nœud Worker recommandées, voir [Créer des clusters Apache Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Après avoir créé un nœud de périmètre, vous pouvez vous connecter au nœud de périmètre à l’aide de SSH et exécuter les outils clients pour accéder au cluster Hadoop dans HDInsight.

> [!WARNING]   
> Les composants personnalisés qui sont installés sur le nœud de périphérie bénéficient du support commercialement raisonnable de Microsoft. Ainsi, cela peut aider à résoudre les problèmes rencontrés. Vous pouvez également consulter les ressources de la communauté pour obtenir de l’aide. Voici quelques-uns des sites communautaires les plus actifs, sur lesquels vous pouvez obtenir de l’aide :
>
> * [Forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Si vous utilisez une technologie Apache, vous pouvez obtenir de l’aide par l’intermédiaire des sites de projets Apache sur [https://apache.org](https://apache.org), comme le site [Apache Hadoop](https://hadoop.apache.org/).

> [!IMPORTANT]
> Les images Ubuntu sont accessibles pour la création d'un nouveau cluster HDInsight dans les 3 mois qui suivent leur publication. Depuis janvier 2019, **plus aucun** correctif automatique n'est disponible pour les clusters (y compris les nœuds de périphérie) en cours d'exécution. Les clients doivent utiliser des actions de script ou d'autres mécanismes pour corriger un cluster en cours d'exécution.  Pour plus d’informations, consultez [Mise à jour corrective du système d’exploitation pour HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Ajouter un nœud de périmètre à un cluster existant
Dans cette section, vous allez utiliser un modèle Resource Manager pour ajouter un nœud de périmètre à un cluster HDInsight existant.  Le modèle Resource Manager se trouve dans [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Le modèle Resource Manager appelle une action de script située dans https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Le script n’effectue aucune action.  Cela sert à illustrer l’appel d’action de script à partir d’un modèle Resource Manager.

**Pour ajouter un nœud de périmètre vide à un cluster existant**

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Azure Resource Manager dans le portail Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configurez les propriétés suivantes :
   
   * **Abonnement**: sélectionnez l’abonnement Azure utilisé pour créer le cluster.
   * **Groupe de ressources** : sélectionnez le groupe de ressources utilisé pour le cluster HDInsight existant.
   * **Emplacement** : sélectionnez l’emplacement du cluster HDInsight existant.
   * **Nom du cluster** : entrez le nom d’un cluster HDInsight existant.
   * **Taille du nœud de périphérie** : sélectionnez l’une des tailles de machine virtuelle. La taille de la machine virtuelle doit respecter les exigences de taille de machine virtuelle du nœud Worker. Pour connaître les tailles de machine virtuelle de nœud Worker recommandées, voir [Créer des clusters Apache Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Préfixe de nœud de périphérie** : la valeur par défaut est **new**.  À l’aide de la valeur par défaut, le nom du nœud de périmètre est **new-edgenode**.  Vous pouvez personnaliser le préfixe à partir du portail. Vous pouvez également personnaliser le nom complet à partir du modèle.

4. Cochez **J’accepte les termes et conditions mentionnés ci-dessus**, puis cliquez sur **Acheter** pour créer le nœud de périmètre.

>[!IMPORTANT]  
> Veillez à sélectionner le groupe de ressources Azure utilisé pour le cluster HDInsight existant.  Dans le cas contraire, vous obtenez le message d’erreur « Can not perform requested operation on nested resource. Parent resource '&lt;ClusterName>' not found » (Impossible d’effectuer l’opération demandée sur la ressource imbriquée. Ressource parente <Nom du cluster> introuvable).

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Ajouter un nœud de périmètre lors de la création d’un cluster
Dans cette section, vous allez utiliser un modèle Resource Manager pour créer un cluster HDInsight avec un nœud de périmètre.  Vous trouverez le modèle Resource Manager dans le [galerie de modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Le modèle Resource Manager appelle une action de script située dans https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Le script n’effectue aucune action.  Cela sert à illustrer l’appel d’action de script à partir d’un modèle Resource Manager.

**Pour créer un cluster HDInsight avec un nœud de périphérie**

1. Créez un cluster HDInsight si vous n’avez pas encore.  Consultez [Prise en main de Hadoop dans HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Azure Resource Manager dans le portail Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configurez les propriétés suivantes :
   
   * **Abonnement**: sélectionnez l’abonnement Azure utilisé pour créer le cluster.
   * **Groupe de ressources** : créez un groupe de ressources pour le cluster.
   * **Emplacement** : Sélectionnez l’emplacement du groupe de ressources.
   * **Nom du cluster** : entrez un nom pour le nouveau cluster à créer.
   * **Nom d’utilisateur de la connexion au cluster** : entrez le nom d’utilisateur HTTP Hadoop.  Le nom par défaut est **admin**.
   * **Mot de passe de la connexion au cluster** : entrez le mot de passe de l’utilisateur HTTP Hadoop.
   * **Nom d’utilisateur SSH** : entrez le nom d’utilisateur SSH. Le nom par défaut est **sshuser**.
   * **Mot de passe SSH** : entrez le mot de passe de l’utilisateur SSH.
   * **Action du script d’installation** : conservez la valeur par défaut pour ce tutoriel.
     
     Certaines propriétés ont été codées en dur dans le modèle : type de cluster, nombre de nœuds de travail de cluster, taille de nœud de périphérie et nom de nœud de périphérie.
4. Cochez **J’accepte les termes et conditions mentionnés ci-dessus**, puis cliquez sur **Acheter** pour créer le cluster avec le nœud de périmètre.

## <a name="add-multiple-edge-nodes"></a>Ajouter plusieurs nœuds de périphérie

Vous pouvez ajouter plusieurs nœuds de périphérie à un cluster HDInsight.  La configuration de plusieurs nœuds de périphérie peut uniquement être effectuée à l’aide de modèles Azure Resource Manager.  Consultez l’exemple de modèle au début de cet article.  Vous devez mettre à jour l’élément **targetInstanceCount** afin de refléter le nombre de nœuds de périphérie que vous souhaitez créer.

## <a name="access-an-edge-node"></a>Accéder à un nœud de périmètre
Le point de terminaison ssh de nœud de périphérie est &lt;EdgeNodeName>.&lt;ClusterName>-ssh.azurehdinsight.net:22.  Par exemple, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Le nœud de périmètre apparaît comme une application sur le portail Azure.  Le portail vous donne les informations d’accès au nœud de périmètre à l’aide de SSH.

**Pour vérifier le point de terminaison SSH de nœud de périmètre**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HDInsight avec un nœud de périmètre.
3. Cliquez sur **Applications**. Vous devriez voir le nœud de périmètre.  Le nom par défaut est **new-edgenode**.
4. Cliquez sur le nœud de périmètre. Vous devriez voir le point de terminaison SSH.

**Pour utiliser Hive sur le nœud de périmètre**

1. Utilisez SSH pour vous connecter au nœud de périmètre. Pour en savoir plus, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Après vous être connecté au nœud de périmètre à l’aide de SSH, utilisez la commande suivante pour ouvrir la console Hive :
   
        hive
3. Exécutez la commande suivante pour afficher les tables Hive dans le cluster :
   
        show tables;

## <a name="delete-an-edge-node"></a>Supprimer un nœud de périmètre
Vous pouvez supprimer un nœud de périmètre à partir du portail Azure.

**Pour accéder à un nœud de périmètre**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HDInsight avec un nœud de périmètre.
3. Cliquez sur **Applications**. Vous devriez voir une liste des nœuds de périmètre.  
4. Cliquez avec le bouton droit sur le nœud de périmètre à supprimer, puis cliquez sur **Supprimer**.
5. Cliquez sur **Yes** (Oui) pour confirmer.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment ajouter un nœud de périmètre et comment y accéder. Pour en savoir plus, consultez les articles suivants :

* [Installer des applications HDInsight](hdinsight-apps-install-applications.md) : découvrez comment installer une application HDInsight sur vos clusters.
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md): découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Publier des applications HDInsight](hdinsight-apps-publish-applications.md) : découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN : Installer une application HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) : découvrez comment définir les applications HDInsight.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md): apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Créer des clusters Apache Hadoop sous Linux dans HDInsight à l’aide de modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) : apprenez à appeler des modèles Resource Manager pour la création de clusters HDInsight.

