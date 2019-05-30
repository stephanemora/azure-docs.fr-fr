---
title: Mode débogage de la fonctionnalité de mappage de flux de données dans Azure Data Factory
description: Démarrer une session de débogage interactive lors de la génération de flux de données
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: a50778db5fd57202c17f05407045259371912586
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239189"
---
# <a name="mapping-data-flow-debug-mode"></a>Mode de débogage du mappage de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mappage de flux de données a un mode de débogage, ce qui peut être activé avec le bouton de déboguer des flux de données en haut de l’aire de conception. En activant le mode débogage au moment de la conception de flux de données, vous pouvez suivre de manière interactive la transformation de la forme des données à mesure que vous générez et déboguez vos flux de données. La session de débogage peut être utilisée à la fois dans les sessions de conception de flux de données, ainsi que pendant l’exécution de débogage de pipeline de flux de données.

![Bouton de débogage](media/data-flow/debugbutton.png "le bouton de débogage")

## <a name="overview"></a>Vue d'ensemble
Lorsque le mode débogage est activé, vous générerez interactivement votre flux de données avec un cluster Spark actif. La session se ferme dès que vous désactivez le débogage dans Azure Data Factory. Prenez connaissance des frais horaires engendrés par Azure Databricks pendant la durée d’activation de la session de débogage.

Dans la plupart des cas, nous vous recommandons de créer vos flux de données en mode débogage pour que vous puissiez valider votre logique métier et afficher vos transformations de données avant de publier votre travail dans Azure Data Factory. Vous devez également utiliser le bouton « Debug » sur le panneau de configuration de pipeline pour tester votre flux de données à l’intérieur d’un pipeline.

> [!NOTE]
> Alors que la lumière de mode de débogage est verte dans la barre d’outils de Data Factory, vous serez facturé au taux de débogage de flux de données de 8 cœurs/heure de calcul général avec une minute 60 time-to-live 

## <a name="debug-mode-on"></a>Sur le mode débogage
Quand vous activez le mode débogage, un formulaire dans un panneau latéral vous invite à pointer vers votre cluster Azure Databricks interactif et à sélectionner les options relatives à l’échantillonnage source. Vous devez utiliser un cluster Azure Databricks interactif. Ensuite, sélectionnez une taille d’échantillonnage dans chacune de vos transformations Source ou choisissez un fichier texte à utiliser pour vos données de test.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Quand vous exécutez le mode débogage dans Data Flow, vos données ne sont pas écrites dans la transformation Sink. Le but d’une session de débogage est de servir d’atelier de test pour vos transformations. Les récepteurs ne sont pas obligatoires durant le débogage et sont ignorés dans votre flux de données. Si vous souhaitez tester l’écriture des données dans votre récepteur, exécutez le flux de données à partir d’un pipeline Azure Data Factory et utilisez l’exécution Débogage à partir d’un pipeline.

## <a name="debug-settings"></a>Paramètres de débogage
Paramètres de débogage peut être chaque Source à partir de votre flux de données s’affiche dans le panneau latéral et peuvent également être modifiée en sélectionnant « paramètres de la source » sur la barre d’outils Concepteur de flux de données. Vous pouvez sélectionner les limites et/ou la source de fichier à utiliser pour chacune de vos transformations Source ici. Les limites de ligne dans ce paramètre sont uniquement pour la session de débogage actuelle. Vous pouvez également utiliser le paramètre d’échantillonnage dans la source pour la limitation des lignes dans la transformation Source.

## <a name="cluster-status"></a>État du cluster
Un indicateur d’état du cluster en haut de l’aire de conception devient vert quand le cluster est prêt pour le débogage. Si votre cluster est déjà chaud, l’indicateur vert apparaît presque instantanément. Si votre cluster n’est pas en cours d’exécution quand vous entrez en mode débogage, vous devez attendre 5 à 7 minutes avant que le cluster ne soit opérationnel. L’indicateur reste jaune tant que le cluster n’est pas prêt. Quand votre cluster est prêt pour le débogage de flux de données, l’indicateur passe au vert.

Lorsque vous avez terminé le débogage, désactivez le commutateur de débogage afin que votre cluster Azure Databricks peut se terminer et vous serez n’est plus facturé pour l’activité de débogage.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Aperçu des données
Quand le débogage est activé, l’onglet d’aperçu des données s’allume dans le panneau inférieur. Si le mode débogage n’est pas activé, Data Flow affiche uniquement les métadonnées entrantes et sortantes pour chacune de vos transformations sous l’onglet Inspecter. L’aperçu des données interrogera uniquement le nombre de lignes que vous avez défini en tant que votre limite dans vos paramètres de débogage. Vous devrez peut-être cliquer sur Récupérer les données pour actualiser l’aperçu des données.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Profils de données
La sélection de colonnes individuelles sous l’onglet d’aperçu des données fait apparaître un graphique à l’extrême droite de votre grille de données avec des statistiques détaillées sur chaque champ. Azure Data Factory détermine le type de graphique à afficher en fonction de l’échantillonnage des données. Les champs à cardinalité élevée affichent par défaut des graphiques NULL/NOT NULL tandis que les données catégoriques et numériques à cardinalité faible affichent des graphiques à barres indiquant la fréquence des valeurs de données. Les champs suivants sont également disponibles : longueur max/len des chaînes, valeurs min/max dans les champs numériques, écart type, centiles, comptes et moyenne. 

<img src="media/data-flow/chart.png" width="400">

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous êtes fin de la création et le débogage de votre flux de données, [exécuter à partir d’un pipeline.](control-flow-execute-data-flow-activity.md)

Lorsque vous testez votre pipeline avec un flux de données, utilisez le pipeline [débogage exécuter l’option d’exécution.](iterative-development-debugging.md)
