---
title: Accéder aux réseaux virtuels Azure
description: Découvrez la manière dont les environnements de service d’intégration (ISE) aident les applications logiques à accéder aux réseaux virtuels Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 1f743384f467e4559412fa1a46d48011b568d249
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191570"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps à l’aide d’environnements de service d’intégration (ISE)

Parfois, vos applications logiques et vos comptes d’intégration doivent accéder à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services à l’intérieur d’un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer cet accès, vous pouvez [créer un *environnement de service d’intégration* (ISE, integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) dans lequel vous pouvez exécuter vos applications logiques et créer vos comptes d’intégration.

Lorsque vous créez un environnement ISE, Azure *l'injecte* dans votre réseau virtuel Azure, qui déploie ensuite une instance privée et isolée du service Logic Apps dans votre réseau virtuel Azure. Cette instance privée utilise des ressources dédiées, comme du stockage, et s’exécute séparément du service Logic Apps « mondial » multilocataire public. La séparation entre votre instance privée isolée et l’instance globale publique permet également de réduire l’impact que les autres locataires Azure peuvent avoir sur les performances de vos applications, ce que l’on appelle également [l’effet « voisins bruyants »](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un environnement ISE vous fournit également vos propres adresses IP statiques. Ces adresses IP sont séparées des adresses IP statiques qui sont partagées par les applications logiques dans le service multilocataire public.

Après avoir créé votre ISE, lorsque vous vous apprêtez à créer votre application logique ou votre compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement de l’application logique ou du compte d’intégration :

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Votre application logique peut désormais accéder directement aux systèmes se trouvant sur votre réseau virtuel ou connectés à votre réseau virtuel à l’aide de l’un de ces éléments, qui s’exécutent dans le même ISE que votre application logique :

* Un connecteur étiqueté **ISE** pour ce système
* Une action ou un déclencheur intégré étiqueté **CORE**, tel qu’une action ou un déclencheur HTTP
* Un connecteur personnalisé

Cette vue d’ensemble explique de façon plus détaillée comment un environnement de service d’intégration permet aux applications logiques et aux comptes d’intégration d’accéder directement aux ressources à votre réseau virtuel Azure, et compare un environnement de service d’intégration au service Logic Apps mondial.

> [!IMPORTANT]
> Les applications logiques, les déclencheurs et actions intégrés et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan de tarification que celui basé sur la consommation. Pour plus d’informations sur la tarification et la facturation des environnements de service d’intégration, consultez le [modèle de tarif pour Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pour connaître la tarification, consultez [Tarification Logic Apps](../logic-apps/logic-apps-pricing.md).
>
> Votre ISE a également augmenté les limites de durée d’exécution, de rétention du stockage, de débit, de délai d’attente des requêtes et réponses HTTP, de taille des messages et de requêtes de connecteur personnalisé. 
> Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolé ou global

Quand vous créez un environnement de service d’intégration dans Azure, vous pouvez sélectionner un réseau virtuel Azure où vous souhaitez *injecter* votre environnement. Azure injecte ou déploie une instance privée du service Logic Apps dans votre réseau virtuel. Cette action crée un environnement isolé où vous pouvez créer et exécuter vos applications logiques sur des ressources dédiées. Quand vous créez une application logique, vous sélectionnez votre ISE en tant qu’emplacement de votre application, ce qui donne à votre application logique un accès direct au réseau virtuel et aux ressources correspondantes.

Les applications logiques d’un ISE fournissent les mêmes expériences utilisateur avec des fonctionnalités similaires que le service Logic Apps global public. Vous pouvez utiliser les mêmes actions, déclencheurs intégrés et connecteurs managés que ceux disponibles dans le service global Logic Apps. Certains connecteurs managés offrent des versions d’ISE supplémentaires. La différence réside dans l’emplacement où elles s’exécutent et les étiquettes qu’elles affichent dans le concepteur d’applications logiques quand vous travaillez dans un environnement ISE.

![Connecteurs avec et sans étiquettes dans un ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Les déclencheurs et actions intégrés présentent l’étiquette **CORE**, et s’exécutent toujours dans le même ISE que votre application logique. Les connecteurs managés qui présentent l’étiquette **ISE** s’exécutent aussi dans le même ISE que votre application logique.

  Voici par exemple quelques connecteurs qui proposent des versions d’ISE :

  * Stockage Fichier, stockage Table et stockage Blob Azure
  * Files d’attente Azure, Azure Service Bus, Azure Event Hubs et IBM MQ
  * FTP et SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 et EDIFACT

* Les connecteurs managés qui n’affichent pas d’étiquettes supplémentaires s’exécutent toujours dans le service Logic Apps global public, mais vous pouvez quand même utiliser ces connecteurs dans une application logique basée sur ISE.

Un ISE offre également de plus grandes limites de durée d’exécution, de rétention du stockage, de débit, de délai d’attente des requêtes et réponses HTTP, de taille des messages et de requêtes de connecteur personnalisé. Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>Références (SKU) de l’environnement de service d’intégration

Lorsque vous créez votre environnement ISE, vous pouvez sélectionner la référence SKU de développeur ou la référence SKU Premium. Voici les différences entre ces SKU :

* **Développeur**

  Fournit un environnement ISE à moindre coût que vous pouvez utiliser pour l’expérimentation, le développement et les tests, mais pas pour les tests de production ou de performances. La référence SKU Développeur comprend des déclencheurs et des actions intégrés, des connecteurs standard, des connecteurs d'entreprise et un seul compte d’intégration de [niveau gratuit](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) pour un tarif mensuel fixe. Toutefois, cette référence (SKU) n’inclut aucun contrat de niveau de service (SLA), d’options de mise à l’échelle de la capacité ou de redondance pendant le recyclage, ce qui signifie que vous pouvez rencontrer des retards ou des temps d’arrêt.

* **Premium**

  Fournit un environnement ISE que vous pouvez utiliser pour la production et inclut la prise en charge des contrats SLA, les déclencheurs et les actions intégrés, les connecteurs standard, les connecteurs d’entreprise, un seul compte d’intégration de [niveau standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), des options de mise à l’échelle de la capacité et une redondance pendant le recyclage pour un tarif mensuel fixe.

> [!IMPORTANT]
> L’option SKU est disponible uniquement lors de la création de votre ISE et ne peut pas être modifiée ultérieurement.

Pour connaître la tarification, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Pour plus d’informations sur la tarification et la facturation des environnements de service d’intégration, consultez le [modèle de tarif pour Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Point de terminaison d’accès ISE

Lorsque vous créez votre ISE, vous pouvez choisir d’utiliser des points de terminaison d’accès internes ou externes. Votre sélection détermine si les déclencheurs de demande ou de webhook sur les applications logiques dans votre ISE peuvent recevoir des appels en dehors de votre réseau virtuel.

Ces points de terminaison influencent également la manière dont vous pouvez accéder aux entrées et aux sorties dans l’historique des exécutions de votre application logique.

* **Interne** : Les points de terminaison privés qui autorisent les appels à des applications logiques dans votre ISE, où vous pouvez voir des entrées et des sorties dans l’historique des exécutions et y accéder *uniquement de l’intérieur de votre réseau virtuel*

* **Externe** : Les points de terminaison publics qui autorisent les appels à des applications logiques dans votre ISE, où vous pouvez voir des entrées et des sorties dans l’historique des exécutions et y accéder *de l’extérieur de votre réseau virtuel*. Si vous utilisez des groupes de sécurité réseau (NSG), assurez-vous qu’ils sont configurés avec des règles de trafic entrant pour autoriser l’accès aux entrées et sorties de l’historique des exécutions. Pour plus d'informations, consultez [Activer l’accès à ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> L’option de point de terminaison d’accès est disponible uniquement lors de la création de votre ISE et ne peut pas être modifiée ultérieurement.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Accéder aux sources de données locales

Pour les systèmes locaux qui sont connectés à un réseau virtuel Azure, injectez un environnement de service d’intégration dans ce réseau, pour que vos applications logiques puissent accéder directement à ces systèmes en utilisant un de ces éléments :

* Action HTTP

* Connecteur étiqueté ISE pour ce système

  > [!NOTE]
  > Pour utiliser l’authentification Windows avec le connecteur SQL Server dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), utilisez la version non ISE du connecteur avec la [passerelle de données locale](../logic-apps/logic-apps-gateway-install.md). La version étiquetée ISE ne prend pas en charge l’authentification Windows.

* Connecteur personnalisé

  * Si vous disposez de connecteurs personnalisés qui ont besoin de la passerelle de données locale et que vous avez créé ces connecteurs hors d’un ISE, les applications logiques d’un ISE peuvent également utiliser ces connecteurs.

  * Les connecteurs personnalisés créés au sein d’un ISE ne fonctionnent pas avec la passerelle de données locale. Toutefois, ces connecteurs peuvent accéder directement aux sources de données locales qui sont connectées au réseau virtuel hébergeant l’ISE. Par conséquent, les applications logiques d’un ISE n’ont généralement pas besoin de la passerelle de données lorsqu’elles communiquent avec ces ressources.

Dans le cas de systèmes locaux non connectés à un réseau virtuel ou sans connecteurs étiquetés ISE, vous devez d’abord [configurer la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) pour que les applications logiques puissent se connecter à ces systèmes.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec des applications logiques à l’intérieur d’un environnement de service d’intégration. Cependant, ces comptes d’intégration doivent utiliser le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration. Pour plus d’informations sur la tarification et la facturation des comptes d’intégration avec un ISE, consultez [Modèle de tarification Logic apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pour connaître la tarification, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Ajouter des artefacts à des environnements de service d’intégration](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gérer les environnements de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
