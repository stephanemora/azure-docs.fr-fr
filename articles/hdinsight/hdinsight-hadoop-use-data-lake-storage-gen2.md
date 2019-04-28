---
title: Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight
description: Découvrez comment utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 6452e73b2429ab89a466b1c3b59cc892eca31205
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766928"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight

Azure Data Lake Storage Gen2 est un service de stockage cloud dédié à l’analytique de big data, basée sur le stockage Blob Azure. Data Lake Storage Gen2 combine les fonctionnalités du stockage Blob Azure et Azure Data Lake Storage Gen1. Le service qui en résulte offre des fonctionnalités à partir d’Azure Data Lake Storage Gen1, telles que la sémantique du système de fichiers, la sécurité au niveau du répertoire et au niveau du fichier et l’évolutivité, ainsi que le stockage hiérarchisé économique, haute disponibilité et de reprise après sinistre à partir du stockage d’objets Blob Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilité de Data Lake Storage Gen2

Data Lake Storage Gen2 est disponible comme option de stockage pour presque tous les types de cluster Azure HDInsight en tant que valeur par défaut et un compte de stockage supplémentaire. HBase, cependant, peut avoir qu’un seul compte Data Lake Storage Gen2.

> [!Note] 
> Une fois que vous sélectionnez Data Lake Storage Gen2 comme votre **type de stockage principal**, vous ne pouvez pas sélectionner un compte Data Lake Storage Gen1 comme stockage supplémentaire.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Créer un cluster avec Data Lake Storage Gen2 via le portail Azure

Pour créer un cluster HDInsight qui utilise le stockage Data Lake Gen2 pour le stockage, suivez ces étapes pour configurer un compte Data Lake Storage Gen2.

### <a name="create-a-user-managed-identity"></a>Créer une identité d’utilisateur géré

Créez une identité managée affectée à l’utilisateur, si vous n’en avez pas encore. Consultez [Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Pour plus d’informations sur la gestion des identités dans Azure HDInsight, consultez [gérés des identités dans Azure HDInsight](hdinsight-managed-identities.md).

![Créer une identité managée attribuée par l’utilisateur](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Créer un compte Data Lake Storage Gen2

Créez un compte de stockage Azure Data Lake Storage Gen2. Assurez-vous que le **espace de noms hiérarchique** est activée. Pour plus d’informations, consultez [Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Capture d’écran montrant la création du compte de stockage dans le portail Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurer des autorisations pour l’identité gérée sur le compte Data Lake Storage Gen2

Affecter l’identité gérée pour le **propriétaire des données de stockage Blob** rôle sur le compte de stockage. Pour plus d’informations, consultez l’article [Gérer les droits d’accès aux données d’objet blob et de file d’attente Azure avec RBAC (préversion)](../storage/common/storage-auth-aad-rbac.md).

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
1. Sélectionnez votre compte de stockage, puis sélectionnez **contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès pour le compte. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.
    
    ![Capture d’écran montrant les paramètres de contrôle d’accès du stockage](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Sélectionnez le **+ ajouter une attribution de rôle** pour ajouter un nouveau rôle.
1. Dans le **ajouter une attribution de rôle** fenêtre, sélectionnez le **propriétaire des données de stockage Blob** rôle. Ensuite, sélectionnez l’abonnement qui contient le compte de stockage et l’identité managée. Ensuite, recherchez l’identité managée affectée par l’utilisateur que vous avez créée. Enfin, sélectionnez l’identité gérée, et il sera répertorié sous **membres sélectionnés**.
    
    ![Capture d’écran montrant comment attribuer un rôle RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Sélectionnez **Enregistrer**. L’identité affectée à l’utilisateur que vous avez sélectionné est maintenant répertoriée sous le rôle sélectionné.
1. Une fois cette configuration initiale terminée, vous pouvez créer un cluster par le biais du portail. Le cluster doit être dans la même région Azure que le compte de stockage. Dans la section **Stockage** du menu de création du cluster, sélectionnez les options suivantes :
        
    * Pour **type de stockage principal**, sélectionnez **Azure Data Lake Storage Gen2**.
    * Sous **sélectionner un compte de stockage**, recherchez et sélectionnez le compte de stockage Data Lake Storage Gen2 nouvellement créé.
        
        ![Paramètres de stockage pour l’utilisation de Data Lake Storage Gen2 avec Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Sous **identité**nouvellement créé affectée à l’utilisateur et sélectionnez l’abonnement approprié identité gérée.
        
        ![Paramètres d’identité pour l’utilisation de Data Lake Storage Gen2 avec Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Pour ajouter un compte Data Lake Storage Gen2 secondaire, au niveau du compte de stockage, suffit d’attribuer l’identité gérée créée précédemment dans le nouveau compte de stockage Data Lake Storage Gen2 que vous souhaitez ajouter. Veuillez noter que l’ajout d’un compte Data Lake Storage Gen2 secondaire via le panneau « comptes de stockage supplémentaire » sur HDInsight n’est pas pris en charge. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Créer un cluster avec Data Lake Storage Gen2 via l’interface CLI Azure

Vous pouvez [télécharger un exemple de fichier de modèle](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) et [télécharger un exemple de fichier de paramètres](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Avant d’utiliser le modèle, remplacez la chaîne `<SUBSCRIPTION_ID>` avec votre ID d’abonnement Azure réel. En outre, remplacez la chaîne `<PASSWORD>` avec votre mot de passe choisi pour définir à la fois le mot de passe que vous allez utiliser pour se connecter à votre cluster et le mot de passe SSH.

L’extrait de code ci-dessous effectue les étapes préliminaires suivantes :

1. Se connecte à votre compte Azure.
1. Définit l’abonnement actif dans lequel les opérations de création seront effectuées.
1. Crée un nouveau groupe de ressources pour les nouvelles activités de déploiement nommé `hdinsight-deployment-rg`.
1. Crée une identité gérée affectée à l’utilisateur nommée `test-hdinsight-msi`.
1. Ajoute une extension à l’interface CLI à utiliser les fonctionnalités pour le stockage Data Lake Gen2.
1. Crée un nouveau compte Data Lake Storage Gen2 nommé `hdinsightadlsgen2`, à l’aide de la `--hierarchical-namespace true` indicateur.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Ensuite, connectez-vous au portail. Ajouter la nouvelle identité managée affectée à l’utilisateur pour le **contributeur aux données stockage Blob** rôle sur le compte de stockage, comme décrit à l’étape 3 sous [à l’aide du portail Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Une fois que vous avez affecté le rôle pour l’identité gérée affectée à l’utilisateur, déployez le modèle à l’aide de l’extrait de code suivant.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Contrôle d’accès pour Data Lake Storage Gen2 dans HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quels sont les types d’autorisation pris en charge par Data Lake Storage Gen2 ?

Data Lake Storage Gen2 utilise un modèle de contrôle d’accès qui prend en charge le contrôle d’accès en fonction du rôle (RBAC) et de listes de contrôle d’accès POSIX (ACL). Prend en charge de la génération 1 de stockage Data Lake contrôle d’accès uniquement pour contrôler l’accès aux données.

RBAC utilise les attributions de rôles à appliquer efficacement des ensembles d’autorisations aux utilisateurs, groupes et principaux de service pour les ressources Azure. En règle générale, ces ressources Azure sont limitées aux ressources de niveau supérieur (par exemple, les comptes de stockage Azure). Pour stockage Azure et également le stockage Data Lake Gen2, ce mécanisme a été étendu dans la ressource de système de fichiers.

 Pour plus d’informations sur les autorisations de fichiers avec RBAC, consultez [contrôle d’accès en fonction du rôle Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Pour plus d’informations sur les autorisations de fichiers avec des listes ACL, consultez [listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Comment contrôler l’accès à mes données dans Data Lake Storage Gen2 ?

Capacité de votre cluster HDInsight pour accéder aux fichiers dans Data Lake Storage Gen2 est contrôlée par le biais des identités gérées. Une identité gérée est une identité inscrite dans Azure Active Directory (Azure AD) dont informations d’identification sont gérées par Azure. Avec des identités gérées, vous n’avez pas besoin d’inscrire des principaux de service dans Azure AD ou mettre à jour les informations d’identification telles que les certificats.

Services Azure ont deux types d’identités gérées : attribué par le système et affectée à l’utilisateur. HDInsight utilise des identités gérées affectée à l’utilisateur pour l’accès Data Lake Storage Gen2. Une identité managée affectée par l’utilisateur est créée en tant que ressource Azure autonome. Via un processus de création, Azure crée une identité dans le locataire Azure AD approuvé par l’abonnement en cours d’utilisation. Une fois l’identité créée, elle peut être affectée à une ou plusieurs instances de service Azure.

Le cycle de vie d’une identité attribuée par l’utilisateur est géré séparément du cycle de vie des instances de service Azure auxquelles elle est affectée. Pour plus d’informations sur les identités, consultez [comment... : les identités gérées pour le travail de ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Comment définir des autorisations pour utilisateurs Azure AD pour interroger des données dans Data Lake Storage Gen2 à l’aide de Hive ou autres services ?

Pour définir des autorisations pour les utilisateurs pour interroger des données, utilisez les groupes de sécurité Azure AD comme principal affecté dans les listes ACL. N’affectez directement les autorisations d’accès au fichier à des utilisateurs individuels ou des principaux du service. Lorsque vous utilisez des groupes de sécurité Azure AD pour contrôler le flux d’autorisations, vous pouvez ajouter et supprimer des utilisateurs ou principaux du service sans la réapplication des ACL à une structure de répertoire entier. Il vous suffit d’ajouter ou de supprimer les utilisateurs du groupe de sécurité Azure AD approprié. ACL ne sont pas héritées, donc de la réapplication des ACL nécessite la mise à jour de l’ACL sur chaque fichier et chaque sous-répertoire.

## <a name="next-steps"></a>Étapes suivantes

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Présentation d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
