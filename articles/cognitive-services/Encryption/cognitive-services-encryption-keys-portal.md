---
title: Utiliser le Portail Microsoft Azure pour configurer des clés gérées par le client
titleSuffix: Cognitive Services
description: Découvrez comment utiliser le portail Azure pour configurer des clés gérées par le client avec Azure Key Vault. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455255"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurer des clés gérées par le client avec Azure Key Vault à l’aide du Portail Microsoft Azure

Vous devez utiliser Azure Key Vault pour stocker vos clés managées par le client. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. La ressource Cognitive Services et le coffre de clés doivent se trouver dans la même région et dans le même locataire Azure Active Directory, mais ils peuvent être dans des abonnements différents. Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Cet article explique comment configurer un coffre de clés Azure avec des clés gérées par le client à l’aide du [portail Azure](https://portal.azure.com/). Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurer Azure Key Vault

Pour utiliser des clés gérées par le client, deux propriétés doivent être configurées sur le coffre de clés, **Suppression réversible** et **Ne pas vider**. Ces propriétés ne sont pas activées par défaut, mais peuvent être activées à l’aide de PowerShell ou d’Azure CLI sur un coffre de clés nouveau ou existant.

> [!IMPORTANT]
> Si les propriétés**Suppression réversible** et **Ne pas purger** ne sont pas activées et que vous supprimez votre clé, vous ne pourrez pas récupérer les données dans votre ressource Cognitive Service.

Pour savoir comment activer ces propriétés sur un coffre de clés existant, consultez les sections intitulées **Activation de la suppression réversible** et **Activation de la protection contre le vidage** dans l’un des articles suivants :

- [Guide pratique pour utiliser la suppression réversible avec Azure Power​Shell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Guide pratique pour utiliser la suppression réversible avec Azure CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Seules les clés RSA de taille 2048 sont prises en charge avec le chiffrement Azure Storage. Pour plus d’informations sur les clés, consultez **Clés Key Vault** dans [À propos des clés, des secrets et des certificats Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez à votre ressource Cognitive Services.
1. Dans le panneau **Paramètres** de votre ressource Cognitive Services, cliquez sur **Chiffrement**. Sélectionnez l’option **Clés gérées par le client**, comme illustré dans l’illustration suivante.

    ![Capture d’écran montrant comment sélectionner des clés gérées par le client](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Spécifier une clé

Après avoir activé les clés gérées par le client, vous pourrez spécifier une clé à associer à la ressource Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Spécifier une clé en tant qu’URI

Pour spécifier une clé en tant qu’URI, procédez comme suit :

1. Pour localiser l’URI de la clé dans le portail Azure, naviguez jusqu'à votre coffre de clés, puis sélectionnez le paramètre **Clés**. Sélectionnez la clé souhaitée, puis cliquez dessus pour afficher ses versions. Sélectionnez une version de clé pour afficher les paramètres de cette version.
1. Copiez la valeur du champ **Identificateur de clé**, qui fournit l’URI.

    ![Capture d’écran montrant l’URI de la clé du coffre de clés](../media/cognitive-services-encryption/key-uri-portal.png)

1. Dans les paramètres de **chiffrement** de votre compte de stockage, choisissez l’option **Entrer l’URI de la clé**.
1. Collez l’URI que vous avez copié dans le champ **URI de clé**.

   ![Capture d’écran montrant comment entrer l’URI d’une clé](../media/cognitive-services-encryption/ssecmk2.png)

1. Spécifiez l’abonnement qui contient le coffre de clés.
1. Enregistrez vos modifications.

### <a name="specify-a-key-from-a-key-vault"></a>Spécifiez une clé à partir d’un coffre de clés

Pour spécifier une clé à partir d’un coffre de clés, assurez-vous d’abord que vous disposez d’un coffre de clés contenant une clé. Pour spécifier une clé à partir d’un coffre de clés, procédez comme suit :

1. Choisissez l’option **Sélectionner dans le coffre de clés**.
1. Sélectionnez le coffre de clés contenant la clé que vous souhaitez utiliser.
1. Sélectionnez la clé dans le coffre de clés.

   ![Capture d’écran montrant une option de clé gérée par le client](../media/cognitive-services-encryption/ssecmk3.png)

1. Enregistrez vos modifications.

## <a name="update-the-key-version"></a>Mettre à jour la version de la clé

Lors de la création d’une nouvelle version d’une clé, mettez à jour la ressource Cognitive Services afin qu’elle utilise cette nouvelle version. Procédez comme suit :

1. Accédez à votre ressource de Cognitive Services et affichez les paramètres **Chiffrement**.
1. Saisissez l’URI de la nouvelle version de clé. Vous pouvez également sélectionner à nouveau le coffre de clés et la clé pour mettre à jour la version.
1. Enregistrez vos modifications.

## <a name="use-a-different-key"></a>Utiliser une autre clé

Pour modifier la clé utilisée pour le chiffrement, procédez comme suit :

1. Accédez à votre ressource de Cognitive Services et affichez les paramètres **Chiffrement**.
1. Entrez l’URI de la nouvelle clé. Vous pouvez également sélectionner le coffre de clés et choisir une nouvelle clé.
1. Enregistrez vos modifications.

## <a name="disable-customer-managed-keys"></a>Désactiver les clés gérées par le client

Lorsque vous désactivez les clés gérées par le client, votre ressource Cognitive Services est chiffrée avec des clés gérées par Microsoft. Pour désactiver les clés gérées par le client, procédez comme suit :

1. Accédez à votre ressource de Cognitive Services et affichez les paramètres **Chiffrement**.
1. Désactivez la case à cocher en regard du paramètre **Utiliser votre propre clé**.

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulaire de demande de clé gérée par le client Cognitive Services](https://aka.ms/cogsvc-cmk)
* [Chiffrement des données au repos des services Visage](../Face/face-encryption-of-data-at-rest.md)
* [Chiffrement des données au repos de QnA Maker](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Chiffrement des données au repos du service Language Understanding](../LUIS/luis-encryption-of-data-at-rest.md)
* [Chiffrement des données au repos de Content Moderator](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Chiffrement des données au repos du traducteur](../translator/translator-encryption-of-data-at-rest.md)
* [Chiffrement des données au repos de Personalizer](../personalizer/personalizer-encryption-of-data-at-rest.md)
