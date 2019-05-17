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
ms.date: 03/25/2019
ms.openlocfilehash: c3d06d3c0f9c86b4fe6495632b48051fd69dc663
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544537"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modèle de tarif pour Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous permet de créez et exécutez des workflows d’intégration automatisés qui permettre mettre à l’échelle dans le cloud. Cet article décrit le fonctionnement de facturation et la tarification pour Azure Logic Apps. Pour plus d’informations concernant la tarification, consultez [tarification de Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modèle de tarification de la consommation

Pour les nouvelles applications logique qui s’exécutent dans le service Azure Logic Apps public ou « global », vous payez uniquement ce que vous utilisez. Ces applications logiques utilisent un modèle tarifaire et un plan basés sur la consommation. Dans la définition de votre application logique, chaque étape est une action. Par exemple, actions :

* Déclencheurs, qui sont des actions particulières. Toutes les applications logiques nécessitent un déclencheur en tant que la première étape.
* Actions « Intégrées » ou natives tels que HTTP, les appels à Azure Functions et de gestion des API, etc.
* Appels aux connecteurs, tels qu’Outlook 365, Dropbox et ainsi de suite
* Contrôler les étapes du flux, telles que des boucles, des instructions conditionnelles et ainsi de suite

Azure Logic Apps mesure toutes les actions qui s’exécutent dans votre application logique. En savoir plus sur le fonctionne de la facturation pour [déclencheurs](#triggers) et [actions](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modèle de tarif fixe

Un [ *environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) offre un moyen privé, isolé et dédié pour pouvoir créer et exécuter des applications logiques qui peuvent accéder aux ressources dans un réseau virtuel Azure. Pour les nouvelles applications logique qui s’exécutent dans une fenêtre ISE, vous payez un tarif mensuel fixe pour les déclencheurs et actions intégrées et également pour les connecteurs Standard.

Votre ISE inclut également un connecteur gratuit Enterprise, qui inclut toutes les connexions que vous le souhaitez. Utilisation de connecteurs d’entreprise supplémentaires est facturée selon le tarif de la consommation d’entreprise.

> [!NOTE]
> Dans une fenêtre ISE, les déclencheurs intégrés et les actions afficher le **Core** étiqueter et de s’exécuter dans l’environnement ISE même en tant que vos applications logiques. Standard et des connecteurs d’entreprise qui affichent la **ISE** étiquette s’exécutent dans l’environnement ISE même en tant que vos applications logiques. Les connecteurs qui ne s’affichent pas l’étiquette ISE s’exécuter dans le service Logic Apps global.

Votre unité de base ISE a résolu la capacité, donc si vous avez besoin de davantage de débit, vous pouvez [ajouter des unités d’échelle](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), soit lors de la création ou ultérieurement. 

Pour plus d’informations concernant la tarification, consultez [tarification de Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Connecteurs

Les connecteurs Azure Logic Apps aident votre logique application accéder aux applications, services et les systèmes localement ou dans le cloud en fournissant [déclencheurs](#triggers), [actions](#actions), ou les deux. Les connecteurs sont classés comme Standard ou Enterprise. Pour une vue d’ensemble sur ces connecteurs, consultez [connecteurs pour Azure Logic Apps](../connectors/apis-list.md). Les sections suivantes fournissent plus d’informations sur comment la facturation des déclencheurs et actions fonctionnent.

<a name="triggers"></a>

## <a name="triggers"></a>Déclencheurs

Les déclencheurs sont des actions particulières qui créent une instance d’application logique, quand un événement spécifique se produit. Les déclencheurs agissent de différentes façons, ce qui affecte la façon dont l’application logique est mesurée. Voici les différents types de déclencheurs qui existent dans Azure Logic Apps :

* **Déclencheur d’interrogation**: Ce déclencheur vérifie constamment un point de terminaison pour les messages qui répondent aux critères de création d’une instance d’application logique et de démarrage du workflow. Même quand aucune application logique n’est créée, Logic Apps mesure chaque requête d’interrogation en tant qu’exécution. Pour spécifier l’intervalle d’interrogation, configurez le déclencheur par le biais du concepteur d’application logique.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Déclencheur Webhook**: Ce déclencheur attend qu’un client envoie une demande à un point de terminaison spécifique. Chaque requête envoyée au point de terminaison webhook est considérée comme une exécution d’action. Par exemple, les déclencheurs Requête et Webhook HTTP sont tous deux des déclencheurs webhook.

* **Déclencheur de périodicité**: Ce déclencheur crée une instance d’application logique en fonction de l’intervalle de périodicité que vous avez configurés dans le déclencheur. Par exemple, vous pouvez définir un déclencheur de périodicité qui s’exécute tous les trois jours ou selon une planification plus complexe.

<a name="actions"></a>

## <a name="actions"></a>Actions

Azure Logic Apps mesure les actions « intégrées », tels que HTTP, en tant qu’actions natives. Par exemple, les actions intégrées incluent des appels HTTP, les appels à partir d’Azure Functions ou gestion des API et contrôlent les étapes de flux tels que les conditions, boucles et instructions switch. Chaque action a son propre type d’action. Par exemple, les actions qui appellent [connecteurs](https://docs.microsoft.com/connectors) ont le type « ApiConnection ». Ces connecteurs sont classés comme Standard ou des connecteurs d’entreprise sont mesurées en fonction de leurs détenteurs respectifs [tarification](https://azure.microsoft.com/pricing/details/logic-apps). Connecteurs d’entreprise dans *aperçu* sont facturées en tant que connecteurs Standard.

Azure Logic Apps mesure toutes les actions ayant réussies ou échouées comme des exécutions. Toutefois, les applications logiques ne contrôler ces actions :

* Actions ignorées en raison de conditions non remplies
* Actions qui ne s’exécutent pas, car l’application logique s’est arrêtée avant la fin

Pour les actions qui s’exécutent dans des boucles, Azure Logic Apps est comptabilisé chaque action pour chaque cycle de la boucle. Par exemple, supposons que vous ayez une boucle « for each » qui traite une liste. Logic Apps mesure une action dans cette boucle en multipliant le nombre d’éléments dans la liste par le nombre d’actions dans la boucle, puis ajoute l’action qui démarre la boucle. Par conséquent, le calcul pour obtenir une liste de 10 éléments est (10 * 1) + 1, ce qui se traduit dans 11 exécutions d’action.

## <a name="disabled-logic-apps"></a>Applications logiques désactivées

Désactivé logic apps ne sont pas facturées, car ils ne peuvent pas créer de nouvelles instances tandis que s’ils sont désactivés.
Une fois que vous avez désactivé une application logique, l’arrêt complet des instances en cours d’exécution peut prendre un certain de temps.

## <a name="integration-accounts"></a>Comptes d'intégration

Tarification de la consommation s’applique aux [comptes d’intégration](logic-apps-enterprise-integration-create-integration-account.md) où vous pouvez Explorer, développer et tester le [B2B et EDI](logic-apps-enterprise-integration-b2b.md) et [traitement XML](logic-apps-enterprise-integration-xml.md) fonctionnalités dans Azure Logic Apps gratuitement coût supplémentaire.
Vous pouvez avoir un compte d’intégration dans chaque région Azure. Chaque compte d’intégration peut stocker jusqu’à un [nombre spécifique d’artefacts](../logic-apps/logic-apps-limits-and-config.md), qui incluent les contrats, partenaires commerciaux, cartes, schémas, assemblys, certificats, configurations de lot, etc.

Azure Logic Apps offre également des comptes d’intégration de base et Standard avec un contrat SLA Logic Apps de prise en charge. Voici quelques conseils que vous pouvez choisir s’il faut utiliser un compte d’intégration de base ou Standard :

* Utiliser des comptes d’intégration de base lorsque vous venez de choix de la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenaire commercial avec une entité professionnelle plus importante.

* Utiliser des comptes d’intégration Standard lorsque vous avez des relations B2B plus complexes et que vous souhaitez augmenter le nombre d’entités que vous pouvez gérer.

Pour plus d’informations concernant la tarification, consultez [tarification d’Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Conservation des données

Toutes les entrées et sorties qui sont stockés dans l’historique des exécutions de votre application logique facturés en fonction d’une application logique [exécuter la période de rétention](logic-apps-limits-and-config.md#run-duration-retention-limits). Pour plus d’informations concernant la tarification, consultez [tarification d’Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

Pour vous aider à surveiller la consommation du stockage de votre application logique, vous pouvez :

* Afficher le nombre d’unités de stockage en Go votre application logique utilise tous les mois.
* Afficher les tailles pour les entrées et les sorties d’une action spécifique dans l’historique des exécutions de votre application logique.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Consommation du stockage application vue logique

1. Dans le portail Azure, recherchez et sélectionnez votre application logique.

1. À partir du menu de votre application logique, sous **surveillance**, sélectionnez **métriques**.

1. Dans le volet de droite, sous **titre du graphique**, à partir de la **métrique** liste, sélectionnez **l’utilisation de la facturation pour les exécutions de la consommation de stockage**.

   Cette mesure vous donne le nombre de la consommation des unités de stockage en Go par mois sont facturées.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Afficher l’entrée d’action et de tailles de sortie

1. Dans le portail Azure, recherchez et sélectionnez votre application logique.

1. Dans le menu de votre application logique, sélectionnez **vue d’ensemble**.

1. Dans le volet de droite, sous **historique des exécutions**, sélectionnez l’exécution qui a les entrées et sorties à vérifier.

1. Sous **exécution d’application logique**, choisissez **détails de l’exécution**.

1. Dans le **application logique détails de l’exécution** , dans la table d’actions, qui répertorie l’état et la durée de chaque action, sélectionnez l’action que vous souhaitez afficher.

1. Dans le **action d’application logique** volet, recherchez les tailles pour les entrées et les sorties de cette action apparaissent respectivement sous **Inputs link** et **Outputs link**.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Azure Logic Apps](logic-apps-overview.md)
* [Créez votre première application logique](quickstart-create-first-logic-app-workflow.md)