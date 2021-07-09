---
title: La fonctionnalité QnA Maker managé est désormais nommée Réponses aux questions personnalisées
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur les modifications apportées à la fonctionnalité QnA Maker.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/11/2021
ms.custom: references_regions
ms.openlocfilehash: 6e8f9a26836be14d4952ba309933511635ff673a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982643"
---
# <a name="qna-maker-managed-is-now-renamed-to-custom-question-answering"></a>La fonctionnalité QnA Maker managé est désormais nommée Réponses aux questions personnalisées

La fonctionnalité [QnA Maker managé (préversion)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-qna-maker-managed-now-in-public-preview/ba-p/1845575) a été lancée en novembre 2020 en tant qu’offre en préversion publique gratuite. Elle introduisait plusieurs nouvelles fonctionnalités, dont une meilleure pertinence, à l’aide d’un ranker de Deep Learning, de réponses précises et d’une prise en charge régionale de bout en bout. 

Dans le cadre de notre effort de consolidation des offres linguistiques de Cognitive Services, QnA Maker managé est désormais une fonctionnalité du service Analyse de texte, qui a été renommée en Réponses aux questions personnalisées.  

## <a name="creating-a-new-custom-question-answering-service"></a>Création d’un service Réponses aux questions personnalisées

[Créez une ressource Analyse de texte](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) pour utiliser la fonctionnalité de réponses aux questions, ainsi que d’autres fonctionnalités telles que la reconnaissance d’entité, l’analyse des sentiments, etc.  

Désormais, lorsque vous créez une ressource Analyse de texte, vous pouvez sélectionner les fonctionnalités que vous souhaitez y inclure. Sélectionnez **Réponses aux questions personnalisées (préversion)** , puis continuez à créer votre ressource.  

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran du menu d’interface utilisateur Créer une ressource Analyse de texte avec la fonctionnalité Réponses aux questions personnalisées sélectionnée]( ./media/select-feature.png) ]( ./media/select-feature.png#lightbox)


Vous ne pouvez plus créer de ressource managée QnA Maker à partir du flux de création de QnA Maker. Au lieu de cela, vous êtes redirigé vers le service Analyse de texte. Aucune modification n’a été apportée à la version stable de QnA Maker. 

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran du menu de création de ressource]( ./media/create-resource.png) ]( ./media/create-resource.png#lightbox)

## <a name="details"></a>Détails

- Toutes les ressources QnA Maker managé (préversion) existantes continuent de fonctionner comme avant. Vous n’avez aucune action à effectuer pour ces ressources.
- Le flux de création pour le service Réponses aux questions personnalisées (préversion) est la principale modification. Le service, le portail, les points de terminaison, le Kit de développement logiciel (SDK) et le reste ne changent pas.
- Le service Réponses aux questions personnalisées (préversion) continue à être proposé en préversion publique gratuite. Cette fonctionnalité est disponible uniquement dans le cadre des ressources Analyse de texte standard. Ne définissez pas votre niveau tarifaire pour les ressources Analyse de texte sur Gratuit.
- Le service Réponses aux questions personnalisées (préversion) est disponible dans les régions suivantes :
    - États-Unis - partie centrale méridionale
    - Europe Nord
    - Australie Est.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrer avec la bibliothèque de client QnA Maker](./quickstarts/quickstart-sdk.md)
* [Démarrer avec le portail QnA Maker](./quickstarts/create-publish-knowledge-base.md)

