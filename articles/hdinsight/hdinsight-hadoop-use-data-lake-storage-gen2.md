---
title: Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight
description: Découvrez comment utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: hrasheed
ms.openlocfilehash: 40caabc08b08e4c9268bf60d588819ce81717986
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900269"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight

Azure Data Lake Storage Gen2 est un service de stockage cloud dédié à l’analytique de Big Data et intégré au Stockage Blob Azure. Data Lake Storage Gen2 réunit les capacités du Stockage Blob Azure et d’Azure Data Lake Storage Gen1. Ce nouveau service offre les fonctionnalités d’Azure Data Lake Storage Gen1, comme la sémantique des systèmes de fichiers, la sécurité au niveau du fichier/répertoire et l’extensibilité, en plus de celles du Stockage Blob Azure, comme le stockage hiérarchisé à faible coût, la haute disponibilité et la reprise d’activité après sinistre.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilité de Data Lake Storage Gen2

Data Lake Storage Gen2 est disponible comme option de stockage pour presque tous les types de clusters Azure HDInsight en tant que compte par défaut et compte de stockage supplémentaire. HBase, en revanche, ne peut avoir qu’un seul compte Data Lake Storage Gen2.

Pour une comparaison complète des options de création de cluster à l'aide de Data Lake Storage Gen2, consultez [Comparer les options de stockage à utiliser avec les clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Après avoir sélectionné Data Lake Storage Gen2 comme **type de stockage principal**, vous ne pouvez pas sélectionner un compte Data Lake Storage Gen1 comme stockage supplémentaire.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Créer un cluster avec Data Lake Storage Gen2 par le biais du portail Azure

Si vous souhaitez créer un cluster HDInsight qui utilise Data Lake Storage Gen2 comme stockage, effectuez les étapes suivantes pour configurer un compte Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Créez une identité managée affectée à l’utilisateur, si vous n’en avez pas encore. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le coin supérieur gauche, cliquez sur **Créer une ressource**.
1. Dans la zone de recherche, tapez **user assigned (utilisateur affecté)** , puis cliquez sur **Identité managée affectée par l'utilisateur**.
1. Cliquez sur **Créer**.
1. Entrez un nom pour votre identité managée, sélectionnez l'abonnement, le groupe de ressources et l'emplacement correspondants.
1. Cliquez sur **Créer**.

Pour plus d’informations sur le fonctionnement des identités managées dans Azure HDInsight, consultez [Identités managées dans Azure HDInsight](hdinsight-managed-identities.md).

![Créer une identité managée attribuée par l’utilisateur](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Créer un compte Data Lake Storage Gen2

Créez un compte de stockage Azure Data Lake Storage Gen2. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le coin supérieur gauche, cliquez sur **Créer une ressource**.
1. Dans la zone de recherche, tapez **storage (stockage)** , puis cliquez sur **Compte de stockage**.
1. Cliquez sur **Créer**.
1. Sur l'écran **Créer un compte de stockage** :
    1. Sélectionnez l’abonnement et le groupe de ressources correspondants.
    1. Entrez un nom pour votre compte Data Lake Storage Gen2. Pour plus d'informations sur les conventions d’affectation de noms des comptes de stockage, voir [Conventions d’affectation de noms pour les ressources Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#storage).
    1. Cliquez sur l’onglet **Avancé**.
    1. Cliquez sur **Activé** en regard de **Espace de noms hiérarchique** sous **Data Lake Storage Gen2**.
    1. Cliquez sur **Revoir + créer**.
    1. Cliquez sur **Créer**

Pour plus d'informations sur les autres options lors de la création d'un compte de stockage, voir [Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Capture d’écran montrant la création du compte de stockage dans le portail Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Définir les autorisations de l’identité managée sur le compte Data Lake Storage Gen2

Affectez l’identité managée au rôle **Propriétaire des données Blob du stockage** sur le compte de stockage.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
1. Sélectionnez votre compte de stockage, puis sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du compte. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.
    
    ![Capture d’écran montrant les paramètres de contrôle d’accès du stockage](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Sélectionnez le bouton **+ Ajouter une attribution de rôle** pour ajouter un nouveau rôle.
1. Dans la fenêtre **Ajouter une attribution de rôle**, sélectionnez le rôle **Propriétaire des données Blob du stockage**. Ensuite, sélectionnez l’abonnement qui contient le compte de stockage et l’identité managée. Ensuite, recherchez l’identité managée affectée par l’utilisateur que vous avez créée. Pour finir, sélectionnez l’identité managée pour qu’elle soit listée sous **Membres sélectionnés**.
    
    ![Capture d’écran montrant comment attribuer un rôle RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Sélectionnez **Enregistrer**. L’identité affectée par l’utilisateur que vous avez sélectionnée est maintenant listée sous le rôle sélectionné.
1. Une fois cette configuration initiale terminée, vous pouvez créer un cluster par le biais du portail. Le cluster doit être dans la même région Azure que le compte de stockage. Dans la section **Stockage** du menu de création du cluster, sélectionnez les options suivantes :
        
    * Comme **Type de stockage principal**, sélectionnez **Azure Data Lake Storage Gen2**.
    * Sous **Sélectionner un compte de stockage**, recherchez et sélectionnez le nouveau compte de stockage Data Lake Storage Gen2.
        
        ![Paramètres de stockage pour l’utilisation de Data Lake Storage Gen2 avec Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Sous **Identité**, sélectionnez l’abonnement approprié et la nouvelle identité managée affectée par l’utilisateur.
        
        ![Paramètres d’identité pour l’utilisation de Data Lake Storage Gen2 avec Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Pour ajouter un compte Data Lake Storage Gen2 secondaire, au niveau du compte de stockage, affectez simplement l’identité managée créée précédemment au nouveau compte de stockage Data Lake Storage Gen2 que vous souhaitez ajouter. Notez que l’ajout d’un compte Data Lake Storage Gen2 secondaire via le panneau « Comptes de stockage supplémentaires » dans HDInsight n’est pas pris en charge. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Créer un cluster avec Data Lake Storage Gen2 par le biais d’Azure CLI

Vous pouvez [télécharger un exemple de fichier de modèle](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) et [télécharger un exemple de fichier de paramètres](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Avant d’utiliser le modèle et l’extrait de code Azure CLI ci-dessous, remplacez les espaces réservés suivants par leurs valeurs correctes :

| Placeholder | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | L’ID de votre abonnement Azure |
| `<RESOURCEGROUPNAME>` | Le groupe de ressources dans lequel vous souhaitez créer le cluster et le compte de stockage. |
| `<MANAGEDIDENTITYNAME>` | Le nom de l’identité managée qui recevra les autorisations sur votre compte Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nouveau compte Azure Data Lake Storage Gen2 qui sera créé. |
| `<CLUSTERNAME>` | Nom de votre cluster HDInsight |
| `<PASSWORD>` | Le mot de passe que vous avez choisi pour vous connecter au cluster à l’aide de SSH et du tableau de bord Ambari. |

L’extrait de code ci-dessous effectue les étapes initiales suivantes :

1. Il établit la connexion à votre compte Azure.
1. Il définit l’abonnement actif où les opérations de création seront effectuées.
1. Il crée un groupe de ressources pour les nouvelles activités de déploiement. 
1. Il crée une identité managée attribuée par l’utilisateur.
1. Il ajoute une extension à Azure CLI pour utiliser les fonctionnalités de Data Lake Storage Gen2.
1. Il crée un compte Data Lake Storage Gen2 à l’aide de `--hierarchical-namespace true`l’indicateur. 

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Ensuite, connectez-vous au portail. Ajoutez la nouvelle identité managée affectée par l’utilisateur au rôle **Contributeur aux données Blob du stockage** dans le compte de stockage, comme décrit à l’étape 3 sous [Utilisation du portail Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Une fois que vous avez défini le rôle de l’identité managée affectée par l’utilisateur, déployez le modèle en utilisant l’extrait de code suivant.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Contrôle d’accès à Data Lake Storage Gen2 dans HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quels sont les types d’autorisation pris en charge par Data Lake Storage Gen2 ?

Data Lake Storage Gen2 utilise un modèle de contrôle d’accès qui prend en charge le contrôle d’accès en fonction du rôle (RBAC) ainsi que les listes de contrôle d’accès (ACL) POSIX. Data Lake Storage Gen1 prend en charge les listes de contrôle d’accès uniquement pour contrôler l’accès aux données.

RBAC utilise des attributions de rôles pour appliquer efficacement des ensembles d’autorisations aux utilisateurs, groupes et principaux de service des ressources Azure. En règle générale, ces ressources Azure sont limitées aux ressources de niveau supérieur (par exemple, les comptes de stockage Azure). Pour le stockage Azure, et aussi Data Lake Storage Gen2, ce mécanisme a été étendu à la ressource du système de fichiers.

 Pour plus d’informations sur les autorisations de fichiers avec RBAC, consultez [Contrôle d’accès en fonction du rôle Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Pour plus d’informations sur les autorisations de fichiers avec des listes ACL, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Comment contrôler l’accès à mes données dans Data Lake Storage Gen2 ?

La capacité de votre cluster HDInsight à accéder aux fichiers dans Data Lake Storage Gen2 est contrôlée par le biais d’identités managées. Une identité managée est une identité inscrite dans Azure Active Directory (Azure AD) dont les informations d’identification sont gérées par Azure. Avec les identités managées, vous n’avez pas besoin d’inscrire des principaux de service dans Azure AD, ni de tenir à jour les informations d’identification telles que les certificats.

Les services Azure utilisent deux types d’identités managées : celles affectées par le système et celles affectées par l’utilisateur. HDInsight utilise des identités managées affectées par l’utilisateur pour accéder à Data Lake Storage Gen2. Une identité managée affectée par l’utilisateur est créée en tant que ressource Azure autonome. Via un processus de création, Azure crée une identité dans le locataire Azure AD approuvé par l’abonnement en cours d’utilisation. Une fois l’identité créée, elle peut être affectée à une ou plusieurs instances de service Azure.

Le cycle de vie d’une identité attribuée par l’utilisateur est géré séparément du cycle de vie des instances de service Azure auxquelles elle est affectée. Pour plus d’informations sur les identités managées, consultez [Comment fonctionnent les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Comment définir des autorisations pour que les utilisateurs Azure AD puissent interroger des données dans Data Lake Storage Gen2 avec Hive ou d’autres services ?

Pour définir des autorisations pour que les utilisateurs puissent interroger des données, utilisez les groupes de sécurité Azure AD comme principal affecté dans les listes de contrôle d’accès. N’attribuez pas directement des autorisations d’accès aux fichiers à des utilisateurs ou principaux de service individuels. Quand vous utilisez des groupes de sécurité Azure AD pour contrôler le flux d’autorisations, vous pouvez ajouter et supprimer des utilisateurs ou des principaux de service sans avoir à réappliquer les listes de contrôle d’accès à une structure de répertoires entière. Il vous suffit d’ajouter ou de supprimer les utilisateurs du groupe de sécurité Azure AD approprié. Les listes de contrôle d’accès ne sont pas héritées. Par conséquent, pour les réappliquer, vous devez les mettre à jour dans chaque fichier et sous-répertoire.

## <a name="next-steps"></a>Étapes suivantes

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Présentation d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
