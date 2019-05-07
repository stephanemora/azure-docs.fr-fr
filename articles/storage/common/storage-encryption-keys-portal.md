---
title: Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir du portail Azure
description: Découvrez comment utiliser le portail Azure pour configurer les clés de gérée par le client pour le chiffrement de stockage Azure. Clés gérées par le client permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: baabc5a8e1d063cb51a3edea3a7218591e85aa1a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154153"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir du portail Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés avec des clés gérées par le client à l’aide de la [Azure portal](https://portal.azure.com/). Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Guide de démarrage rapide : Définir et récupérer un secret dans Azure Key Vault à l’aide du portail Azure](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> À l’aide de clés gérées par le client avec chiffrement de stockage Azure nécessite que le coffre de clés ont deux propriétés requises configurées, **suppression réversible** et **ne pas vider**. Ces propriétés sont activées par défaut lorsque vous créez un coffre de clés dans le portail Azure. Toutefois, si vous avez besoin activer ces propriétés sur un coffre de clés existant, vous devez utiliser PowerShell ou Azure CLI.

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage.
1. Sur le panneau **Paramètres** du compte de stockage, cliquez sur **Chiffrement**. Sélectionnez l’option **Utiliser votre propre clé**, comme indiqué dans l’illustration suivante.

    ![Capture d’écran de portail affichant l’option de chiffrement](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Spécifier une clé

Après avoir activé les clés gérées par le client, vous aurez la possibilité de spécifier une clé à associer au compte de stockage.

### <a name="specify-a-key-as-a-uri"></a>Spécifier une clé en tant qu’URI

Pour spécifier une clé en tant qu’URI, procédez comme suit :

1. Pour trouver l’URI de la clé dans le portail Azure, accédez à votre coffre de clés, puis sélectionnez le **clés** paramètre. Sélectionnez la clé souhaitée, puis cliquez sur la clé pour afficher ses paramètres. Copiez la valeur de la **identificateur de clé** champ, qui fournit l’URI.

    ![Capture d’écran montrant coffre de clés URI de la clé](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Dans le **chiffrement** paramètres pour votre compte de stockage, choisissez le **URI de la clé entrée** option.
1. Dans le champ **Clé URI**, spécifiez l’URI.

   ![Capture d’écran montrant comment entrer l’URI de la clé](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Spécifiez une clé à partir d’un coffre de clés

Pour spécifier une clé à partir d’un coffre de clés, assurez-vous d’abord que vous disposez d’un coffre de clés qui contient une clé. Pour spécifier une clé à partir d’un coffre de clés, procédez comme suit :

1. Choisissez l’option **Sélectionner dans le coffre de clés**.
2. Cliquez sur le coffre de clés contenant la clé que vous souhaitez utiliser.
3. Sélectionnez la clé dans le coffre de clés.

   ![Capture d’écran montrant l’option de clé gérée par le client](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Mise à jour la version de la clé

Lorsque vous créez une nouvelle version d’une clé, vous devez mettre à jour le compte de stockage pour utiliser la nouvelle version. Procédez comme suit :

1. Accédez à votre compte de stockage et d’afficher le **chiffrement** paramètres.
1. Spécifiez l’URI pour la nouvelle version de clé. Vous pouvez également sélectionner le coffre de clés et la clé à nouveau à jour la version.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
- [Qu’est Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
