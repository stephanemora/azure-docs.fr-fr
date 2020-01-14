---
title: Utiliser le Portail Microsoft Azure pour configurer des clés gérées par le client
titleSuffix: Azure Storage
description: Découvrez comment utiliser le portail Azure afin de configurer des clés gérées par le client avec Azure Key Vault pour le chiffrement du stockage Azure. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f592872e67ff8559060706ddb3b1e45839b6acaf
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665465"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurer des clés gérées par le client avec Azure Key Vault à l’aide du Portail Microsoft Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés Azure avec des clés gérées par le client à l’aide du [portail Azure](https://portal.azure.com/). Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurer Azure Key Vault

Pour utiliser des clés gérées par le client avec le chiffrement Stockage Azure, deux propriétés doivent être configurées sur le coffre de clés, **Suppression réversible** et **Ne pas vider**. Ces propriétés ne sont pas activées par défaut, mais peuvent être activées à l’aide de PowerShell ou d’Azure CLI sur un coffre de clés nouveau ou existant.

Pour savoir comment activer ces propriétés sur un coffre de clés existant, consultez les sections intitulées **Activation de la suppression réversible** et **Activation de la protection contre le vidage** dans l’un des articles suivants :

- [Guide pratique pour utiliser la suppression réversible avec Azure Power​Shell](../../key-vault/key-vault-soft-delete-powershell.md).
- [Guide pratique pour utiliser la suppression réversible avec Azure CLI](../../key-vault/key-vault-soft-delete-cli.md).

Seules les clés RSA de taille 2048 sont prises en charge avec le chiffrement Azure Storage. Pour plus d’informations sur les clés, consultez **Clés Key Vault** dans [À propos des clés, des secrets et des certificats Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage.
1. Sur le panneau **Paramètres** du compte de stockage, cliquez sur **Chiffrement**. Sélectionnez l’option **Utiliser votre propre clé**, comme indiqué dans l’illustration suivante.

    ![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Spécifier une clé

Après avoir activé les clés gérées par le client, vous pourrez spécifier une clé à associer au compte de stockage.

### <a name="specify-a-key-as-a-uri"></a>Spécifier une clé en tant qu’URI

Pour spécifier une clé en tant qu’URI, procédez comme suit :

1. Pour localiser l’URI de la clé dans le portail Azure, naviguez jusqu'à votre coffre de clés, puis sélectionnez le paramètre **Clés**. Sélectionnez la clé souhaitée, puis cliquez dessus pour afficher ses versions. Sélectionnez une version de clé pour afficher les paramètres de cette version.
1. Copiez la valeur du champ **Identificateur de clé**, qui fournit l’URI.

    ![Capture d’écran montrant l’URI de la clé du coffre de clés](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Dans les paramètres de **chiffrement** de votre compte de stockage, choisissez l’option **Entrer l’URI de la clé**.
1. Collez l’URI que vous avez copié dans le champ **URI de clé**.

   ![Capture d’écran montrant comment entrer l’URI d’une clé](./media/storage-encryption-keys-portal/ssecmk2.png)

1. Spécifiez l’abonnement qui contient le coffre de clés.
1. Enregistrez vos modifications.

### <a name="specify-a-key-from-a-key-vault"></a>Spécifiez une clé à partir d’un coffre de clés

Pour spécifier une clé à partir d’un coffre de clés, assurez-vous d’abord que vous disposez d’un coffre de clés contenant une clé. Pour spécifier une clé à partir d’un coffre de clés, procédez comme suit :

1. Choisissez l’option **Sélectionner dans le coffre de clés**.
2. Sélectionnez le coffre de clés contenant la clé que vous souhaitez utiliser.
3. Sélectionnez la clé dans le coffre de clés.

   ![Capture d’écran montrant une option de clé gérée par le client](./media/storage-encryption-keys-portal/ssecmk3.png)

1. Enregistrez vos modifications.

## <a name="update-the-key-version"></a>Mettre à jour la version de la clé

Lors de la création d’une nouvelle version d’une clé, mettez à jour le compte de stockage afin qu’il utilise cette nouvelle version. Procédez comme suit :

1. Accédez à votre compte de stockage et affichez les paramètres de **chiffrement**.
1. Saisissez l’URI de la nouvelle version de clé. Vous pouvez également sélectionner à nouveau le coffre de clés et la clé pour mettre à jour la version.
1. Enregistrez vos modifications.

## <a name="use-a-different-key"></a>Utiliser une autre clé

Pour modifier la clé utilisée pour le chiffrement Azure Storage, procédez comme suit :

1. Accédez à votre compte de stockage et affichez les paramètres de **chiffrement**.
1. Entrez l’URI de la nouvelle clé. Vous pouvez également sélectionner le coffre de clés et choisir une nouvelle clé.
1. Enregistrez vos modifications.

## <a name="disable-customer-managed-keys"></a>Désactiver des clés gérées par le client

Lorsque vous désactivez les clés gérées par le client, votre compte de stockage est ensuite chiffré avec les clés gérées par Microsoft. Pour désactiver les clés gérées par le client, procédez comme suit :

1. Accédez à votre compte de stockage et affichez les paramètres de **chiffrement**.
1. Désactivez la case à cocher en regard du paramètre **Utiliser votre propre clé**.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
