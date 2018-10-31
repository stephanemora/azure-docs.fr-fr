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
ms.date: 09/24/2018
ms.openlocfilehash: f21af23cf0b7b121441b1433f382db60ef7f13fe
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408657"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps à l’aide d’environnements de service d’intégration (ISE)

> [!NOTE]
> Cette fonctionnalité est disponible en *préversion privée*. Pour demander l’accès, [créez votre demande de participation ici](https://aka.ms/iseprivatepreview).

Parfois, vos applications logiques et vos comptes d’intégration doivent accéder à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services à l’intérieur d’un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer cet accès, vous pouvez [créer un *environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) que vous utilisez en tant qu’emplacement pour vos applications logiques et comptes d’intégration. 

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

La création d’un environnement de service d’intégration déploie une instance Logic Apps privée et isolée dans votre réseau virtuel Azure. L’instance privée utilise des ressources dédiées, telles que le stockage, et s’exécute séparément à partir du service Logic Apps « global » public. Cette séparation permet également de réduire l’impact que d’autres locataires Azure peuvent avoir sur les performances de vos applications, l’[effet « voisins bruyants » en quelque sorte](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Cette vue d’ensemble explique comment la création d’un environnement de service d’intégration permet aux applications logiques et aux comptes d’intégration d’accéder directement aux ressources à l’intérieur de votre réseau virtuel Azure, et compare un environnement de service d’intégration au service Logic Apps global.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolé ou global

Quand vous créez un environnement de service d’intégration dans Azure, vous pouvez sélectionner un réseau virtuel Azure comme *pair* pour votre environnement. Azure déploie une instance privée du service Logic Apps sur votre réseau virtuel, ce qui se traduit par un environnement isolé dans lequel vous pouvez créer et exécuter vos applications logiques sur des ressources dédiées. Quand vous créez une application logique, vous pouvez sélectionner cet environnement comme emplacement pour votre application, ce qui donne également à votre application logique un accès direct aux ressources de votre réseau virtuel.  

Les applications logiques d’un ISE fournissent les mêmes expériences d’utilisateur avec des fonctionnalités similaires que le service Logic Apps global. Non seulement vous pouvez utiliser les mêmes éléments intégrés et connecteurs fournis par le service Logic Apps global, mais vous pouvez également choisir parmi les connecteurs fournissant des versions d’environnement de service d’intégration. Voici, par exemple, quelques connecteurs standard offrant des versions qui s’exécutent dans un ISE :
 
* Stockage Fichier, stockage Table et stockage Blob Azure
* Files d'attente Azure
* Azure Service Bus
* FTP
* SSH FTP (SFTP)
* SQL Server
* AS2, X12 et EDIFACT

La différence entre les connecteurs d’environnement de service d’intégration et les autres connecteurs réside dans les emplacements où s’exécutent les déclencheurs et les actions :

* Si, dans votre ISE, vous utilisez des actions et des déclencheurs intégrés, tels que HTTP, ces actions et déclencheurs s’exécutent toujours dans le même ISE que votre application logique. 

* Pour les connecteurs qui offrent deux versions : une des versions s’exécute dans un ISE tandis que l’autre s’exécute dans le service Logic Apps global.  

  Les connecteurs qui portent le libellé **ISE** s’exécutent toujours dans le même ISE que votre application logique. Les connecteurs ne présentant pas le libellé **ISE** s’exécutent dans le service Logic Apps global. 

  ![Sélection de connecteurs ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Les connecteurs que vous configurez dans votre ISE sont également utilisables dans le service Logic Apps global. 

> [!IMPORTANT]
> Les applications logiques, les actions intégrées et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan tarifaire que le tarif de facturation à l’utilisation. Pour plus d’informations, consultez [Tarifs Logic Apps](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Autorisations d’accès au réseau virtuel

Quand vous créez un environnement de service d’intégration, vous pouvez sélectionner un réseau virtuel Azure comme *pair* pour votre environnement. Toutefois, vous pouvez créer cette relation, ou *Peering*, *uniquement* lorsque vous créez votre ISE. Cette relation permet à votre environnement de service d’intégration d’accéder aux ressources de votre réseau virtuel, ce qui donne ainsi la possibilité aux applications logiques de cet environnement de se connecter directement aux ressources de votre réseau virtuel. Pour les systèmes locaux se trouvant dans un réseau virtuel qui est lié à un environnement de service d’intégration, les applications logiques peuvent accéder directement à ces systèmes en utilisant un de ces éléments : 

* Connecteur ISE pour ce système, par exemple, SQL Server

* Action HTTP 

* Connecteur personnalisé

Pour les systèmes locaux qui ne se trouvent pas dans un réseau virtuel ou qui n’ont pas de connecteurs d’environnement de service d’intégration, vous pouvez néanmoins toujours vous connecter après avoir [configuré et utilisé la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md).

Avant de pouvoir sélectionner un réseau virtuel Azure comme pair pour votre environnement, vous devez définir les autorisations de contrôle d’accès en fonction du rôle (RBAC) dans votre réseau virtuel pour le service Azure Logic Apps. Cette tâche nécessite que vous affectiez les rôles **Contributeur de réseau** et **Contributeur classique** au service Azure Logic Apps. Pour plus d’informations sur les autorisations de rôle nécessaires au peering, consultez la [section Autorisations de l’article Créer, modifier ou supprimer une homologation de réseau virtuel](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec les applications logiques qui s’exécutent dans un environnement de service d’intégration (ISE), à condition que ces comptes d’intégration utilisent le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum Azure Logic Apps</a>.
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le <a href="http://aka.ms/logicapps-wish" target="_blank">site de commentaires des utilisateurs Logic Apps</a>.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
