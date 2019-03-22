---
title: Fonctionnalités de recherche à l’aide de blocs-notes Sentinel version préliminaire d’Azure | Microsoft Docs
description: Cet article décrit comment utiliser des blocs-notes avec les fonctionnalités de recherche Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107676"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Utiliser des blocs-notes pour la recherche d’anomalies

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel Azure tire parti de la puissance de bloc-notes interactives Jupyter pour fournir un aperçu et les actions d’approfondir ou de recherche d’anomalies de sécurité au sein de votre environnement. Sentinel Azure est préinstallé avec les ordinateurs portables qui ont été développées par les analystes de sécurité de Microsoft. Chaque bloc-notes est spécialement conçus avec un flux de travail autonome pour un cas d’usage spécifiques. Visualisations sont incluses dans chaque bloc-notes pour l’exploration de données plus rapide et de chasse de menace. Vous personnalisez les notebooks intégrés pour répondre à vos besoins, de créer de nouveaux ordinateurs portables à partir de zéro ou d’importer des ordinateurs portables à partir de l’Azure Sentinel' Communauté GitHub. Les blocs-notes Jupyter sont importés en tant que projet dans la page Azure Notebooks – Cela permet aux utilisateurs à accéder à tous leurs blocs-notes Azure Sentinel au même endroit. Ordinateurs portables peuvent être exécutés avec un clic sur un bouton ou peuvent être configurés par l’utilisateur pour correspondre à leur environnement.

L’expérience entièrement intégrée permet les blocs-notes à exécuter sur le Cloud de calcul et de stockage sans aucune surcharge de maintenance sous-jacent. La possibilité de tirer parti de l’écosystème de blocs-notes Jupyter existant vous permet de modèles de source de réactions, sans partager les données client. 


Chaque bloc-notes est hébergé sur les blocs-notes Azure (actuellement en version préliminaire), un environnement de développement interactif dans le cloud Azure. Les blocs-notes sont toujours accessibles, toujours disponible à partir de n’importe quel navigateur, n’importe où dans le monde entier.  Le Azure Sentinel' notebooks intégrés d’investigation et de chasse sont clonés dans un projet qui vous appartient et que vous pouvez modifier et adapter à votre environnement. Les blocs-notes intégrées les plus populaires sont notamment :

- **Alerte d’investigation et de chasse**: Ce bloc-notes interactif permet un triage rapide de différentes classes d’alertes en récupérant des activités connexes et enrichir l’alerte avec l’activité associée et des données à partir de laquelle l’alerte a été générée.

- **Hôte du point de terminaison guidée chasse**: Vous permet de recherche pour rechercher des signes de violation de la sécurité en explorant les activités pertinentes de sécurité associées à un hôte de point de terminaison.  

- **Office connectez-vous guidée chasse**: Vous permet de recherche pour les connexions suspectes en visualisant des emplacements géographiques de journaux suspects, ainsi que d’affichage de connexion inhabituelles dérivés des données d’Office 365.

## <a name="run-a-notebook"></a>Exécuter une instance notebook
Dans l’exemple suivant, nous exécutons le bloc-notes intégré à rechercher approfondie approfondissement des anomalies d’emplacement pour voir si tout le monde dans un emplacement inattendu fait quelque chose sur votre réseau.

1. Dans le portail Azure Sentinel, cliquez sur **blocs-notes** , puis sélectionnez un des ordinateurs portables intégrés.
  
   ![Sélectionnez le bloc-notes](./media/notebooks/select-notebook.png)

3. Cliquez sur **importation** pour cloner le référentiel GitHub dans votre projet Azure Notebooks.
   ![Bloc-notes d’importation](./media/notebooks/import1.png)
4. Chaque bloc-notes vous guide tout au long des étapes d’exécution d’une recherche ou une enquête. Modèles, bibliothèques et autres dépendances et la configuration pour la connectivité à Azure Sentinel est automatiquement importé pour permettre l’exécution d’un seul clic. Tout code et les bibliothèques nécessaires à l’exécution d’un bloc-notes sont préchargées. Vous pouvez commencer immédiatement un bloc-notes en cours d’exécution par rapport à votre espace de travail Analytique de journal sans aucune configuration.

   ![Importation de dépôt](./media/notebooks/import2.png)

5. Explorez, modifier et exécuter tous les exemples de notebooks fournis. Ces peuvent être utilisés en tant que blocs de construction pour de nombreux scénarios différents.

   ![Sélectionnez le bloc-notes](./media/notebooks/import3.png)



## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment exécuter une investigation de chasse à l’aide de notebooks dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants :

- [Recherche de façon proactive les menaces](hunting.md)
- [Utiliser des signets pour enregistrer des informations intéressantes lors de la recherche](bookmarks.md)