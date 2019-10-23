---
title: Créer un espace de travail Azure Databricks dans un réseau virtuel
description: Cet article explique comment déployer Azure Databricks sur votre réseau virtuel.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 69afe2aab3c10707f7160d727b970ad73d59a952
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791559"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Démarrage rapide : Créer un espace de travail Azure Databricks dans un réseau virtuel

Ce démarrage rapide montre comment créer un espace de travail Azure Databricks dans un réseau virtuel. Vous allez également créer un cluster Apache Spark au sein de cet espace de travail.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

> [!Note]
> Ce didacticiel ne peut pas être suivi avec un **abonnement d’essai gratuit Azure**.
> Si vous avez un compte gratuit, accédez à votre profil et modifiez votre abonnement sur **Paiement à l’utilisation**. Pour plus d’informations, consultez la page [Compte Azure gratuit](https://azure.microsoft.com/free/). Ensuite, [supprimez la limite de dépense](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), et [demandez une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour les processeurs virtuels dans votre région. Lorsque vous créez votre espace de travail Azure Databricks, vous pouvez sélectionner le tarif **Version d’évaluation (Premium - 14 jours de DBU offerts)** pour donner à l’accès de l’espace de travail un accès gratuit aux DBU d’Azure Databricks pendant 14 jours.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.

2. Sous **Créer un réseau virtuel**, appliquez les paramètres suivants : 

    |Paramètre|Valeur suggérée|Description|
    |-------|---------------|-----------|
    |Nom|databricks-quickstart|Sélectionnez un nom pour votre réseau virtuel.|
    |Espace d’adressage|10.1.0.0/16|Plage d’adresses du réseau virtuel en notation CIDR.|
    |Subscription|\<Votre abonnement\>|Sélectionnez l’abonnement Azure que vous souhaitez utiliser.|
    |Resource group|databricks-quickstart|Sélectionnez **Créer** et saisissez le nom du nouveau groupe de ressources pour votre compte.|
    |Location|\<Sélectionnez la région la plus proche de vos utilisateurs\>|Sélectionnez l’emplacement géographique où vous pouvez héberger votre réseau virtuel. Utilisez l’emplacement le plus proche de vos utilisateurs.|
    |Nom du sous-réseau|default|Sélectionnez un nom pour le sous-réseau par défaut de votre réseau virtuel.|
    |Plage d’adresses de sous-réseau|10.1.0.0/24|Plage d’adresses du sous-réseau en notation CIDR. Elle doit être contenue dans l’espace d’adressage du réseau virtuel. La plage d’adresses d’un sous-réseau qui est en cours d’utilisation ne peut pas être modifiée.|

    ![Créer un réseau virtuel dans le portail Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Une fois le déploiement terminé, accédez à votre réseau virtuel et sélectionnez **Espace d’adressage** sous **Paramètres**. Dans la zone intitulée *Ajouter une plage d’adresses supplémentaires*, insérez `10.179.0.0/16` et sélectionnez **Enregistrer**.

    ![Espace d’adressage de réseau virtuel Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Analytics** > **Databricks**.

2. Sous **Service Azure Databricks**, appliquez les paramètres suivants :

    |Paramètre|Valeur suggérée|Description|
    |-------|---------------|-----------|
    |Nom de l’espace de travail|databricks-quickstart|Sélectionnez un nom pour votre espace de travail Azure Databricks.|
    |Subscription|\<Votre abonnement\>|Sélectionnez l’abonnement Azure que vous souhaitez utiliser.|
    |Resource group|databricks-quickstart|Sélectionnez le même groupe de ressources que celui que vous avez utilisé pour le réseau virtuel.|
    |Location|\<Sélectionnez la région la plus proche de vos utilisateurs\>|Choisissez le même emplacement que pour votre réseau virtuel.|
    |Niveau de tarification|Choisissez entre Standard ou Premium.|Pour plus d’informations sur les niveaux de tarification, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Déployer l’espace de travail Azure Databricks dans votre réseau virtuel|OUI|Ce paramètre vous permet de déployer un espace de travail Azure Databricks dans votre réseau virtuel.|
    |Réseau virtuel|databricks-quickstart|Sélectionnez le réseau virtuel créé dans la section précédente.|
    |Nom du sous-réseau public|public-subnet|Utilisez le nom de sous-réseau public par défaut.|
    |Plage CIDR du sous-réseau public|10.179.64.0/18|La plage CIDR pour ce sous-réseau doit être comprise entre /18 et /26.|
    |Nom du sous-réseau privé|private-subnet|Utilisez le nom de sous-réseau privé par défaut.|
    |Plage CIDR du sous-réseau privé|10.179.0.0/18|La plage CIDR pour ce sous-réseau doit être comprise entre /18 et /26.|

    ![Créer un espace de travail Azure Databricks dans le portail Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Une fois le déploiement terminé, accédez à la ressource Azure Databricks. Notez que le peering de réseau virtuel est désactivé. Notez également le groupe de ressources et le groupe de ressources managé dans la page de présentation. 

    ![Présentation d’Azure Databricks dans le portail Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Le groupe de ressources managé contient l’emplacement physique du compte de stockage (DBFS), worker-sg (groupe de sécurité réseau) workers-vnet (réseau virtuel). C’est également l’emplacement de création des machines virtuelles, du disque, de l’adresse IP et de l’interface réseau. Ce groupe de ressources est verrouillé par défaut. Toutefois lorsqu’un cluster est démarré dans le réseau virtuel, une interface réseau est créée entre workers-vnet dans le groupe de ressources managé et le réseau virtuel « hub ».

    ![Groupe de ressources managé Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Créer un cluster

> [!NOTE]
> Pour utiliser un compte gratuit pour créer le cluster Azure Databricks, avant de créer le cluster, accédez à votre profil et définissez votre abonnement sur **paiement à l’utilisation**. Pour plus d’informations, consultez la page [Compte Azure gratuit](https://azure.microsoft.com/free/).

1. Revenez à votre service Azure Databricks et sélectionnez **Lancer l’espace de travail** sur la page **Vue d’ensemble**.

2. Sélectionnez **Clusters** >  **+ Créer un cluster**. Puis créez un nom de cluster, comme *databricks-quickstart-cluster*et acceptez les autres paramètres par défaut. Sélectionnez **Créer un cluster**.

    ![Créer un cluster Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Une fois que le cluster est en cours d’exécution, revenez au groupe de ressources managé dans le portail Azure. Notez les nouveaux éléments : machines virtuelles, disques, adresse IP et interfaces réseau. Une interface réseau est créée dans les sous-réseaux public et privé avec des adresses IP.  

    ![Groupe de ressources managé Azure Databricks après la création du cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Revenez à votre espace de travail Azure Databricks et sélectionnez le cluster que vous avez créé. Puis accédez à l’onglet **Exécuteurs** sur la page **Interface utilisateur Spark**. Notez que les adresses pour le pilote et les exécuteurs sont dans la plage du sous-réseau privé. Dans cet exemple, l’adresse du pilote est 10.179.0.6 et les adresses des exécuteurs sont 10.179.0.4 et 10.179.0.5. Vos adresses IP peuvent être différentes.

    ![Exécuteurs IU Spark Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois l’article terminé, vous pouvez arrêter le cluster. Pour cela, dans l’espace de travail Azure Databricks, dans le volet gauche, sélectionnez **Clusters**. Pour le cluster que vous voulez arrêter, déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis sélectionnez l’icône **Arrêter**. Cela arrête le cluster.

Si vous n’arrêtez pas le cluster manuellement, il s’arrête automatiquement, à condition d’avoir coché la case **Arrêter après \_\_ minutes d’inactivité** durant la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

Si vous ne souhaitez pas réutiliser le cluster, vous pouvez supprimer le groupe de ressources que vous avez créé dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un cluster Spark dans Azure Databricks que vous avez déployé sur un réseau virtuel. Passez à l’article suivant pour découvrir comment interroger un conteneur Docker SQL Server Linux dans le réseau virtuel avec JDBC à partir d’un bloc-notes Azure Databricks.  

> [!div class="nextstepaction"]
>[Interroger un conteneur Docker SQL Server Linux dans un réseau virtuel à partir d’un bloc-notes Azure Databricks](vnet-injection-sql-server.md)
