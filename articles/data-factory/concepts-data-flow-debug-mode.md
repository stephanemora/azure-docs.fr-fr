---
title: Mode débogage de la fonctionnalité de mappage de flux de données dans Azure Data Factory
description: Démarrer une session de débogage interactive lors de la génération de flux de données
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147389"
---
# <a name="mapping-data-flow-debug-mode"></a>Mode de débogage du mappage de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Le mode débogage de la fonctionnalité de mappage de flux de données d’Azure Data Factory peut être activé à l’aide du bouton « Data Flow Debug » (Débogage de flux de données) situé en haut de l’aire de conception. En activant le mode débogage au moment de la conception de flux de données, vous pouvez suivre de manière interactive la transformation de la forme des données à mesure que vous générez et déboguez vos flux de données. La session de débogage peut être utilisée dans des sessions de conception de flux de données et lors d’exécution de débogage de flux de données dans un pipeline.

![Bouton de débogage](media/data-flow/debugbutton.png "Bouton de débogage")

## <a name="overview"></a>Vue d'ensemble
Lorsque le mode débogage est activé, vous allez générer de manière interactive votre flux de données avec un cluster Spark actif. La session se ferme dès que vous désactivez le débogage dans Azure Data Factory. Prenez connaissance des frais horaires engendrés par Azure Databricks pendant la durée d’activation de la session de débogage.

Dans la plupart des cas, nous vous recommandons de créer vos flux de données en mode débogage pour que vous puissiez valider votre logique métier et afficher vos transformations de données avant de publier votre travail dans Azure Data Factory. Utilisez le bouton « Débogage » sur le panneau du pipeline pour test votre flux de données dans un pipeline.

> [!NOTE]
> Tant que la lumière du mode débogage est verte dans la barre d’outils Data Factory, vous êtes facturé au taux de débogage de flux de données de 8 cœurs par heure de calcul général, avec une durée de vie de 60 minutes. 

> [!NOTE]
>Quand vous exécutez le mode débogage dans Data Flow, vos données ne sont pas écrites dans la transformation Sink. Le but d’une session de débogage est de servir d’atelier de test pour vos transformations. Les récepteurs ne sont pas obligatoires durant le débogage et sont ignorés dans votre flux de données. Si vous souhaitez tester l’écriture des données dans votre récepteur, exécutez le flux de données à partir d’un pipeline Azure Data Factory et utilisez l’exécution Débogage à partir d’un pipeline.

## <a name="debug-settings"></a>Paramètres de débogage
Les paramètres de débogage peuvent être modifiés en cliquant sur « Paramètres de débogage » sur la barre d’outils du canevas Flux de données. Vous pouvez sélectionner les limites et/ou la source de fichier à utiliser pour chacune de vos transformations Source ici. Les limites de lignes de ce paramètre s’appliquent uniquement à la session de débogage actuelle. Vous pouvez aussi sélectionner le service lié intermédiaire à utiliser pour une source SQL Data Warehouse. 

![Paramètres de débogage](media/data-flow/debug-settings.png "Paramètres de débogage")

## <a name="cluster-status"></a>État du cluster
Un indicateur d’état du cluster en haut de l’aire de conception devient vert quand le cluster est prêt pour le débogage. Si votre cluster est déjà chaud, l’indicateur vert apparaît presque instantanément. Si votre cluster n’est pas en cours d’exécution quand vous entrez en mode débogage, vous devez attendre 5 à 7 minutes avant que le cluster ne soit opérationnel. L’indicateur tournera jusqu’à être prêt.

Une fois le débogage terminé, désactivez-le à l’aide du commutateur pour que votre cluster Azure Databricks puisse se terminer. Vous n’êtes alors plus facturé pour l’activité de débogage.

## <a name="data-preview"></a>Aperçu des données
Quand le débogage est activé, l’onglet d’aperçu des données s’allume dans le panneau inférieur. Si le mode débogage n’est pas activé, Data Flow affiche uniquement les métadonnées entrantes et sortantes pour chacune de vos transformations sous l’onglet Inspecter. L’aperçu des données interroge uniquement le nombre de lignes que vous avez défini comme limite dans vos paramètres de débogage. Vous devrez peut-être cliquer sur Récupérer les données pour actualiser l’aperçu des données.

![Aperçu des données](media/data-flow/datapreview.png "Aperçu des données")

## <a name="data-profiles"></a>Profils de données
La sélection de colonnes individuelles sous l’onglet d’aperçu des données fait apparaître un graphique à l’extrême droite de votre grille de données avec des statistiques détaillées sur chaque champ. Azure Data Factory détermine le type de graphique à afficher en fonction de l’échantillonnage des données. Les champs à cardinalité élevée affichent par défaut des graphiques NULL/NOT NULL tandis que les données catégoriques et numériques à cardinalité faible affichent des graphiques à barres indiquant la fréquence des valeurs de données. Les champs suivants sont également disponibles : longueur max/len des chaînes, valeurs min/max dans les champs numériques, écart type, centiles, comptes et moyenne. 

![Statistiques de colonnes](media/data-flow/stats.png "Statistiques de colonnes")

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez terminé la création et le débogage de votre flux de données, [exécutez-le depuis un pipeline](control-flow-execute-data-flow-activity.md).

Lors du test de votre pipeline avec un flux de données, utilisez l’[option Exécution du débogage](iterative-development-debugging.md) du pipeline.
