---
title: Mode débogage de la fonctionnalité de mappage de flux de données dans Azure Data Factory
description: Démarrer une session de débogage interactive lors de la génération de flux de données
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2851517aee0176775bde8b58994f0ed20f6ed01d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212387"
---
# <a name="azure-data-factory-mapping-data-flow-debug-mode"></a>Mode débogage de la fonctionnalité de mappage de flux de données dans Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La fonctionnalité de mappage de flux de données d’Azure Data Factory propose un mode débogage que vous pouvez activer à l’aide du bouton de débogage situé en haut de l’aire de conception. En activant le mode débogage au moment de la conception de flux de données, vous pouvez suivre de manière interactive la transformation de la forme des données à mesure que vous générez et déboguez vos flux de données.

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>Vue d’ensemble
Quand le mode débogage est activé, vous générez de manière interactive votre flux de données avec un cluster interactif Azure Databricks en cours d’exécution. La session se ferme dès que vous désactivez le débogage dans Azure Data Factory. Prenez connaissance des frais horaires engendrés par Azure Databricks pendant la durée d’activation de la session de débogage.

Dans la plupart des cas, nous vous recommandons de créer vos flux de données en mode débogage pour que vous puissiez valider votre logique métier et afficher vos transformations de données avant de publier votre travail dans Azure Data Factory.

## <a name="debug-mode-on"></a>Mode débogage activé
Quand vous activez le mode débogage, un formulaire dans un panneau latéral vous invite à pointer vers votre cluster Azure Databricks interactif et à sélectionner les options relatives à l’échantillonnage source. Vous devez utiliser un cluster Azure Databricks interactif. Ensuite, sélectionnez une taille d’échantillonnage dans chacune de vos transformations Source ou choisissez un fichier texte à utiliser pour vos données de test.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Quand vous exécutez le mode débogage dans Data Flow, vos données ne sont pas écrites dans la transformation Sink. Le but d’une session de débogage est de servir d’atelier de test pour vos transformations. Les récepteurs ne sont pas obligatoires durant le débogage et sont ignorés dans votre flux de données. Si vous souhaitez tester l’écriture des données dans votre récepteur, exécutez le flux de données à partir d’un pipeline Azure Data Factory et utilisez l’exécution Débogage à partir d’un pipeline.

## <a name="debug-settings"></a>Paramètres de débogage
Vous pouvez définir vos paramètres de débogage de manière à ce que chaque source de votre flux de données apparaisse dans le panneau latéral. Pour les modifier, sélectionnez les paramètres de source dans la barre d’outils de conception de Data Flow. Vous pouvez sélectionner les limites et/ou la source de fichier à utiliser pour chacune de vos transformations Source ici. Vous pouvez également sélectionner le cluster Databricks à utiliser pour le débogage.

## <a name="cluster-status"></a>État du cluster
Un indicateur d’état du cluster en haut de l’aire de conception devient vert quand le cluster est prêt pour le débogage. Si votre cluster est déjà chaud, l’indicateur vert apparaît presque instantanément. Si votre cluster n’est pas en cours d’exécution quand vous entrez en mode débogage, vous devez attendre 5 à 7 minutes avant que le cluster ne soit opérationnel. L’indicateur reste jaune tant que le cluster n’est pas prêt. Quand votre cluster est prêt pour le débogage de flux de données, l’indicateur passe au vert.

Une fois le débogage terminé, désactivez-le à l’aide du commutateur pour que votre cluster Azure Databricks puisse se terminer.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Aperçu des données
Quand le débogage est activé, l’onglet d’aperçu des données s’allume dans le panneau inférieur. Si le mode débogage n’est pas activé, Data Flow affiche uniquement les métadonnées entrantes et sortantes pour chacune de vos transformations sous l’onglet Inspecter. L’aperçu des données interroge uniquement le nombre de lignes que vous avez défini comme limite dans vos paramètres de source. Vous devrez peut-être cliquer sur Récupérer les données pour actualiser l’aperçu des données.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Profils de données
La sélection de colonnes individuelles sous l’onglet d’aperçu des données fait apparaître un graphique à l’extrême droite de votre grille de données avec des statistiques détaillées sur chaque champ. Azure Data Factory détermine le type de graphique à afficher en fonction de l’échantillonnage des données. Les champs à cardinalité élevée affichent par défaut des graphiques NULL/NOT NULL tandis que les données catégoriques et numériques à cardinalité faible affichent des graphiques à barres indiquant la fréquence des valeurs de données. Les champs suivants sont également disponibles : longueur max/len des chaînes, valeurs min/max dans les champs numériques, écart type, centiles, comptes et moyenne. 

<img src="media/data-flow/chart.png" width="400">
