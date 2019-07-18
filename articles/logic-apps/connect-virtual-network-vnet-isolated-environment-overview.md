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
ms.openlocfilehash: f981452b06ec06f2be1b8fe0319cd49a678ccfe0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441578"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps à l’aide d’environnements de service d’intégration (ISE)

Parfois, vos applications logiques et vos comptes d’intégration doivent accéder à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services à l’intérieur d’un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer cet accès, vous pouvez [créer un *environnement de service d’intégration* (ISE, integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) dans lequel vous pouvez exécuter vos applications logiques et créer vos comptes d’intégration.

Lorsque vous créez un ISE, Azure déploie une instance privée et isolée du service Logic Apps dans votre réseau virtuel Azure. Cette instance privée utilise des ressources dédiées, comme du stockage, et s’exécute séparément à partir du service Logic Apps « mondial » public. La séparation entre votre instance privée isolée et l’instance globale publique permet également de réduire l’impact que les autres locataires Azure peuvent avoir sur les performances de vos applications, ce que l’on appelle également [l’effet « voisins bruyants »](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Après avoir créé votre ISE, lorsque vous vous apprêtez à créer votre application logique ou votre compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement de l’application logique ou du compte d’intégration :

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Votre application logique peut désormais accéder directement aux systèmes se trouvant dans votre réseau virtuel ou connectés à votre réseau virtuel à l’aide de l’un de ces éléments :

* Un connecteur étiqueté **ISE** pour ce système, tel que SQL Server
* Une action ou un déclencheur intégré étiqueté **Core**, tel qu’une action ou un déclencheur HTTP
* Un connecteur personnalisé

Cette vue d’ensemble explique de façon plus détaillée comment un environnement de service d’intégration permet aux applications logiques et aux comptes d’intégration d’accéder directement aux ressources à votre réseau virtuel Azure, et compare un environnement de service d’intégration au service Logic Apps mondial.

> [!NOTE]
> Les applications logiques, les déclencheurs et actions intégrés et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan de tarification que celui basé sur la consommation. Pour plus d’informations, consultez [Tarifs Logic Apps](../logic-apps/logic-apps-pricing.md). Votre ISE a également augmenté les limites de durée d’exécution, de rétention du stockage, de débit, de délai d’attente des requêtes et réponses HTTP, de taille des messages et de requêtes de connecteur personnalisé. Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolé ou global

Quand vous créez un environnement de service d’intégration dans Azure, vous pouvez sélectionner un réseau virtuel Azure où vous souhaitez *injecter* votre environnement. Azure injecte ou déploie une instance privée du service Logic Apps dans votre réseau virtuel. Cette action crée un environnement isolé où vous pouvez créer et exécuter vos applications logiques sur des ressources dédiées. Quand vous créez une application logique, vous sélectionnez votre ISE en tant qu’emplacement de votre application, ce qui donne à votre application logique un accès direct au réseau virtuel et aux ressources correspondantes.

Les applications logiques d’un ISE fournissent les mêmes expériences d’utilisateur avec des fonctionnalités similaires que le service Logic Apps global. Vous pouvez non seulement utiliser les mêmes actions et déclencheurs intégrés et les mêmes connecteurs du service Logic Apps mondial, mais aussi des connecteurs spécifiques à l’environnement de service d’intégration. Voici, par exemple, quelques connecteurs standard offrant des versions qui s’exécutent dans un ISE :

* Stockage Fichier, stockage Table et stockage Blob Azure
* Files d’attente Azure, Azure Service Bus, Azure Event Hubs et IBM MQ
* FTP et SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 et EDIFACT

La différence entre les connecteurs d’environnement de service d’intégration et les autres connecteurs réside dans les emplacements où s’exécutent les déclencheurs et les actions :

* Dans votre ISE, les déclencheurs et les actions intégrés, comme HTTP, s’exécutent toujours dans le même environnement de service d’intégration que votre application logique et affichent l’étiquette **Core**.

  ![Sélectionner les actions et déclencheurs intégrés « Core »](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Les connecteurs qui s’exécutent dans un ISE ont des versions hébergées publiquement disponibles dans le service Logic Apps mondial. Pour les connecteurs offrant deux versions, ceux qui portent l’étiquette **ISE** s’exécutent toujours dans le même ISE que votre application logique. Les connecteurs ne présentant pas le libellé **ISE** s’exécutent dans le service Logic Apps global.

  ![Sélection de connecteurs ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

Un ISE offre également de plus grandes limites de durée d’exécution, de rétention du stockage, de débit, de délai d’attente des requêtes et réponses HTTP, de taille des messages et de requêtes de connecteur personnalisé. Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md).

### <a name="access-to-on-premises-data-sources"></a>Accéder aux sources de données locales

Pour les systèmes locaux qui sont connectés à un réseau virtuel Azure, injectez un environnement de service d’intégration dans ce réseau, pour que vos applications logiques puissent accéder directement à ces systèmes en utilisant un de ces éléments :

* Connecteur de version ISE pour ce système, par exemple, SQL Server
* Action HTTP
* Connecteur personnalisé

  * Si vous disposez de connecteurs personnalisés qui ont besoin de la passerelle de données locale et que vous avez créé ces connecteurs hors d’un ISE, les applications logiques d’un ISE peuvent également utiliser ces connecteurs.
  
  * Les connecteurs personnalisés créés au sein d’un ISE ne fonctionnent pas avec la passerelle de données locale. Toutefois, ces connecteurs peuvent accéder directement aux sources de données locales qui sont connectées au réseau virtuel hébergeant l’ISE. Par conséquent, les applications logiques d’un ISE n’ont généralement pas besoin de la passerelle de données lorsqu’elles communiquent avec ces ressources.

Pour les systèmes locaux qui ne sont pas connectés à un réseau virtuel ou qui n’ont pas de connecteurs de version ISE, vous devez [configurer la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) pour que vos applications logiques puissent se connecter à ces systèmes.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec des applications logiques à l’intérieur d’un environnement de service d’intégration. Cependant, ces comptes d’intégration doivent utiliser le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
