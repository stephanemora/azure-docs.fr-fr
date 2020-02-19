---
title: Apporter sa propre clé pour Apache Kafka sur Azure HDInsight
description: Cet article explique comment utiliser votre propre clé Azure Key Vault pour chiffrer des données stockées dans Apache Kafka sur Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cba8a4fd64b948d7a3e443426ca1f779af68a3fe
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049020"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Apporter sa propre clé pour Apache Kafka sur Azure HDInsight

Azure HDInsight assure la prise en charge du modèle BYOK (Bring Your Own Key) pour Apache Kafka. Cette fonctionnalité vous permet de posséder et de gérer les clés servant à chiffrer les données au repos.

Tous les disques managés dans HDInsight sont protégés par Azure Storage Service Encryption (SSE). Par défaut, les données stockées sur ces disques sont chiffrées à l’aide de clés gérées par Microsoft. Si vous activez BYOK, vous devez fournir la clé de chiffrement pour permettre à HDInsight de l’utiliser et de la gérer à l’aide d’Azure Key Vault.

Le chiffrement BYOK est un processus en une étape géré pendant la création d’un cluster sans coût supplémentaire. Il vous suffit d’inscrire HDInsight comme identité managée auprès d’Azure Key Vault et d’ajouter la clé de chiffrement pendant la création du cluster.

Tous les messages adressés au cluster Kafka (y compris les réplicas gérés par Kafka) sont chiffrés avec une clé de chiffrement de données (DEK) symétrique. La clé DEK est protégée avec la clé de chiffrement principale (KEK) de votre coffre de clés. Les processus de chiffrement et de déchiffrement sont entièrement gérés par Azure HDInsight.

Vous pouvez utiliser le portail Azure ou Azure CLI pour faire alterner les clés du coffre de clés en toute sécurité. Quand une clé alterne, le cluster HDInsight Kafka démarre en quelques minutes en utilisant la nouvelle clé. Activez les fonctionnalités de protection de clés « Suppression réversible » pour vous protéger contre les scénarios de ransomware et de suppression accidentelle. Les coffres de clés sans cette fonctionnalité de protection ne sont pas pris en charge.

## <a name="get-started-with-byok"></a>Commencer avec BYOK

Pour créer un cluster Kafka avec BYOK activé, nous procéderons comme suit :

1. Création d’identités managées pour les ressources Azure
2. Configuration d’Azure Key Vault et des clés
3. Création d’un cluster Kafka HDInsight avec BYOK activé
4. Rotation de la clé de chiffrement

## <a name="create-managed-identities-for-azure-resources"></a>Création d’identités managées pour les ressources Azure

Pour vous authentifier auprès de Key Vault, créez une identité managée affectée par l’utilisateur via le [Portail Microsoft Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) ou [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Pour plus d’informations sur le fonctionnement des identités managées dans Azure HDInsight, consultez [Identités managées dans Azure HDInsight](../hdinsight-managed-identities.md). Si Azure Active Directory est obligatoire pour les identités managées et BYOK pour Kafka, le pack Sécurité Entreprise (ESP) n’est en revanche pas exigé. Veillez à enregistrer l’ID de ressource d’identité managée, vous en aurez besoin pour l’ajouter à la stratégie d’accès Key Vault.

![Créer une identité managée affectée par l’utilisateur dans le portail Azure](./media/apache-kafka-byok/azure-portal-create-managed-identity.png)

## <a name="set-up-the-key-vault-and-keys"></a>Configurer le Key Vault et les clés

HDInsight prend uniquement en charge Azure Key Vault. Si vous disposez de votre propre coffre de clés, vous pouvez importer vos clés dans Azure Key Vault. N’oubliez pas que la fonctionnalité « Suppression réversible » doit être activée pour les clés. La fonctionnalité « Suppression réversible » est disponible via les interfaces REST, .NET/C#, PowerShell et Azure CLI.

1. Pour créer un coffre de clés, suivez le guide de démarrage rapide [Azure Key Vault](../../key-vault/quick-create-cli.md). Pour plus d’informations sur l’importation de clés existantes, consultez [Présentation des clés, des secrets et des certificats](../../key-vault/about-keys-secrets-and-certificates.md).

1. Activez la fonctionnalité « Suppression réversible » sur le coffre de clés à l’aide de la commande CLI [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update).

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Créez des clés.

    a. Pour créer une clé, sélectionnez **Générer/Importer** dans le menu **Clés** sous **Paramètres**.

    ![Générer une nouvelle clé dans Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png "Générer une nouvelle clé dans Azure Key Vault")

    b. Définissez **Options** sur **Générer** et donnez un nom à la clé.

    ![Apache Kafka génère le nom de la clé](./media/apache-kafka-byok/apache-kafka-create-key.png "Générer le nom de la clé")

    c. Sélectionnez la clé que vous avez créée dans la liste des clés.

    ![Apache Kafka - Liste des clés Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. Lorsque vous utilisez votre propre clé pour le chiffrement du cluster Kafka, vous devez fournir l’URI de la clé. Copiez l’**identificateur de clé** et enregistrez-le quelque part jusqu’à la création du cluster.

    ![Apache Kafka - Obtenir l’identificateur de clé d’accès](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. Ajoutez l’identité managée à la stratégie d’accès au coffre de clés.

    a. Créez une stratégie d’accès Azure Key Vault.

    ![Créer une stratégie d’accès Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. Sous **Sélectionner le principal**, choisissez l’identité managée affectée par l’utilisateur que vous avez créé.

    ![Définir Sélectionner le principal pour la stratégie d’accès Azure Key Vault](./media/apache-kafka-byok/azure-portal-add-access-policy.png)

    c. Définissez **Autorisations de clé** sur **Obtenir**, **Ne pas inclure la clé** et **Inclure la clé**.

    ![Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault1")

    d. Définissez **Autorisations du secret** sur **Obtenir**, **Définir** et **Supprimer**.

    ![Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault2")

    e. Sélectionnez **Enregistrer**.

    ![Enregistrer une stratégie d’accès Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Créer un cluster HDInsight

Vous êtes maintenant prêt à créer un cluster HDInsight. Sous l’onglet **De base**, sélectionnez **Kafka** pour le paramètre **Type de cluster**.

![Sélection du type Kafka dans le Portail Azure](./media/apache-kafka-byok/azure-portal-cluster-basics-type-kafka.png)

BYOK ne peut être appliqué qu’aux nouveaux clusters, pendant leur création. Le chiffrement ne peut pas être retiré des clusters BYOK, et BYOK ne peut pas être ajouté aux clusters existants.

![Chiffrement de disque Kafka dans le portail Azure](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka-byok.png)

Pendant la création du cluster, indiquez l’URL complète de la clé en incluant la version de la clé. Par exemple : `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Vous devez aussi affecter l’identité managée au cluster et indiquer l’URI de la clé. Pour plus d’informations sur la création de clusters, consultez [Création de clusters Apache Hadoop à l’aide du Portail Azure](./apache-kafka-get-started.md).

## <a name="rotating-the-encryption-key"></a>Rotation de la clé de chiffrement

Dans certains scénarios, vous pouvez être amené à modifier les clés de chiffrement qui sont utilisées par le cluster Kafka une fois que celui-ci a été créé. Cette tâche peut être facilement effectuée dans le portail. Pour cette opération, le cluster doit avoir accès à la clé actuelle et à la nouvelle clé prévue. Sinon, l’opération de rotation des clés échouera.

Pour procéder à la rotation des clés, vous devez disposer de l’URL complète de la nouvelle clé (voir l’étape 3 de [Configurer Key Vault et les clés](#set-up-the-key-vault-and-keys)). Une fois que vous avez effectué cette opération, accédez à la section Propriétés du cluster Kafka dans le portail, puis cliquez sur **Changer de clé** sous **URL de la clé de chiffrement de disque**. Entrez la nouvelle URL de clé et envoyez-la pour effectuer la rotation.

![Rotation de la clé de chiffrement du disque dans Kafka](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Questions fréquentes (FAQ) sur BYOK pour Apache Kafka

**Comment le cluster Kafka accède-t-il à mon coffre de clés ?**

Associez une identité managée au cluster HDInsight Kafka pendant la création de ce dernier. Cette identité managée peut être créée avant ou pendant la création du cluster. Vous devez aussi accorder à l’identité managée un accès au coffre de clés dans lequel la clé est stockée.

**Cette fonctionnalité est-elle accessible à tous les clusters Kafka sur HDInsight ?**

Le chiffrement BYOK est possible uniquement pour les clusters Kafka version 1.1 et ultérieures.

**Est-il possible de disposer de clés différentes pour différentes rubriques/partitions ?**

Non, tous les disques managés du cluster sont chiffrés par la même clé.

**Que se passe-t-il si le cluster ne peut plus accéder au coffre de clés ou à la clé ?**

Si le cluster ne peut plus accéder à la clé, des avertissements s’affichent dans le portail Apache Ambari. Dans ce cas, l’opération **Changer la clé** échouera. Une fois l’accès à la clé rétabli, les avertissements Ambari disparaissent et les opérations telles que la rotation des clés peuvent de nouveau être effectuées.

![Apache Kafka - Alerte Ambari concernant l’accès aux clés](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Comment puis-je récupérer le cluster si les clés sont supprimées ?**

Sachant que seules sont prises en charge les clés ayant la « Suppression réversible » activée, si les clés sont récupérées dans le coffre de clés, le cluster doit retrouver l’accès aux clés. Pour récupérer une clé Azure Key Vault, consultez [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Est-il possible de faire fonctionner des applications producteur/consommateur simultanément avec un cluster BYOK et un cluster non BYOK ?**

Oui. L’utilisation de BYOK est transparente pour les applications producteur/consommateur. Le chiffrement se produit au niveau de la couche du système d’exploitation. Aucune modification n’a besoin d’être apportée aux applications Kafka producteur/consommateur existantes.

**Les disques de système d’exploitation/ressources sont-ils également chiffrés ?**

Non. Les disques de système d’exploitation et les disques de ressources ne sont pas chiffrés.

**En cas de montée en puissance d’un cluster, les nouveaux répartiteurs prennent-ils en charge BYOK sans interruption ?**

Oui. Le cluster doit accéder à la clé dans le coffre de clés pendant la montée en puissance. La même clé sert à chiffrer tous les disques managés du cluster.

**La fonctionnalité BYOK est-elle disponible à mon emplacement ?**

La fonctionnalité BYOK de Kafka est disponible dans tous les clouds publics.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault](../../key-vault/key-vault-overview.md) ?
* Pour démarrer avec Azure Key Vault, consultez [Bien démarrer avec Azure Key Vault](../../key-vault/key-vault-overview.md).
