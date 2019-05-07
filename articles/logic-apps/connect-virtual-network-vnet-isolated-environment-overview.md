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
ms.openlocfilehash: 0206fd2b2ea0a7cfaf79aaf19052e0174645780b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143154"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps à l’aide d’environnements de service d’intégration (ISE)

Parfois, vos applications logiques et vos comptes d’intégration doivent accéder à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services, dans un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer cet accès, vous pouvez [créer un *environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) pour exécuter vos applications logiques et vos comptes d’intégration. Lorsque vous créez une fenêtre ISE, Azure déploie une instance privée et isolée du service Logic Apps dans votre réseau virtuel Azure. Cette instance privée utilise des ressources dédiées, comme du stockage, et s’exécute séparément à partir du service Logic Apps « mondial » public. Séparer votre instance privée isolé et l’instance globale publique permet également de réduire l’impact que les autres clients Azure peuvent avoir sur les performances de vos applications, ce qui sont également connu sous le [effet de « voisins bruyants »](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Après avoir créé votre ISE, lorsque vous accédez à créer votre compte d’application ou de l’intégration de logique, vous pouvez sélectionner votre ISE en tant qu’emplacement de la logique application ou l’intégration de votre compte :

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Votre application logique peut désormais accéder directement aux systèmes qui sont trouvent dans ou connecté à votre réseau virtuel à l’aide d’un de ces éléments :

* Un connecteur avec contrôle de version ISE pour ce système, par exemple, SQL Server
* Un déclencheur intégrée ou une action, telles que le déclencheur HTTP ou l’action
* Un connecteur personnalisé

Cette vue d’ensemble décrit plus en détail comment une ISE offre à vos applications logiques et intégration de comptes d’accès direct à votre réseau virtuel Azure et compare les différences entre une ISE et le service Logic Apps global.
Pour les systèmes locaux qui ne sont pas connectés à un réseau virtuel ou n’ont pas les connecteurs de version de l’ISE, vous pouvez vous connecter à ces systèmes par [configuration et l’utilisation de la passerelle de données sur site](../logic-apps/logic-apps-gateway-install.md).

> [!IMPORTANT]
> Les applications logiques, les actions intégrées et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan tarifaire que le tarif de facturation à l’utilisation. Pour plus d’informations, consultez [Tarifs Logic Apps](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolé ou global

Lorsque vous créez un environnement de service intégré (ISE) dans Azure, vous pouvez sélectionner le réseau virtuel Azure où vous souhaitez *injecter* votre ISE. Azure puis injecte ou déploie, une instance privée du service Logic Apps dans votre réseau virtuel. Cette action crée un environnement isolé où vous pouvez créer et exécuter vos applications logiques sur des ressources dédiées. Lorsque vous créez votre application logique, vous sélectionnez votre ISE en tant qu’emplacement de votre application, ce qui donne un accès direct de votre application logique à votre réseau virtuel et les ressources de ce réseau.

Les applications logiques d’un ISE fournissent les mêmes expériences d’utilisateur avec des fonctionnalités similaires que le service Logic Apps global. Vous pouvez non seulement utiliser les actions et connecteurs intégrés dans le service Logic Apps mondial, mais aussi des connecteurs spécifiques à l’environnement de service d’intégration. Voici, par exemple, quelques connecteurs standard offrant des versions qui s’exécutent dans un ISE :

* Stockage Fichier, stockage Table et stockage Blob Azure
* Files d’attente Azure, Azure Service Bus, Azure Event Hubs et IBM MQ
* FTP et SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 et EDIFACT

La différence entre les connecteurs d’environnement de service d’intégration et les autres connecteurs réside dans les emplacements où s’exécutent les déclencheurs et les actions :

* Dans votre ISE, les déclencheurs et les actions intégrés, comme HTTP, s’exécutent toujours dans le même environnement de service d’intégration que votre application logique.

* Pour les connecteurs qui offrent deux versions : une des versions s’exécute dans un ISE tandis que l’autre s’exécute dans le service Logic Apps mondial.  

  Les connecteurs qui portent le libellé **ISE** s’exécutent toujours dans le même ISE que votre application logique. Les connecteurs ne présentant pas le libellé **ISE** s’exécutent dans le service Logic Apps global.

  ![Sélection de connecteurs ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Les connecteurs qui s’exécutent dans un ISE sont également disponibles dans le service Logic Apps mondial.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec des applications logiques à l’intérieur d’un environnement de service d’intégration. Cependant, ces comptes d’intégration doivent utiliser le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
