---
title: Déplacer un projet d’évaluation limité vers Azure
titlesuffix: Azure Cognitive Services
description: Découvrez comment déplacer un projet d’évaluation limité vers Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816521"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Comment déplacer votre projet d’évaluation limité vers Azure

Lorsque le service Custom Vision termine son déplacement vers Azure, le support pour les projets d’évaluation limités en dehors d’Azure prend fin. Ce document va vous montrer comment utiliser les API Custom Vision pour copier votre projet d’évaluation limité sur une ressource Azure.

La prise en charge pour l’affichage de projets d’évaluation limités sur le [site web de Custom Vision](https://customvision.ai) a pris fin le 25 mars 2019. Ce document montre maintenant comment utiliser les API Custom Vision avec un [script python de migration](https://github.com/Azure-Samples/custom-vision-move-project) (sur GitHub) pour dupliquer votre projet sur une ressource Azure.

Pour plus d’informations, notamment concernant les délais de validité des clés dans le processus de dépréciation de l’évaluation limitée, reportez-vous aux [notes de version](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) ou eux e-mails envoyés aux propriétaires des projets d’évaluation limités.

Le [script de migration](https://github.com/Azure-Samples/custom-vision-move-project) permet de recréer un projet en téléchargeant, puis en chargeant toutes les balises, régions et images dans votre itération actuelle. Vous avez ainsi dans votre nouvel abonnement un nouveau projet que vous pouvez ensuite former.

## <a name="prerequisites"></a>Prérequis

- Vous aurez besoin un abonnement Azure valide associé au compte Microsoft ou au compte Azure Active Directory (AAD) que vous souhaitez utiliser pour vous connecter au [site web de Custom Vision](https://customvision.ai). 
    - Si vous n’avez pas de compte Azure, [créez-en un](https://azure.microsoft.com/free/) gratuitement.
    - Pour une introduction aux concepts des abonnements et des ressources Azure, reportez-vous au [guide du développeur Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Créer des ressources Custom Vision dans le portail Azure

Pour utiliser le service Custom Vision avec Azure, vous devez créer des ressources de formation et de prédiction Custom Vision dans le [portail Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Plusieurs projets peuvent être associés à une ressource unique. Plus de détails sur la [tarification et les limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) sont disponibles. Pour continuer à utiliser le service Custom Vision gratuitement, vous pouvez sélectionner le niveau de F0 dans le portail Azure. 

> [!NOTE]
> Lorsque vous déplacez votre projet Custom Vision vers une ressource Azure, il hérite des [autorisations]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) sous-jacentes de cette ressource Azure. Si d’autres utilisateurs dans votre organisation sont les propriétaires de la ressource Azure dans laquelle se trouve votre projet , ils pourront accéder à votre projet sur le [site web Custom Vision](https://customvision.ai). De même, la suppression de vos ressources entraînera la suppression de vos projets.  

## <a name="find-your-limited-trial-project-information"></a>Trouver des informations sur votre projet d’évaluation limité

Pour déplacer votre projet, vous avez besoin de l’_ID du projet_ et de la _clé de formation_ pour le projet que vous essayez de migrer. Si vous n’avez pas ces informations, visitez [https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects) pour obtenir l’ID et la clé pour chacun de vos projets. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Utilisez l’exemple de code Python pour copier votre projet vers Azure

Suivez les [instructions de l’exemple de code](https://github.com/Azure-Samples/custom-vision-move-project) et utilisez votre clé d’évaluation limitée et votre ID de projet comme « source » et la clé de la nouvelle ressource Azure que vous avez créée comme « destination ».

Par défaut, tous les projets d’évaluation limitée sont hébergés dans la région Azure USA Centre Sud.

## <a name="next-steps"></a>Étapes suivantes

Votre projet a maintenant été déplacé vers une ressource Azure. Vous devez mettre à jour vos clés de formation et de prédiction dans toutes les applications que vous avez écrites.

Pour afficher votre projet sur le [site web Custom Vision](https://customvision.ai), connectez-vous avec le même compte que celui utilisé pour vous connecter au portail Azure. Si vous ne voyez pas votre projet, vérifiez que vous êtes dans le même répertoire du [site web Custom Vision](https://customvision.ai) que le répertoire où se trouvent vos ressources dans le portail Azure. Dans le portail Azure et CustomVision.ai, vous pouvez sélectionner votre annuaire dans le menu utilisateur déroulant en haut à droite de l’écran.