---
title: Gestion des ressources Azure
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dans cet article, vous créez une clé de point de terminaison facturée à l’usage pour votre compte LUIS afin de fournir un trafic illimité vers votre point de terminaison selon un forfait.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5d3e837cf644e0cb9b35a0cfc715a9dcace592e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082983"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Gérer les clés d’abonnement de point de terminaison Azure

Pour des tests et des prototypes uniquement, utilisez le niveau gratuit (F0). Pour les systèmes de production, utilisez un niveau [payant](https://aka.ms/luis-price-tier). 

> [!NOTE]
> N’utilisez pas la [clé de création](luis-concept-keys.md#authoring-key) pour les requêtes de point de terminaison en production.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Créer une clé de point de terminaison LUIS

1. Connectez-vous à **[Microsoft Azure](https://ms.portal.azure.com/)**. 
2. Cliquez sur l’icône **+** verte de connexion dans le panneau en haut à gauche et recherchez « LUIS » dans la marketplace, puis cliquez sur **Language Understanding** et suivez le lien de **création d’expérience**  pour créer un compte d’abonnement LUIS. 

    ![Recherche Azure](./media/luis-azure-subscription/azure-search.png) 

3. Configurez l’abonnement avec des paramètres comme le nom du compte, les niveaux tarifaires, etc. 

    ![Choix d’API Azure](./media/luis-azure-subscription/azure-api-choice.png) 

4. Une fois que vous avez créé le service LUIS, vous pouvez afficher les clés d’accès générées dans **Gestion des ressources -> Clés**.  

    ![Clés Azure](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > Connectez-vous au site web [LUIS](luis-reference-regions.md) de votre région et [affectez la nouvelle clé de point de terminaison LUIS](luis-how-to-manage-keys.md#assign-endpoint-key). Vous avez besoin du nom de l’abonnement LUIS obtenu à l’étape 3.

## <a name="change-luis-pricing-tier"></a>Modifier le niveau tarifaire LUIS

1.  Dans [Azure](https://portal.azure.com), recherchez votre abonnement LUIS. Cliquez sur l’abonnement LUIS.
    ![Rechercher votre abonnement LUIS](./media/luis-usage-tiers/find.png)
2.  Cliquez sur **Niveau tarifaire** afin de voir les niveaux tarifaires disponibles. 
    ![Afficher les niveaux tarifaires](./media/luis-usage-tiers/subscription.png)
3.  Cliquez sur le niveau tarifaire, puis sur **Sélectionner** pour enregistrer les modifications. 
    ![Modifier votre niveau de paiement LUIS](./media/luis-usage-tiers/plans.png)
4.  Lorsque la modification de tarification est terminée, une fenêtre contextuelle vérifie le nouveau niveau tarifaire. 
    ![Vérifier votre niveau de paiement LUIS](./media/luis-usage-tiers/updated.png)
5. N’oubliez pas d’[affecter cette clé de point de terminaison](luis-how-to-manage-keys.md#assign-endpoint-key) sur la page **Publier** et de l’utiliser dans toutes les requêtes de point de terminaison. 

## <a name="exceed-pricing-tier-usage"></a>Dépassement de l’utilisation du niveau tarifaire
Chaque niveau autorise des requêtes de point de terminaison pour votre compte LUIS à un débit spécifique. Si le débit de requêtes est supérieur à celui autorisé pour votre compte facturé à l’usage par minute ou par mois, les requêtes reçoivent une erreur HTTP « 429 : 429 Trop de requêtes. »

Chaque niveau permet un nombre de requêtes cumulées par mois. Si le nombre total de requêtes dépasse le débit autorisé, les requêtes reçoivent une erreur HTTP « 403 : interdit ».  

## <a name="viewing-summary-usage"></a>Affichage résumé de l’utilisation
Vous pouvez afficher des informations sur l’utilisation de LUIS dans Azure. La page **Vue d’ensemble** affiche des informations récapitulatives récentes, y compris les appels et les erreurs. Si vous effectuez une requête de point de terminaison LUIS, puis consultez immédiatement la **page Vue d’ensemble**, attendez cinq minutes, le temps que l’utilisation s’affiche.

![Affichage résumé de l’utilisation](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Personnalisation des graphiques d’utilisation
Les métriques fournissent une vue plus détaillée des données.

![Mesures par défaut](./media/luis-usage-tiers/metrics-default.png)

Vous pouvez configurer vos graphiques de métriques pour la période et le type de métrique. 

![Mesures personnalisées](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Alerte de seuil de nombre total de transactions
Si vous souhaitez connaître le moment où vous avez atteint un certain seuil de transactions (10 000 transactions, par exemple), vous pouvez créer une alerte. 

![Alertes par défaut](./media/luis-usage-tiers/alert-default.png)

Ajout d’une alerte de métrique pour le **nombre total d’appels** pendant une certaine période. Ajouter les adresses de messagerie de toutes les personnes qui doivent recevoir l’alerte. Ajoutez des webhooks pour tous les systèmes qui doivent recevoir l’alerte. Vous pouvez également exécuter une application logique lorsque l’alerte est déclenchée. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser des [versions](luis-how-to-manage-versions.md) pour gérer les modifications apportées à votre application LUIS.