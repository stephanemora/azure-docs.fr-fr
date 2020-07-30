---
title: Chiffrement des données - Portail Azure - Azure Database pour MySQL
description: Découvrez comment configurer et gérer le chiffrement des données pour votre Azure Database pour MySQL à l’aide du Portail Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.openlocfilehash: dcc8a99480400786a48ff952eb378a57c595464f
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388184"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Chiffrement des données pour Azure Database pour MySQL à l’aide du Portail Azure

Découvrez comment utiliser le Portail Azure pour configurer et gérer le chiffrement des données pour votre Azure Database pour MySQL.

## <a name="prerequisites-for-azure-cli"></a>Prérequis pour Azure CLI

* Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement.
* Dans Azure Key Vault, créez un coffre de clés et une clé à utiliser pour une clé gérée par le client.
* Le coffre de clés doit avoir les propriétés suivantes à utiliser en tant que clé gérée par le client :
  * [Suppression réversible](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Protégé contre le vidage](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* La clé doit avoir les attributs suivants à utiliser en tant que clé gérée par le client :
  * Aucune date d’expiration
  * Non activée
  * En mesure d’effectuer des opérations get, wrap key et unwrap key

## <a name="set-the-right-permissions-for-key-operations"></a>Définir les permissions appropriées pour les opérations sur les clés

1. Dans Key Vault, sélectionnez **Stratégies d’accès** > **Ajouter une stratégie d’accès**.

   ![Capture d’écran de Key Vault, avec mise en évidence des éléments Stratégies d’accès et Ajouter une stratégie d’accès](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Sélectionnez **Autorisations de clé**, puis **Get**, **Wrap**, **Unwrap** et **Principal**, qui est le nom du serveur MySQL. Si votre principal de serveur est introuvable dans la liste des principaux existants, vous devez l’inscrire. Vous êtes invité à inscrire votre principal de serveur quand vous tentez de configurer le chiffrement des données pour la première fois et que l’opération échoue.

   ![Vue d’ensemble de la stratégie d’accès](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Sélectionnez **Enregistrer**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Configurer le chiffrement des données pour Azure Database pour MySQL

1. Dans Azure Database pour MySQL, sélectionnez **Chiffrement des données** pour configurer la clé gérée par le client.

   ![Capture d’écran d’Azure Database pour MySQL, avec mise en évidence de l’élément Chiffrement des données](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Vous pouvez sélectionner un coffre de clés et une paire de clés ou entrer un identificateur de clé.

   ![Capture d’écran d’Azure Database pour MySQL, avec mise en évidence des options de chiffrement des données](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Sélectionnez **Enregistrer**.

4. Pour que tous les fichiers (y compris les fichiers temporaires) soient entièrement chiffrés, redémarrez le serveur.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Utilisation du chiffrement des données pour les serveurs de restauration ou réplicas

Une fois Azure Database pour MySQL chiffré à l'aide d'une clé gérée par le client stockée dans Key Vault, toute copie nouvellement créée du serveur est également chiffrée. Vous pouvez effectuer cette nouvelle copie par l’intermédiaire d’une opération de restauration locale ou géographique, ou par le biais d’une opération de réplica (locale ou interrégion). Ainsi, pour un serveur MySQL chiffré, vous pouvez utiliser les étapes suivantes afin de créer un serveur restauré chiffré.

1. Sur votre serveur, sélectionnez **Vue d’ensemble** > **Restaurer**.

   ![Capture d’écran d’Azure Database pour MySQL, avec mise en évidence des éléments Vue d’ensemble et Restaurer](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Autrement, pour un serveur compatible avec la réplication, sous le titre **Paramètres**, sélectionnez **Réplication**.

   ![Capture d’écran d’Azure Database pour MySQL, avec mise en évidence de Réplication](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Une fois l’opération de restauration terminée, le serveur créé est chiffré avec la clé du serveur principal. Toutefois, les fonctionnalités et les options du serveur sont désactivées et le serveur est inaccessible. Toute manipulation de données est ainsi impossible, car l’identité du nouveau serveur n’a pas encore reçu l’autorisation d’accéder au coffre de clés.

   ![Capture d’écran d’Azure Database pour MySQL, avec mise en évidence de l’état Inaccessible](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Pour rendre le serveur accessible, revalidez la clé sur le serveur restauré. Sélectionnez **Chiffrement des données** > **Revalider la clé**.

   > [!NOTE]
   > La première tentative de revalidation échouera, car le principal de service du nouveau serveur doit avoir accès au coffre de clés. Pour générer le principal de service, sélectionnez **Revalider la clé**. Vous verrez un message d’erreur, mais pourrez générer le principal de service. Ensuite, reportez-vous à [ces étapes](#set-the-right-permissions-for-key-operations) plus haut dans cet article.

   ![Capture d’écran d’Azure Database pour MySQL, avec mise en évidence de l’option de revalidation](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Vous devez autoriser le coffre de clés à accéder au nouveau serveur.

4. Après avoir inscrit le principal de service, revalidez la clé pour que le serveur reprenne son fonctionnement normal.

   ![Capture d’écran d’Azure Database pour MySQL, montrant les fonctionnalités restaurées](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Étapes suivantes

 Pour en savoir plus sur le chiffrement des données, consultez [Chiffrement des données Azure Database pour MySQL avec une clé gérée par le client](concepts-data-encryption-mysql.md).
