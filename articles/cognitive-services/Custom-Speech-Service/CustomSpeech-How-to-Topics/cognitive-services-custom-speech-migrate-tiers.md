---
title: Migrer les niveaux tarifaires des points de terminaison depuis Custom Speech Service sur Azure | Microsoft Docs
description: Découvrez comment migrer les déploiements des niveaux S0 et S1 vers le niveau S2 des points de terminaison Custom Speech dans Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 71aa20c779ae0c73db3d7ce6f267524c5bf71ea5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214559"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migrer les déploiements vers le nouveau modèle de fixation des prix

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

À compter du mois de juillet 2017, Custom Speech Service offre un [nouveau modèle de fixation des prix](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Le nouveau modèle est *plus facile à comprendre*, *plus simple pour calculer les coûts* et *plus souple* en matière de mise à l’échelle. Pour la mise à l’échelle, Microsoft a introduit le concept d’une unité d’échelle. Chaque unité d’échelle peut gérer cinq demandes simultanées. La mise à l’échelle des demandes simultanées de l’ancien modèle a été définie à 5 demandes simultanées pour le niveau S0, et à 12 pour le niveau S1. Nous avons ouvert ces limites pour vous offrir une plus grande flexibilité dans la gestion de vos besoins en cas d’usage.

Si vous exécutez un ancien niveau S0 ou S1, nous vous recommandons de migrer vos déploiements existants au nouveau niveau S2. Le nouveau niveau S2 couvre les deux niveaux S0 et S1. Vous pouvez voir les options disponibles dans la figure suivante :

![La page « Choisissez votre niveau tarifaire »](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Microsoft gère la migration d’une manière semi-automatique. Tout d’abord, vous déclenchez la migration en sélectionnant le nouveau niveau tarifaire. Ensuite, nous migrez automatiquement votre déploiement.

La correspondance entre les anciens niveaux et les unités d’échelle est indiquée dans le tableau suivant :

| Niveau | Demandes simultanées (ancien modèle) | Migration | Demandes simultanées |
|----- | ----- | ---- | ---- |
| S0 |  5.   |   => **S2** avec 1 unité d’échelle |   5. |
| S1 |  12  |   => **S2** avec 3 unités d’échelle |  15 |

Pour migrer vers le nouveau niveau, effectuez les actions suivantes :

## <a name="step-1-check-your-existing-deployment"></a>Étape 1 : Vérifier votre déploiement existant
Accédez au [portail Custom Speech Service](http://cris.ai) et contrôlez vos déploiements existants. Dans notre exemple, il existe deux déploiements. L’un s’exécute sur un niveau S0, l’autre sur un niveau S1. Les déploiements sont affichés dans la colonne **Deployment Options** (Options de déploiement) du tableau suivant :

![La page des déploiements](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Étape 2 : Sélectionner votre nouveau niveau tarifaire dans le portail Azure
1. Ouvrez un nouvel onglet du navigateur et connectez-vous au [portail Azure](http://ms.portal.azure.com/). 

2. Dans la liste **Abonnements** du volet **Cognitive Services**, sélectionnez votre abonnement de reconnaissance vocale personnalisée. 

3. Dans le volet de votre abonnement, sélectionnez **Niveau tarifaire**.

    ![Le lien « Niveau tarifaire »](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Dans la page **Choisir votre niveau tarifaire**, sélectionnez **Standard S2**. Ce niveau tarifaire est la nouvelle tranche de tarifs simplifiée et plus souple.

5. Cliquez sur le bouton **Sélectionner**.

    ![La page « Choisissez votre niveau tarifaire »](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Étape 3 : Vérifier l’état de migration dans le portail Custom Speech Service
Revenez au portail Custom Speech Service et vérifiez vos déploiements. (Si la fenêtre du navigateur est toujours ouverte, actualisez-la.) 

L’état du déploiement associé doit être basculé sur *Processing* (Traitement en cours). Vous pouvez également valider la migration en vérifiant la colonne **Deployment Options** (Options de déploiement). Vous trouvez là désormais des informations sur les unités d’échelle et la journalisation. Les unités d’échelle doivent refléter votre précédent niveau tarifaire. La journalisation doit également être activée, comme indiqué dans le tableau :

![La colonne des options de déploiement](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Si vous rencontrez des problèmes lors de la migration, contactez-nous.
>

## <a name="next-steps"></a>Étapes suivantes
Pour les autres tutoriels, consultez :
* [Créer un modèle acoustique personnalisé](cognitive-services-custom-speech-create-acoustic-model.md)
* [Créer un modèle linguistique personnalisé](cognitive-services-custom-speech-create-language-model.md)
* [Créer un point de terminaison de reconnaissance vocale personnalisée](cognitive-services-custom-speech-create-endpoint.md)
