---
title: Mode débogage de la fonctionnalité de mappage de flux de données dans Azure Data Factory
description: Démarrer une session de débogage interactive lors de la génération de flux de données
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 945d123c0901722a527e7cc8181c91f09e4e95ec
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014522"
---
# <a name="mapping-data-flow-debug-mode"></a>Mode de débogage du mappage de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="overview"></a>Vue d'ensemble

Le mode débogage de la fonctionnalité de mappage de flux de données d’Azure Data Factory peut être activé à l’aide du bouton « Data Flow Debug » (Débogage de flux de données) situé en haut de l’aire de conception. En activant le mode débogage au moment de la conception de flux de données, vous pouvez suivre de manière interactive la transformation de la forme des données à mesure que vous générez et déboguez vos flux de données. La session de débogage peut être utilisée dans des sessions de conception de flux de données et lors d’exécution de débogage de flux de données dans un pipeline.

![Bouton de débogage](media/data-flow/debugbutton.png "Bouton de débogage")

Lorsque le mode débogage est activé, vous allez générer de manière interactive votre flux de données avec un cluster Spark actif. La session se ferme dès que vous désactivez le débogage dans Azure Data Factory. Prenez connaissance des frais horaires engendrés par Azure Databricks pendant la durée d’activation de la session de débogage.

Dans la plupart des cas, nous vous recommandons de créer vos flux de données en mode débogage pour que vous puissiez valider votre logique métier et afficher vos transformations de données avant de publier votre travail dans Azure Data Factory. Utilisez le bouton « Débogage » sur le panneau du pipeline pour tester votre flux de données dans un pipeline.

> [!NOTE]
> Tant que la lumière du mode débogage est verte dans la barre d’outils Data Factory, vous êtes facturé au taux de débogage de flux de données de 8 cœurs par heure de calcul général, avec une durée de vie de 60 minutes. 

## <a name="cluster-status"></a>État du cluster

L’indicateur d’état du cluster en haut de l’aire de conception devient vert quand le cluster est prêt pour le débogage. Si votre cluster est déjà chaud, l’indicateur vert apparaît presque instantanément. Si votre cluster n’est pas en cours d’exécution quand vous entrez en mode débogage, vous devez attendre 5 à 7 minutes avant que le cluster ne soit opérationnel. L’indicateur tournera jusqu’à être prêt.

Une fois le débogage terminé, désactivez-le à l’aide du commutateur pour que votre cluster Azure Databricks puisse se terminer. Vous n’êtes alors plus facturé pour l’activité de débogage.

## <a name="debug-settings"></a>Paramètres de débogage

Les paramètres de débogage peuvent être modifiés en cliquant sur « Paramètres de débogage » sur la barre d’outils du canevas Flux de données. Vous pouvez sélectionner la limite de ligne ou la source de fichier à utiliser pour chacune de vos transformations Source ici. Les limites de lignes de ce paramètre s’appliquent uniquement à la session de débogage actuelle. Vous pouvez aussi sélectionner le service lié intermédiaire à utiliser pour une source SQL Data Warehouse. 

![Paramètres de débogage](media/data-flow/debug-settings.png "Paramètres de débogage")

Si vous avez des paramètres dans votre flux de données ou dans l’un de ses jeux de données référencés, vous pouvez spécifier les valeurs à utiliser pendant le débogage en sélectionnant l'onglet **Paramètres**.

![Paramètres de débogage](media/data-flow/debug-settings2.png "Paramètres de débogage")

## <a name="data-preview"></a>Aperçu des données

Quand le débogage est activé, l’onglet d’aperçu des données s’allume dans le panneau inférieur. Si le mode débogage n’est pas activé, Data Flow affiche uniquement les métadonnées entrantes et sortantes pour chacune de vos transformations sous l’onglet Inspecter. L’aperçu des données interroge uniquement le nombre de lignes que vous avez défini comme limite dans vos paramètres de débogage. Cliquez sur **Actualiser** pour récupérer l’aperçu des données.

![Aperçu des données](media/data-flow/datapreview.png "Aperçu des données")

Quand vous exécutez le mode débogage dans Data Flow, vos données ne sont pas écrites dans la transformation Sink. Le but d’une session de débogage est de servir d’atelier de test pour vos transformations. Les récepteurs ne sont pas obligatoires durant le débogage et sont ignorés dans votre flux de données. Si vous souhaitez tester l’écriture des données dans votre récepteur, exécutez le flux de données à partir d’un pipeline Azure Data Factory et utilisez l’exécution Débogage à partir d’un pipeline.

### <a name="quick-actions"></a>Actions rapides

Une fois que vous voyez l’aperçu des données, vous pouvez générer une transformation rapide pour convertir en typecast, supprimer ou modifier une colonne. Cliquez sur l’en-tête de colonne, puis sélectionnez l’une des options dans la barre d’outils d’aperçu des données.

![Actions rapides](media/data-flow/quick-actions1.png "Actions rapides")

Une fois que vous avez sélectionné une modification, l’aperçu des données est immédiatement actualisé. Cliquez sur **Confirmer** dans le coin supérieur droit pour générer une nouvelle transformation.

![Actions rapides](media/data-flow/quick-actions2.png "Actions rapides")

**Typecast** et **Modifier** permettent de générer une transformation de colonne dérivée et **Supprimer** de générer une transformation de sélection (Select).

![Actions rapides](media/data-flow/quick-actions3.png "Actions rapides")

> [!NOTE]
> Si vous modifiez votre flux de données, vous devez extraire à nouveau l’aperçu des données avant d’ajouter une transformation rapide.

### <a name="data-profiling"></a>Profilage des données

En sélectionnant une colonne sous l’onglet d’aperçu des données et en cliquant sur **Statistiques** dans la barre d’outils de l’aperçu des données, un graphique s’affiche à l’extrême droite de votre grille de données avec des statistiques détaillées sur chaque champ. Azure Data Factory détermine le type de graphique à afficher en fonction de l’échantillonnage des données. Les champs à cardinalité élevée affichent par défaut des graphiques NULL/NOT NULL tandis que les données catégoriques et numériques à cardinalité faible affichent des graphiques à barres indiquant la fréquence des valeurs de données. Les champs suivants sont également disponibles : longueur max/len des chaînes, valeurs min/max dans les champs numériques, écart type, centiles, comptes et moyenne.

![Statistiques de colonnes](media/data-flow/stats.png "Statistiques de colonnes")

## <a name="next-steps"></a>Étapes suivantes

* Une fois que vous avez terminé la création et le débogage de votre flux de données, [exécutez-le depuis un pipeline](control-flow-execute-data-flow-activity.md).
* Lors du test de votre pipeline avec un flux de données, utilisez l’[option Exécution du débogage](iterative-development-debugging.md) du pipeline.
