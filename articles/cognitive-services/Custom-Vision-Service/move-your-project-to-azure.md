---
title: Déplacer un projet d’évaluation limité vers Azure
titlesuffix: Azure Cognitive Services
description: Découvrez comment déplacer un projet de la version d’évaluation limitée vers Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274448"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Comment déplacer votre projet de la version d’évaluation limitée vers Azure

Comme le Service Vision personnalisée se termine son déplacement vers Azure, de la fin de prise en charge pour les projets de version d’évaluation limitée en dehors d’Azure. Ce document va vous montrer à utiliser les API de Vision personnalisée pour copier votre projet de la version d’évaluation limitée à une ressource Azure.

Prise en charge pour l’affichage des projets de la version d’évaluation limitée sur le [site Web de Vision personnalisée](https://customvision.ai) a pris fin le 25 mars 2019. Ce document montre maintenant comment utiliser les API de Vision personnalisée avec un [le script python migration](https://github.com/Azure-Samples/custom-vision-move-project) sur GitHub) de dupliquer votre projet à une ressource Azure.

Pour plus d’informations, y compris les échéances des clés dans le processus de désapprobation d’évaluation limitée, reportez-vous à la [notes de version](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) ou pour les communications par e-mail envoyées aux propriétaires des projets d’évaluation limitées.

Le [script de migration](https://github.com/Azure-Samples/custom-vision-move-project) vous permet de recréer un projet en téléchargeant, puis téléchargez toutes les balises, régions et des images dans votre itération actuelle. Il vous laisse avec un nouveau projet dans votre nouvel abonnement dont vous pouvez ensuite former.

## <a name="prerequisites"></a>Conditions préalables

- Vous aurez besoin un abonnement Azure valid associé au compte Microsoft ou d’un compte Azure Active Directory (AAD) vous souhaitez utiliser pour vous connecter à la [site Web de Vision personnalisée](https://customvision.ai). 
    - Si vous n’avez pas un compte Azure, [créer un compte](https://azure.microsoft.com/free/) gratuitement.
    - Pour une introduction aux concepts des abonnements et des ressources Azure, reportez-vous à la [guide du développeur Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Créer des ressources de Vision personnalisée dans le portail Azure

Pour utiliser le Service Vision personnalisée avec Azure, vous devrez créer des ressources de formation de Vision personnalisée et de prédiction dans le [Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Plusieurs projets peuvent être associés à une ressource unique. Plus de détails sur [tarification et limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) est disponible. Pour continuer à utiliser le Service Vision personnalisée gratuitement, vous pouvez sélectionner le niveau de F0 dans le portail Azure. 

> [!NOTE]
> Lorsque vous déplacez votre projet de Vision personnalisée à une ressource Azure, il hérite sous-jacent [autorisations]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) de cette ressource Azure. Si d’autres utilisateurs dans votre organisation sont les propriétaires de la ressource Azure que votre projet se trouve dans, ils seront en mesure d’accès à votre projet sur le [site Web de Vision personnalisée](https://customvision.ai). De même, la suppression de vos ressources supprimera vos projets.  

## <a name="find-your-limited-trial-project-information"></a>Rechercher des informations de votre projet d’évaluation limitée

Pour déplacer votre projet, vous devez le _ID de projet_ et _clé de formation_ pour le projet que vous voulez migrer. Si vous n’avez pas ces informations, visitez [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) pour obtenir l’ID et la clé pour chacun de vos projets. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Utilisez l’exemple de code Python pour copier votre projet vers Azure

Suivez le [exemple d’instructions de code](https://github.com/Azure-Samples/custom-vision-move-project), à l’aide de votre clé de version d’évaluation limitée et ID de projet en tant que la documentation de le « source » et la clé à partir de la nouvelle ressource Azure que vous avez créé en tant que la « destination ».

Par défaut, tous les projets de la version d’évaluation limitée sont hébergés dans la région South Central US Azure.

## <a name="next-steps"></a>Étapes suivantes

Votre projet a maintenant été déplacé vers une ressource Azure. Vous devez mettre à jour vos clés d’apprentissage et de prédiction dans toutes les applications que vous avez écrit.

Pour afficher votre projet sur le [site Web de Vision personnalisée](https://customvision.ai), connectez-vous avec le même compte que vous avez utilisé pour vous connecter au portail Azure. Si vous ne voyez pas votre projet, veuillez confirmer que vous êtes dans le même répertoire dans le [site Web de Vision personnalisée](https://customvision.ai) en tant que le répertoire où se trouvent vos ressources dans le portail Azure. Dans le portail Azure et CustomVision.ai, vous pouvez sélectionner votre annuaire dans le menu d’utilisateur de liste déroulante en haut à droite de l’écran.