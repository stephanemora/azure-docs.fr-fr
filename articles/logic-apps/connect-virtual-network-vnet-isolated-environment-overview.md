---
title: Accéder aux réseaux virtuels Azure à partir d’Azure Logic Apps avec des environnements de service d’intégration (ISE)
description: Cette vue d’ensemble décrit la manière dont les environnements de service d’intégration (ISE) aident les applications logiques à accéder aux réseaux virtuels Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 1ef8c8eec3865f2a6e363e7da1dbda9504b81c05
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546438"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps à l’aide d’environnements de service d’intégration (ISE)

Parfois, vos applications logiques et les comptes d’intégration ont besoin d’accéder à des ressources sécurisées, telles que des machines virtuelles (VM) et d’autres systèmes ou services, qui se trouvent dans un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer cet accès, vous pouvez [créer un *environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) où vous pouvez exécuter vos applications logiques et créer votre intégration des comptes.

Lorsque vous créez une fenêtre ISE, Azure déploie une instance privée et isolée du service Logic Apps dans votre réseau virtuel Azure. Cette instance privée utilise des ressources dédiées, comme du stockage, et s’exécute séparément à partir du service Logic Apps « mondial » public. Séparer votre instance privée isolé et l’instance globale publique permet également de réduire l’impact que les autres clients Azure peuvent avoir sur les performances de vos applications, ce qui sont également connu sous le [effet de « voisins bruyants »](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Après avoir créé votre ISE, lorsque vous accédez à créer votre compte d’application ou de l’intégration de logique, vous pouvez sélectionner votre ISE en tant qu’emplacement de la logique application ou l’intégration de votre compte :

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Votre application logique peut désormais accéder directement aux systèmes qui sont trouvent dans ou connecté à votre réseau virtuel à l’aide d’un de ces éléments :

* Un **ISE**-étiqueté connecteur pour ce système, telles que SQL Server
* Un **Core**-étiquetées intégré déclencheur ou action, telle que le déclencheur HTTP ou l’action
* Un connecteur personnalisé

Cette vue d’ensemble décrit plus en détail comment une ISE offre à vos applications logiques et intégration de comptes d’accès direct à votre réseau virtuel Azure et compare les différences entre une ISE et le service Logic Apps global.

> [!NOTE]
> Logic apps, les déclencheurs intégrés, les actions intégrées et les connecteurs qui s’exécutent dans votre utilisation ISE un plan de tarification différents à partir du plan de tarification basé sur la consommation. Pour plus d’informations, consultez [Tarifs Logic Apps](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolé ou global

Lorsque vous créez un environnement de service intégré (ISE) dans Azure, vous pouvez sélectionner le réseau virtuel Azure où vous souhaitez *injecter* votre ISE. Azure puis injecte ou déploie, une instance privée du service Logic Apps dans votre réseau virtuel. Cette action crée un environnement isolé où vous pouvez créer et exécuter vos applications logiques sur des ressources dédiées. Lorsque vous créez votre application logique, vous sélectionnez votre ISE en tant qu’emplacement de votre application, ce qui donne un accès direct de votre application logique à votre réseau virtuel et les ressources de ce réseau.

Les applications logiques d’un ISE fournissent les mêmes expériences d’utilisateur avec des fonctionnalités similaires que le service Logic Apps global. Non seulement vous pouvez utiliser mêmes intégrés déclencheurs, actions intégrées et les connecteurs à partir du service Logic Apps global, mais vous pouvez également utiliser des connecteurs spécifiques d’ISE. Par exemple, Voici certains connecteurs Standard qui offrent des versions qui s’exécutent dans une fenêtre ISE :

* Stockage Fichier, stockage Table et stockage Blob Azure
* Files d’attente Azure, Azure Service Bus, Azure Event Hubs et IBM MQ
* FTP et SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 et EDIFACT

La différence entre les connecteurs d’environnement de service d’intégration et les autres connecteurs réside dans les emplacements où s’exécutent les déclencheurs et les actions :

* Dans votre ISE, intégrés de déclencheurs et actions, tels que HTTP, s’exécutent toujours dans le même ISE en tant que votre application logique et l’affichage le **Core** étiquette.

  ![Sélectionnez les actions et déclencheurs intégrés « Core »](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Connecteurs qui s’exécutent dans une fenêtre ISE ont hébergé publiquement disponibles dans le service Logic Apps global de versions. Pour les connecteurs qui offrent deux versions, les connecteurs avec les **ISE** étiquette toujours s’exécuter dans l’environnement ISE même que votre application logique. Les connecteurs ne présentant pas le libellé **ISE** s’exécutent dans le service Logic Apps global.

  ![Sélection de connecteurs ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

### <a name="access-to-on-premises-data-sources"></a>Accès aux sources de données locales

Pour les systèmes locaux qui sont connectés à un réseau virtuel Azure, injecter une ISE dans ce réseau pour que vos applications logiques puissent accéder directement ces systèmes à l’aide d’un de ces éléments :

* Connecteur ISE-version pour ce système, par exemple, SQL Server
  
* Action HTTP
  
* Connecteur personnalisé

  * Si vous avez des connecteurs personnalisés qui nécessitent la passerelle de données en local, et que vous avez créé ces connecteurs en dehors d’une fenêtre ISE, applications logiques dans une fenêtre ISE peuvent également utiliser ces connecteurs.
  
  * Connecteurs personnalisés créés dans une fenêtre ISE ne fonctionnent pas avec la passerelle de données sur site. Toutefois, ces connecteurs peuvent accéder directement à des sources de données locales qui sont connectés au réseau virtuel qui héberge l’environnement ISE. Par conséquent, les applications logiques dans une fenêtre ISE très probablement inutile la passerelle de données lors de la communication avec ces ressources.

Pour les systèmes locaux qui ne sont pas connectés à un réseau virtuel ou n’ont pas les connecteurs de version de l’ISE, vous devez d’abord [configurer la passerelle de données sur site](../logic-apps/logic-apps-gateway-install.md) avant votre logique d’applications peuvent se connecter à ces systèmes.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec des applications logiques à l’intérieur d’un environnement de service d’intégration. Cependant, ces comptes d’intégration doivent utiliser le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
