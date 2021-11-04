---
title: Qu’est-ce que la classification personnalisée (préversion) dans Azure Cognitive Service for Langage ?
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la classification de texte personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 729cf1ed5d17345c530bd78f9c5c75c15990ab12
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096820"
---
# <a name="what-is-custom-text-classification-preview"></a>Qu’est-ce que la classification de texte personnalisée (préversion) ?

La classification de texte personnalisée est une des fonctionnalités offertes par [Azure Cognitive Service for Langage](../overview.md). Il s’agit d’un service d’API basé sur le cloud qui applique l’intelligence du machine learning à la création de modèles personnalisés pour les tâches de classification de texte. 

La classification de texte personnalisée est proposée dans le cadre des fonctionnalités personnalisées d’Azure Cognitive for Language. Cette fonctionnalité permet à ses utilisateurs de créer des modèles IA personnalisés pour classer du texte dans des catégories personnalisées prédéfinies par l’utilisateur. Un projet de classification personnalisée permet aux développeurs de manière itérative d’étiqueter les données, d’entraîner, d’évaluer et d’améliorer les performances du modèle avant de le rendre disponible pour la consommation. La qualité des données étiquetées impacte considérablement les performances du modèle. Pour simplifier la création et la personnalisation de votre modèle, le service offre un portail web personnalisé accessible dans [Langage Studio](https://aka.ms/languageStudio). Vous pouvez facilement commencer à utiliser le service en suivant les étapes de ce [guide de démarrage rapide](quickstart.md). 

La classification de texte personnalisée prend en charge deux types de projets : 

* **Classification avec une seule étiquette** : vous pouvez attribuer une seule classe à chaque fichier de votre jeu de données. Par exemple, un script de film peut uniquement être classé comme « Action » ou « Thriller ». 
* **Classification avec plusieurs étiquettes** : vous pouvez attribuer plusieurs classes à chaque fichier de votre jeu de données. Par exemple, un script de film peut être classé comme « Action », ou « Action » et « Thriller ». 

## <a name="example-usage-scenarios"></a>Exemples de scénarios d’utilisation

### <a name="automatic-emailsticket-triage"></a>Triage automatique des e-mails/tickets

Les centres de support de tous types reçoivent des milliers à des centaines de milliers d’e-mails et de tickets contenant du texte en forme libre non structuré et des pièces jointes. Le délai de lecture de ces e-mails, d’accusé de réception et de routage vers les experts des équipes internes est crucial. Toutefois, le triage des e-mails à cette échelle, qui implique que des personnes les lisent et les renvoient aux bons services, prend du temps et des ressources précieuses. La classification personnalisée peut permettre d’analyser le triage du texte entrant et de catégoriser le contenu pour qu’il soit automatiquement routé vers le service approprié qui prendra les mesures nécessaires.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>Exploration des connaissances pour améliorer/enrichir la recherche sémantique

La recherche est fondamentale pour les applications qui présentent du contenu textuel aux utilisateurs. Parmi les scénarios courants, citons la recherche dans un catalogue ou dans des documents, la recherche sur un site de vente au détail ou l’exploration de connaissances pour la science des données.De nombreuses entreprises de différents secteurs cherchent à créer une expérience de recherche enrichie sur du contenu privé et hétérogène, qui comprend à la fois des documents structurés et non structurés. Dans le cadre de leur pipeline, les développeurs peuvent utiliser la classification personnalisée afin de catégoriser du texte en classes pertinentes pour leur secteur d’activité. Les classes prédites peuvent être utilisées pour enrichir l’indexation du fichier afin d’obtenir une expérience de recherche plus personnalisée. 

## <a name="application-development-lifecycle"></a>Cycle de vie de développement des applications

La création d’une application de classification personnalisée implique généralement différentes étapes. 

:::image type="content" source="media/development-lifecycle.png" alt-text="Cycle de vie de développement" lightbox="media/development-lifecycle.png":::

Suivez ces étapes pour tirer le meilleur parti de votre modèle :

1. **Définir le schéma** : connaissez vos données et identifiez les différentes classes qui vous intéressent, évitez toute ambiguïté.

2. **Étiqueter les données** : la qualité de l’étiquetage des données est un facteur clé dans la détermination des performances du modèle. Étiquetez tous les fichiers que vous voulez ajouter dans l’entraînement. Les fichiers qui appartiennent à la même classe doivent toujours avoir la même classe, si vous avez un fichier qui peut appartenir à deux classes, utilisez un projet de **classification avec plusieurs classes**. Évitez l’ambiguïté de classe, veillez à ce que vos classes soient clairement dissociées les unes des autres, en particulier pour les projets de classification avec une seule classe.

3. **Entraîner le modèle** : votre modèle commence à apprendre à partir de vos données étiquetées.

4. **Voir les détails de l’évaluation du modèle** : consultez les détails de l’évaluation de votre modèle pour déterminer ses performances quand de nouvelles données lui sont présentées.

5. **Améliorer le modèle** : cherchez à améliorer les performances de votre modèle en examinant les prédictions de modèle incorrectes et la répartition des données.

6. **Déployer le modèle** : le déploiement d’un modèle permet de l’utiliser avec l’[API Analyse](https://aka.ms/ct-runtime-swagger).

7. **Classer le texte** : utilisez votre modèle personnalisé pour les tâches de classification de texte.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez le [guide de démarrage rapide](quickstart.md) pour commencer à utiliser la classification de texte personnalisée.  

* Pendant le cycle de vie du développement de l’application, consultez le [glossaire](glossary.md) pour en savoir plus sur les termes utilisés dans la documentation de cette fonctionnalité. 

* N’oubliez pas de consulter les [limites du service](service-limits.md) pour plus d’informations, comme la [disponibilité régionale](service-limits.md#regional-availability).
