---
title: Utiliser le Portail Microsoft Azure pour configurer des clés gérées par le client
titleSuffix: Azure Storage
description: Découvrez comment utiliser le portail Azure afin de configurer des clés gérées par le client avec Azure Key Vault pour le chiffrement du stockage Azure. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b1006fead92763c5c2e670527b5e232618b633e5
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895303"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurer des clés gérées par le client avec Azure Key Vault à l’aide du Portail Microsoft Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés Azure avec des clés gérées par le client à l’aide du [portail Azure](https://portal.azure.com/). Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Pour utiliser des clés gérées par le client avec le chiffrement Stockage Azure, deux propriétés doivent être configurées sur le coffre de clés, **Suppression réversible** et **Ne pas vider**. Ces propriétés ne sont pas activées par défaut. Pour activer ces propriétés, utilisez PowerShell ou Azure CLI.
> Seules les clés RSA et la taille de clé 2048 sont prises en charge.

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage.
1. Sur le panneau **Paramètres** du compte de stockage, cliquez sur **Chiffrement**. Sélectionnez l’option **Utiliser votre propre clé**, comme indiqué dans l’illustration suivante.

    ![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Spécifier une clé

Après avoir activé les clés gérées par le client, vous pourrez spécifier une clé à associer au compte de stockage.

### <a name="specify-a-key-as-a-uri"></a>Spécifier une clé en tant qu’URI

Pour spécifier une clé en tant qu’URI, procédez comme suit :

1. Pour localiser l’URI de la clé dans le portail Azure, naviguez jusqu'à votre coffre de clés, puis sélectionnez le paramètre **Clés**. Sélectionnez la clé souhaitée, puis cliquez dessus pour afficher ses paramètres. Copiez la valeur du champ **Identificateur de clé**, qui fournit l’URI.

    ![Capture d’écran montrant l’URI de la clé du coffre de clés](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Dans les paramètres de **chiffrement** de votre compte de stockage, choisissez l’option **Entrer l’URI de la clé**.
1. Dans le champ **Clé URI**, spécifiez l’URI.

   ![Capture d’écran montrant comment entrer l’URI d’une clé](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Spécifiez une clé à partir d’un coffre de clés

Pour spécifier une clé à partir d’un coffre de clés, assurez-vous d’abord que vous disposez d’un coffre de clés contenant une clé. Pour spécifier une clé à partir d’un coffre de clés, procédez comme suit :

1. Choisissez l’option **Sélectionner dans le coffre de clés**.
2. Cliquez sur le coffre de clés contenant la clé que vous souhaitez utiliser.
3. Sélectionnez la clé dans le coffre de clés.

   ![Capture d’écran montrant une option de clé gérée par le client](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Mettre à jour la version de la clé

Lors de la création d’une version d’une clé, vous devez mettre à jour le compte de stockage si vous voulez utiliser cette nouvelle version. Procédez comme suit :

1. Accédez à votre compte de stockage et affichez les paramètres de **chiffrement**.
1. Spécifiez l’URI de la nouvelle version de clé. Vous pouvez également sélectionner à nouveau le coffre de clés et la clé pour mettre à jour la version.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
