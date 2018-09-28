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
ms.date: 09/24/2018
ms.openlocfilehash: b75fba2ba0e9fa922b1252378e0bab326cada7d2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974304"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modèle de tarif pour Azure Logic Apps

Vous pouvez créer et exécuter des flux de travail d’intégration scalables et automatisés dans le cloud avec Azure Logic Apps. Voici les détails du fonctionnement de la facturation et de la tarification de Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modèle de tarification de la consommation

Pour les nouvelles applications logiques que vous créez en utilisant le service Logic Apps public ou « global », vous payez uniquement ce que vous utilisez. Ces applications logiques utilisent un plan de consommation et un modèle de tarif, ce qui signifie que toutes les exécutions d’actions effectuées par une application logique sont mesurées et facturées à l’usage. Chaque étape dans une définition d’application logique est une action, ce qui inclut des déclencheurs, des étapes de flux de contrôle, et des appels aux actions intégrées et aux connecteurs. Pour plus d’informations, consultez [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modèle de tarif fixe

> [!NOTE]
> L’environnement de service d’intégration est disponible en *préversion privée*. Pour demander l’accès, [créez votre demande de participation ici](https://aka.ms/iseprivatepreview).

Pour les nouvelles applications logiques que vous créez avec un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), qui est une instance Logic Apps privée, isolée et utilisant des ressources dédiées, vous payez un prix mensuel fixe pour des actions intégrées et des connecteurs standard libellés ISE. Votre ISE inclut un connecteur entreprise gratuit, mais les connecteurs entreprise supplémentaires sont facturés en fonction du tarif entreprise à l’usage. Pour plus d’informations, consultez [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Déclencheurs

Les déclencheurs sont des actions particulières qui créent une instance d’application logique, quand un événement spécifique se produit. Les déclencheurs agissent de différentes façons, ce qui affecte la façon dont l’application logique est mesurée.

* **Déclencheur d’interrogation**: ce déclencheur vérifie constamment un point de terminaison pour les messages conformes aux critères de création d’une instance d’application logique et de démarrage de flux de travail. Chaque requête d’interrogation compte comme une exécution et est mesurée, même quand aucune instance d’application logique n’est créée. Pour spécifier l’intervalle d’interrogation, configurez le déclencheur par le biais du concepteur d’application logique.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Déclencheur webhook** : ce déclencheur attend qu’un client envoie une requête à un point de terminaison spécifique. Chaque requête envoyée au point de terminaison webhook est considérée comme une exécution d’action. Par exemple, les déclencheurs Requête et Webhook HTTP sont tous deux des déclencheurs webhook.

* **Déclencheur de périodicité** : ce déclencheur crée une instance d’application logique en fonction de l’intervalle de périodicité que vous avez configuré dans le déclencheur. Par exemple, vous pouvez configurer un déclencheur de périodicité qui s’exécute tous les trois jours ou d’après un calendrier plus complexe.

Vous trouverez les exécutions de déclencheur dans le volet Vue d’ensemble de votre application logique, dans la section Historique du déclencheur.

## <a name="actions"></a>Actions

Les actions intégrées, telles celles qui appellent HTTP, Azure Functions ou Gestion des API, ainsi que des étapes de flux de contrôle, sont mesurées en tant qu’actions natives qui ont leurs types respectifs. Les actions qui appellent des [connecteurs](https://docs.microsoft.com/connectors) ont le type « ApiConnection ». Ces connecteurs sont classés en connecteurs standard ou d’entreprise, et sont mesurés en fonction de leur [tarification][pricing] respective. 

Toutes les actions d’exécution ayant réussi ou non sont considérées et mesurées comme des exécutions d’action. Toutefois, les actions qui sont ignorées en raison d’une condition non remplie ou celles qui n’ont pas été exécutées (car l’application logique s’est arrêtée avant son achèvement) ne sont pas considérées comme des exécutions d’action. Les applications logiques désactivées ne peuvent pas instancier d’instances. Elles ne sont donc pas facturées pendant qu’elles sont désactivées.

> [!NOTE]
> Une fois que vous avez désactivé une application logique, l’arrêt complet des instances en cours d’exécution peut prendre un certain de temps.

Les actions qui s’exécutent dans des boucles sont comptées à chaque cycle de la boucle. Par exemple, une action unique dans une boucle « for each » qui traite une liste de 10 éléments est comptée en multipliant le nombre d’éléments de liste (10) par le nombre d’actions dans la boucle (1) plus un pour le démarrage de la boucle. Pour cet exemple, le calcul est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action.

## <a name="integration-account-usage"></a>Utilisation d’un compte d’intégration

La consommation en fonction de l’utilisation inclut un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) à des fins d’exploration, de développement et de test des fonctionnalités de [traitement XML](logic-apps-enterprise-integration-xml.md) et [B2B/EDI](logic-apps-enterprise-integration-b2b.md) de Logic Apps sans coût supplémentaire. Vous pouvez disposer d’un compte d’intégration par région et stocker jusqu’à un [nombre spécifique d’artefacts](../logic-apps/logic-apps-limits-and-config.md), comme les contrats et partenaires commerciaux, cartes, schémas, assemblys, certificats et configurations de lot.

Logic Apps offre également des comptes d’intégration de base et standard avec le contrat SLA Logic Apps pris en charge. Vous pouvez utiliser des comptes d’intégration de base quand vous souhaitez uniquement utiliser la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenariat commercial avec une entité professionnelle plus importante. Les comptes d’intégration standard prennent en charge les relations B2B plus complexes et augmentent le nombre d’entités que vous pouvez gérer. Pour plus d’informations, consultez la page [Tarification Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Logic Apps][whatis]
* [Créer votre première application logique][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

