---
title: Vue d’ensemble – Accès à des réseaux virtuels Azure
description: Découvrez comment accéder à des réseaux virtuels Azure à partir d’Azure Logic Apps en utilisant un environnement de service d’intégration.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: 8e377ae50d19df3b9c86b05cbe207479c2b0597d
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664176"
---
# <a name="access-to-azure-virtual-networks-from-azure-logic-apps-using-an-integration-service-environment-ise"></a>Accéder à des réseaux virtuels Azure à partir d’Azure Logic Apps en utilisant un environnement de service d’intégration

Vos flux de travail d’applications logiques doivent parfois accéder à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services, qui se trouvent à l’intérieur d’un réseau virtuel Azure ou y sont connectées. Pour accéder directement à ces ressources à partir de flux de travail qui s’exécutent généralement dans un service Azure Logic Apps mutualisé, vous pouvez créer et exécuter vos applications logiques dans un *environnement de service d’intégration* à la place. Un environnement de service d’intégration est en réalité une instance de service Azure Logic Apps qui s’exécute séparément sur des ressources dédiées, en dehors de l’environnement Azure mutualisé global.

Par exemple, certains réseaux virtuels Azure utilisent des points de terminaison privés ([Azure Private Link](../private-link/private-link-overview.md)) pour fournir un accès à des services Azure PaaS, tels que Stockage Azure, Azure Cosmos DB ou Azure SQL Database, à des services partenaires ou à des services clients hébergés sur Azure. Si vos flux de travail d’application logique requièrent un accès à des réseaux virtuels qui utilisent des points de terminaison privés, vous disposez des options suivantes :

* Si vous souhaitez développer des flux de travail utilisant le type de ressource **Application logique (consommation)** , et que vos flux de travail doivent utiliser des points de terminaison privés, vous *devez* créer, déployer et exécuter vos applications logiques dans un environnement de service d’intégration. Pour plus d’informations, consultez [Se connecter aux réseaux virtuels Azure à partir d’Azure Logic Apps en utilisant un environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Si vous souhaitez développer des flux de travail utilisant le type de ressource **Application logique (standard)** , et que vos flux de travail doivent utiliser des points de terminaison privés, vous n’avez pas besoin d’un environnement de service d’intégration. Au lieu de cela, vos workflows peuvent communiquer en privé et en toute sécurité avec des réseaux virtuels à l’aide de points de terminaison privés pour le trafic entrant et l’intégration du réseau virtuel pour le trafic sortant. Pour plus d’informations, consultez [Sécuriser le trafic entre réseaux virtuels et monolocataires dans Azure Logic Apps à l’aide de points de terminaison privés](secure-single-tenant-workflow-virtual-network-private-endpoint.md).

Pour plus d’informations, examinez les [différences entre des services Azure Logic Apps mutualisés et des environnements de service d’intégration](logic-apps-overview.md#resource-environment-differences).

## <a name="how-an-ise-works-with-a-virtual-network"></a>Fonctionnement d’un environnement de service d’intégration avec un réseau virtuel

Lorsque vous créez un environnement de service d’intégration, vous sélectionnez le réseau virtuel Azure dans lequel vous souhaitez qu’Azure *injecte* ou déploie votre environnement. Lorsque vous créez des applications logiques et des comptes d’intégration qui doivent accéder à ce réseau virtuel, vous pouvez sélectionner votre environnement de service d’intégration comme emplacement hôte pour ces applications logiques et comptes d’intégration. À l’intérieur de l’environnement de service d’intégration, les applications logiques s’exécutent sur des ressources dédiées séparément des autres dans l’environnement Azure Logic Apps mutualisé. Dans un environnement ISE, les données restent dans la [région où vous créez et déployez cet environnement ISE](https://azure.microsoft.com/global-infrastructure/data-residency/).

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Pour mieux contrôler les clés de chiffrement utilisées par le Stockage Azure, vous pouvez configurer, utiliser et gérer votre propre clé avec [Azure Key Vault](../key-vault/general/overview.md). Cette fonctionnalité est également appelée « Bring Your Own Key » (BYOK) et votre clé est appelée « clé gérée par le client ». Pour plus d’informations, consultez [Configuration de clés gérées par le client afin de chiffrer les données au repos pour les environnements de service d’intégration (ISE) dans Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

Cette vue d’ensemble fournit des informations complémentaires sur les [bonnes raisons d’utiliser un environnement ISE](#benefits), les [différences entre le service dédié et le service Logic Apps multilocataire](#difference) et la procédure permettant d’accéder directement aux ressources qui se trouvent dans votre réseau virtuel Azure ou y sont connectées.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Pourquoi utiliser un environnement de service d’intégration

L’exécution d’applications logiques dans votre propre instance dédiée distincte aide à réduire l’impact que d’autres locataires Azure peuvent avoir sur le niveau de performance de vos applications. Cet impact est également appelé [effet « voisins bruyants »](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un ISE offre également les avantages suivants :

* Accès direct aux ressources qui se trouve à l’intérieur de votre réseau virtuel ou y sont connectées

  Les applications logiques créées et exécutées dans un environnement ISE peuvent utiliser des [connecteurs spécifiquement conçus qui s’exécutent dans votre environnement ISE](../connectors/managed.md#ise-connectors). S’il existe un connecteur ISE pour une source de données ou un système local, vous pouvez vous connecter directement sans avoir à utiliser la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Pour plus d’informations, consultez [Dédié ou multilocataire](#difference) et [Accès aux systèmes locaux](#on-premises) plus loin dans cette rubrique.

* Accès continu aux ressources qui se trouvent en dehors de votre réseau virtuel ou n’y sont pas connectées

  Les applications logiques créées et exécutées dans un environnement ISE peuvent toujours utiliser des connecteurs qui s’exécutent dans le service Logic Apps multilocataire lorsque aucun connecteur propre à l’environnement ISE n’est disponible. Pour plus d’informations, consultez [Dédié ou multilocataire](#difference).

* Vous avez vos propres adresses IP statiques, qui sont distinctes des adresses IP statiques partagées par les applications logiques dans le service multilocataire. Vous pouvez également configurer une adresse IP sortante publique, statique et prévisible pour communiquer avec les systèmes de destination. De cette façon, vous n’avez pas besoin de configurer d’ouvertures de pare-feu supplémentaires sur ces systèmes de destination pour chaque environnement ISE.

* Des limites accrues quant à la durée d’exécution, la conservation du stockage, le débit, le délai d’attente des requêtes et réponses HTTP, la taille des messages et les requêtes de connecteur personnalisé. Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dédié ou multilocataire

Quand vous créez et exécutez des applications logiques dans un ISE, vous bénéficiez des mêmes expériences utilisateur et de fonctionnalités similaires au service Logic Apps multilocataire. Vous pouvez utiliser les mêmes actions, déclencheurs intégrés et connecteurs managés que ceux disponibles dans le service Logic Apps multilocataire. Certains connecteurs managés offrent des versions d’ISE supplémentaires. La différence entre les connecteurs ISE et les connecteurs non-IS réside dans l’emplacement où ils s’exécutent et les étiquettes qu’elles ont dans le concepteur d’applications logiques quand vous travaillez dans un ISE.

![Connecteurs avec et sans étiquettes dans un ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Les déclencheurs et les actions intégrés (par exemple, HTTP) présentent l’étiquette **CORE** et s’exécutent dans le même environnement ISE que vos applications logiques.

* Les connecteurs managés qui comportent l’étiquette **ISE** sont spécialement conçus pour les environnements ISE et *s’exécutent toujours dans le même environnement ISE que votre application logique*. Voici par exemple quelques [connecteurs qui proposent des versions d’environnements ISE](../connectors/managed.md#ise-connectors) :<p>

  * Stockage Fichier, stockage Table et stockage Blob Azure
  * Azure Service Bus, Files d’attente Azure, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid et journaux Azure Monitor
  * FTP, SFTP-SSH, File System et SMTP
  * SAP, IBM MQ, IBM DB2 et IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 et EDIFACT

  Sauf rares exceptions, si un connecteur ISE est disponible pour une source de données ou un système local, vous pouvez vous connecter directement sans utiliser la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Pour plus d’informations, consultez [Accès aux systèmes locaux](#on-premises) plus loin dans cette rubrique.

* Les connecteurs managés qui ne présentent pas l’étiquette **ISE** continuent de fonctionner pour les applications logiques situées à l’intérieur d’un environnement ISE. Ces connecteurs *s’exécutent toujours dans le service Logic Apps multilocataire*, et non dans l’environnement ISE.

* Que la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md) soit ou non requise, les connecteurs personnalisés créés *en dehors d’un environnement ISE*, continuent de fonctionner pour les applications logiques situées à l’intérieur d’un environnement ISE. Cependant, les connecteurs personnalisés créés *au sein d’un environnement ISE* ne fonctionnent pas avec la passerelle de données locale. Pour plus d’informations, consultez [Accès aux systèmes locaux](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Accès aux systèmes locaux

Les applications logiques qui s’exécutent dans un environnement ISE peuvent accéder directement aux sources de données et systèmes locaux qui se trouvent à l’intérieur d’un réseau virtuel Azure ou y sont connectés à l’aide des éléments suivants :<p>

* Le déclencheur ou l’action HTTP, qui présente l’étiquette **CORE**

* Le connecteur **ISE**, s’il est disponible, pour une source de données ou un système local

  Si un connecteur ISE est disponible, vous pouvez accéder directement au système ou à la source de données sans la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Si toutefois vous devez accéder à SQL Server à partir d’un environnement ISE et utiliser l’Authentification Windows, vous devez opter pour la version hors environnement ISE du connecteur et la passerelle de données locale. La version ISE du connecteur ne prend pas en charge l’Authentification Windows. Pour plus d’informations, consultez [Connecteurs ISE](../connectors/managed.md#ise-connectors) et [Connexion à partir d’un environnement de service d’intégration](../connectors/managed.md#integration-account-connectors).

* Un connecteur personnalisé

  * Que la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md) soit ou non requise, les connecteurs personnalisés créés *en dehors d’un environnement ISE*, continuent de fonctionner pour les applications logiques situées à l’intérieur d’un environnement ISE.

  * Les connecteurs personnalisés créés *au sein d’un environnement ISE* ne fonctionnent pas avec la passerelle de données locale. Cependant, ils peuvent accéder directement aux sources de données et systèmes locaux qui se trouvent à l’intérieur du réseau virtuel qui héberge votre environnement ISE ou y sont connectés. Ainsi, les applications logiques qui se trouvent à l’intérieur d’un environnement ISE n’ont généralement pas besoin de la passerelle de données pour accéder à ces ressources.

Pour accéder à des sources de données et systèmes locaux qui ne possèdent pas de connecteurs ISE, se trouvent en dehors de votre réseau virtuel ou n’y sont pas connectés, vous devez toujours utiliser la passerelle de données locale. Les applications logiques qui se trouvent au sein d’un environnement ISE peuvent continuer à utiliser les connecteurs dépourvus de l’étiquette **CORE** ou **ISE**. Ces connecteurs s’exécutent dans le service Logic Apps multilocataire, plutôt que dans votre environnement ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>Références (SKU) de l’environnement de service d’intégration

Lorsque vous créez votre environnement ISE, vous pouvez sélectionner la référence SKU de développeur ou la référence SKU Premium. Cette option SKU est disponible uniquement lors de la création de votre ISE et ne peut pas être modifiée ultérieurement. Voici les différences entre ces SKU :

* **Développeur**

  Fournit un environnement ISE à moindre coût que vous pouvez utiliser pour l’exploration, l’expérimentation, le développement et les tests, mais pas pour les tests de production ou de performances. La référence SKU Développeur comprend des déclencheurs et des actions intégrés, des connecteurs standard, des connecteurs d'entreprise et un seul compte d’intégration de [niveau gratuit](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) pour un [tarif mensuel fixe](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Cette référence SKU n’a pas de contrat de niveau de service (SLA), de fonctionnalité de scale-up ou de redondance pendant le recyclage, ce qui signifie que vous pouvez rencontrer des retards ou des temps d’arrêt. Les mises à jour du back-end peuvent interrompre le service par intermittence.

  Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Pour plus d’informations sur la facturation des environnements de service d’intégration, consultez le [modèle de tarif pour Logic Apps](../logic-apps/logic-apps-pricing.md#ise-pricing).

* **Premium**

  Fournit un environnement ISE que vous pouvez utiliser pour les tests de production et de performances. La référence SKU Premium inclut la prise en charge des contrats SLA, les déclencheurs et les actions intégrés, les connecteurs standard, les connecteurs d’entreprise, un seul compte d’intégration de [niveau standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), des options de mise à l’échelle de la capacité et une redondance pendant le recyclage pour un [tarif mensuel fixe](https://azure.microsoft.com/pricing/details/logic-apps).

  Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Pour plus d’informations sur la facturation des environnements de service d’intégration, consultez le [modèle de tarif pour Logic Apps](../logic-apps/logic-apps-pricing.md#ise-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Point de terminaison d’accès ISE

Lorsque vous créez votre ISE, vous pouvez choisir d’utiliser des points de terminaison d’accès internes ou externes. Votre sélection détermine si les déclencheurs de demande ou de webhook sur les applications logiques dans votre ISE peuvent recevoir des appels en dehors de votre réseau virtuel. Ces points de terminaison influencent également la manière dont vous pouvez accéder aux entrées et aux sorties à partir de l’historique des exécutions de vos applications logiques.

> [!IMPORTANT]
> Vous pouvez sélectionner le point de terminaison d’accès uniquement lors de la création de l’ISE et ne pouvez pas modifier cette option ultérieurement.

* **Interne** : Les points de terminaison privés autorisent les appels aux applications logiques dans votre ISE, où vous pouvez voir les entrées et sorties dans l’historique des exécutions des applications logiques *uniquement depuis l’intérieur de votre réseau virtuel*.

  > [!IMPORTANT]
  > Si vous devez utiliser ces déclencheurs basés sur webhook et que le service se trouve en dehors de votre réseau virtuel et des réseaux virtuels appairés, utilisez des points de terminaison externes, *non* des points de terminaison internes, lorsque vous créez votre environnement de service d’intégration :
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (version mutualisée)
  > 
  > Vérifiez également que vous disposez d’une connectivité réseau entre les points de terminaison privés et l’ordinateur à partir duquel vous souhaitez accéder à l’historique des exécutions. Dans le cas contraire, lorsque vous essayez d’afficher l’historique des exécutions de votre application logique, un message d’erreur indiquant « Erreur inattendue. Échec de récupération » s’affiche.
  >
  > ![Erreur d’action Stockage Azure résultant de l’impossibilité d’envoyer du trafic via le pare-feu](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Par exemple, votre ordinateur client peut exister à l’intérieur du réseau virtuel de l’environnement de service d’intégration ou à l’intérieur d’un réseau virtuel connecté au réseau virtuel de l’environnement de service d’intégration, par le biais d’un peering ou d’un réseau privé virtuel. 

* **Externe** : Les points de terminaison publics autorisent les appels à des applications logiques dans votre ISE, où vous pouvez voir les entrées et sorties de l’historique des exécutions des applications logiques *depuis l’extérieur de votre réseau virtuel*. Si vous utilisez des groupes de sécurité réseau (NSG), assurez-vous qu’ils sont configurés avec des règles de trafic entrant pour autoriser l’accès aux entrées et sorties de l’historique des exécutions. Pour plus d'informations, consultez [Activer l’accès à ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Pour déterminer si votre environnement ISE utilise un point de terminaison d’accès interne ou externe, dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Propriétés** et recherchez la propriété **Point de terminaison d’accès** :

![Trouver le point de terminaison de l’accès ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modèle de tarification

Les applications logiques, les déclencheurs et actions intégrés et les connecteurs qui s’exécutent dans votre environnement ISE suivent un plan tarifaire fixe différent de celui qui dépend de la consommation. Pour plus d’informations, consultez [Modèle de tarif pour Azure Logic Apps](../logic-apps/logic-apps-pricing.md#ise-pricing). Pour connaître la tarification, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Comptes d’intégration et environnement de service d’intégration

Vous pouvez utiliser des comptes d’intégration avec des applications logiques à l’intérieur d’un environnement de service d’intégration. Cependant, ces comptes d’intégration doivent utiliser le *même ISE* que les applications logiques liées. Les applications logiques d’un ISE ne peuvent faire référence qu’aux comptes d’intégration se trouvant dans le même ISE. Lorsque vous créez un compte d’intégration, vous pouvez sélectionner votre ISE en tant qu’emplacement pour votre compte d’intégration. Pour plus d’informations sur la tarification et la facturation des comptes d’intégration avec un ISE, consultez [Modèle de tarification Logic apps](../logic-apps/logic-apps-pricing.md#ise-pricing). Pour connaître la tarification, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Pour plus d’informations sur les limites, consultez [Limites du compte d’intégration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à des réseaux virtuels Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
