---
title: S’inscrire pour l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Instructions pour s’inscrire et utiliser le service Analyse de texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829643"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>S’inscrire pour l’API Analyse de texte

Les ressources Analyse de texte sont disponibles 24h/24 et 7 j/7 dans le cloud. Avant de pouvoir télécharger votre contenu pour analyse, vous devez vous inscrire pour obtenir une clé d’accès. Chaque appel à l’API nécessite une clé d’accès avec la demande.

+ Démarrez avec un abonnement Azure. Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) pour le tester sans frais.

+ Créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), en choisissant **l’API Analyse de texte**. Votre clé est générée lorsque vous vous inscrivez.

Pour l’Analyse de texte, il existe un niveau gratuit pour l’exploration et l’évaluation et des niveaux payants pour les charges de travail de production. Vous pouvez avoir plusieurs inscriptions dans chaque abonnement : un gratuit, un payant et ainsi de suite. Vous pouvez basculer vers un niveau offrant davantage de transactions si votre volume de demandes augmente.

Il n’existe aucun contrat de niveau de service pour les services dans la préversion ou le niveau gratuit. Pour plus d’informations, consultez la page [Contrat SLA pour Cognitive Services](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

## <a name="how-to-change-tiers"></a>Comment changer de niveaux

Commencez avec un niveau gratuit, puis passez à un niveau facturable pour les charges de travail de production. Une facturation standard de différents niveaux est proposée. Vous pouvez changer de niveau et garder le même point de terminaison et les mêmes clés d’accès.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et [trouvez votre service](text-analytics-how-to-access-key.md).

2. Cliquez sur **Niveau de prix**.

   ![Commande de niveau de prix dans le menu de navigation gauche](../media/portal-pricing-tier.png)

3. Sélectionnez un niveau, puis cliquez sur **Sélectionner**.  Les nouvelles limites prennent effet une fois la sélection traitée. 

   ![Vignettes et bouton Sélectionner dans la page de sélection de niveaux](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Comment la facturation fonctionne

La facturation est basée sur le nombre de transactions. Vous pouvez acheter un bloc de transactions à un niveau spécifique dans un cycle de facturation mensuel. Si vous dépassez, des petits frais sont appliqués pour chaque transaction. Si vous dépassez régulièrement la limite maximale, songez à passer à un niveau supérieur.

Pour plus d’informations, consultez la [page relative à la tarification](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Qu’est-ce qui constitue une transaction dans l’API Analyse de texte ?
L’annotation d’un document est comptabilisée comme une transaction. Les appels de notation Batch tiennent également compte du nombre de documents devant être notés dans la transaction en question. Ainsi, par exemple, si 1 000 documents sont envoyés à des fins d’analyse des sentiments dans un appel d’API unique, 1 000 transactions sont comptabilisées.

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir une clé d’accès](text-analytics-how-to-access-key.md)
