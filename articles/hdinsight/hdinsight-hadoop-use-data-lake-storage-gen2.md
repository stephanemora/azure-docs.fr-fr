---
title: Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight
description: Découvrez comment utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: 1d43c7b6dd1bdec0a2507d8ce1a3883f5ce31a39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479396"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight

Azure Data Lake Storage (Data Lake Storage) Gen2 est un ensemble de fonctionnalités dédié à l’analytique du Big Data qui s’appuie sur Stockage Blob Azure. Data Lake Storage Gen2 combine les fonctionnalités de Stockage Blob Azure et d’Azure Data Lake Storage Gen1. Vous bénéficiez ainsi d’un service qui offre les fonctionnalités d’Azure Data Lake Storage Gen1, comme la sémantique des systèmes de fichiers, les répertoires, la sécurité au niveau du fichier et la mise à l’échelle, en plus de celles de Stockage Blob Azure, comme le stockage hiérarchisé à bas coût et la haute disponibilité/reprise d’activité après sinistre.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilité de Data Lake Storage Gen2

Azure Data Lake Storage Gen2 est disponible comme option de stockage pour presque tous les types de clusters Azure HDInsight en tant que compte par défaut et compte de stockage supplémentaire. HBase, en revanche, ne peut avoir qu’un seul compte Data Lake Storage Gen2.

> [!Note] 
> Une fois que vous avez sélectionné Data Lake Storage Gen2 comme **type de stockage principal**, vous ne pouvez pas sélectionner un compte Data Lake Storage Gen1 comme stockage supplémentaire.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Création d’un cluster HDInsight avec Data Lake Storage Gen2

Pour créer un cluster HDInsight qui utilise le stockage Data Lake Gen2 pour le stockage, effectuez les étapes suivantes afin de créer un compte Data Lake Storage Gen2 configuré correctement.

1. Créez une identité managée affectée à l’utilisateur, si vous n’en avez pas encore. Consultez [Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](/../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity.md).

    ![Créer une identité managée attribuée par l’utilisateur](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Créez un compte de stockage Azure Data Lake Storage Gen2. Vérifiez que l’option **Système hiérarchique de fichiers** est activée. Consultez [Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2](/../storage/blobs/data-lake-storage-quickstart-create-account.md) pour plus de détails.

    ![Capture d’écran montrant la création du compte de stockage dans le portail Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. Affectez l’identité managée au rôle **Contributeur aux données Blob du stockage (préversion)** sur le compte de stockage. Consultez [Gérer les droits d’accès aux données d’objet blob et de file d’attente Azure avec RBAC (préversion)](/../storage/common/storage-auth-aad-rbac#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal.md).

    1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
    1. Sélectionnez votre compte de stockage, puis sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du compte. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.
    
        ![Capture d’écran montrant les paramètres de contrôle d’accès du stockage](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Cliquez sur le bouton **Ajouter une attribution de rôle** pour ajouter un nouveau rôle.
    1. Dans la fenêtre **Ajouter une attribution de rôle**, sélectionnez le rôle **Contributeur aux données Blob du stockage (préversion)**. Ensuite, sélectionnez l’abonnement qui contient le compte de stockage et l’identité managée. Ensuite, recherchez l’identité managée affectée par l’utilisateur que vous avez créée. Pour finir, sélectionnez l’identité managée pour qu’elle soit listée sous **Membres sélectionnés**.
    
        ![Capture d’écran montrant comment attribuer un rôle RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. Cliquez sur **Enregistrer**. L’identité affectée par l’utilisateur que vous avez sélectionnée est maintenant listée sous le rôle **Contributeur**.

    1. Une fois cette configuration initiale terminée, vous pouvez créer un cluster par le biais du portail. Le cluster doit être dans la même région Azure que le compte de stockage. Dans la section **Stockage** du menu de création du cluster, sélectionnez les options suivantes :
        
        * Pour **Type de stockage principal**, cliquez sur **Azure Data Lake Storage Gen2**.
        * Sous **Sélectionner un compte de stockage**, recherchez et sélectionnez le nouveau compte de stockage Data Lake Storage Gen2.
        
            ![Paramètres de stockage pour l’utilisation de Data Lake Storage Gen2 avec Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * Sous **Identité** sélectionnez l’abonnement approprié et la nouvelle identité managée affectée par l’utilisateur.
        
            ![Paramètres d’identité pour l’utilisation de Data Lake Storage Gen2 avec Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Contrôle d’accès pour Data Lake Storage Gen2 dans HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quels sont les types d’autorisation pris en charge par Data Lake Storage Gen2 ?

Azure Data Lake Storage Gen2 implémente un modèle de contrôle d’accès qui prend en charge le contrôle d’accès en fonction du rôle (RBAC) Azure et les listes de contrôle d’accès (ACL) POSIX. Data Lake Storage Gen1 prenait uniquement en charge les listes de contrôle d’accès pour contrôler l’accès aux données.

Le contrôle d’accès en fonction du rôle (RBAC) Azure utilise des attributions de rôles pour appliquer efficacement des ensembles d’autorisations sur les utilisateurs, groupes et principaux de service des ressources Azure. En règle générale, ces ressources Azure sont limitées aux ressources de niveau supérieur (par exemple les comptes Stockage Azure). Pour Stockage Azure, et aussi Azure Data Lake Storage Gen2, ce mécanisme a été étendu à la ressource du système de fichiers.

 Pour plus d’informations sur les autorisations de fichiers avec RBAC, consultez [Contrôle d’accès en fonction du rôle Azure (RBAC)](/../storage/blobs/data-lake-storage-access-control#azure-role-based-access-control-rbac.md).

Pour plus d’informations sur les autorisations de fichiers avec des listes ACL, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](/../storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories.md).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Comment contrôler l’accès à mes données dans Gen2 ?

La capacité de votre cluster HDInsight à accéder aux fichiers dans Data Lake Storage Gen2 est contrôlée par le biais d’identités managées. Une identité managée est une identité inscrite dans Azure AD dont les informations d’identification sont gérées par Azure. Vous n’avez pas besoin d’inscrire des principaux de service dans Azure AD, ni de tenir à jour les informations d’identification telles que les certificats.

Il existe deux types d’identités managées pour les services Azure : celles affectées par le système et celles affectées par l’utilisateur. Azure HDInsight utilise des identités managées affectées par l’utilisateur pour accéder à Azure Data Lake Storage Gen2. Une identité managée affectée par l’utilisateur est créée en tant que ressource Azure autonome. Via un processus de création, Azure crée une identité dans le locataire Azure AD approuvé par l’abonnement en cours d’utilisation. Une fois l’identité créée, elle peut être affectée à une ou plusieurs instances de service Azure. Le cycle de vie d’une identité attribuée par l’utilisateur est géré séparément du cycle de vie des instances de service Azure auxquelles elle est affectée. Pour plus d’informations sur les identités managées, consultez [Comment fonctionnent les identités managées pour les ressources Azure ?](/../active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Comment définir des autorisations pour que les utilisateurs Azure AD puissent interroger des données dans Data Lake Storage Gen2 à l’aide de Hive ou d’autres services ?

Utilisez les groupes de sécurité Azure AD comme principal affecté dans les listes ACL. N’affectez pas directement des utilisateurs ou des principaux de service individuels avec des autorisations d’accès aux fichiers. Quand vous utilisez des groupes de sécurité AD pour contrôler le flux d’autorisations, vous pouvez ajouter et supprimer des utilisateurs ou des principaux de service sans réappliquer des listes ACL à une structure de répertoires entière. Il vous suffit d’ajouter ou de supprimer les utilisateurs du groupe de sécurité Azure AD approprié. Les listes ACL ne sont pas héritées. Par conséquent, leur réapplication nécessite de les mettre à jour sur chaque fichier et sous-répertoire.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser Azure Data Lake Storage Gen2 Preview avec des clusters Azure HDInsight](/../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Présentation d’Azure Data Lake Storage Gen2 (préversion)](/../storage/blobs/data-lake-storage-introduction.md)