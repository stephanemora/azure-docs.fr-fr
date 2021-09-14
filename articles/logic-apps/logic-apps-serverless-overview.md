---
title: Plus de développement d’applications, moins de gestion d’infrastructure
description: Azure serverless vous aide à vous concentrer sur la création d’applications cloud, tout en réduisant les coûts de gestion de l’infrastructure quand vous utilisez Azure Logic Apps et Azure Functions
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 335a553897f2487eef317852f23cd475a92b2f81
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362677"
---
# <a name="azure-serverless-overview-create-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Vue d’ensemble d’Azure serverless : créer des applications et des solutions cloud avec Azure Logic Apps et Azure Functions

Même si « serverless »ne signifie pas « aucun serveur », Azure serverless vous aide à réduire les coûts de gestion de votre infrastructure. Dans le développement d’applications traditionnel, vous pouvez consacrer beaucoup de temps et d’énergie à la discussion et au traitement de solutions d’hébergement, de mise à l’échelle et de supervision pour répondre aux exigences et aux demandes de vos applications. Vous pouvez gérer plus facilement ces aspects dans le cadre d’une application ou solution serverless. La technologie serverless offre d’autres avantages :développement plus rapide, moins de code, simplicité et mise à l’échelle flexible. Toutes ces fonctionnalités vous permettent de vous concentrer davantage sur la logique métier. De plus, la technologie serverless est généralement facturée en fonction de l’utilisation. Donc, sans consommation, pas de frais facturés. Pour plus d’informations, découvrez en plus sur [Azure serverless](https://azure.microsoft.com/solutions/serverless/).

Cet article résume dans les grandes lignes les offres serverless de base dans Azure, que sont Azure Logic Apps et Azure Functions. Les deux services s’alignent sur les principes décrits plus haut et vous aident à créer des solutions et des applications cloud robustes avec un minimum de code.

Pour plus d’informations introductives, consultez les pages Azure consacrées à [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) et [Azure Functions](https://azure.microsoft.com/services/functions/). Pour plus d’informations détaillées, consultez les pages de la documentation [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md) et [Introduction à Azure Functions](../azure-functions/functions-overview.md).

## <a name="azure-logic-apps"></a>Azure Logic Apps

Ce service fournit des méthodes simplifiées pour concevoir, développer et orchestrer des workflows d’intégration automatisés pilotés par les événements qui s’exécutent et se mettent à l’échelle dans le cloud. Avec Azure Logic Apps, vous pouvez utiliser un concepteur visuel pour modéliser rapidement des processus métier en tant que workflows. La première étape d’un workflow est systématiquement un déclencheur. Après le déclencheur, une ou plusieurs actions exécutent les opérations suivantes du workflow. Ces opérations peuvent inclure diverses combinaisons d’actions, notamment la logique conditionnelle et les conversions de données.

Pour connecter vos workflows à d’autres services Azure, services Microsoft, environnements cloud et environnements locaux sans écrire de code, vous pouvez ajouter des déclencheurs et des actions prédéfinis à vos workflows en choisissant parmi des [centaines de connecteurs](/connectors/connector-reference/connector-reference-logicapps-connectors/), tous gérés par Microsoft. Chaque connecteur est en fait un proxy ou un wrapper autour d’une API, ce qui permet au service sous-jacent de communiquer avec Azure Logic Apps. Par exemple, le connecteur Office 365 Outlook offre un déclencheur nommé **À la réception d’un e-mail**. Pour les applications et les solutions serverless, vous pouvez utiliser Azure Logic Apps pour orchestrer plusieurs fonctions créées dans Azure Functions. En procédant ainsi, vous pouvez facilement appeler diverses fonctions en tant que processus unique, en particulier quand le processus nécessite l’utilisation d’une API ou d’un système externe.

Si aucun connecteur n’est disponible pour répondre à vos besoins, vous pouvez utiliser le déclencheur de demande ou d’opération HTTP intégré pour communiquer avec n’importe quel point de terminaison de service. Vous pouvez aussi créer votre propre connecteur en utilisant une API existante.

En fonction du type de ressource d’application logique que vous choisissez, le workflow associé s’exécute dans un environnement Azure Logic Apps multilocataire, Azure Logic Apps monolocataire ou de service d’intégration dédié. Chacun a ses propres fonctionnalités, avantages et modèles de facturation. Le portail Azure offre le moyen le plus rapide de commencer à créer des workflows d’application logique. Toutefois, vous pouvez également utiliser d’autres outils tels que Visual Studio Code, Visual Studio ou Azure PowerShell. Pour plus d’informations, consultez la page [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md).

Pour commencer à utiliser Azure Logic Apps, suivez un guide de [démarrage rapide pour créer votre premier workflow d’application logique dans le portail Azure](quickstart-create-first-logic-app-workflow.md). Vous pouvez aussi essayer d’effectuer ces [étapes permettant de créer un exemple d’application serverless avec Azure Logic Apps et Azure Functions dans Visual Studio](create-serverless-apps-visual-studio.md).

Pour plus d’informations, consultez la documentation suivante :

* [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md)
* [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md)
* [Connecteurs - Azure Logic Apps, Microsoft Power Automate, Microsoft Power Apps](/connectors/connectors)
* [Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps](single-tenant-overview-compare.md)
* [Mesure de l’utilisation, facturation et modèles tarifaires d’Azure Logic Apps](logic-apps-pricing.md)

## <a name="azure-functions"></a>Azure Functions

Ce service offre un moyen simplifié d’écrire et d’exécuter des morceaux de code ou des *fonctions* dans le cloud. Vous pouvez n’écrire que le code dont vous avez besoin pour le problème actuel, sans avoir à configurer une application complète ou l’infrastructure requise, ce qui rend le développement plus rapide et plus productif. Utilisez le langage de développement de votre choix, tel que C#, Java, JavaScript, PowerShell, Python et TypeScript. Vous n’êtes facturé que pour la durée d’exécution de votre code et Azure évolue en fonction des besoins.

Pour commencer à utiliser Azure Functions, essayez de [créer votre première fonction Azure dans le portail Azure](../azure-functions/functions-create-function-app-portal.md).

Pour plus d’informations, consultez la documentation suivante :

* [Qu’est-ce qu’Azure Functions ?](../azure-functions/functions-overview.md)
* [Bien démarrer avec Azure Functions](../azure-functions/functions-get-started.md)
* [Langages pris en charge dans Azure Functions](../azure-functions/supported-languages.md)
* [Options d’hébergement Azure Functions](../azure-functions/functions-scale.md)
* [Tarification d’Azure Functions](../azure-functions/pricing.md)

## <a name="get-started-with-serverless-apps-in-azure"></a>Démarrer avec les applications serverless dans Azure

Azure fournit des outils riches pour le développement, le déploiement et la gestion d’applications sans serveur. Vous pouvez créer des applications serverless avec le portail Azure, Visual Studio ou [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Après avoir créé votre application, vous pouvez [déployer cette application rapidement avec les modèles Azure Resource Manager](logic-apps-deploy-azure-resource-manager-templates.md). Azure fournit également une supervision, à laquelle vous pouvez accéder via la Portail Azure, via l’API ou les kits de développement logiciel (SDK), ou avec des outils intégrés aux journaux Azure Monitor et à Application Insights.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un exemple d’application serverless avec Azure Logic Apps et Azure Functions dans Visual Studio](create-serverless-apps-visual-studio.md)
* [Créer un tableau de bord des insights client sans serveur](logic-apps-scenario-social-serverless.md)