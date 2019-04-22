---
title: Créer un espace de travail Azure Databricks dans un réseau virtuel
description: Cet article décrit comment déployer Azure Databricks à votre réseau virtuel.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288200"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Démarrage rapide : Créer un espace de travail Azure Databricks dans un réseau virtuel

Ce démarrage rapide montre comment créer un espace de travail Azure Databricks dans un réseau virtuel. Vous allez également créer un cluster Apache Spark au sein de cet espace de travail.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Dans le portail Azure, sélectionnez **créer une ressource** > **mise en réseau** > **réseau virtuel**.

2. Sous **créer un réseau virtuel**, appliquer les paramètres suivants : 

    |Paramètre|Valeur suggérée|Description|
    |-------|---------------|-----------|
    |Nom|databricks-quickstart|Sélectionnez un nom pour votre réseau virtuel.|
    |Espace d’adressage|10.1.0.0/16|Plage d'adresses du réseau virtuel en notation CIDR.|
    |Abonnement|\<Votre abonnement\>|Sélectionnez l’abonnement Azure que vous souhaitez utiliser.|
    |Groupe de ressources|databricks-quickstart|Sélectionnez **créer un nouveau** et entrez un nouveau nom de groupe de ressources pour votre compte.|
    |Lieu|\<Sélectionnez la région la plus proche de vos utilisateurs\>|Sélectionnez un emplacement géographique où vous pouvez héberger votre réseau virtuel. Utilisez l’emplacement le plus proche de vos utilisateurs.|
    |Nom du sous-réseau|default|Sélectionnez un nom pour le sous-réseau par défaut dans votre réseau virtuel.|
    |Plage d’adresses de sous-réseau|10.1.0.0/24|Plage d’adresses du sous-réseau en notation CIDR. Elle doit être contenue dans l’espace d’adressage du réseau virtuel. La plage d’adresses d’un sous-réseau qui est en cours d’utilisation ne peut pas être modifiée.|

    ![Créer un réseau virtuel sur le portail Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Une fois le déploiement terminé, accédez à votre réseau virtuel et sélectionnez **espace d’adressage** sous **paramètres**. Dans la zone intitulée *ajouter la plage d’adresses supplémentaires*, insérer `10.179.0.0/16` et sélectionnez **enregistrer**.

    ![Espace d’adressage de réseau virtuel Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

1. Dans le portail Azure, sélectionnez **créer une ressource** > **Analytique** > **Databricks**.

2. Sous **Service Azure Databricks**, appliquer les paramètres suivants :

    |Paramètre|Valeur suggérée|Description|
    |-------|---------------|-----------|
    |Nom de l’espace de travail|databricks-quickstart|Sélectionnez un nom pour votre espace de travail Azure Databricks.|
    |Abonnement|\<Votre abonnement\>|Sélectionnez l’abonnement Azure que vous souhaitez utiliser.|
    |Groupe de ressources|databricks-quickstart|Sélectionnez le même groupe de ressources que vous avez utilisé pour le réseau virtuel.|
    |Lieu|\<Sélectionnez la région la plus proche de vos utilisateurs\>|Choisissez le même emplacement que votre réseau virtuel.|
    |Niveau de tarification|Choisir entre Standard ou Premium.|Pour plus d’informations sur les niveaux tarifaires, consultez le [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Déployer l’espace de travail Azure Databricks dans votre réseau virtuel|Oui|Ce paramètre vous permet de déployer un espace de travail Azure Databricks dans votre réseau virtuel.|
    |Réseau virtuel|databricks-quickstart|Sélectionnez le réseau virtuel que vous avez créé dans la section précédente.|
    |Nom du sous-réseau public|public-subnet|Utilisez le nom de sous-réseau public par défaut.|
    |Plage CIDR de sous-réseau public|10.179.64.0/18|Plage CIDR pour ce sous-réseau doit être comprise entre /18 et /26.|
    |Nom du sous-réseau privé|private-subnet|Utilisez le nom de sous-réseau privé par défaut.|
    |Plage CIDR de sous-réseau privé|10.179.0.0/18|Plage CIDR pour ce sous-réseau doit être comprise entre /18 et /26.|

    ![Créer un espace de travail Azure Databricks sur le portail Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Une fois le déploiement est terminé, accédez à la ressource Azure Databricks. Notez que l’homologation de réseaux virtuels est désactivée. Notez également le groupe de ressources et le groupe de ressources managé dans la page de présentation. 

    ![Vue d’ensemble de Azure Databricks dans le portail Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Le groupe de ressources managé contient l’emplacement physique du compte de stockage (DBFS), travail-sg (groupe de sécurité réseau) travailleurs-réseau virtuel (réseau virtuel). Il est également l’emplacement où les machines virtuelles, disque, adresse IP et interface réseau seront créés. Ce groupe de ressources est verrouillé par défaut ; Toutefois lorsqu’un cluster est démarré dans le réseau virtuel, une Interface réseau est créée entre le réseau virtuel le travailleurs dans le groupe de ressources managé et le réseau virtuel « hub ».

    ![Groupe de ressources managé Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Créer un cluster

> [!NOTE]
> Pour utiliser un compte gratuit pour créer le cluster Azure Databricks, avant de créer le cluster, accédez à votre profil et définissez votre abonnement sur **paiement à l’utilisation**. Pour plus d’informations, consultez la page [Compte Azure gratuit](https://azure.microsoft.com/free/).

1. Revenez à votre service Azure Databricks et sélectionnez **lancer un espace de travail** sur le **vue d’ensemble** page.

2. Sélectionnez **Clusters** > **+ créer le Cluster**. Puis créez un nom de cluster, tels que *databricks-démarrage rapide-cluster*et acceptez les paramètres par défaut restantes. Sélectionnez **Créer un cluster**.

    ![Créer un cluster Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Une fois le cluster est en cours d’exécution, revenez au groupe de ressources managé dans le portail Azure. Notez les nouvelles machines virtuelles, disques, adresse IP et les interfaces réseau. Une interface réseau est créée dans chaque sous-réseau avec des adresses IP publiques et privées.  

    ![Groupe de ressources managé Azure Databricks après la création du cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Revenez à votre espace de travail Azure Databricks et sélectionnez le cluster que vous avez créé. Puis accédez à la **exécuteurs** onglet sur le **l’interface utilisateur Spark** page. Notez que les adresses pour le pilote et les exécuteurs sont dans la plage de sous-réseau privé. Dans cet exemple, le pilote est 10.179.0.6 et exécuteurs sont 10.179.0.4 et 10.179.0.5. Vos adresses IP peut être différents.

    ![Exécuteurs de l’interface utilisateur de Databricks Spark Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois l’article terminé, vous pouvez arrêter le cluster. Pour cela, dans l’espace de travail Azure Databricks, dans le volet gauche, sélectionnez **Clusters**. Pour le cluster que vous voulez arrêter, déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis sélectionnez l’icône **Arrêter**. Cela arrête le cluster.

Si vous n’arrêtez pas le cluster manuellement, il s’arrête automatiquement, à condition d’avoir coché la case **Arrêter après \_\_ minutes d’inactivité** durant la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

Si vous ne souhaitez pas réutiliser le cluster, vous pouvez supprimer le groupe de ressources que vous avez créé dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un cluster Spark dans Azure Databricks que vous avez déployé à un réseau virtuel. Passez à l’article suivant pour découvrir comment interroger un conteneur Docker SQL Server Linux dans le réseau virtuel à l’aide de JDBC à partir d’un bloc-notes Azure Databricks.  

> [!div class="nextstepaction"]
>[Interroger un conteneur Docker SQL Server Linux dans un réseau virtuel à partir d’un bloc-notes Azure Databricks](vnet-injection-sql-server.md)
