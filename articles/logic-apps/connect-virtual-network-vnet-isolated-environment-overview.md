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
ms.date: 02/12/2019
ms.openlocfilehash: 204138e7b8b3846e2d50607b3c5ec0836abefe24
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162371"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps à l’aide d’environnements de service d’intégration (ISE)

> [!NOTE]
> Cette fonctionnalité est disponible en *préversion privée*. Pour vous inscrire à la préversion privée, [créez votre demande ici](https://aka.ms/iseprivatepreview).

Parfois, vos applications logiques et vos comptes d’intégration doivent accéder à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services, dans un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer cet accès, vous pouvez [créer un *environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) pour exécuter vos applications logiques et vos comptes d’intégration.

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

La création d’un environnement de service d’intégration déploie une instance Logic Apps privée et isolée dans votre réseau virtuel Azure. Cette instance privée utilise des ressources dédiées, comme du stockage, et s’exécute séparément à partir du service Logic Apps « mondial » public. Cette séparation permet également de réduire l’impact que d’autres locataires Azure peuvent avoir sur les performances de vos applications, l’[effet « voisins bruyants » en quelque sorte](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Cette vue d’ensemble explique comment un environnement de service d’intégration permet aux applications logiques et aux comptes d’intégration d’accéder directement aux ressources à votre réseau virtuel Azure, et compare un environnement de service d’intégration au service Logic Apps mondial.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolé ou global

Quand vous créez un environnement de service d’intégration dans Azure, vous sélectionnez un réseau virtuel Azure où vous souhaitez *injecter* votre environnement. Azure déploie une instance privée du service Logic Apps dans votre réseau virtuel. Cette action crée un environnement isolé où vous pouvez créer et exécuter vos applications logiques sur des ressources dédiées. Quand vous créez une application logique, vous sélectionnez cet environnement comme emplacement pour votre application, ce qui donne à votre application logique un accès direct aux ressources de votre réseau virtuel.

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

> [!IMPORTANT]
> Les applications logiques, les actions intégrées et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan tarifaire que le tarif de facturation à l’utilisation. Pour plus d’informations, consultez [Tarifs Logic Apps](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Autorisations d’accès au réseau virtuel

Quand vous créez un environnement de service d’intégration, vous pouvez sélectionner un réseau virtuel Azure dans lequel vous *injectez* votre environnement. L’injection déploie une instance privée du service Logic Apps dans votre réseau virtuel. Cette action aboutit à un environnement isolé, où vous pouvez créer et exécuter vos applications logiques sur des ressources dédiées. Quand vous créez vos applications logiques sélectionnez votre ISE comme emplacement pour vos applications. Ces applications logiques peuvent alors accéder directement à votre réseau virtuel et se connecter aux ressources de ce réseau.

Pour les systèmes qui sont connectés à un réseau virtuel, vous pouvez injecter un environnement de service d’intégration dans ce réseau virtuel, pour que vos applications logiques puissent accéder directement à ces systèmes en utilisant un de ces éléments :

* Connecteur ISE pour ce système, par exemple, SQL Server

* Action HTTP

* Connecteur personnalisé

Pour les systèmes locaux qui ne sont pas connectés à un réseau virtuel ou qui n’ont pas de connecteurs d’environnement de service d’intégration, vous pouvez vous connecter à ces systèmes en [configurant et en utilisant la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md).

Avant de pouvoir sélectionner un réseau virtuel Azure pour injecter votre environnement, vous devez définir les autorisations de contrôle d’accès en fonction du rôle (RBAC) dans votre réseau virtuel pour le service Azure Logic Apps. Cette tâche nécessite que vous affectiez les rôles **Contributeur de réseau** et **Contributeur classique** au service Azure Logic Apps.
Pour configurer ces autorisations, consultez [Se connecter à des réseaux virtuels Azure à partir d’applications logiques](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec des applications logiques à l’intérieur d’un environnement de service d’intégration. Cependant, ces comptes d’intégration doivent utiliser le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum Azure Logic Apps</a>.
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le <a href="https://aka.ms/logicapps-wish" target="_blank">site de commentaires des utilisateurs Logic Apps</a>.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
