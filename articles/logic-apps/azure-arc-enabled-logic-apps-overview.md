---
title: Présentation - Logic Apps avec Azure Arc
description: Découvrez les workflows Logic Apps à locataire unique qui peuvent s’exécuter partout où Kubernetes peut s’exécuter.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, reylons, archidda, sopai, azla
ms.topic: overview
ms.date: 05/25/2021
ms.openlocfilehash: dcaa0e9fe10d26bb6b1e7d014e0b6e50f09a81f2
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385755"
---
# <a name="what-is-azure-arc-enabled-logic-apps-preview"></a>Présentation de Logic Apps avec Azure Arc (Préversion)

> [!NOTE]
> Cette fonctionnalité est en préversion et elle est soumise aux [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) des préversions de Microsoft Azure.

Avec Logic Apps avec Azure Arc, vous pouvez développer et exécuter des applications logiques basées sur un seul locataire partout où Kubernetes peut s’exécuter. Par exemple, vous pouvez exécuter vos workflows d’application logique sur Azure, Azure Kubernetes Service, localement et même d’autres fournisseurs de cloud. Cette offre fournit une plateforme de gestion unique centralisée via Azure Arc et le portail Azure et vous propose les fonctionnalités suivantes : 

- Utilisez Azure Logic Apps comme plateforme d’intégration.
- Connectez vos workflows à tous vos services, quel que soit l’endroit où ils sont hébergés.
- Exécutez vos solutions d’intégration directement avec vos services.
- Créez et modifiez vos workflows avec Visual Studio Code.
- Déployez à l’aide de votre choix de pipelines pour DevOps.
- Contrôlez votre infrastructure et vos ressources dans Azure, en dehors d’Azure, dans plusieurs clouds, localement et dans des environnements de périphérie.

Pour plus d’informations, consultez la documentation suivante :

- [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)
- [Environnements Logic Apps à un seul locataire et autres environnements Logic Apps](../logic-apps/single-tenant-overview-compare.md)
- [Vue d’ensemble d’Azure Arc](../azure-arc/overview.md)
- [Présentation d’Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Qu’est-ce que Kubernetes avec Azure Arc ?](../azure-arc/kubernetes/overview.md)
- [Qu’est-ce que Kubernetes ?](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

<a name="why-use"></a>

## <a name="why-use-arc-enabled-logic-apps"></a>Pourquoi utiliser Logic Apps avec Azure Arc

Avec Logic Apps avec Azure Arc, vous pouvez créer et déployer des workflows d’application logique de la même façon que dans l’expérience à locataire unique pour Azure Logic Apps. Vous bénéficiez également d’un contrôle et d’une flexibilité accrus quand des applications logiques s’exécutent sur une infrastructure Kubernetes que vous exploitez et gérez.

Il existe des différences mineures entre les expériences Logic Apps avec Azure Arc et à locataire unique pour la création, la conception et le déploiement d’applications logiques. Lorsque vous utilisez Logic Apps avec Azure Arc, la principale différence réside dans le fait que vos applications logiques s’exécutent dans un *emplacement personnalisé*. Cet emplacement est mappé à un cluster Kubernetes avec Azure Arc sur lequel vous avez installé et activé le pack d’extensions de la plateforme Azure App Service.

Par exemple, ce cluster peut avoir une configuration Azure Kubernetes Service, Kubernetes seule ou une autre configuration. Le pack d’extensions vous permet d’exécuter des services de plateforme tels qu’Azure Logic Apps, Azure App Service et Azure Functions sur votre cluster Kubernetes.

Pour plus d’informations, consultez la documentation suivante :

- [Environnements Azure Logic Apps à un seul locataire et autres environnements Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md)
- [Présentation d’Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Qu’est-ce que Kubernetes avec Azure Arc ?](../azure-arc/kubernetes/overview.md)
- [Emplacements personnalisés sur Kubernetes avec Azure Arc](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [App Service, Functions et Logic Apps sur Azure Arc (préversion)](../app-service/overview-arc-integration.md)
- [Configurer un cluster Kubernetes avec Azure Arc pour exécuter App Service, Functions et Logic Apps (préversion)](../app-service/manage-create-arc-environment.md)

<a name="when-to-use"></a>

## <a name="when-to-use-arc-enabled-logic-apps"></a>Quand utiliser Logic Apps avec Azure Arc

Bien que Kubernetes offre davantage de contrôle et de flexibilité, cette solution constitue également une surcharge opérationnelle. Si vous considérez que le service Logic Apps répond à vos besoins, vous êtes invité à continuer à utiliser ce service. Toutefois, envisagez d’utiliser Logic Apps avec Azure Arc lorsque vous rencontrez les scénarios suivants :

- Vous exécutez déjà l’ensemble de vos applications et services sur Kubernetes. Vous souhaitez étendre ces processus et contrôles à tous vos autres services PaaS.

- Vous voulez utiliser Azure Logic Apps comme plateforme d’intégration. Toutefois, vous avez besoin d’une mise en réseau affinée avec un contrôle sur le calcul et de la flexibilité. Vous ne souhaitez pas utiliser un environnement de service d’intégration (ISE) ou App Service Environment (ASE).

- Pour des raisons de sécurité, vous devez contrôler l’emplacement d’exécution de vos applications logiques, par exemple, dans votre propre région ou dans votre propre centre de données. 

- Vous souhaitez exécuter vos applications logiques dans des scénarios multiclouds et utiliser le service Logic Apps comme plateforme d’intégration unique pour toutes vos applications, quel que soit l’emplacement où elles s’exécutent.

<a name="compare"></a>

## <a name="compare-offerings"></a>Comparer les offres

Ce tableau fournit une comparaison générale entre les fonctionnalités des offres Azure Logic Apps actuelles :

:::row:::
   :::column:::
      **Fonctionnalité**
   :::column-end:::
   :::column:::
      **Logic Apps multilocataire (Consommation)**
   :::column-end:::
   :::column:::
      **Logic Apps à locataire unique (Standard)**
   :::column-end:::
   :::column:::
      **Conteneurs autonomes**
   :::column-end:::
   :::column:::
      **Azure Arc**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Développement local
   :::column-end:::
   :::column:::
      Visual Studio Code, Visual Studio
   :::column-end:::
   :::column:::
      Visual Studio Code, notamment l’historique des exécutions et la vue d’ensemble avec débogage de point d’arrêt
   :::column-end:::
   :::column:::
      Visual Studio Code
   :::column-end:::
   :::column:::
      Visual Studio Code, notamment l’historique des exécutions et la vue d’ensemble avec débogage de point d’arrêt
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Hébergement
   :::column-end:::
   :::column:::
      Exécution dans Azure uniquement
   :::column-end:::
   :::column:::
      Exécution dans Azure uniquement
   :::column-end:::
   :::column:::
      Exécution partout où vos conteneurs s’exécutent
   :::column-end:::
   :::column:::
      Exécution n’importe où avec un cluster Kubernetes avec Azure Arc
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Gestion
   :::column-end:::
   :::column:::
      Expérience Logic Apps complètement managée
   :::column-end:::
   :::column:::
      Expérience Logic Apps complètement managée
   :::column-end:::
   :::column:::
      Non managé
   :::column-end:::
   :::column:::
      Expérience Logic Apps managée avec contrôle opérationnel au niveau de Kubernetes
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Surveillance
   :::column-end:::
   :::column:::
      Surveiller dans le portail Azure, notamment l’historique des exécutions, soumettre à nouveau l’exécution et des fonctionnalités Application Insights, si nécessaire
   :::column-end:::
   :::column:::
      Surveiller dans le portail Azure, notamment l’historique des exécutions, soumettre à nouveau l’exécution et les fonctionnalités Application Insights, si nécessaire
   :::column-end:::
   :::column:::
      Surveiller uniquement avec Application Insights ou d’autres outils d’analyse de conteneurs
   :::column-end:::
   :::column:::
      Surveiller dans le portail Azure, notamment l’historique des exécutions, soumettre à nouveau l’exécution et les fonctionnalités Application Insights, si nécessaire
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Mise à l'échelle
   :::column-end:::
   :::column:::
      Contrôler la mise à l’échelle à l’aide du plan Consommation
   :::column-end:::
   :::column:::
      Contrôler la mise à l’échelle à l’aide du plan Standard
   :::column-end:::
   :::column:::
      Non disponible
   :::column-end:::
   :::column:::
      Contrôler la mise à l’échelle à l’aide de [KEDA (Kubernetes-based Event Driven Autoscaling)](https://keda.sh/). Configurer des événements de mise à l’échelle en fonction de la longueur de la file d’attente.
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer et déployer des workflows sur Logic Apps avec Azure Arc](azure-arc-enabled-logic-apps-create-deploy-workflows.md)
