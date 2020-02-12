---
title: Personnaliser des clusters Azure HDInsight à l’aide d’actions de script
description: Ajoutez des composants personnalisés à des clusters HDInsight Linux à l’aide d’actions de script. Les actions de script sont des scripts Bash qui permettent de personnaliser la configuration d’un cluster ou d’ajouter d’autres services et utilitaires comme Hue, Solr ou R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 0930bbcfff41a667f08f5dfc5744c16476ddd8a1
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031444"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personnaliser des clusters Azure HDInsight à l’aide d’actions de script

Azure HDInsight fournit une méthode de configuration appelée **actions de script**, qui appelle des scripts personnalisés pour personnaliser le cluster. Ces scripts sont utilisés pour installer des composants supplémentaires et modifier des paramètres de configuration. Les actions de script peuvent être utilisées pendant ou après la création du cluster.

Des actions de script peuvent également être publiées dans la Place de marché Azure en tant qu’application HDInsight. Pour plus d’informations sur les applications HDInsight, consultez [Publier une application HDInsight sur la Place de marché Azure](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Autorisations

Quand vous utilisez des actions de script avec un cluster HDInsight joint à un domaine, deux autorisations Apache Ambari sont requises :

* **AMBARI.RUN\_CUSTOM\_COMMAND**. Le rôle Administrateur d’Ambari dispose de cette autorisation par défaut.
* **CLUSTER.RUN\_CUSTOM\_COMMAND**. L’administrateur de cluster HDInsight et l’administrateur Ambari disposent de cette autorisation par défaut.

Pour plus d’informations sur l’utilisation des autorisations avec un cluster HDInsight joint à un domaine, consultez [Gérer des clusters HDInsight avec le pack Sécurité Entreprise](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Contrôle d’accès

Si vous n’êtes pas l’administrateur ou le propriétaire de votre abonnement Azure, votre compte doit avoir au minimum un accès Contributeur au groupe de ressources qui contient le cluster HDInsight.

Si vous créez un cluster HDInsight, une personne ayant au minimum un accès Contributeur à l’abonnement Azure doit avoir préalablement inscrit le fournisseur pour HDInsight. L’inscription du fournisseur se produit quand un utilisateur disposant d’un accès Collaborateur à l’abonnement crée une ressource pour la première fois sur l’abonnement. Cela peut également se faire sans avoir à créer une ressource si vous [inscrivez un fournisseur à l’aide de REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Obtenez plus d’informations sur le fonctionnement de la gestion des accès :

* [Bien démarrer avec la gestion des accès dans le portail Azure](../role-based-access-control/overview.md)
* [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Comprendre les actions de script

Une action de script est un script bash qui s’exécute sur les nœuds dans un cluster HDInsight. Les actions de script présentent les caractéristiques et fonctionnalités suivantes :

* Elles doivent être stockées sur un URI accessible à partir du cluster HDInsight. Voici les emplacements de stockage possibles :

    * Pour les clusters normaux :

      * ADLS Gen1 : Le principal de service utilisé par HDInsight pour accéder à Data Lake Storage doit avoir accès en lecture au script. Le format d’URI pour les scripts stockés dans Data Lake Storage Gen1 est `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Un blob est un compte de stockage Azure utilisé comme compte de stockage principal ou supplémentaire pour le cluster HDInsight. HDInsight peut accéder à ces deux types de comptes de stockage lors de la création du cluster.

        > [!IMPORTANT]  
        > Ne faites pas pivoter la clé de stockage sur ce compte Stockage Azure, car cela entraînera l’échec des actions de script suivantes avec des scripts stockés ici.

      * Un service de partage de fichiers public accessible via les chemins d’accès http://. Exemple : Blob Azure, GitHub, OneDrive.

        Pour obtenir des exemples d’URI, consultez [Exemples de scripts d’action de script](#example-script-action-scripts).

     * Pour les clusters avec ESP :

         * Les URI wasb:// ou wasbs:// ou http[s]:// sont pris en charge.

* Elles peuvent être limitées de manière à s’exécuter uniquement sur certains types de nœuds, par exemple des nœuds principaux ou des nœuds worker.

* Elles peuvent avoir un état persistant ou ad hoc.

    Les scripts persistants servent à personnaliser les nouveaux nœuds worker qui sont ajoutés au cluster lors d’opérations de mise à l’échelle. Un script persistant peut également appliquer des modifications à un autre type de nœud au moment de ces opérations. Le nœud principal en est un exemple.

  > [!IMPORTANT]  
  > Les actions de script persistantes doivent avoir un nom unique.

    Les scripts ad hoc ne sont pas persistants. Ils ne sont pas appliqués aux nœuds worker ajoutés au cluster après l’exécution du script. Vous pouvez donc promouvoir un script ad hoc en script persistant ou abaisser un script persistant en script ad hoc.

  > [!IMPORTANT]  
  > Les actions de script utilisées lors de la création du cluster sont automatiquement rendues persistantes.
  >
  > Les scripts qui échouent ne sont pas rendus persistants, même si vous précisez qu’ils doivent l’être.

* Elles peuvent prendre les paramètres utilisés par le script au moment de l’exécution.

* Elles s’exécutent avec des privilèges racines sur les nœuds du cluster.

* Elles peuvent être utilisées par le biais du portail Azure, d’Azure PowerShell, d’Azure Classic CLI ou du kit HDInsight .NET SDK.

Le cluster conserve un historique de tous les scripts qui ont été exécutés. L’historique facilite la recherche de l’ID d’un script pour promouvoir ou abaisser des opérations.

> [!IMPORTANT]  
> Il n’existe aucune méthode automatique pour annuler les modifications apportées par une action de script. Il faut soit inverser les modifications manuellement, soit fournir un script qui les inverse.

### <a name="script-action-in-the-cluster-creation-process"></a>Action de script dans le processus de création de cluster

Les actions de script utilisées lors de la création d’un cluster sont légèrement différentes de celles exécutées sur un cluster existant :

* Le script est automatiquement rendu persistant.

* Un échec dans le script peut provoquer l’échec du processus de création du cluster.

Le diagramme suivant illustre la phase d’exécution de l’action de script pendant le processus de création :

![Personnalisation du cluster HDInsight et procédure de création d’un cluster][img-hdi-cluster-states]

Le script est exécuté pendant la configuration de HDInsight. Le script s’exécute en parallèle sur tous les nœuds du cluster spécifiés. Il s’exécute avec des privilèges racines sur les nœuds.

> [!NOTE]  
> Vous pouvez effectuer des opérations comme l’arrêt et le démarrage des services, notamment des services liés à Apache Hadoop. Si vous arrêtez des services, assurez-vous que le service Ambari et d’autres services liés à Hadoop sont en cours d’exécution avant la fin du script. Ces services sont nécessaires pour déterminer l’intégrité et l’état du cluster qui est en cours de création.

Durant la création du cluster, vous pouvez exécuter de nombreuses actions de script à la fois. Ces scripts sont appelés dans l’ordre dans lequel ils ont été spécifiés.

> [!IMPORTANT]  
> Les actions de script doivent se terminer dans un délai de 60 minutes, faute de quoi elles expirent. Lors de l’approvisionnement du cluster, le script est exécuté en même temps que les autres processus d’installation et de configuration. En raison de cette concurrence sur les ressources, par exemple au niveau du temps processeur ou de la bande passante, l’exécution du script risque de prendre plus de temps que dans votre environnement de développement.
>
> Pour réduire le temps d’exécution du script, évitez les tâches comme le téléchargement et la compilation d’applications à partir de la source. À la place, précompilez les applications et enregistrez le fichier binaire dans le stockage Azure.

### <a name="script-action-on-a-running-cluster"></a>Action de script sur un cluster en cours d’exécution

Un échec dans un script exécuté sur un cluster déjà en cours d’exécution n’entraîne pas automatiquement l’échec du cluster. À la fin d’un script, le cluster doit normalement revenir à un état en cours d’exécution.

> [!IMPORTANT]  
> Toutefois, même si le cluster présente un état en cours d’exécution, l’échec du script peut avoir endommagé l’installation. Par exemple, un script peut avoir supprimé certains fichiers nécessaires au cluster.
>
> Les actions de script s’exécutent avec des privilèges racines. Soyez donc sûr de comprendre ce que fait un script avant de l’appliquer à votre cluster.

Quand vous appliquez un script à un cluster, le cluster passe de l’état **En cours d’exécution** à **Accepté**, puis à **Configuration HDInsight**. Si le script s’est correctement exécuté, le cluster revient finalement à l’état **En cours d’exécution**. L’état du script est enregistré dans l’historique des actions de script. Cette information vous permet de savoir si le script a réussi ou échoué. Par exemple, l’applet de commande PowerShell `Get-AzHDInsightScriptActionHistory` affiche l’état d’un script. Cette commande renvoie des informations semblables au texte suivant :

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Si vous changez le mot de passe, l’utilisateur ou l’administrateur du cluster après la création de ce dernier, les actions de script exécutées sur ce cluster risquent d’échouer. Si des actions de script persistantes ciblent des nœuds worker, ces scripts risquent d’échouer au moment d’une mise à l’échelle du cluster.

## <a name="example-script-action-scripts"></a>Exemple de script d’action de script

Des scripts d’action de script peuvent être utilisés par les utilitaires suivants :

* Le portail Azure
* Azure PowerShell
* Azure Classic CLI
* Le kit HDInsight .NET SDK

HDInsight propose des scripts pour installer les composants suivants sur des clusters HDInsight :

| Name | Script |
| --- | --- |
| Ajouter un compte de stockage Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](hdinsight-hadoop-add-storage.md). |
| Installer Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Consultez [Installer et utiliser Hue sur des clusters HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Précharger les bibliothèques Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Consultez [Ajouter des bibliothèques Apache Hive personnalisées lors de la création de votre cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Utiliser une action de script lors de la création d’un cluster

Cette section explique les différentes façons d’utiliser des actions de script quand vous créez un cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Utiliser une action de script lors de la création d’un cluster à partir du portail Azure

1. Passez à la création d’un cluster en suivant les étapes décrites dans [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Sous l’onglet **Configuration + prix**, sélectionnez **+ Ajouter une action de script**.

    ![Portail Azure - Action de script dans le cluster](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Utilisez l’entrée __Sélectionner un script__ pour sélectionner un script prédéfini. Pour utiliser un script personnalisé, sélectionnez __Personnalisé__. Entrez ensuite le __nom__ et l’__URI de script Bash__ pour votre script.

    ![Ajouter un script sous la forme du script sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    La table suivante décrit les éléments sous la forme :

    | Propriété | Valeur |
    | --- | --- |
    | Sélectionner un script | Pour utiliser votre propre script, sélectionnez __Personnalisé__. Sinon, sélectionnez un des scripts fournis. |
    | Name |Indiquez un nom pour l’action de script. |
    | URI de script bash |Spécifiez l’URI du script. |
    | Head/Worker/ZooKeeper |Indiquez les nœuds sur lesquels le script est exécuté : **Head**, **Worker** ou **ZooKeeper**. |
    | Paramètres |Spécifiez les paramètres, si le script le demande. |

    Utilisez l’option __Conserver cette action de script__ pour vous assurer que le script sera appliqué aux nœuds lors des opérations de mise à l’échelle.

1. Sélectionnez __Créer__ pour enregistrer le script. Vous pouvez ensuite utiliser __+ Soumettre nouveau__ pour ajouter un autre script.

    ![HDInsight - Plusieurs actions de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Lorsque vous avez terminé d’ajouter des scripts, revenez à l’onglet **Configuration + prix**.

1. Effectuez les étapes de création de cluster restantes comme vous le faites d’habitude.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Utiliser une action de script à partir de modèles Azure Resource Manager

Les actions de script peuvent être utilisées avec des modèles Azure Resource Manager. Pour obtenir un exemple, consultez [Créer un cluster HDInsight Linux et exécuter une action de script](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Dans cet exemple, l’action de script est ajoutée à l’aide du code suivant :

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Obtenez plus d’informations sur le déploiement d’un modèle :

* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Utiliser une action de script lors de la création d’un cluster à partir d’Azure PowerShell

Dans cette section, vous allez utiliser la cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) pour appeler des scripts afin de personnaliser un cluster. Avant de commencer, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour utiliser ces commandes PowerShell, vous avez besoin du [module AZ](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le script suivant montre comment appliquer une action de script quand vous créez un cluster à l’aide de PowerShell :

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

La création du cluster peut prendre plusieurs minutes.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Utiliser une action de script lors de la création d’un cluster à l’aide du kit HDInsight .NET SDK

Le kit HDInsight .NET SDK fournit des bibliothèques clientes qui facilitent l’utilisation d’HDInsight à partir d’une application .NET. Pour obtenir un exemple de code, consultez [Actions de script](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Appliquer une action de script à un cluster en cours d’exécution

Cette section explique comment appliquer des actions de script à un cluster en cours d’exécution.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Appliquer une action de script à un cluster en cours d’exécution à partir du portail Azure

Accédez au [portail Azure](https://portal.azure.com) :

1. Dans le menu de gauche, accédez à **Tous les services** >  **Analytique** > **Clusters HDInsight**.

1. Sélectionnez votre cluster dans la liste ; la vue par défaut s’affiche.

1. Dans la vue par défaut, sous **Paramètres**, sélectionnez **Actions de script**.

1. En haut de la page **Actions de script**, sélectionnez **+ Envoyer**.

    ![Ajouter un script à un cluster en cours d’exécution](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Utilisez l’entrée __Sélectionner un script__ pour sélectionner un script prédéfini. Pour utiliser un script personnalisé, sélectionnez __Personnalisé__. Entrez ensuite le __nom__ et l’__URI de script Bash__ pour votre script.

    ![Ajouter un script sous la forme du script sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    La table suivante décrit les éléments sous la forme :

    | Propriété | Valeur |
    | --- | --- |
    | Sélectionner un script | Pour utiliser votre propre script, sélectionnez __Personnalisé__. Sinon, sélectionnez un script fourni. |
    | Name |Indiquez un nom pour l’action de script. |
    | URI de script bash |Spécifiez l’URI du script. |
    | Head/Worker/Zookeeper |Indiquez les nœuds sur lesquels le script est exécuté : **Head**, **Worker** ou **ZooKeeper**. |
    | Paramètres |Spécifiez les paramètres, si le script le demande. |

    Utilisez l’entrée __Continuer cette action de script__ pour vous assurer que le script est appliqué aux nœuds lors de la mise à l’échelle.

1. Pour finir, sélectionnez le bouton **Créer** afin d’appliquer le script au cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Appliquer une action de script à un cluster en cours d’exécution à partir d’Azure PowerShell

Pour utiliser ces commandes PowerShell, vous avez besoin du [module AZ](https://docs.microsoft.com/powershell/azure/overview).

L’exemple suivant montre comment appliquer une action de script à un cluster en cours d’exécution :

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Au terme de l’opération, vous voyez s’afficher des informations similaires au texte ci-dessous :

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Appliquer une action de script à un cluster en cours d’exécution à partir d’Azure CLI

Avant de commencer, assurez-vous que vous avez installé et configuré Azure CLI. Pour plus d’informations, consultez [Installer Azure Classic CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Basculez en mode Azure Resource Manager :

    ```bash
    azure config mode arm
    ```

2. Authentifiez-vous auprès de votre abonnement Azure :

    ```bash
    azure login
    ```

3. Appliquez une action de script à un cluster en cours d’exécution :

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Si vous omettez les paramètres de cette commande, vous êtes invité à les entrer. Si le script que vous spécifiez avec `-u` prend des paramètres, vous pouvez les spécifier à l’aide du paramètre `-p`.

    Les types de nœud valides sont `headnode`, `workernode` et `zookeeper`. Si le script doit être appliqué à plusieurs types de nœuds, entrez ces types en les séparant par un point-virgule (`;`). Par exemple : `-n headnode;workernode`.

    Pour rendre le script persistant, ajoutez le paramètre `--persistOnSuccess`. Vous pouvez également continuer le script à une date ultérieure à l’aide de `azure hdinsight script-action persisted set`.

    Une fois le travail terminé, vous obtenez une sortie similaire au texte suivant :

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Appliquer une action de script à un cluster en cours d’exécution à l’aide de l’API REST

Consultez [API REST de cluster dans Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Appliquer une action de script à un cluster en cours d’exécution à partir du kit HDInsight .NET SDK

Pour obtenir un exemple d’utilisation du SDK .NET afin d’appliquer des scripts à un cluster, consultez [Appliquer une action de script sur un cluster HDInsight Linux en cours d’exécution](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Afficher l’historique et promouvoir ou abaisser des actions de script

### <a name="the-azure-portal"></a>Le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu de gauche, accédez à **Tous les services** > **Analytique** > **Clusters HDInsight**.

1. Sélectionnez votre cluster dans la liste ; la vue par défaut s’affiche.

1. Dans la vue par défaut, sous **Paramètres**, sélectionnez **Actions de script**.

1. Un historique des scripts pour ce cluster s’affiche dans la section des actions de script. Ces informations comprennent une liste de scripts persistants. La capture d’écran suivante montre que le script Solr a été exécuté sur ce cluster. La capture d’écran n’affiche aucun script persistant.

    ![Actions de script du portail - Historique des soumissions](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Sélectionnez un script dans l’historique pour afficher la section **Propriétés** du script. Dans le coin supérieur de l’écran, vous pouvez réexécuter le script ou le promouvoir.

    ![Propriétés des actions de script - Promouvoir](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Vous pouvez également sélectionner les points de suspension ( **…** ) à droite des entrées dans la section Actions de script pour effectuer des actions.

    ![Actions de script persistantes - Supprimer](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Applet de commande | Fonction |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Récupérer des informations sur les actions de script persistantes. |
| `Get-AzHDInsightScriptActionHistory` |Récupérer l’historique des actions de script appliquées au cluster ou les informations d’un script spécifique. |
| `Set-AzHDInsightPersistedScriptAction` |Promouvoir une action de script ad hoc en action de script persistante. |
| `Remove-AzHDInsightPersistedScriptAction` |Abaisser une action de script persistante en action ad hoc. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` n’annule pas les actions effectuées par un script. Cette applet de commande supprime uniquement l’indicateur persistant.

L’exemple de script suivant illustre l’utilisation des applets de commande pour promouvoir, puis abaisser un script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Azure Classic CLI

| Applet de commande | Fonction |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Récupérer une liste des actions de script persistantes. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Récupérer des informations sur une action de script persistante spécifique. |
| `azure hdinsight script-action history list <clustername>` |Récupérer l’historique des actions de script appliquées au cluster. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Récupérer des informations sur une action de script spécifique. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promouvoir une action de script ad hoc en action de script persistante. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Abaisser une action de script persistante en action ad hoc. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` n’annule pas les actions effectuées par un script. Cette applet de commande supprime uniquement l’indicateur persistant.

### <a name="the-hdinsight-net-sdk"></a>Le kit de développement logiciel (SDK) HDInsight .NET

Pour obtenir un exemple d’utilisation du SDK .NET afin de récupérer l’historique des scripts d’un cluster, et de promouvoir ou d’abaisser des scripts, consultez [Appliquer une action de script sur un cluster HDInsight Linux en cours d’exécution](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Cet exemple montre également comment installer une application HDInsight à l’aide du SDK .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight utilise un écosystème de technologies open source formées autour d’Apache Hadoop. Microsoft Azure fournit un niveau général de prise en charge pour les technologies open source. Pour plus d’informations, consultez la section **Étendue du support** du [Forum aux questions sur le support technique Azure](https://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau supplémentaire de prise en charge pour les composants intégrés.

Deux types de composants open source sont disponibles dans le service HDInsight :

* **Composants intégrés**. Ces composants sont préinstallés sur les clusters HDInsight et fournissent les fonctionnalités principales du cluster. Les composants suivants appartiennent à cette catégorie :

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * Le langage de requête Hive [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/).

    La liste complète des composants de cluster est disponible dans [Quels sont les composants et versions Apache Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md)

* **Composants personnalisés**. En tant qu’utilisateur du cluster, vous pouvez installer ou utiliser dans votre charge de travail tout composant qui est disponible dans la communauté ou que vous avez créé.

> [!WARNING]  
> Les composants fournis avec le cluster HDInsight sont entièrement pris en charge. Le support Microsoft vous aide à isoler et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d’un support commercialement raisonnable pour vous aider à résoudre le problème. Le support Microsoft peut réussir à résoudre le problème. Ou alors, il peut vous inviter à faire appel à d’autres canaux qui offrent une expertise reconnue sur ces technologies open source. De nombreux sites de communauté sont disponibles, comme le [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) et [Stack Overflow](https://stackoverflow.com).
>
> Par ailleurs, les projets Apache ont des sites de projet sur le [site web Apache](https://apache.org). [Hadoop](https://hadoop.apache.org/) en est un exemple.

Le service HDInsight fournit plusieurs méthodes d’utilisation de ces composants personnalisés. Quel que soit le mode d’utilisation ou d’installation du composant sur le cluster, le même niveau de support s’applique. La liste suivante décrit les méthodes les plus courantes d’utilisation des composants personnalisés sur des clusters HDInsight :

1. **Envoi de travaux**. Il est possible d’envoyer au cluster des travaux Hadoop ou d’autres types de travaux qui exécutent ou utilisent des composants personnalisés.

2. **Personnalisation de cluster**. Quand vous créez le cluster, vous pouvez définir des paramètres supplémentaires et des composants personnalisés qui sont installés sur les nœuds du cluster.

3. **Exemples**. Pour les composants personnalisés fréquemment utilisés, Microsoft et d’autres éditeurs fournissent parfois des exemples illustrant leur utilisation sur des clusters HDInsight. Ces exemples sont fournis sans support.

## <a name="troubleshooting"></a>Dépannage

Vous pouvez utiliser l’interface utilisateur web d’Ambari pour afficher les informations journalisées par des actions de script. Si le script échoue pendant la création du cluster, les journaux d’activité sont également disponibles dans le compte de stockage par défaut associé au cluster. Cette section fournit des informations sur la façon de récupérer les journaux d’activité à l’aide de ces deux options.

### <a name="the-apache-ambari-web-ui"></a>Interface utilisateur web d’Apache Ambari

1. Dans votre navigateur, accédez à `https://CLUSTERNAME.azurehdinsight.net`. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

    Lorsque vous y êtes invité, entrez le nom du compte d’administrateur, **admin**, et le mot de passe associé au cluster. Vous devrez parfois retaper les informations d’identification d’administrateur dans un formulaire web.

2. Dans la barre située en haut de la page, sélectionnez l’entrée **ops**. Une liste affiche les opérations en cours et précédentes effectuées sur le cluster via Ambari.

    ![Barre de l’interface utilisateur web d’Ambari avec ops sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

3. Recherchez les entrées comportant **run\_customscriptaction** dans la colonne **Operations**. Ces entrées sont créées lors de l’exécution des actions de script.

    ![Apache Ambari - Action de script, opérations](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Pour voir les sorties **STDOUT** et **STDERR**, sélectionnez l’entrée **run\customscriptaction** et suivez les différents liens. Une sortie est générée à chaque exécution du script. Elle contient des informations potentiellement utiles.

### <a name="access-logs-from-the-default-storage-account"></a>Accès aux journaux d’activité à partir du compte de stockage par défaut

Si la création du cluster échoue en raison d’une erreur de script, les journaux d’activité sont conservés dans le compte de stockage du cluster.

* Les journaux d’activité de stockage sont disponibles dans `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Journaux d’actions de script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Sous ce répertoire, les journaux d’activité sont organisés séparément pour le **nœud principal**, le **nœud worker** et le **nœud zookeeper**. Regardez les exemples suivants :

    * **Nœud principal** : `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nœud worker** : `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nœud zookeeper** : `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Toutes les valeurs **stdout** et **stderr** de l’hôte correspondant sont chargées vers le compte de stockage. Il existe un fichier **output-\*.txt** et un fichier **errors-\*.txt** pour chaque action de script. Le fichier **output-*.txt** contient des informations sur l’URI du script exécuté sur l’ordinateur hôte. Le texte suivant constitue un exemple de ces informations :

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Vous pouvez créer plusieurs fois un cluster d’action de script portant le même nom. Dans ce cas, vous pouvez différencier les journaux d’activité correspondants par le nom de dossier **DATE**. Par exemple, la structure de dossiers d’un cluster, **mycluster**, créé à différentes dates ressemble aux entrées de journaux suivantes :

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Si vous créez un cluster d’action de script avec le même nom le même jour, vous pouvez utiliser le préfixe unique pour identifier les fichiers journaux correspondants.

* Si vous créez un cluster vers minuit (0 h 00), il est possible que les fichiers journaux s’étendent sur deux jours. Dans ce cas, vous voyez deux dossiers de date différente pour le même cluster.

* Le chargement des fichiers journaux vers le conteneur par défaut peut prendre jusqu’à cinq minutes, en particulier si les clusters sont de grande taille. Par conséquent, si vous souhaitez accéder aux journaux d’activité, vous ne devez pas immédiatement supprimer le cluster en cas d’échec d’une action de script.

### <a name="ambari-watchdog"></a>Agent de surveillance Ambari

> [!WARNING]  
> Ne changez pas le mot de passe de l’agent de surveillance Ambari (hdinsightwatchdog) sur votre cluster HDInsight Linux. La modification du mot de passe pour ce compte élimine la possibilité d’exécuter de nouvelles actions de script sur le cluster HDInsight.

### <a name="cant-import-name-blobservice"></a>Impossible d’importer le nom BlobService

__Symptômes__. L’action de script échoue. Un texte semblable à l’erreur suivante s’affiche lorsque vous observez l’opération dans Ambari :

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Cause__. Cette erreur se produit si vous mettez à niveau le client Stockage Azure Python qui est inclus dans le cluster HDInsight. HDInsight attend le client de stockage Azure 0.20.0.

__Résolution__. Pour résoudre cette erreur, connectez-vous manuellement à chaque nœud de cluster avec `ssh`. Exécutez la commande suivante pour réinstaller la version correcte du client de stockage :

```bash
sudo pip install azure-storage==0.20.0
```

Pour plus d’informations sur la connexion au cluster via SSH, consultez [Se connecter à HDInsight (Apache Hadoop) avec SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>L’historique n’affiche pas les scripts utilisés pendant la création du cluster

Si votre cluster a été créé avant le 15 mars 2016, il est possible que l’historique des actions de script n’affiche pas de script. Le redimensionnement du cluster provoque l’affichage des scripts dans l’historique des actions de script.

Deux exceptions :

* Votre cluster a été créé avant le 1er septembre 2015. Il s’agit de la date à laquelle les actions de script ont été introduites. Tout cluster créé avant cette date n’aurait pas pu utiliser les actions de script pour la création du cluster.

* Vous avez utilisé plusieurs actions de script durant la création du cluster. Ou vous avez donné le même nom à plusieurs scripts, ou utilisé le même nom et le même URI, mais des paramètres différents pour plusieurs scripts. Dans tous ces cas, l’erreur suivante s’affiche :

    Aucune nouvelle action de script ne peut être exécutée sur ce cluster en raison d’un conflit de noms de script dans les scripts existants. Les noms de script fournis au moment de la création du cluster doivent être uniques. Les scripts existants sont exécutés lors du redimensionnement.

## <a name="next-steps"></a>Étapes suivantes

* [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Ajouter un stockage supplémentaire à un cluster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Procédure de création d’un cluster"
