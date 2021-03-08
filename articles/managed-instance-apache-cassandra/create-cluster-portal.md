---
title: 'Démarrage rapide : créer un cluster Azure Managed Instance pour Apache Cassandra à partir du portail Azure'
description: Ce démarrage rapide vous montre comment créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide du portail Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: a05769c66c4b13de5c7197ef5612d64781574987
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747676"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Démarrage rapide : créer un cluster Azure Managed Instance pour Apache Cassandra à partir du portail Azure (préversion)
 
Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés, ce qui permet d’accélérer les scénarios hybrides et de réduire la maintenance continue.

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce démarrage rapide montre comment utiliser le portail Azure pour créer un cluster Azure Managed Instance pour Apache Cassandra.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Créer un cluster Manage Instance

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Dans la barre de recherche, recherchez **Managed Instance pour Apache Cassandra** et sélectionnez le résultat.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Recherchez Managed Instance pour Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Sélectionnez le bouton **Créer un cluster Managed Instance pour Apache Cassandra**.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Créez le cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. Dans le volet **Créer Managed Instance pour Apache Cassandra**, entrez les informations suivantes :

   * **Abonnement** : sélectionnez votre abonnement Azure dans la liste déroulante.
   * **Groupe de ressources** : indiquez si vous souhaitez créer un groupe de ressources ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez l’article de présentation [Groupes de ressources Azure](../azure-resource-manager/management/overview.md).
   * **Nom de cluster** : entrez un nom pour votre cluster.
   * **Emplacement** : emplacement où votre cluster sera déployé.
   * **Référence SKU** : type de référence SKU de votre cluster.
   * **Nombre de nœuds** : nombre de nœuds dans un cluster. Ces nœuds jouent le rôle de réplicas pour vos données.
   * **Mot de passe d’administrateur Cassandra initial** : mot de passe utilisé pour créer le cluster.
   * **Confirmer le mot de passe d’administrateur Cassandra** : entrez à nouveau votre mot de passe.

    > [!NOTE]
    > Durant la préversion publique, vous pouvez créer le cluster Managed Instance dans les régions suivantes : *USA Est, USA Ouest, USA Est 2, USA Ouest 2, USA Centre, USA Centre Sud, Europe Nord, Europe Ouest, Asie Sud-Est et Australie Est*.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Remplissez le formulaire de création du cluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Ensuite, sélectionnez l’onglet **Réseau**.

1. Dans le volet **Réseau**, choisissez le nom du **Réseau virtuel** et le **Sous-réseau**. Vous pouvez sélectionner un réseau virtuel existant ou en créer un.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Configurez les détails de la mise en réseau." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Si vous avez créé un nouveau réseau virtuel à l’étape précédente, passez à l’étape 9. Si vous avez sélectionné un réseau virtuel existant, avant de créer votre cluster, vous devez appliquer des autorisations spéciales au réseau virtuel et au sous-réseau. Pour ce faire, vous devez obtenir l’ID de ressource de votre réseau virtuel existant. Exécutez la commande suivante dans [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) :

   ```azurecli-interactive
   # get the resource ID of the Virtual Network
   az network vnet show -n <VNet_name> -g <Resource_Group_Name> --query "id" --output tsv

1. Now apply the special permissions by using the `az role assignment create` command. Replace `<Resource ID>` with the output of previous command:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```

   > [!NOTE]
   > Les valeurs `assignee` et `role` dans la commande précédente sont des identificateurs fixes de principal de service et de rôle, respectivement.

1. Maintenant que vous avez terminé la mise en réseau, cliquez sur **Vérifier + créer** > **Créer**

    > [!NOTE]
    > La création du cluster peut prendre jusqu’à 15 minutes.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Passez en revue le résumé pour créer le cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Une fois le déploiement terminé, vérifiez votre groupe de ressources pour voir le cluster Manage Instance nouvellement créé :

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Page de vue d’ensemble après la création du cluster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Pour parcourir les nœuds du cluster, accédez au volet Réseau virtuel que vous avez utilisé pour créer le cluster et ouvrez le volet **Vue d’ensemble** pour les afficher :

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Affichez les ressources du cluster." lightbox="./media/create-cluster-portal/resources.png" border="true":::



## <a name="connecting-to-your-cluster"></a>Connexion à votre cluster

Azure Managed Instance pour Apache Cassandra ne crée pas de nœuds avec des adresses IP publiques. Donc, pour vous connecter à votre cluster Cassandra nouvellement créé, vous devez créer une autre ressource dans le réseau virtuel. Il peut s’agir d’une application ou d’une machine virtuelle sur laquelle [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html), l’outil de requête open source d’Apache, est installé. Vous pouvez utiliser un [modèle](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) pour déployer une machine virtuelle Ubuntu. Une fois la machine déployée, utilisez SSH pour vous y connecter et installez CQLSH à l’aide des commandes ci-dessous :

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser ce cluster Managed Instance, supprimez-le en effectuant les étapes suivantes :

1. Dans le menu de gauche du portail Azure, sélectionnez **Groupes de ressources**.
1. Dans la liste, sélectionnez le groupe de ressources créé pour ce guide de démarrage rapide.
1. Dans le volet **Vue d’ensemble** du groupe de ressources, sélectionnez **Supprimer un groupe de ressources**.
1. Dans la fenêtre suivante, entrez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide du portail Azure. Vous pouvez maintenant commencer à utiliser le cluster :

> [!div class="nextstepaction"]
> [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
