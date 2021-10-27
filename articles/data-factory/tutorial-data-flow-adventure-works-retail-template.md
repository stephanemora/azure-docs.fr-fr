---
title: Modèle ETL pour le modèle de données du secteur de la vente au détail
description: Ce tutoriel présente les étapes d’utilisation du prototype de modèle de données sectoriel pour la vente au détail à l’aide des exemples de données d’AdventureWorks.
author: kromerm
ms.author: aamerril
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 10/18/2021
ms.openlocfilehash: 888a6c78572940dd69ef2161fc1ded979259d63f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181667"
---
# <a name="adventureworks-template-documentation"></a>Documentation du modèle AdventureWorks

Ce document explique comment configurer et utiliser le modèle de pipeline AdventureWorks de Microsoft pour démarrer l’exploration du jeu de données AdventureWorks à l’aide d’Azure Synapse Analytics et du modèle de base de données Retail.

## <a name="overview"></a>Vue d’ensemble
AdventureWorks est un détaillant fictif d’équipements sportifs utilisé pour la démonstration d’applications Microsoft. Dans le cas présent, il est utilisé comme exemple d’utilisation de Synapse Pipelines pour mapper les données de vente au détail vers le modèle de base de données Retail en vue d’une analyse plus approfondie dans Azure Synapse.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Espace de travail Azure Synapse**. [Créez un espace de travail Azure Synapse](../storage/common/storage-account-create.md) si vous n’en avez pas déjà un.

## <a name="find-the-template"></a>Rechercher le modèle

Suivez ces étapes pour localiser le modèle.

1. Accédez à votre espace de travail Synapse. Dans la page d’accueil, sélectionnez **En savoir plus**, puis sélectionnez **Parcourir la galerie**. La galerie de Synapse s’ouvre. Vous pouvez rechercher des jeux de données, des scripts, des pipelines, etc. à installer dans votre espace de travail. 

1. Sélectionnez **Pipelines** et filtrez les résultats avec le mot clé « AdventureWorks ».

1. Sélectionnez le modèle **AdventureWorks**, puis sélectionnez **Continuer**.

Ces étapes ouvrent la page de présentation du modèle.

## <a name="configure-the-template"></a>Configurer le modèle
Le modèle est conçu pour nécessiter une configuration minimale. À partir de la page de présentation du modèle, vous pouvez voir un aperçu de la configuration initiale de départ du pipeline et cliquer sur **Ouvrir un pipeline** pour créer les ressources dans votre propre espace de travail. Vous recevrez une notification indiquant que les 31 ressources du modèle ont été créées et vous pourrez les examiner avant de les valider ou de les publier. Vous trouverez les composants du modèle ci-dessous :

* 17 pipelines : Ils sont planifiés pour garantir le chargement correct des données dans les tables cibles et incluent un pipeline par table source plus les pipelines de planification.
* 14 flux de données : Ils contiennent la logique à charger à partir du système source et déposent les données dans la base de données cible.

Si vous avez chargé le jeu de données AdventureWorks dans une autre base de données, vous pouvez mettre à jour les sources de flux de données pour qu’elles pointent vers ce jeu de données. Sinon, suivez les étapes ci-dessous pour créer une base de données source et cible correspondant au schéma défini dans le modèle.


## <a name="dataset-and-sourcetarget-models"></a>Jeu de données et modèles source/cible
Le jeu de données d’AdventureWorks au format Excel peut être téléchargé à partir de ce [site GitHub](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorks%20Data.zip). En outre, vous pouvez accéder à la [définition de schéma pour les bases de données source et cible](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorksSchemas.xlsx). À l’aide du concepteur de base de données de Synapse, recréez les bases de données source et cible avec le schéma du fichier Excel que vous avez téléchargé précédemment. Pour plus de détails sur le concepteur de base de données, consultez cette [documentation](https://aka.ms/SynapseDatabaseDesignerDocumentation).

Une fois les bases de données créées, assurez-vous que les flux de données pointent vers les bonnes tables en modifiant les listes déroulantes dans les paramètres de la source et du récepteur de la base de données de l’espace de travail. Vous pouvez charger les données dans le modèle source en plaçant les fichiers CSV fournis dans l’exemple de jeu de données dans les dossiers appropriés spécifiés par les tables. Une fois cela fait, il ne reste plus qu’à exécuter les pipelines.

## <a name="troubleshoot-the-pipelines"></a>Dépanner les pipelines
Si le pipeline ne parvient pas à s’exécuter correctement, il y a quelques éléments principaux à vérifier pour rechercher les erreurs.

* Schéma du jeu de données. Assurez-vous que les paramètres de données pour les fichiers CSV sont exacts. Si vous avez inclus des en-têtes de ligne, assurez-vous que l’option « En-têtes de ligne » est activée dans la table de base de données.
* Sources de flux de données. Si vous avez utilisé des noms de colonne ou de table différents de ceux fournis dans l’exemple de schéma, vous devez parcourir les flux de données pour vérifier que les colonnes sont correctement mappées.
* Récepteur de flux de données. Les configurations du schéma et du format des données sur la base de données cible doivent correspondre au modèle de flux de données. Comme ci-dessus, si des modifications ont été apportées, ces éléments doivent être alignés.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [flux de données de mappage](concepts-data-flow-overview.md)
* En savoir plus sur les [modèles de pipeline](solution-templates-introduction.md)
