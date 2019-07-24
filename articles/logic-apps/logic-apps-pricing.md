---
title: Tarification et facturation - Azure Logic Apps | Microsoft Docs
description: Découvrir le fonctionnement de la tarification et de la facturation d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 04b1d0eda85972517155f80488ad590fb56619ab
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190677"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modèle de tarif pour Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous aide à créer et à exécuter des flux de travail d’intégration automatisés et pouvant être mis à l’échelle dans le cloud. Cet article décrit comment fonctionnent la tarification et la facturation pour Azure Logic Apps. Pour plus d’informations sur les prix, consultez [Tarifs Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modèle de tarification de la consommation

Pour les nouvelles applications logiques qui s’exécutent dans le service Azure Logic Apps public ou « global », vous payez uniquement ce que vous utilisez. Ces applications logiques utilisent un modèle tarifaire et un plan basés sur la consommation. Dans la définition de votre application logique, chaque étape est une action. Ces actions comprennent par exemple :

* Des déclencheurs, qui sont des actions spéciales. Toutes les applications logiques nécessitent un déclencheur comme première étape.
* Des actions « intégrées » ou natives, telles que HTTP, les appels à Azure Functions et la gestion des API, etc.
* Des appels aux connecteurs tels qu’Outlook 365, Dropbox, etc
* Des étapes de flux de contrôle, telles que des boucles, des instructions conditionnelles, etc

Azure Logic Apps mesure toutes les actions qui s’exécutent dans votre application logique. En savoir sur le fonctionnement de la facturation pour les [déclencheurs](#triggers) et les [actions](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modèle de tarif fixe

Un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) vous offre une méthode privée, isolée et dédiée de créer et d’exécuter des applications logiques pouvant accéder aux ressources dans un réseau virtuel Azure. Pour les nouvelles applications logiques qui s’exécutent à l’intérieur d’un ISE, vous payez un [tarif fixe mensuel](https://azure.microsoft.com/pricing/details/logic-apps) pour les actions intégrées et les déclencheurs standard.

Votre ISE inclut aussi un connecteur entreprise gratuit, qui comprend autant de *connexions* que vous le souhaitez. L’utilisation de connecteurs entreprise supplémentaires est facturée selon les [tarifs de la consommation Entreprise](https://azure.microsoft.com/pricing/details/logic-apps). Seuls les connecteurs entreprise universels sont facturés selon les tarifs de la consommation Entreprise. Les connecteurs entreprise en préversion publique sont facturés au [taux d’un connecteur standard](https://azure.microsoft.com/pricing/details/logic-apps).

> [!NOTE]
> Au sein d’un ISE, les déclencheurs et actions intégrés présentent l’étiquette **Core** et s’exécutent dans le même ISE que vos applications logiques. Les connecteurs standard et entreprise qui présentent l’étiquette **ISE** s’exécutent dans le même ISE que vos applications logiques. Les connecteurs qui ne portent pas l’étiquette ISE s’exécutent dans le service Logic Apps global.

Votre unité de base d’ISE dispose d’une capacité fixe ; si vous avez besoin de davantage de débit, vous pouvez [ajouter des unités d’échelle](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), pendant la création ou après. Les applications logiques qui s’exécutent dans un ISE n’entraînent aucun coût en termes de conservation des données.

Pour plus d’informations sur les prix, consultez [Tarifs Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Connecteurs

Les connecteurs Azure Logic Apps aident votre application logique à accéder aux applications, services et systèmes localement ou dans le cloud en fournissant des [déclencheurs](#triggers), des [actions](#actions), ou les deux. Les connecteurs sont classés en tant que connecteurs standard ou entreprise. Pour une vue d’ensemble de ces connecteurs, consultez la [liste des connecteurs Azure Logic Apps](../connectors/apis-list.md). Si aucun connecteur prédéfini n’est disponible pour les API REST que vous voulez utiliser dans vos applications logiques, vous pouvez créer des [connecteurs personnalisés](https://docs.microsoft.com/connectors/custom-connectors), qui ne sont que des wrappers autour de ces API REST. Les connecteurs personnalisés sont facturés comme des connecteurs standard. Les sections offrent plus d’informations sur la facturation des déclencheurs et des actions.

<a name="triggers"></a>

## <a name="triggers"></a>Déclencheurs

Les déclencheurs sont des actions particulières qui créent une instance d’application logique, quand un événement spécifique se produit. Les déclencheurs agissent de différentes façons, ce qui affecte la façon dont l’application logique est mesurée. Voici les différents types de déclencheurs qui existent dans Azure Logic Apps :

* **Déclencheur d’interrogation** : ce déclencheur vérifie constamment un point de terminaison pour les messages conformes aux critères de création d’une instance d’application logique et de démarrage de flux de travail. Même quand aucune application logique n’est créée, Logic Apps mesure chaque requête d’interrogation en tant qu’exécution. Pour spécifier l’intervalle d’interrogation, configurez le déclencheur par le biais du concepteur d’application logique.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Déclencheur de webhook** : ce déclencheur attend qu’un client envoie une requête à un point de terminaison spécifique. Chaque requête envoyée au point de terminaison webhook est considérée comme une exécution d’action. Par exemple, les déclencheurs Requête et Webhook HTTP sont tous deux des déclencheurs webhook.

* **Déclencheur de périodicité** : ce déclencheur crée une instance d’application logique en fonction de l’intervalle de périodicité que vous avez configuré dans le déclencheur. Par exemple, vous pouvez configurer un déclencheur de périodicité qui s’exécute tous les trois jours ou d’après un calendrier plus complexe.

<a name="actions"></a>

## <a name="actions"></a>Actions

Azure Logic Apps mesure les actions « intégrées » (telles que HTTP) en tant qu’actions natives. Par exemple, les actions intégrées incluent les appels HTTP, les appels à partir d’Azure Functions ou de Gestion des API et les étapes de flux de contrôle telles que les conditions, les boucles et les instructions switch. Chaque action a son propre type. Par exemple, les actions qui appellent des [connecteurs](https://docs.microsoft.com/connectors) ont le type « ApiConnection ». Ces connecteurs sont classés en connecteurs standard ou d’entreprise, et sont mesurés en fonction de leur [tarification](https://azure.microsoft.com/pricing/details/logic-apps) respective. Les connecteurs d’entreprise en *préversion* sont facturés comme connecteurs standard.

Azure Logic Apps mesure toutes les actions ayant réussi ou non en tant qu’exécutions. Cependant, Logic Apps ne mesure pas les actions suivantes :

* Actions ignorées en raison de conditions non remplies
* Actions qui ne s’exécutent pas, car l’application logique s’est arrêtée avant la fin

Pour les actions qui s’exécutent dans des boucles, Azure Logic Apps prend en compte chaque action par cycle dans la boucle. Par exemple, supposons que vous ayez une boucle « for each » qui traite une liste. Logic Apps mesure une action dans cette boucle en multipliant le nombre d’éléments dans la liste par le nombre d’actions dans la boucle, puis ajoute l’action qui démarre la boucle. Le calcul pour une liste de 10 éléments est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action.

## <a name="disabled-logic-apps"></a>Applications logiques désactivées

Les applications logiques désactivées ne sont pas facturées, car elles ne peuvent pas créer d’instances lorsqu’elles sont désactivées.
Une fois que vous avez désactivé une application logique, l’arrêt complet des instances en cours d’exécution peut prendre un certain de temps.

## <a name="integration-accounts"></a>Comptes d’intégration

Le modèle de tarification fixé s’applique aux [comptes d’intégration](logic-apps-enterprise-integration-create-integration-account.md) à des fins d’exploration, de développement et de test des fonctionnalités de [traitement XML](logic-apps-enterprise-integration-xml.md) et [B2B/EDI](logic-apps-enterprise-integration-b2b.md) d’Azure Logic Apps sans coût supplémentaire.
Vous pouvez avoir un compte d’intégration par région. Chaque compte d’intégration peut stocker jusqu’à un [nombre spécifique d’artefacts](../logic-apps/logic-apps-limits-and-config.md), qui incluent les contrats, partenaires commerciaux, cartes, schémas, assemblys, certificats, configurations de lot, etc.

Azure Logic Apps offre des comptes d’intégration gratuit, de base et standard. Les niveaux de base et standard sont pris en charge par le contrat SLA Logic Apps, tandis que le niveau Gratuit n’est pas pris en charge par un contrat SLA et a des limites d’utilisation et de débit.

Pour choisir un compte d’intégration gratuit, de base ou standard :

* **Gratuit** : Lorsque vous voulez essayer les scénarios exploratoires, pas les scénarios de production.

* **De base** : Quand vous souhaitez utiliser la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenariat commercial avec une entité professionnelle plus importante.

* **Standard** : Quand vous avez des relations B2B plus complexes et un nombre croissant d’entités que vous devez gérer.

Pour plus d’informations sur les prix, consultez [Tarifs Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Conservation des données

Hormis les applications logiques qui s’exécutent dans un ISE, toutes les entrées et sorties qui sont stockées dans votre historique des exécutions d’applications logiques sont facturées en fonction de la [période de rétention de l’application logique](logic-apps-limits-and-config.md#run-duration-retention-limits). Les applications logiques qui s’exécutent dans un ISE n’entraînent aucun coût en termes de conservation des données. Pour plus d’informations sur les prix, consultez [Tarifs Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

Pour mieux surveiller la consommation de stockage de votre application logique, vous pouvez :

* Affichez le nombre des unités de stockage en Go utilisés mensuellement par votre application logique.
* Affichez les tailles des entrées et sorties d’une action spécifique dans l’historique des exécutions de votre application logique.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Afficher la consommation de stockage de l’application logique

1. Dans le portail Azure, recherchez et sélectionnez votre application logique.

1. Depuis le menu de l’application logique, sous **Surveillance**, sélectionnez **Métriques**.

1. Dans le volet à droite, sous **Titre du graphique**, depuis la liste **Métrique**, sélectionnez **Utilisation de la facturation pour les exécutions de consommation du stockage**.

   Cette mesure vous donne le nombre d’unités de consommation de stockage en Go par mois qui sont facturées.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Afficher les tailles d’entrée et de sortie d’une action

1. Dans le portail Azure, recherchez et sélectionnez votre application logique.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Dans le volet à droite, sous **Historique des exécutions**, sélectionnez l’exécution qui dispose des entrées et des sorties que vous voulez vérifier.

1. Sous **Exécution de l’application logique**, choisissez **Détails de l’exécution**.

1. Dans le volet **Détails de l’exécution de l’application logique**, dans la table des actions qui répertorie la durée et l’état de chaque action, sélectionnez l’action que vous souhaitez afficher.

1. Dans le volet **Action de l’application logique**, trouvez les tailles des entrées et des sorties de l’action pour qu’elles apparaissent sous **Lien des entrées** et **Lien des sorties**.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Azure Logic Apps](logic-apps-overview.md)
* [Créez votre première application logique](quickstart-create-first-logic-app-workflow.md)
