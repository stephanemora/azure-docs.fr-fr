---
title: Chiffrement des données au repos de QnA Maker
titleSuffix: Azure Cognitive Services
description: Microsoft propose des clés de chiffrement gérées par Microsoft et vous permet également de gérer vos abonnements Cognitive Services à l’aide de vos propres clés, appelées clés gérées par le client (CMK). Cet article traite du chiffrement des données au repos pour QnA Maker et de l’activation et de la gestion de CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: egeaney
ms.custom: ignite-fall-2021
ms.openlocfilehash: 81267ba4c8b1d903e9ca83ff1333a5347c2c1463
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043753"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>Chiffrement des données au repos de QnA Maker

QnA Maker chiffre automatiquement vos données lorsqu'elles sont conservées dans le cloud, ce qui vous permet de satisfaire aux exigences de votre organisation en matière de sécurité et de conformité.

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Par défaut, votre abonnement utilise des clés de chiffrement gérées par Microsoft. Il existe aussi la possibilité de gérer votre abonnement avec vos propres clés appelées clés gérées par le client (CMK). Les CMK offrent plus de flexibilité pour créer, alterner, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données. Si une CMK est configurée pour votre abonnement, un double chiffrement est fourni, ce qui offre une deuxième couche de protection, tout en vous permettant de contrôler la clé de chiffrement par le biais de votre coffre de clés Azure.

QnA Maker utilise la prise en charge de CMK à partir d’Azure Search. Configurez [CMK dans Recherche Azure à l’aide d’Azure Key Vault](../../search/search-security-manage-encryption-keys.md). Cette instance Azure doit être associée au service QnA Maker pour la rendre compatible avec CMK.


> [!IMPORTANT]
> Votre ressource de service Azure Search doit avoir été créée après le janvier 2019 et ne peut pas se trouver dans le niveau gratuit (partagé). Il n’existe aucune prise en charge pour configurer des clés gérées par le client dans le portail Azure.

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Le service QnA Maker utilise CMK à partir du service Azure Search. Procédez comme suit pour activer des clés CMK :

1. Créez une nouvelle instance Azure Search et activez les [conditions préalables relatives à la clé gérée par le client pour Azure Cognitive Search](../../search/search-security-manage-encryption-keys.md#prerequisites).

   ![Afficher les paramètres de chiffrement 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Lorsque vous créez une ressource QnA Maker, cette ressource est automatiquement associée à une instance Azure Search. Cette instance n’est pas utilisable avec CMK. Pour utiliser CMK, vous devrez associer la nouvelle instance Azure Search créée à l’étape 1. Plus précisément, vous devrez mettre à jour les éléments `AzureSearchAdminKey` et `AzureSearchName` de votre ressource QnA Maker.

   ![Afficher les paramètres de chiffrement 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Créez ensuite un nouveau paramètre d'application :
   * **Nom** : Paramètre à définir sur `CustomerManagedEncryptionKeyUrl`
   * **Valeur** : Utilisez la valeur que vous avez obtenue à l’étape 1 lors de la création de votre instance Recherche Azure.

   ![Afficher les paramètres de chiffrement 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Lorsque vous avez terminé, redémarrez le runtime. Votre service QnA Maker est désormais compatible CMK.

## <a name="regional-availability"></a>Disponibilité régionale

Les clés gérées par le client sont disponibles dans toutes les régions Azure Search.

## <a name="encryption-of-data-in-transit"></a>Chiffrement des données en transit

Le portail QnA Maker s’exécute dans le navigateur de l’utilisateur. Chaque action déclenche un appel direct à l’API Cognitive Services respective. QnA Maker est donc conforme pour les données en transit.
Toutefois, comme le service du portail QnA Maker est hébergé dans la région USA Ouest, il n’est toujours pas idéal pour les clients non américains. 

## <a name="next-steps"></a>Étapes suivantes

* [Chiffrement dans Azure Search à l’aide de clés CMK dans Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Chiffrement des données au repos](../../security/fundamentals/encryption-atrest.md)
* [En savoir plus sur Azure Key Vault](../../key-vault/general/overview.md)
