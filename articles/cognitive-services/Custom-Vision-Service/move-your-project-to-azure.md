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
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821300"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>Comment déplacer votre projet de la version d’évaluation limitée vers Azure à l’aide du site CustomVision.ai


Comme le Service Vision personnalisée est désormais dans [Azure en version préliminaire](https://azure.microsoft.com/services/preview/), prise en charge pour les projets de version d’évaluation limitée en dehors d’Azure se termine. Ce document va vous apprendre à utiliser le [site Web de Vision personnalisée](https://customvision.ai) pour déplacer votre projet de la version d’évaluation limitée à associer à une ressource Azure. 

> [!NOTE]
> Lorsque vous déplacez vos projets de Vision personnalisée à une ressource Azure, ils hériter sous-jacent [autorisations]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) de cette ressource Azure. Si d’autres utilisateurs dans votre organisation sont les propriétaires de la ressource Azure que votre projet se trouve dans, ils seront en mesure d’accès à votre projet sur le [site Web de Vision personnalisée](https://customvision.ai). De même, la suppression de vos ressources supprimera vos projets.  


Pour une introduction aux concepts des abonnements et des ressources Azure, reportez-vous à la [guide du développeur Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Conditions préalables

Vous aurez besoin un abonnement Azure valid associé au même compte Microsoft ou d’un compte Azure Active Directory (AAD) que vous utilisez pour vous connecter à la [site Web de Vision personnalisée](https://customvision.ai). 

Si vous n’avez pas un compte Azure, [créer un compte](https://azure.microsoft.com/free/) gratuitement.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Créer des ressources de Vision personnalisée dans le portail Azure
Pour utiliser le Service Vision personnalisée avec Azure, vous devrez créer des ressources de formation de Vision personnalisée et de prédiction dans le [Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Pour déplacer votre projet à l’aide de ce [site Web de Vision personnalisée](https://customvision.ai) expérience, vous devez créer vos ressources dans la région Sud du centre des États-Unis, car tous les projets de la version d’évaluation limitée sont hébergés dans sud du centre des États-Unis. 

Plusieurs projets peuvent être associés à une ressource unique. Plus de détails sur [tarification et limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) est disponible. Pour continuer à utiliser le Service Vision personnalisée gratuitement, vous pouvez sélectionner le niveau de F0 dans le portail Azure. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Déplacer votre projet de la version d’évaluation limitée à une ressource Azure

1.  Dans votre navigateur web, accédez à la [site Web de Vision personnalisée](https://customvision.ai) et sélectionnez __connectez-vous__. Ouvrez le projet que vous souhaitez migrer vers un compte Azure. 
2.  Ouvrez la page des paramètres pour votre projet en cliquant sur l’icône d’engrenage dans le coin en haut à droite, de l’écran. 

    ![Paramètres du projet est l’icône d’engrenage dans l’angle supérieur droit de la page de projet.](./media/move-your-project-to-azure/settings-icon.png)


3. Cliquez sur __déplacer vers Azure__.

    ![Migration vers Azure bouton est dans la coin inférieur gauche de la page Paramètres du projet.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. Dans la liste déroulante sur le __déplacer vers Azure__ bouton, sélectionnez la ressource Azure que vous souhaitez déplacer votre projet. Cliquez sur __déplacer__. 

5. Si vous ne voyez pas la ressource Azure que vous avez créé précédemment pour le Service Vision personnalisée, il peut être dans un autre répertoire. Pour déplacer votre projet à une ressource dans un autre répertoire, suivez les instructions ci-dessous. 

    ![Fenêtre de Migration de projet.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>Déplacer le projet vers un autre répertoire Azure 

> [!NOTE]
> Dans le portail Azure et CustomVision.ai, vous pouvez sélectionner votre annuaire dans le menu d’utilisateur de liste déroulante en haut à droite de l’écran.   


1. Identifier le répertoire de votre ressource Azure. Vous pouvez trouver le répertoire répertorié sous votre nom d’utilisateur dans l’angle supérieur droit de la barre de menus du portail Azure. 

    ![Votre répertoire est répertorié sous votre nom d’utilisateur dans l’angle supérieur droit de la barre de menus du portail Azure. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Rechercher l’ID de ressource de votre ressource de formation de Vision personnalisée. Vous pouvez le trouver dans le portail Azure en ouvrant votre ressource de formation de Vision personnalisée et en sélectionnant « Propriétés » dans la section « Gestion des ressources ». Votre ID de ressource seront conservées. 

    ![Trouver votre ID de ressource dans le portail Azure en ouvrant votre ressource de formation de Vision personnalisée et en sélectionnant « Propriétés » dans la section « Gestion des ressources ».](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. Vous pouvez également, trouver l’ID de ressource de votre ressource de Vision personnalisée directement dans le site Web de Vision personnalisée [page Paramètres]( https://www.customvision.ai/projects#/settings). Vous devez basculer vers le même répertoire que votre ressource Azure se trouve dans.

    ![Votre ID de ressource est répertoriée pour chaque ressource dans votre page de paramètres sur le site Web de Vision personnalisée.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Maintenant que vous avez votre ID de ressource, retournez sur le projet de Vision personnalisée que vous essayez de déplacer à partir d’une version d’évaluation limitée à une ressource Azure. Pour rappel, vous devrez peut-être revenir à votre répertoire d’origine pour le trouver. Suivez les instructions fournies [ci-dessus](#move-your-limited-trial-project-to-an-azure-resource) pour ouvrir la page de paramètres de votre projet et sélectionnez __déplacer vers Azure__. 


5. Dans le passage à la fenêtre Azure, cochez la case pour « Déplacer vers un autre répertoire Azure ? ». Sélectionnez le répertoire que vous souhaitez déplacer votre projet et entrez l’ID de ressource de la ressource que vous déplacez votre projet pour. Cliquez sur __déplacer__. 



5. N’oubliez pas, votre projet est désormais dans un répertoire différent. Pour trouver votre projet, vous devez basculer vers le même répertoire sur le portail web de Vision personnalisée figurant dans votre projet. Dans les deux le portail Azure et le [site Web de Vision personnalisée](https://customvision.ai), vous pouvez sélectionner votre annuaire dans le menu de compte de la liste déroulante en haut à droite de l’écran. 

## <a name="next-steps"></a>Étapes suivantes

Votre projet a maintenant été déplacé vers une ressource Azure. Vous devez mettre à jour vos clés d’apprentissage et de prédiction dans toutes les applications que vous avez écrit.
