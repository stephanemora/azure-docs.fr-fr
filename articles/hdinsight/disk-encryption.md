---
title: Chiffrement de disque avec clés gérées par le client pour Azure HDInsight
description: Cet article explique comment utiliser votre propre clé de chiffrement Azure Key Vault pour chiffrer des données stockées sur des disques managés dans des clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: 2c015db828bcbfa8b26f519b3a4707b5ec69b8f3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982510"
---
# <a name="customer-managed-key-disk-encryption"></a>Chiffrement de disque avec clés gérées par le client

Azure HDInsight prend en charge les clés gérées par le client, également appelées chiffrement Bring Your Own Key (BYOK), pour les données sur les disques managés et les disques de ressources attachés aux machines virtuelles de cluster HDInsight. Cette fonctionnalité vous permet d’utiliser Azure Key Vault pour gérer les clés de chiffrement qui sécurisent les données au repos sur vos clusters HDInsight. Vos clusters peuvent avoir un ou plusieurs comptes Stockage Azure attachés où les clés de chiffrement peuvent également être managées par Microsoft ou gérées par le client, mais le service de chiffrement est différent.

Ce document ne traite pas des données stockées dans votre compte Stockage Azure. Pour plus d’informations sur ASE, consultez [Fonctionnalité de chiffrement du service Stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).

Tous les disques managés dans HDInsight sont protégés par Azure Storage Service Encryption (SSE). Par défaut, les données stockées sur ces disques sont chiffrées à l’aide de clés gérées par Microsoft. Si vous activez les clés gérées par le client pour HDInsight, vous fournissez les clés de chiffrement pour que HDInsight utilise et gère ces clés à l’aide d’Azure Key Vault.

Le chiffrement à l’aide de clés gérées par le client est un processus en une étape géré pendant la création d’un cluster sans coût supplémentaire. Il vous suffit d’inscrire HDInsight comme identité managée auprès d’Azure Key Vault et d’ajouter la clé de chiffrement pendant la création du cluster.

Le disque de ressources et les disques managés sur chaque nœud du cluster sont chiffrés avec une clé de chiffrement de données (DEK) symétrique. La clé DEK est protégée avec la clé de chiffrement principale (KEK) de votre coffre de clés. Les processus de chiffrement et de déchiffrement sont entièrement gérés par Azure HDInsight.

Vous pouvez utiliser le portail Azure ou Azure CLI pour faire alterner les clés du coffre de clés en toute sécurité. Quand une clé permute, le cluster HDInsight démarre en quelques minutes en utilisant la nouvelle clé. Activez les fonctionnalités de protection de clés « Suppression réversible » pour vous protéger contre les scénarios de ransomware et de suppression accidentelle. Les coffres de clés sans cette fonctionnalité de protection ne sont pas pris en charge.

## <a name="get-started-with-customer-managed-keys"></a>Prise en main des clés gérées par le client

Pour créer un cluster HDInsight avec clé gérée par le client, nous allons passer en revue les étapes suivantes :

1. Création d’identités managées pour les ressources Azure
2. Configuration d’Azure Key Vault et des clés
3. Création d’un cluster HDInsight avec clé gérée par le client
4. Rotation de la clé de chiffrement

## <a name="create-managed-identities-for-azure-resources"></a>Création d’identités managées pour les ressources Azure

Pour vous authentifier auprès de Key Vault, créez une identité managée affectée par l’utilisateur via le [Portail Microsoft Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) ou [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Pour plus d’informations sur le fonctionnement des identités managées dans Azure HDInsight, consultez [Identités managées dans Azure HDInsight](hdinsight-managed-identities.md). Veillez à enregistrer l’ID de ressource d’identité managée, vous en aurez besoin pour l’ajouter à la stratégie d’accès Key Vault.

## <a name="set-up-the-key-vault-and-keys"></a>Configurer le Key Vault et les clés

HDInsight prend uniquement en charge Azure Key Vault. Si vous disposez de votre propre coffre de clés, vous pouvez importer vos clés dans Azure Key Vault. N’oubliez pas que la fonctionnalité « Suppression réversible » doit être activée pour les clés. La fonctionnalité « Suppression réversible » est disponible via les interfaces REST, .NET/C#, PowerShell et Azure CLI.

1. Pour créer un coffre de clés, suivez le guide de démarrage rapide [Azure Key Vault](../key-vault/key-vault-overview.md). Pour plus d’informations sur l’importation de clés existantes, consultez [Présentation des clés, des secrets et des certificats](../key-vault/about-keys-secrets-and-certificates.md).

1. Activez la fonctionnalité « Suppression réversible » sur le coffre de clés à l’aide de la commande CLI [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update).

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Créez des clés.

    a. Pour créer une clé, sélectionnez **Générer/Importer** dans le menu **Clés** sous **Paramètres**.

    ![Générer une nouvelle clé dans Azure Key Vault](./media/disk-encryption/create-new-key.png "Générer une nouvelle clé dans Azure Key Vault")

    b. Définissez **Options** sur **Générer** et donnez un nom à la clé.

    ![génère un nom de clé](./media/disk-encryption/create-key.png "Générer le nom de la clé")

    c. Sélectionnez la clé que vous avez créée dans la liste des clés.

    ![liste de clés de coffre de clés](./media/disk-encryption/key-vault-key-list.png)

    d. Quand vous utilisez votre propre clé pour le chiffrement du cluster HDInsight, vous devez spécifier l’URI de la clé. Copiez l’**identificateur de clé** et enregistrez-le quelque part jusqu’à la création du cluster.

    ![obtenir l’identificateur de clé](./media/disk-encryption/get-key-identifier.png)

1. Ajoutez l’identité managée à la stratégie d’accès au coffre de clés.

    a. Créez une stratégie d’accès Azure Key Vault.

    ![Créer une stratégie d’accès Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy.png)

    b. Sous **Sélectionner le principal**, choisissez l’identité managée affectée par l’utilisateur que vous avez créé.

    ![Définir Sélectionner le principal pour la stratégie d’accès Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-select-principal.png)

    c. Définissez **Autorisations de clé** sur **Obtenir**, **Ne pas inclure la clé** et **Inclure la clé**.

    ![Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault1")

    d. Définissez **Autorisations du secret** sur **Obtenir**, **Définir** et **Supprimer**.

    ![Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault2](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault2")

    e. Sélectionnez **Enregistrer**.

    ![Enregistrer une stratégie d’accès Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Créer un cluster avec chiffrement de disque par clé gérée par le client

Vous êtes maintenant prêt à créer un cluster HDInsight. La clé gérée par le client ne peut être appliquée qu’aux nouveaux clusters, pendant leur création. Vous ne pouvez pas supprimer le chiffrement des clusters à clés gérées par le client, ni ajouter la clé gérée par le client aux clusters existants.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pendant la création du cluster, indiquez l’URL complète de la clé en incluant la version de la clé. Par exemple : `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Vous devez aussi affecter l’identité managée au cluster et indiquer l’URI de la clé.

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

L’exemple suivant montre comment utiliser Azure CLI pour créer un nouveau cluster Apache Spark avec le chiffrement de disque activé. Pour plus d’informations, consultez la documentation [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Rotation de la clé de chiffrement

Dans certains scénarios, vous pouvez être amené à changer les clés de chiffrement qui sont utilisées par le cluster HDInsight une fois que celui-ci a été créé. Cette tâche peut être facilement effectuée dans le portail. Pour cette opération, le cluster doit avoir accès à la clé actuelle et à la nouvelle clé prévue. Sinon, l’opération de rotation des clés échouera.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour procéder à la rotation des clés, vous devez disposer de l’URL complète de la nouvelle clé (voir l’étape 3 de [Configurer Key Vault et les clés](#set-up-the-key-vault-and-keys)). Une fois que vous avez effectué cette opération, accédez à la section Propriétés du cluster HDInsight dans le portail, puis cliquez sur **Changer de clé** sous **URL de la clé de chiffrement de disque**. Entrez la nouvelle URL de clé et envoyez-la pour effectuer la rotation.

![permutation de la clé de chiffrement de disque](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

L’exemple suivant montre comment faire permuter la clé de chiffrement de disque d’un cluster HDInsight existant. Pour plus d’informations, consultez [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>FAQ sur le chiffrement avec clé gérée par le client

**Comment le cluster HDInsight accède-t-il à mon coffre de clés ?**

HDInsight accède à votre instance Azure Key Vault à l’aide de l’identité managée que vous associez au cluster HDInsight. Cette identité managée peut être créée avant ou pendant la création du cluster. Vous devez aussi accorder à l’identité managée un accès au coffre de clés dans lequel la clé est stockée.

**Cette fonctionnalité est-elle accessible à tous les clusters sur HDInsight ?**

Le chiffrement avec clé gérée par le client est disponible pour tous les types de cluster sauf Spark 2.1 et 2.2.

**Puis-je utiliser plusieurs clés pour chiffrer différents disques ou dossiers ?**

Non, tous les disques managés et les disques de ressources sont chiffrés à l’aide de la même clé.

**Que se passe-t-il si le cluster ne peut plus accéder au coffre de clés ou à la clé ?**

Si le cluster ne peut plus accéder à la clé, des avertissements s’affichent dans le portail Apache Ambari. Dans ce cas, l’opération **Changer la clé** échouera. Une fois l’accès à la clé rétabli, les avertissements Ambari disparaissent et les opérations telles que la rotation des clés peuvent de nouveau être effectuées.

![alerte Ambari concernant l’accès à la clé](./media/disk-encryption/ambari-alert.png)

**Comment puis-je récupérer le cluster si les clés sont supprimées ?**

Sachant que seules sont prises en charge les clés ayant la « Suppression réversible » activée, si les clés sont récupérées dans le coffre de clés, le cluster doit retrouver l’accès aux clés. Pour récupérer une clé Azure Key Vault, consultez [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Quels sont les types de disques chiffrés ? Les disques de système d’exploitation/ressources sont-ils également chiffrés ?**

Les disques de ressources et les disques de données/managés sont chiffrés. Les disques de système d’exploitation ne sont pas chiffrés.

**Si un cluster subit un scale-up, les nouveaux nœuds prendront-ils en charge les clés gérées par le client sans interruption ?**

Oui. Le cluster doit accéder à la clé dans le coffre de clés pendant la montée en puissance. La même clé est utilisée pour chiffrer les disques managés et les disques de ressources dans le cluster.

**Les clés gérées par le client sont-elles disponibles à mon emplacement ?**

Les clés gérées par le client HDInsight sont disponibles dans tous les clouds publics et les clouds nationaux.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la sécurité d’entreprise dans Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
