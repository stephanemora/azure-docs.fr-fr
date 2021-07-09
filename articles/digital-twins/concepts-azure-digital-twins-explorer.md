---
title: Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Découvrez les fonctionnalités et le rôle d’Azure Digital Twins Explorer
author: baanders
ms.author: baanders
ms.date: 4/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: a0ecf0200b56b602468f1ca6c3dbfe19a5860c60
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966378"
---
# <a name="azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer (préversion)

**Azure Digital Twins Explorer** est un outil de développement qui vous aide à visualiser et à interagir avec les données de votre instance Azure Digital Twins, notamment vos [modèles](concepts-models.md) et votre [graphe de jumeaux](concepts-twins-graph.md). 

>[!NOTE]
>Cet outil est actuellement en **préversion publique**.

Voici une vue de la fenêtre de l’Explorateur, présentant des modèles et des jumeaux qui ont été renseignés pour un exemple de graphe :

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer montrant des exemples de modèles et de jumeaux" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

L’interface visuelle est un outil formidable pour l’exploration et la compréhension de la forme de votre graphe et de votre jeu de modèles, ainsi que pour apporter des modifications ponctuelles et précises à des jumeaux et des relations spécifiques.

Cet article contient des informations supplémentaires sur Azure Digital Twins Explorer, notamment ses cas d’usage et une vue d’ensemble de ses fonctionnalités. Pour obtenir des instructions détaillées sur l’utilisation de chaque fonctionnalité, consultez [Guide pratique pour utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md).

## <a name="when-to-use"></a>Quand l’utiliser

Azure Digital Twins Explorer est un outil visuel conçu pour les utilisateurs qui souhaitent explorer leur graphe de jumeaux et modifier les jumeaux et les relations dans le contexte de leur graphe.

Les développeurs trouveront cet outil particulièrement utile dans les scénarios suivants :
* **Exploration** : utilisez l’Explorateur pour en savoir plus sur Azure Digital Twins et la façon dont il représente votre environnement réel. Importez des exemples de modèles et de graphes que vous pouvez afficher et modifier pour vous familiariser avec le service. Pour connaître les étapes à suivre pour commencer à utiliser Azure Digital Twins Explorer, consultez [Démarrage rapide : Bien démarrer avec Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md).
* **Développement** : utilisez l’Explorateur pour afficher et valider votre graphe de jumeaux, ainsi que pour examiner des propriétés spécifiques des modèles, jumeaux et relations. Apportez des modifications ad hoc à votre graphe et à ses données. Pour obtenir des instructions détaillées sur l’utilisation de chaque fonctionnalité, consultez [Guide pratique pour utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md). 

L’objectif principal de l’Explorateur est de vous aider à visualiser et à comprendre votre graphe, et à le mettre à jour en fonction des besoins. Pour les solutions à grande échelle et pour le travail qui doit être répété ou automatisé, utilisez plutôt les [API et les kits SDK](./concepts-apis-sdks.md) pour interagir avec votre instance par le biais de code.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

## <a name="features-and-capabilities"></a>Fonctionnalités et fonctions

Azure Digital Twins Explorer est organisé en panneaux, chacun offrant un ensemble de fonctionnalités différent pour l’exploration et la gestion de vos modèles, jumeaux et relations.

Les sections de l’Explorateur sont les suivantes :
* **Models** : ajoutez, supprimez et affichez les détails de vos modèles à partir d’un affichage de liste.
* **Model Graph** : visualisez vos modèles et leurs interconnexions sous la forme d’un graphe de modèle personnalisable.
* **Twin Graph** : visualisez vos jumeaux et relations sous la forme d’un graphe de jumeaux personnalisable. Créez et supprimez des jumeaux et des relations, et affichez ou modifiez leurs propriétés.
* **Query Explorer** : exécutez des requêtes sur le graphe de jumeaux, et observez les résultats visuels dans le panneau **Twin Graph**.

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence chacun des panneaux décrits ci-dessus." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png":::

Pour obtenir des instructions détaillées sur l’utilisation de chaque fonctionnalité, consultez [Guide pratique pour utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md). 

## <a name="how-to-contribute"></a>Marche à suivre pour contribuer

Azure Digital Twins Explorer est un outil **open source** qui accueille toute contribution au code et à la documentation. L’application hébergée est déployée régulièrement à partir d’un dépôt de code source dans GitHub.

Pour voir le code source de l’outil et lire des instructions détaillées sur la façon de contribuer au code, visitez son dépôt GitHub : [digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer).

Pour voir les instructions permettant de contribuer à cette documentation, consultez le [Guide du contributeur Microsoft Docs](/contribute/).

## <a name="other-considerations"></a>Autres considérations

### <a name="region-support"></a>Prise en charge de la région

Azure Digital Twins Explorer peut être utilisé avec toutes les instances d’Azure Digital Twins dans toutes les [régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

Toutefois, pendant la préversion publique, les données peuvent être envoyées pour traitement dans des régions différentes de celle où l’instance est hébergée. Pour éviter cela dans les situations où la souveraineté des données est un problème, vous pouvez télécharger le [code open source](#how-to-contribute) pour créer une version hébergée localement de l’Explorateur sur votre propre machine.

### <a name="billing"></a>Facturation

Azure Digital Twins Explorer est un outil gratuit qui permet d’interagir avec le service Azure Digital Twins. Bien que l’outil lui-même soit gratuit, des coûts peuvent être facturés pendant son utilisation lorsque des requêtes sont envoyées au service Azure Digital Twins, conformément aux règles tarifaires suivantes : [Tarification Azure Digital Twins](https://azure.microsoft.com/pricing/details/digital-twins/).

## <a name="next-steps"></a>Étapes suivantes 

Découvrez comment utiliser les fonctionnalités d’Azure Digital Twins Explorer en détail : [Guide pratique pour utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md).