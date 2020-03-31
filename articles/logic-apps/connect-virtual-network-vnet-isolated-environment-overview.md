---
title: Accéder aux réseaux virtuels Azure
description: Découvrez la manière dont les environnements de service d’intégration (ISE) aident les applications logiques à accéder aux réseaux virtuels Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127247"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps à l’aide d’environnements de service d’intégration (ISE)

Parfois, vos applications logiques doivent accéder à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services à l’intérieur d’un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer cet accès, vous pouvez [créer un *environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Un ISE est une instance isolée du service Logic Apps qui utilise des ressources dédiées et s’exécute séparément du service Logic Apps multilocataire « mondial ».

L’exécution d’applications logiques dans votre propre instance isolée distincte aide à réduire l’impact que d’autres locataires Azure pourraient avoir sur les performances de vos applications, également appelé [effet « voisins bruyants »](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un ISE offre également les avantages suivants :

* Vous avez vos propres adresses IP statiques, qui sont distinctes des adresses IP statiques partagées par les applications logiques dans le service multilocataire. Vous pouvez également configurer une adresse IP sortante publique, statique et prévisible pour communiquer avec les systèmes de destination. De cette façon, vous n’avez pas besoin de configurer d’ouvertures de pare-feu supplémentaires sur ces systèmes de destination pour chaque environnement ISE.

* Des limites accrues quant à la durée d’exécution, la conservation du stockage, le débit, le délai d’attente des requêtes et réponses HTTP, la taille des messages et les requêtes de connecteur personnalisé. Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md).

> [!NOTE]
> Certains réseaux virtuels Azure utilisent des points de terminaison privés ([Azure Private Link](../private-link/private-link-overview.md)) pour fournir un accès aux services PaaS Azure, tels que Stockage Azure, Azure Cosmos DB ou Azure SQL Database, aux services partenaires ou aux services clients hébergés sur Azure. Si vos applications logiques ont besoin d’accéder à des réseaux virtuels qui utilisent des points de terminaison privés, vous devez créer, déployer et exécuter ces applications logiques à l’intérieur d’un environnement ISE.

Quand vous créez un ISE, Azure l’*injecte* ou le déploie sur votre réseau virtuel Azure. Vous pouvez ensuite utiliser cet ISE comme emplacement pour les applications logiques et les comptes d’intégration qui ont besoin d’un accès.

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Les applications logiques peuvent accéder aux ressources se trouvant sur votre réseau virtuel ou connectés à votre réseau virtuel à l’aide de ces éléments, qui s’exécutent dans le même ISE que votre application logique :

* Une action ou un déclencheur intégré étiqueté **CORE**, tel qu’une action ou un déclencheur HTTP
* Un connecteur étiqueté **ISE** pour ce système ou service
* Un connecteur personnalisé

Vous pouvez toujours utiliser des connecteurs qui n’ont pas l’étiquette **CORE** ou **ISE** avec les applications logiques de votre ISE. Ces connecteurs s’exécutent plutôt dans le service Logic Apps multilocataire. Pour plus d’informations, consultez ces sections :

* [Isolé ou multilocataire](#difference)
* [Se connecter à partir d’un environnement de service d’intégration](../connectors/apis-list.md#integration-service-environment)
* [Connecteurs ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Les applications logiques, les déclencheurs et actions intégrés et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan de tarification que celui basé sur la consommation. Pour plus d’informations, consultez [Modèle de tarif pour Azure Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pour le détail des prix, consultez [Tarification de Logic Apps](../logic-apps/logic-apps-pricing.md).

Cette vue d’ensemble explique de façon plus détaillée comment un ISE permet aux applications logiques d’accéder directement à votre réseau virtuel Azure et compare un ISE au service Logic Apps multilocataire.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolé ou multilocataire

Quand vous créez et exécutez des applications logiques dans un ISE, vous bénéficiez des mêmes expériences utilisateur et de fonctionnalités similaires au service Logic Apps multilocataire. Vous pouvez utiliser les mêmes actions, déclencheurs intégrés et connecteurs managés que ceux disponibles dans le service Logic Apps multilocataire. Certains connecteurs managés offrent des versions d’ISE supplémentaires. La différence entre les connecteurs ISE et les connecteurs non-IS réside dans l’emplacement où elles s’exécutent et les étiquettes qu’elles ont dans le concepteur d’applications logiques quand vous travaillez dans un ISE.

![Connecteurs avec et sans étiquettes dans un ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Les actions et déclencheurs intégrés affichent l’étiquette **CORE**. Ils s’exécutent toujours dans le même ISE que votre application logique. Les connecteurs managés qui présentent l’étiquette **ISE** s’exécutent aussi dans le même ISE que votre application logique.

  Voici par exemple quelques connecteurs qui proposent des versions d’ISE :

  * Stockage Fichier, stockage Table et stockage Blob Azure
  * Files d’attente Azure, Azure Service Bus, Azure Event Hubs et IBM MQ
  * FTP et SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 et EDIFACT

* Les connecteurs managés qui n’affichent pas d’étiquettes supplémentaires s’exécutent toujours dans le service Logic Apps multilocataire, mais vous pouvez quand même utiliser ces connecteurs dans une application logique hébergée dans un ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Accès aux systèmes locaux

Pour accéder à des systèmes locaux ou à des sources de données qui sont connectées à un réseau virtuel Azure, les applications logiques d’un ISE peuvent utiliser les éléments suivants :

* Action HTTP

* Connecteur étiqueté ISE pour ce système

  > [!NOTE]
  > Pour utiliser l’authentification Windows avec le connecteur SQL Server dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), utilisez la version non ISE du connecteur avec la [passerelle de données locale](../logic-apps/logic-apps-gateway-install.md). La version étiquetée ISE ne prend pas en charge l’authentification Windows.

* Connecteur personnalisé

  * Si vous disposez de connecteurs personnalisés qui ont besoin de la passerelle de données locale et que vous avez créé ces connecteurs hors d’un ISE, les applications logiques d’un ISE peuvent également utiliser ces connecteurs.

  * Les connecteurs personnalisés créés au sein d’un ISE ne fonctionnent pas avec la passerelle de données locale. Toutefois, ces connecteurs peuvent accéder directement aux sources de données locales qui sont connectées au réseau virtuel hébergeant l’ISE. Par conséquent, les applications logiques d’un ISE n’ont généralement pas besoin de la passerelle de données lorsqu’elles communiquent avec ces ressources.

Dans le cas de systèmes locaux non connectés à un réseau virtuel ou sans connecteurs étiquetés ISE, vous devez d’abord [configurer la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) pour que les applications logiques puissent se connecter à ces systèmes.

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

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec des applications logiques à l’intérieur d’un environnement de service d’intégration. Cependant, ces comptes d’intégration doivent utiliser le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration. Pour plus d’informations sur la tarification et la facturation des comptes d’intégration avec un ISE, consultez [Modèle de tarification Logic apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pour connaître la tarification, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à des réseaux virtuels Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
