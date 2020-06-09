---
title: Chiffrement des données au repos de QnA Maker
titleSuffix: Azure Cognitive Services
description: Chiffrement des données au repos de QnA Maker.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 99c21ece202f8d9867045d506574dd7718bd455e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653663"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>Chiffrement des données au repos de QnA Maker

QnA Maker chiffre automatiquement vos données lorsqu'elles sont conservées dans le cloud, ce qui vous permet de satisfaire aux exigences de votre organisation en matière de sécurité et de conformité.

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Par défaut, votre abonnement utilise des clés de chiffrement gérées par Microsoft. Une option vous permet également de gérer votre abonnement avec vos propres clés. Les clés managées par le client (CMK) offrent plus de flexibilité pour créer, permuter, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données.

QnA Maker utilise la prise en charge de CMK à partir d’Azure Search. Vous devez créer [CMK dans Azure Search à l’aide d’Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Cette instance Azure doit être associée au service QnA Maker pour la rendre compatible avec CMK.

> [!IMPORTANT]
> Votre ressource de service Azure Search doit avoir été créée après le janvier 2019 et ne peut pas se trouver dans le niveau gratuit (partagé). Il n’existe aucune prise en charge pour configurer des clés gérées par le client dans le portail Azure.

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Le service QnA Maker utilise CMK à partir du service Azure Search. Procédez comme suit pour activer des clés CMK :

1. Créez une nouvelle instance Azure Search et activez les [conditions préalables relatives à la clé gérée par le client pour Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Afficher les paramètres de chiffrement](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Lorsque vous créez une ressource QnA Maker, cette ressource est automatiquement associée à une instance Azure Search. Elle ne peut pas être utilisée avec CMK. Pour utiliser CMK, vous devrez associer la nouvelle instance Azure Search créée à l’étape 1. Plus précisément, vous devrez mettre à jour les éléments `AzureSearchAdminKey` et `AzureSearchName` de votre ressource QnA Maker.

   ![Afficher les paramètres de chiffrement](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Créez ensuite un nouveau paramètre d'application :
   * **Name** : À définir avec la valeur `CustomerManagedEncryptionKeyUrl`
   * **Valeur** : Il s’agit de la valeur que vous avez obtenue à l’étape 1 lors de la création de votre instance Azure Search.

   ![Afficher les paramètres de chiffrement](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Lorsque vous avez terminé, redémarrez le runtime. Votre service QnA Maker est désormais compatible CMK.

## <a name="regional-availability"></a>Disponibilité régionale

Les clés gérées par le client sont disponibles dans toutes les régions Azure Search.

## <a name="encryption-of-data-in-transit"></a>Chiffrement des données en transit

Le portail QnA Maker s’exécute dans le navigateur de l’utilisateur. Chaque action déclenche un appel direct à l’API du service cognitif respectif. QnA Maker est donc conforme pour les données en transit.
Toutefois, comme le service du portail QnA Maker est hébergé dans la région USA Ouest, il n’est toujours pas idéal pour les clients non américains. 

## <a name="next-steps"></a>Étapes suivantes

* [Chiffrement dans Azure Search à l’aide de clés CMK dans Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Chiffrement des données au repos](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
