---
title: Apache Hadoop et stockage avec transfert sécurisé - Azure HDInsight
description: Découvrez comment créer des clusters HDInsight à l’aide de comptes de stockage Azure doté du transfert sécurisé.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: deb10f2b3e4e2b5e7d911992a601f66e1e557268
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211648"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Clusters Apache Hadoop avec des comptes de stockage avec transfert sécurisé dans Azure HDInsight

La fonctionnalité [Transfert sécurisé requis](../storage/common/storage-require-secure-transfer.md) améliore la sécurité de votre compte de stockage Azure en appliquant toutes les demandes à votre compte via une connexion sécurisée. Cette fonctionnalité et le schéma wasbs sont uniquement pris en charge par les clusters HDInsight 3.6 ou version ultérieure.

> [!IMPORTANT]
> L’activation du transfert de stockage sécurisé après la création d’un cluster peut entraîner des erreurs lors de l’utilisation de votre compte de stockage, et n’est donc pas recommandée. Il est préférable de créer un cluster à l’aide d’un compte de stockage sur lequel le transfert sécurisé est déjà activé.

## <a name="storage-accounts"></a>Comptes de stockage

### <a name="azure-portal"></a>Portail Azure

Par défaut, la propriété Transfert sécurisé requis est activée quand vous créez un compte de stockage dans le Portail Azure.

Pour mettre à jour un compte de stockage existant avec le Portail Azure, consultez [Exiger un transfert sécurisé avec le Portail Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Pour la cmdlet PowerShell [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount), vérifiez que le paramètre `-EnableHttpsTrafficOnly` est défini sur `1`.

Pour mettre à jour un compte de stockage existant avec PowerShell, consultez [Exiger un transfert sécurisé avec PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure CLI

Pour la commande Azure CLI [az storage account create](/cli/azure/storage/account#az_storage_account_create), vérifiez que le paramètre `--https-only` est défini sur `true`.

Pour mettre à jour un compte de stockage existant avec Azure CLI, consultez [Exiger un transfert sécurisé avec Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

### <a name="secure-transfer-errors"></a>Erreur(s) du transfert sécurisé


Si vous avez activé par inadvertance l’option « Exiger un transfert sécurisé » après la création du cluster HDInsight, vous pouvez voir des messages d’erreur semblables à ce qui suit :

`com.microsoft.azure.storage.StorageException: The account being accessed does not support http.`

Pour les clusters HBase uniquement, vous pouvez essayer les étapes suivantes afin de restaurer la fonctionnalité de cluster :
1. Arrêtez HBase à partir d’Ambari.
2. Arrêtez HDFS dans Ambari.
3. Dans Ambari, accédez à HDFS --> Configs --> Advanced --> fs.defaultFS
4. Remplacez wasb par wasbs, puis enregistrez-le.
5. Si vous utilisez la fonctionnalité des écritures accélérées, vous devez également changer « hbase.rootDir » dans les configurations hbase en remplaçant wasb par wasbs.
6. Redémarrez tous les services nécessaires.

## <a name="add-additional-storage-accounts"></a>Ajouter des comptes de stockage

Plusieurs options vous permettent d’ajouter des comptes de stockage dotés du transfert sécurisé :

* Modifiez le modèle Azure Resource Manager dans la dernière section.
* Créez un cluster à l’aide du [portail Azure](https://portal.azure.com) et indiquez le compte de stockage lié.
* Utilisez l’action de script pour ajouter des comptes de stockage doté du transfert sécurisé à un cluster HDInsight existant. Pour plus d’informations, consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Étapes suivantes

* l'utilisation du stockage Azure (WASB) au lieu de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), en tant que stockage de données par défaut
* Pour plus d’informations sur la façon dont HDInsight utilise Stockage Azure, consultez la page [Utilisation de Stockage Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight](hdinsight-upload-data.md).
