---
title: Mise en route avec l’Explorateur de mesures Azure
description: Découvrez comment créer votre premier graphique de métrique avec l’Explorateur de mesures Azure.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537335"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Prise en main Azure Metrics Explorer

## <a name="where-do-i-start"></a>Où commencer
Azure Monitor Metrics Explorer est un composant du portail Microsoft Azure qui permet de tracer des graphiques, de corréler visuellement des tendances et d’investiguer sur les pics et les creux des valeurs de métriques. Utilisez l’Explorateur de mesures pour examiner l’intégrité et l’utilisation de vos ressources. Démarrez dans l’ordre suivant :

1. [Choisir une ressource et une métrique](#create-your-first-metric-chart) et vous voyez un graphique de base. Puis [sélectionner une plage horaire](#select-a-time-range) qui est pertinent pour votre investigation.

1. Essayez [appliquant des filtres de dimension et de fractionnement](#apply-dimension-filters-and-splitting). Les filtres et fractionnement vous permettent d’analyser les segments de la métrique contribuent à la valeur métrique globale et identifier les aberrations possible.

1. Utilisez [paramètres avancés](#advanced-chart-settings-and-next-steps) pour personnaliser le graphique avant d’épingler aux tableaux de bord. [Configurer des alertes](alerts-metric-overview.md) pour recevoir des notifications lorsque la valeur de métrique dépasse ou descend sous un seuil.

## <a name="create-your-first-metric-chart"></a>Créer votre premier graphique de métrique

Pour créer un graphique de mesures, à partir de votre ressource, un groupe de ressources, un abonnement ou une vue d’Azure Monitor, ouvrez le **métriques** onglet et procédez comme suit :

1. Le sélecteur de ressources, sélectionnez la ressource pour laquelle vous souhaitez voir les métriques. (La ressource est présélectionnée si vous avez ouvert **métriques** dans le contexte d’une ressource spécifique).

    > ![Sélectionner une ressource](./media/metrics-getting-started/resource-picker.png)

2. Pour certaines ressources, vous devez choisir un espace de noms. L’espace de noms est simplement un moyen d’organiser des mesures afin que vous pouvez les trouver facilement. Par exemple, les comptes de stockage ont des espaces de noms distincts pour le stockage des métriques de fichiers, Tables, objets BLOB et files d’attente. Plusieurs types de ressources ont uniquement un espace de noms.

3. Sélectionnez une mesure dans la liste des mesures disponibles.

    > ![Sélectionner une mesure](./media/metrics-getting-started/metric-picker.png)

4. Si vous le souhaitez, vous pouvez modifier l’agrégation des mesures. Par exemple, vous souhaiterez peut-être votre graphique pour afficher les valeurs minimum, maximum ou moyenne de la mesure.

> [!NOTE]
> Utilisez le **ajouter une métrique** bouton et répétez ces étapes si vous souhaitez afficher plusieurs mesures tracées dans le même graphique. Pour plusieurs graphiques dans une seule vue, sélectionnez le **ajouter un graphique** bouton en haut.

## <a name="select-a-time-range"></a>Sélectionner un intervalle de temps

Par défaut, le graphique affiche les dernières 24 heures de données de métriques. Utilisez le **sélecteur d’heure** Panneau de configuration pour modifier l’intervalle de temps, un zoom avant ou effectuer un zoom arrière sur votre graphique. 

![Volet modifications de la plage de temps](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Appliquer des filtres de dimension et de fractionnement

[Filtrage](metrics-charts.md#apply-filters-to-charts) et [fractionnement](metrics-charts.md#apply-splitting-to-a-chart) sont de puissants outils de diagnostic pour les mesures qui ont des dimensions. Ces fonctionnalités montrent comment les divers impact de segments métrique (« valeurs de dimension ») la valeur globale de la mesure et vous permettent d’identifier les aberrations possible.

- **Filtrage** vous permet de choisir les valeurs de dimension sont inclus dans le graphique. Par exemple, vous pouvez souhaiter afficher les demandes réussies lorsque vous traitez la *temps de réponse de serveur* métrique. Vous devez appliquer le filtre sur la *succès de la demande* dimension. 

- **Fractionnement** contrôles si le graphique affiche distincts de lignes pour chaque valeur d’une dimension ou regroupe les valeurs en une seule ligne. Par exemple, vous pouvez voir une ligne pour un temps de réponse moyen sur toutes les instances de serveur, ou consultez des lignes distinctes pour chaque serveur. Vous devez appliquer le fractionnement sur les *instance_serveur* dimension pour afficher des lignes distinctes.

Consultez [exemples de graphiques](metric-chart-samples.md) qu’ont filtrant et en segmentant appliqué. L’article explique les étapes ont été utilisées pour configurer les graphiques.

## <a name="advanced-chart-settings-and-next-steps"></a>Paramètres du graphique avancé et étapes suivantes

Vous pouvez personnaliser le style de graphique, titre et modifier les paramètres du graphique avancé. Lorsque vous avez terminé avec la personnalisation, l’épingler au tableau de bord pour enregistrer votre travail. Vous pouvez également configurer des alertes de métriques. Suivez [documentation du produit](metrics-charts.md) pour en savoir plus sur celles-ci et d’autres fonctionnalités avancées d’explorer des métriques Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes

* [Afficher la liste des métriques disponibles pour les services Azure](metrics-supported.md)
* [En savoir plus sur les fonctionnalités avancées de Metrics Explorer](metrics-charts.md)
* [Consultez les exemples de graphiques configurés](metric-chart-samples.md)
