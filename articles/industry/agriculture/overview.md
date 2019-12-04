---
title: Vue d’ensemble d’Azure FarmBeats
description: Offre une vue d’ensemble d’Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ff28ae7dbff40910ac0431fb47f7f0be3e1569c0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538145"
---
# <a name="overview-of-azure-farmbeats"></a>Vue d’ensemble d’Azure FarmBeats

Azure FarmBeats est un ensemble de services et de capacités Azure conçu pour vous aider à générer rapidement des solutions intelligentes pilotées par les données dans le domaine de l’agriculture. Azure FarmBeats est une offre de la Place de marché Azure qui vous permet d’acquérir, d’agréger et de traiter des données liées à l’agriculture provenant de différentes sources telles que des capteurs, des drones, des caméras ou des satellites, sans investir dans des ressources d’ingénierie des données plus approfondies.

> [!NOTE]
> Azure FarmBeats est actuellement disponible en préversion publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats est fourni sans contrat de niveau de service. Utilisez le [Forum Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) pour obtenir du support technique.

Avec Azure FarmBeats, vous pouvez obtenir des données provenant de différentes sources telles que des capteurs, des satellites ou des drones, dans un contexte agricole (zone géographique d’intérêt).

Vous allez pouvoir :

- Agréger des jeux de données agricoles issus de différents fournisseurs
- Créer rapidement des modèles Intelligence artificielle/Machine Language (AI/ML) en fusionnant différents jeux de données

Azure FarmBeats vous offre un moyen simple et fiable d’effectuer les opérations suivantes :

- Créer une carte de l’exploitation à l’aide d’un fichier GeoJSON.
- Évaluer l’intégrité de l’exploitation à l’aide d’un indice de la végétation et de l’eau basé sur des images satellite.
- Obtenir des recommandations sur le nombre de capteurs à utiliser et l’emplacement où les mettre.
- Effectuer le suivi de l’état de l’exploitation en visualisant les données de sol collectées par des capteurs de différents fournisseurs.
- Obtenir une carte du taux d’humidité du sol basé sur la fusion de données satellite et de capteurs.
- Obtenir des insights exploitables en générant des modèles AI/ML à partir de jeux de données agrégés.
- Créer ou enrichir votre solution agricole numérique en fournissant des conseils pour favoriser l’intégrité de l’exploitation.

Les composants d’Azure FarmBeats sont présentés dans les sections suivantes de cet article.

## <a name="data-hub"></a>Hub de données

Le hub de données Azure FarmBeats est une couche d’API qui permet l’agrégation, la normalisation et la contextualisation de jeux de données d’agriculture issus de différents fournisseurs. À partir de cette préversion, vous pouvez tirer parti de deux fournisseurs de capteurs ([Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) et [Teralytic](https://teralytic.com/)), d’un fournisseur d’imagerie satellite ([Sentinel-2](https://sentinel.esa.int/web/sentinel/home)) et de trois fournisseurs d’images de drone ([senseFly](https://www.sensefly.com/), [SlantRange](https://slantrange.com/) et [DJI](https://dji.com/)). Le hub de données est conçu comme une plateforme d’API. Nous cherchons à intégrer d’autres fournisseurs à Azure FarmBeats pour vous proposer davantage d’options lors de la création de votre solution.

## <a name="accelerator"></a>Accélérateur

Exemple de solution reposant sur le hub de données qui lance votre interface utilisateur et votre développement de modèle. Cette application web tire parti des API pour illustrer la visualisation des données de capteurs ingérées sous forme de graphiques et la visualisation de la sortie du modèle sous forme de cartes. Par exemple, vous pouvez utiliser l’accélérateur pour créer rapidement une exploitation et obtenir facilement une carte de l’indice de végétation ou une carte de placement de capteur pour cette exploitation.

## <a name="resources"></a>Ressources

Pour en savoir plus, visitez le [blog](https://aka.ms/AzureFarmBeats) et les [forums](https://aka.ms/FarmBeatsMSDN) consacrés à Azure FarmBeats.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser Azure FarmBeats, visitez la [Place de marché Azure](https://aka.ms/FarmBeatsMarketplace) afin de démarrer le processus de déploiement.
