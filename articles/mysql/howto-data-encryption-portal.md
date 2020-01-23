---
title: Chiffrement des données pour Azure Database pour MySQL à l’aide du portail
description: Découvrez comment configurer et gérer le chiffrement des données pour votre Azure Database pour MySQL à l’aide du Portail Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5bd100a5858660d80f3704844ed974f2bc9257b4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028940"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Chiffrement des données pour Azure Database pour MySQL à l’aide du portail Azure

Dans cet article, vous allez apprendre à configurer et à gérer pour utiliser le portail Azure afin de configurer le chiffrement des données pour votre Azure Database pour MySQL.

## <a name="prerequisites-for-cli"></a>Prérequis pour CLI

* Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement.
* Créez un coffre Azure Key Vault et une clé à utiliser pour la clé gérée par le client.
* Le coffre de clés doit avoir la propriété suivante à utiliser en tant que clé gérée par le client :
  * [Suppression réversible](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Protégé contre le vidage](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La clé doit avoir les attributs suivants pour être utilisée comme clé gérée par le client.
  * Aucune date d’expiration
  * Non activée
  * En mesure d’effectuer des opérations _get_, _wrap key_, _unwrap key_

## <a name="setting-the-right-permissions-for-key-operations"></a>Définir les permissions appropriées pour les opérations sur les clés

1. Dans Azure Key Vault, sélectionnez **Stratégies d’accès**, puis **Ajouter une stratégie d’accès** :

   ![Vue d’ensemble de la stratégie d'accès](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Sélectionnez **Autorisations de clé**, puis **Get**, **Wrap**, **Unwrap** et **Principal**, qui est le nom du serveur MySQL. Si votre serveur principal est introuvable dans la liste des principaux existants, vous devrez l’inscrire en tentant de configurer le chiffrement des données pour la première fois, ce qui échouera.

   ![Vue d’ensemble de la stratégie d'accès](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Enregistrez** les paramètres.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Configurer le chiffrement des données pour Azure Database pour MySQL

1. Sur **Azure Database pour MySQL**, sélectionnez **Chiffrement des données** pour définir la configuration de clé gérée par le client.

   ![Configurer le chiffrement des données](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Vous pouvez sélectionner un **coffre de clés** et une paire de **clé** ou passer un **identificateur de clé**.

   ![Configurer Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Enregistrez** les paramètres.

4. Pour vous assurer que tous les fichiers (y compris les **fichiers temporaires**) sont entièrement chiffrés, un **redémarrage** du serveur est **requis**.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Restauration ou création d’un réplica du serveur, pour lequel le chiffrement des données est activé

Une fois qu’Azure Database pour MySQL est chiffré avec la clé gérée du client stockée dans le coffre de clés, toute copie nouvellement créée du serveur, que ce soit via une opération de restauration locale ou de géo-restauration ou une opération de réplication (locale/inter-région). Ainsi, pour un serveur MySQL, vous pouvez suivre les étapes ci-dessous pour créer un serveur restauré chiffré.

1. Sur votre serveur, sélectionnez **Vue d’ensemble**, puis **Restaurer**.

   ![Initiate-restore](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Sinon, pour un serveur de réplication, sous l’en tête **Paramètres**, sélectionnez **Réplication** comme indiqué ci-dessous :

   ![Initiate-replica](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Une fois l’opération de restauration terminée, le nouveau serveur créé est chiffré avec la clé utilisée pour chiffrer le serveur principal. Toutefois, les fonctionnalités et les options du serveur sont désactivées et le serveur est marqué comme **Inaccessible**. Ce comportement vise à empêcher toute manipulation de données, car l’identité du nouveau serveur n’a toujours pas reçu l’autorisation d’accéder au coffre de clés.

   ![Marquer le serveur comme inaccessible](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Pour corriger l’état « Inaccessible », vous devez revalider la clé sur le serveur restauré. Sélectionnez le volet **Chiffrement des données**, puis le bouton **Revalider la clé**.

   > [!NOTE]
   > La première tentative de revalidation échouera, car le principal de service du nouveau serveur doit avoir accès au coffre de clés. Pour générer le principal de service, sélectionnez **Revalider la clé**. Vous recevrez un message d’erreur mais pourrez générer le principal de service. Ensuite, reportez-vous aux étapes [de la section 2](#setting-the-right-permissions-for-key-operations) ci-dessus.

   ![revalider le serveur](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Vous devez autoriser le nouveau serveur à avoir accès au coffre de clés.

4. Après avoir inscrit le principal de service, vous devrez revalider la clé pour que le serveur reprenne ses fonctionnalités normales.

   ![Serveur normal restauré](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Étapes suivantes

 Pour en savoir plus sur le chiffrement des données, consultez la section [Qu’est-ce que le chiffrement des données Azure ?](concepts-data-encryption-mysql.md).
