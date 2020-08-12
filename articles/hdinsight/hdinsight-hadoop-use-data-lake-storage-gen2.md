---
title: Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight
description: Découvrez comment utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 2992324a1080b75a98264958f56ea28e93b54651
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534581"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight

Azure Data Lake Storage Gen2 est un service de stockage cloud dédié à l’analytique de Big Data et intégré au Stockage Blob Azure. Data Lake Storage Gen2 réunit les capacités du Stockage Blob Azure et d’Azure Data Lake Storage Gen1. Le service qui en résulte offre des fonctionnalités d’Azure Data Lake Storage Gen1. Ces fonctionnalités incluent : la sémantique du système de fichiers, la sécurité au niveau des répertoires et des fichiers, ainsi que l’adaptabilité. En ce qui concerne les fonctionnalités à faible coût du stockage Blob Azure, elles comprennent le stockage hiérarchisé, la haute disponibilité et la récupération d’urgence.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilité de Data Lake Storage Gen2

Data Lake Storage Gen2 est disponible comme option de stockage pour presque tous les types de clusters Azure HDInsight en tant que compte par défaut et compte de stockage supplémentaire. HBase, en revanche, ne peut avoir qu’un seul compte Data Lake Storage Gen2.

Pour une comparaison complète des options de création de cluster à l'aide de Data Lake Storage Gen2, consultez [Comparer les options de stockage à utiliser avec les clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Après avoir sélectionné Data Lake Storage Gen2 comme **type de stockage principal**, vous ne pouvez pas sélectionner un compte Data Lake Storage Gen1 comme stockage supplémentaire.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Créer un cluster avec Data Lake Storage Gen2 par le biais du portail Azure

Si vous souhaitez créer un cluster HDInsight qui utilise Data Lake Storage Gen2 comme stockage, effectuez les étapes suivantes pour configurer un compte Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Créez une identité managée affectée à l’utilisateur, si vous n’en avez pas encore.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le coin supérieur gauche, cliquez sur **Créer une ressource**.
1. Dans la zone de recherche, tapez **user assigned (utilisateur affecté)** , puis cliquez sur **Identité managée affectée par l'utilisateur**.
1. Cliquez sur **Créer**.
1. Entrez un nom pour votre identité managée, sélectionnez l'abonnement, le groupe de ressources et l'emplacement correspondants.
1. Cliquez sur **Créer**.

Pour plus d’informations sur le fonctionnement des identités managées dans Azure HDInsight, consultez [Identités managées dans Azure HDInsight](hdinsight-managed-identities.md).

![Créer une identité managée attribuée par l’utilisateur](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Créer un compte Data Lake Storage Gen2

Créez un compte de stockage Azure Data Lake Storage Gen2.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le coin supérieur gauche, cliquez sur **Créer une ressource**.
1. Dans la zone de recherche, tapez **storage (stockage)** , puis cliquez sur **Compte de stockage**.
1. Cliquez sur **Créer**.
1. Sur l'écran **Créer un compte de stockage** :
    1. Sélectionnez l’abonnement et le groupe de ressources correspondants.
    1. Entrez un nom pour votre compte Data Lake Storage Gen2.
    1. Cliquez sur l’onglet **Avancé**.
    1. Cliquez sur **Activé** en regard de **Espace de noms hiérarchique** sous **Data Lake Storage Gen2**.
    1. Cliquez sur **Vérifier + créer**.
    1. Cliquez sur **Créer**

Pour plus d'informations sur les autres options lors de la création d'un compte de stockage, voir [Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Capture d’écran montrant la création du compte de stockage dans le portail Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Définir les autorisations de l’identité managée sur le compte Data Lake Storage Gen2

Affectez l’identité managée au rôle **Propriétaire des données Blob du stockage** sur le compte de stockage.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
1. Sélectionnez votre compte de stockage, puis sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du compte. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.

    ![Capture d’écran montrant les paramètres de contrôle d’accès du stockage](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Sélectionnez le bouton **+ Ajouter une attribution de rôle** pour ajouter un nouveau rôle.
1. Dans la fenêtre **Ajouter une attribution de rôle**, sélectionnez le rôle **Propriétaire des données Blob du stockage**. Ensuite, sélectionnez l’abonnement qui contient le compte de stockage et l’identité managée. Ensuite, recherchez l’identité managée affectée par l’utilisateur que vous avez créée. Pour finir, sélectionnez l’identité managée pour qu’elle soit listée sous **Membres sélectionnés**.

    ![Capture d’écran montrant comment attribuer un rôle Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Sélectionnez **Enregistrer**. L’identité affectée par l’utilisateur que vous avez sélectionnée est maintenant listée sous le rôle sélectionné.
1. Une fois cette configuration initiale terminée, vous pouvez créer un cluster par le biais du portail. Le cluster doit être dans la même région Azure que le compte de stockage. Sous l’onglet **Stockage** du menu de création du cluster, sélectionnez les options suivantes :

    * Comme **Type de stockage principal**, sélectionnez **Azure Data Lake Storage Gen2**.
    * Sous **Compte de stockage principal**, recherchez et sélectionnez le compte de stockage Data Lake Storage Gen2 nouvellement créé.

    * Sous **Identité**, sélectionnez l’identité managée affectée par l’utilisateur nouvellement créée.

        ![Paramètres de stockage pour l’utilisation de Data Lake Storage Gen2 avec Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Pour ajouter un compte Azure Data Lake Storage Gen2 secondaire, au niveau du compte de stockage, attribuez simplement l’identité managée créée précédemment au nouveau compte de stockage Azure Data Lake Storage Gen2 que vous voulez ajouter. Sachez que l’ajout d’un compte Azure Data Lake Storage Gen2 secondaire via le panneau « Comptes de stockage supplémentaires » de HDInsight n’est pas pris en charge.
    > * Vous pouvez activer RA-GRS ou RA-ZRS sur le compte de stockage Azure utilisé par HDInsight. La création d’un cluster sur le point de terminaison secondaire RA-GRS ou RA-ZRS n’est toutefois pas prise en charge.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Créer un cluster avec Data Lake Storage Gen2 par le biais d’Azure CLI

Vous pouvez [télécharger un exemple de fichier de modèle](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) et [télécharger un exemple de fichier de paramètres](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Avant d’utiliser le modèle et l’extrait de code Azure CLI ci-dessous, remplacez les espaces réservés suivants par leurs valeurs correctes :

| Espace réservé | Description |
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

Ensuite, connectez-vous au portail. Ajoutez la nouvelle identité managée affectée par l’utilisateur au rôle **Contributeur aux données Blob du stockage** sur le compte de stockage. Cette étape est décrite à l’étape 3 sous [Utilisation du Portail Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Une fois que vous avez défini le rôle de l’identité managée affectée par l’utilisateur, déployez le modèle en utilisant l’extrait de code suivant.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Créer un cluster avec Data Lake Storage Gen2 par le biais d’Azure PowerShell

L’utilisation de PowerShell pour créer un cluster HDInsight avec Azure Data Lake Storage Gen2 n’est actuellement pas prise en charge.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Contrôle d’accès à Data Lake Storage Gen2 dans HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quels sont les types d’autorisation pris en charge par Data Lake Storage Gen2 ?

Data Lake Storage Gen2 utilise un modèle de contrôle d’accès qui prend en charge le contrôle d’accès en fonction du rôle (RBAC) ainsi que les listes de contrôle d’accès (ACL) POSIX. Data Lake Storage Gen1 prend en charge les listes de contrôle d’accès uniquement pour contrôler l’accès aux données.

RBAC utilise des attributions de rôles pour appliquer efficacement des ensembles d’autorisations aux utilisateurs, groupes et principaux de service des ressources Azure. En règle générale, ces ressources Azure sont limitées aux ressources de niveau supérieur (par exemple les comptes Stockage Azure). Pour le stockage Azure, et aussi Data Lake Storage Gen2, ce mécanisme a été étendu à la ressource du système de fichiers.

 Pour plus d’informations sur les autorisations de fichiers avec RBAC, consultez [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Pour plus d’informations sur les autorisations de fichiers avec des listes ACL, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Comment contrôler l’accès à mes données dans Data Lake Storage Gen2 ?

La capacité de votre cluster HDInsight à accéder aux fichiers dans Data Lake Storage Gen2 est contrôlée par le biais d’identités managées. Une identité managée est une identité inscrite dans Azure Active Directory (Azure AD) dont les informations d’identification sont gérées par Azure. Grâce aux identités managées, il n’est pas nécessaire d’inscrire des principaux de service dans Azure AD, ni de gérer des informations d’identification, par exemple des certificats.

Les services Azure utilisent deux types d’identités managées : celles affectées par le système et celles affectées par l’utilisateur. HDInsight utilise des identités managées affectées par l’utilisateur pour accéder à Data Lake Storage Gen2. Une `user-assigned managed identity` est créée en tant que ressource Azure autonome. Via un processus de création, Azure crée une identité dans le locataire Azure AD approuvé par l’abonnement en cours d’utilisation. Une fois l’identité créée, elle peut être affectée à une ou plusieurs instances de service Azure.

Le cycle de vie d’une identité attribuée par l’utilisateur est géré séparément du cycle de vie des instances de service Azure auxquelles elle est affectée. Pour en savoir plus sur les identités managées, consultez la section [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Comment définir des autorisations pour que les utilisateurs Azure AD puissent interroger des données dans Data Lake Storage Gen2 avec Hive ou d’autres services ?

Pour définir des autorisations pour que les utilisateurs puissent interroger des données, utilisez les groupes de sécurité Azure AD comme principal affecté dans les listes de contrôle d’accès. N’attribuez pas directement des autorisations d’accès aux fichiers à des utilisateurs ou principaux de service individuels. Grâce aux groupes de sécurité Azure AD qui contrôlent le flux d’autorisations, vous pouvez ajouter et supprimer des utilisateurs ou des principaux de service sans réappliquer des ACL à une structure de répertoires entière. Il vous suffit d’ajouter ou de supprimer les utilisateurs du groupe de sécurité Azure AD approprié. Les listes de contrôle d’accès ne sont pas héritées. Par conséquent, pour les réappliquer, vous devez les mettre à jour dans chaque fichier et sous-répertoire.

## <a name="access-files-from-the-cluster"></a>Accès aux fichiers à partir du cluster

Il existe plusieurs méthodes pour accéder aux fichiers dans Data Lake Storage Gen2 à partir d’un cluster HDInsight.

* **Utilisation du nom complet**. Avec cette approche, vous fournissez le chemin d’accès complet au fichier auquel vous souhaitez accéder.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Utilisation du format de chemin d’accès raccourci**. Avec cette approche, vous remplacez le chemin d’accès à la racine du cluster par :

    ```
    abfs:///<file.path>/
    ```

* **Utilisation du chemin d’accès relatif**. Avec cette approche, vous fournissez uniquement le chemin d’accès relatif au fichier auquel vous souhaitez accéder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemples d’accès aux données

Les exemples sont basés sur une [connexion ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) au nœud principal du cluster. Les exemples utilisent les trois schémas d’URI. Remplacez `CONTAINERNAME` et `STORAGEACCOUNT` par les valeurs correspondantes

#### <a name="a-few-hdfs-commands"></a>Quelques commandes hdfs

1. Créez un fichier sur le stockage local.

    ```bash
    touch testFile.txt
    ```

1. Créez des répertoires sur le stockage en cluster.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiez les données du stockage local vers le stockage en cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Affichez le contenu du répertoire sur le stockage en cluster.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Création d’une table Hive

Trois emplacements de fichiers sont indiqués à titre d’illustration. Pour l’exécution réelle, n’utilisez qu’une seule des entrées `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Étapes suivantes

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Présentation d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutoriel : Extraire, transformer et charger des données à l’aide d’Interactive Query sur Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
