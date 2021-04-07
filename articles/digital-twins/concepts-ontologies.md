---
title: Qu’est-ce qu’une ontologie ?
titleSuffix: Azure Digital Twins
description: En savoir plus sur les ontologies d’un secteur en langage DTDL à des fins de modélisation dans un domaine particulier
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034691"
---
# <a name="what-is-an-ontology"></a>Qu’est-ce qu’une ontologie ? 

Le vocabulaire d’une solution Azure Digital Twins est défini à l’aide de [modèles](concepts-models.md), lesquels décrivent les types d’entité présents dans votre environnement.

Parfois, quand votre solution est liée à un secteur particulier, il est plus facile et plus efficace de démarrer avec un ensemble de modèles qui existent déjà pour ce secteur, au lieu de créer votre propre ensemble de modèles à partir de zéro. Ces ensembles de modèles préexistants sont appelés **ontologies**. 

En général, une ontologie est un ensemble de modèles pour un domaine donné, par exemple une structure de bâtiment, un système IoT, une ville intelligente, un réseau électrique, un contenu web, etc. Les ontologies sont souvent utilisées sous forme de schémas pour les graphes de connaissances, car elles présentent les avantages suivants :
* Harmonisation des composants logiciels, de la documentation, des bibliothèques de requêtes, etc.
* Réduction des investissements dans la modélisation conceptuelle et le développement du système
* Simplification de l’interopérabilité des données au niveau sémantique
* Réutilisation des bonnes pratiques, au lieu de partir de zéro ou de « réinventer la roue »

Cet article explique pourquoi et comment utiliser les ontologies pour vos modèles Azure Digital Twins. Il indique également quels sont les ontologies et outils disponibles.

## <a name="using-ontologies-for-azure-digital-twins"></a>Utilisation d’ontologies pour Azure Digital Twins

Les ontologies constituent un excellent point de départ pour les solutions à base de jumeaux numériques. Elles englobent un ensemble de modèles spécifiques à un domaine et de relations entre entités, qui permettent de concevoir, de créer et d’analyser un graphe de jumeaux numériques. Les ontologies permettent aux développeurs de commencer à créer une solution à base de jumeaux numériques à partir d’un point de départ éprouvé et de se concentrer sur la résolution des problèmes métier. Les ontologies fournies par Microsoft sont également conçues pour être facilement extensibles, ce qui vous permet de les personnaliser pour votre solution. 

De plus, l’utilisation de ces ontologies dans vos solutions entraîne leur intégration de manière plus transparente entre les différents partenaires et fournisseurs, car les ontologies fournissent un vocabulaire commun à toutes les solutions.

Dans la mesure où les modèles dans Azure Digital Twins sont représentés en [langage DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), les ontologies à utiliser avec Azure Digital Twins sont également écrites en DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Stratégies d’intégration des ontologies

Il existe trois stratégies possibles d’intégration des ontologies conformes aux standards d’un secteur en langage DTDL. Vous pouvez choisir celle qui vous convient le mieux en fonction de vos besoins :

| Stratégie | Description | Ressources |
| --- | --- | --- |
| **Adopter** | Vous pouvez démarrer votre solution avec une ontologie DTDL open source qui a été conçue sur des normes du secteur d’activité largement adoptées. Vous pouvez utiliser ces ensembles de modèles prêts à l’emploi, ou les étendre avec vos propres ajouts dans le cadre d’une solution personnalisée. | [*Concepts :&nbsp;Adoption&nbsp;d’ontologies&nbsp;conformes aux standards d’un secteur*](concepts-ontologies-adopt.md)<br><br>[*Concepts :&nbsp;Extension&nbsp;d’ontologies*](concepts-ontologies-extend.md) |
| **Convert** | Si vous avez déjà des modèles existants représentés dans un autre format standard, vous pouvez les convertir en DTDL pour les utiliser avec Azure Digital Twins. | [*Concepts :&nbsp;Conversion&nbsp;d’ontologies*](concepts-ontologies-convert.md)<br><br>[*Concepts :&nbsp;Extension&nbsp;d’ontologies*](concepts-ontologies-extend.md) |
| **Auteur** | Vous pouvez toujours développer vos propres modèles DTDL personnalisés à partir de zéro, en vous inspirant des standards applicables au secteur. | [*Concepts : Modèles DTDL*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Utilisation de stratégies d’ontologie pour le développement de modèles

Quelle que soit la stratégie choisie pour l’intégration d’une ontologie avec Azure Digital Twins, vous pouvez suivre la procédure complète ci-dessous afin de créer et charger votre ontologie sous forme de modèles DTDL.

1. Commencez par examiner et comprendre la [modélisation DTDL dans Azure Digital Twins](concepts-models.md).
1. Utilisez ensuite la stratégie d’intégration d’ontologie choisie ci-dessus : [**adoptez**](concepts-ontologies-adopt.md), [**convertissez**](concepts-ontologies-convert.md) ou [**créez**](concepts-models.md) vos modèles en fonction de votre ontologie.
    1. Si nécessaire, [étendez](concepts-ontologies-extend.md) votre ontologie pour la personnaliser selon vos besoins.
1. [Validez](how-to-parse-models.md) vos modèles pour vérifier qu’ils fonctionnent avec des documents DTDL.
1. Une fois que vos modèles sont prêts, chargez-les vers Azure Digital Twins en utilisant les [API](how-to-manage-model.md#upload-models) ou un exemple d’application tel que le [chargeur de modèles Azure Digital Twins](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

Vous devez pouvoir ensuite utiliser ces modèles dans votre instance d’Azure Digital Twins. 

Vous pouvez les visualiser à l’aide d’exemples d’application comme [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) ou [Azure Digital Twins Model Visualizer](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Vous pouvez également les utiliser directement pour créer des [jumeaux numériques](concepts-twins-graph.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les stratégies d’adoption, de conversion et de création d’ontologies :
* [*Concepts : Adoption d’ontologies conformes aux standards du secteur*](concepts-ontologies-adopt.md)
* [*Concepts : Conversion d’ontologies*](concepts-ontologies-convert.md)
* [*Procédure : Gérer les modèles DTDL*](how-to-manage-model.md)

Ou, découvrez comment les modèles permettent de créer des jumeaux numériques : [*Concepts : Jumeaux numériques et graphe de jumeaux*](concepts-twins-graph.md).