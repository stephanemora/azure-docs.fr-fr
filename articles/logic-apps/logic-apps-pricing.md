---
title: Tarification et facturation - Azure Logic Apps | Microsoft Docs
description: Découvrir le fonctionnement de la tarification et de la facturation d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 02/26/2019
ms.openlocfilehash: 9b5452f112c6325dafd5edbe693b90ec2a94abc0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990235"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modèle de tarif pour Azure Logic Apps

Vous pouvez créer et exécuter des flux de travail d’intégration automatisés et pouvant être mis à l’échelle dans le cloud quand vous utilisez Azure Logic Apps. Voici les détails du fonctionnement de la facturation et de la tarification de Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modèle de tarification de la consommation

Pour les nouvelles applications logiques qui s’exécutent dans le service Logic Apps public ou « global », vous payez uniquement ce que vous utilisez. Ces applications logiques utilisent un modèle tarifaire et un plan basés sur la consommation. Dans la définition de votre application logique, chaque étape est une action. Les actions incluent le déclencheur, toutes les étapes de flux de contrôle, les actions intégrées et les appels de connecteur. Logic Apps mesure toutes les actions qui s’exécutent dans votre application logique.  
Pour plus d’informations, consultez [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modèle de tarif fixe

Pour les nouvelles applications logiques qui s’exécutent à l’intérieur d’un [ *environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vous payez un tarif mensuel fixe pour les actions intégrées et des connecteurs standard. Un ISE vous permet de créer et d’exécuter des applications logiques isolées pouvant accéder aux ressources dans un réseau virtuel Azure. 

Votre unité de base ISE a résolu la capacité, donc si vous avez besoin de davantage de débit, vous pouvez [ajouter des unités d’échelle](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), soit lors de la création ou ultérieurement. Votre ISE inclut un connecteur entreprise gratuit, qui comprend autant de connexions que vous le souhaitez. L’utilisation de connecteurs entreprise supplémentaires est facturée selon les tarifs de la consommation Entreprise. 

> [!NOTE]
> L’environnement ISE est dans [ *version préliminaire publique*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour plus d’informations, consultez [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Déclencheurs

Les déclencheurs sont des actions particulières qui créent une instance d’application logique, quand un événement spécifique se produit. Les déclencheurs agissent de différentes façons, ce qui affecte la façon dont l’application logique est mesurée.

* **Déclencheur d’interrogation**: ce déclencheur vérifie constamment un point de terminaison pour les messages conformes aux critères de création d’une instance d’application logique et de démarrage de flux de travail. Même quand aucune application logique n’est créée, Logic Apps mesure chaque requête d’interrogation en tant qu’exécution. Pour spécifier l’intervalle d’interrogation, configurez le déclencheur par le biais du concepteur d’application logique.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Déclencheur webhook** : ce déclencheur attend qu’un client envoie une requête à un point de terminaison spécifique. Chaque requête envoyée au point de terminaison webhook est considérée comme une exécution d’action. Par exemple, les déclencheurs Requête et Webhook HTTP sont tous deux des déclencheurs webhook.

* **Déclencheur de périodicité** : ce déclencheur crée une instance d’application logique en fonction de l’intervalle de périodicité que vous avez configuré dans le déclencheur. Par exemple, vous pouvez configurer un déclencheur de périodicité qui s’exécute tous les trois jours ou d’après un calendrier plus complexe.

## <a name="actions"></a>Actions

Logic Apps mesure les actions intégrées en tant qu’actions natives. Par exemple, les actions intégrées incluent les appels via HTTP, les appels à partir d’Azure Functions ou de Gestion des API et les étapes de flux de contrôle telles que les boucles et les conditions, 
- qui constituent autant de types d’action. Les actions qui appellent des [connecteurs](https://docs.microsoft.com/connectors) ont le type « ApiConnection ». Ces connecteurs sont classés en connecteurs standard ou d’entreprise, et sont mesurés en fonction de leur [tarification][pricing] respective. Les connecteurs d’entreprise en *préversion* sont facturés comme connecteurs standard.

Logic Apps mesure toutes les actions d’exécution ayant réussi ou non en tant qu’exécutions d’action. Logic Apps ne mesure pas les actions suivantes : 

* Actions ignorées en raison de conditions non remplies
* Actions qui ne s’exécutent pas, car l’application logique s’est arrêtée avant la fin

Les applications logiques désactivées ne sont pas facturées, car elles ne peuvent pas créer d’instances.

> [!NOTE]
> Une fois que vous avez désactivé une application logique, l’arrêt complet des instances en cours d’exécution peut prendre un certain de temps.

Pour les actions qui s’exécutent dans des boucles, Logic Apps prend en compte chaque action par cycle dans la boucle. Par exemple, supposons que vous ayez une boucle « for each » qui traite une liste. Logic Apps mesure une action dans cette boucle en multipliant le nombre d’éléments dans la liste par le nombre d’actions dans la boucle, puis ajoute l’action qui démarre la boucle. Le calcul pour une liste de 10 éléments est (10 * 1) + 1, ce qui donne 11 exécutions d’action.

## <a name="integration-account-usage"></a>Utilisation d’un compte d’intégration

L’utilisation en fonction de la consommation s’applique aux [comptes d’intégration](logic-apps-enterprise-integration-create-integration-account.md) à des fins d’exploration, de développement et de test des fonctionnalités de [traitement XML](logic-apps-enterprise-integration-xml.md) et [B2B/EDI](logic-apps-enterprise-integration-b2b.md) de Logic Apps sans coût supplémentaire. Vous pouvez avoir un compte d’intégration par région. Chaque compte d’intégration peut stocker jusqu’à un [nombre spécifique d’artefacts](../logic-apps/logic-apps-limits-and-config.md), qui incluent les contrats, partenaires commerciaux, cartes, schémas, assemblys, certificats, configurations de lot, etc.

Logic Apps offre également des comptes d’intégration de base et standard avec le contrat SLA Logic Apps pris en charge. Vous pouvez utiliser des comptes d’intégration de base quand vous souhaitez simplement utiliser la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenariat commercial avec une entité professionnelle plus importante. Les comptes d’intégration standard prennent en charge les relations B2B plus complexes et augmentent le nombre d’entités que vous pouvez gérer. Pour plus d’informations, consultez la page [Tarification Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Logic Apps][whatis]
* [Créer votre première application logique][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

