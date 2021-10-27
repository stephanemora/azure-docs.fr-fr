---
title: Architecture et meilleures pratiques pour les réseaux Purview
description: Cet article fournit des exemples des options d’architecture réseau Azure Purview et décrit les meilleures pratiques.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 04121992c3de5e061e54687c8d23469850607870
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073978"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Architecture et meilleures pratiques pour les réseaux Purview

Azure Purview est une solution PaaS (Platform as a Service) pour la gouvernance des données. Les comptes Azure Purview ont des points de terminaison publics accessibles sur Internet pour se connecter au service. Cependant, tous les points de terminaison sont sécurisés via des connexions Azure Active Directory (Azure AD) et le contrôle d’accès en fonction du rôle (RBAC).

Pour une couche de sécurité supplémentaire, vous pouvez créer des points de terminaison privés pour votre compte Azure Purview. Vous pouvez alors obtenir une adresse IP privée de votre réseau virtuel dans Azure vers le compte Azure Purview et ses ressources managées. Ceci permet de restreindre tout le trafic entre votre réseau virtuel et le compte Azure Purview vers une liaison privée pour l’interaction des utilisateurs avec les API et Azure Purview Studio, ou pour l’analyse et l’ingestion. 

Actuellement, le pare-feu Azure Purview fournit un contrôle d’accès pour le point de terminaison public de votre compte Purview. Vous pouvez utiliser le pare-feu pour autoriser ou bloquer tout l’accès via le point de terminaison public quand des points de terminaison privés sont utilisés. 

En fonction de vos exigences en matière de réseau, de connectivité et de sécurité, vous pouvez configurer et gérer des comptes Azure Purview pour accéder aux services sous-jacents ou à l’ingestion. Utilisez ce guide des bonnes pratiques pour définir et préparer votre environnement réseau afin d’accéder à Azure Purview et d’analyser des sources de données provenant de différents emplacements dans votre réseau ou votre cloud. 

Ce guide couvre les options réseau suivantes : 

- Utiliser des [points de terminaison publics Azure](#option-1-use-public-endpoints). 
- Utiliser des [points de terminaison privés](#option-2-use-private-endpoints). 
- Utiliser des [points de terminaison privés et autoriser l’accès public sur le même compte Azure Purview](#option-3-use-both-private-and-public-endpoints). 

Ce guide décrit quelques-uns des scénarios d’architecture réseau les plus courants pour Azure Purview. Bien que vous ne soyez pas limité à ces scénarios, gardez à l’esprit les [limitations](#current-limitations) du service quand vous planifiez le réseau pour vos comptes Azure Purview. 

Le public visé par ce guide inclut :

- Équipe en charge de l’architecture des données 
- Équipe réseau  
- Équipe de sécurité des données 

## <a name="prerequisites"></a>Prérequis

Pour identifier l’option réseau la mieux adaptée à votre environnement, nous vous suggérons d’effectuer les actions suivantes : 

- Examinez la topologie et les conditions requises de sécurité de votre réseau avant d’inscrire et d’analyser des sources de données dans Azure Purview. Pour plus d’informations, consultez [Définir une topologie réseau Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology). 

- Définissez votre [modèle de connectivité réseau pour les services PaaS](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services). 

## <a name="option-1-use-public-endpoints"></a>Option 1 : Utiliser des points de terminaison publics 

Par défaut, vous pouvez utiliser des comptes Azure Purview via des points de terminaison publics accessibles via Internet. Autorisez les réseaux publics dans votre compte Azure Purview si vous avez les exigences suivantes : 

- Aucune connectivité privée n’est requise lors de l’analyse ou de la connexion aux points de terminaison Azure Purview. 
- Toutes les sources de données sont des applications SaaS uniquement. 
- Toutes les sources de données ont un point de terminaison public accessible via Internet. 
- Les utilisateurs métier ont besoin d’accéder à un compte Azure Purview et à Azure Purview Studio via Internet. 

### <a name="integration-runtime-options"></a>Options de runtime d’intégration 

Pour analyser des sources de données alors que le pare-feu du compte Azure Purview est configuré pour autoriser l’accès public, vous pouvez utiliser le runtime d’intégration Azure et le [runtime d’intégration auto-hébergé](./manage-integration-runtimes.md). La façon dont vous les utilisez dépend de la [prise en charge de vos sources de données](manage-data-sources.md).  

Voici quelques bonnes pratiques :

- Vous pouvez utiliser le runtime d’intégration Azure ou un runtime d’intégration auto-hébergé pour analyser des sources de données Azure comme Azure SQL Database ou Stockage Blob Azure. Nous vous recommandons d’utiliser le runtime d’intégration Azure pour analyser les sources de données Azure quand c’est possible, afin de réduire les coûts et la charge d’administration. 
  
- Pour analyser plusieurs sources de données Azure, utilisez un réseau public et le runtime d’intégration Azure. Les étapes suivantes montrent le flux des communications à un niveau général, quand vous utilisez le runtime d’intégration Azure pour analyser une source de données dans Azure :

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Capture d’écran montrant le flux des connexions entre Azure Purview, le runtime Azure et des sources de données."lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. Une analyse manuelle ou automatique est lancée à partir du mappage de données Azure Purview via le runtime d’intégration Azure. 
   
  2. Le runtime d’intégration Azure se connecte à la source de données pour extraire les métadonnées.

  3. Les métadonnées sont mises en file d’attente dans le stockage managé d’Azure Purview et stockées dans Stockage Blob Azure. 

  4. Les métadonnées sont envoyées au mappage de données Azure Purview. 

- L’analyse des sources de données locales et basées sur une machine virtuelle nécessite toujours l’utilisation d’un runtime d’intégration auto-hébergé. Le runtime d’intégration Azure n’est pas pris en charge pour ces sources de données. Les étapes suivantes montrent le flux des communications à un niveau général quand vous utilisez un runtime d’intégration auto-hébergé pour analyser une source de données :

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Capture d’écran montrant le flux des connexions entre Azure Purview, un runtime auto-hébergé et des sources de données."lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. Une analyse manuelle ou automatique est déclenchée. Azure Purview se connecte à Azure Key Vault pour récupérer les informations d’identification permettant d’accéder à une source de données.
   
  2. L’analyse est lancée à partir du mappage de données Azure Purview via un runtime d’intégration auto-hébergé. 
   
  3. Le service du runtime d’intégration auto-hébergé de la machine virtuelle se connecte à la source de données pour extraire les métadonnées.

  4. Les métadonnées sont traitées dans la mémoire de la machine virtuelle pour le runtime d’intégration auto-hébergé. Les métadonnées sont mises en file d’attente dans le stockage managé, puis stockées dans Stockage Blob Azure. 

  5. Les métadonnées sont envoyées au mappage de données Azure Purview. 

### <a name="authentication-options"></a>Options d’authentification  

Quand vous analysez une source de données dans Azure Purview, vous devez fournir des informations d’identification. Azure Purview peut alors lire les métadonnées des ressources en utilisant le runtime d’intégration Azure dans la source de données de destination. Quand vous utilisez un réseau public, les options d’authentification et les exigences varient en fonction des facteurs suivants : 

- **Type de source de données**. Par exemple, si la source de données est Azure SQL Database, vous devez utiliser l’authentification SQL avec l’accès db_datareader à chaque base de données. Il peut s’agir d’une identité gérée par l’utilisateur ou d’une identité gérée par Azure Purview. Il peut aussi s’agir d’un principal de service dans Azure Active Directory ajouté à SQL Database en tant que db_datareader. 

  Si la source de données est Stockage Blob Azure, vous pouvez utiliser une identité managée Azure Purview ou un principal de service dans Azure Active Directory ajouté en tant que rôle Lecteur de données de Stockage Blob sur le compte de stockage Azure. Vous pouvez aussi simplement utiliser la clé du compte de stockage.  

- **Type d’authentification**. Nous vous recommandons d’utiliser une identité managée Azure Purview pour analyser les sources de données Azure quand c’est possible, afin de réduire la charge d’administration. Pour tous les autres types d’authentification, vous devez [configurer des informations d’identification pour l’authentification de la source dans Azure Purview](manage-credentials.md) : 

  1. Générez un secret dans un coffre de clés Azure. 
  1. Inscrivez le coffre de clés dans Azure Purview.  
  1. Dans Azure Purview, créez des informations d’identification en utilisant le secret enregistré dans le coffre de clés. 

- **Type de runtime utilisé dans l’analyse**. Actuellement, vous ne pouvez pas utiliser une identité managée Azure Purview avec un runtime d’intégration auto-hébergé. 

### <a name="additional-considerations"></a>Considérations supplémentaires  

- Si vous choisissez d’analyser des sources de données en utilisant des points de terminaison publics, vos sources de données locales ou basées sur des machines virtuelles doivent disposer d’une connectivité sortante aux points de terminaison Azure. 

- Vos machines virtuelles du runtime d’intégration auto-hébergé doivent disposer d’une [connectivité sortante aux points de terminaison Azure](manage-integration-runtimes.md#networking-requirements). 

- Vos sources de données Azure doivent autoriser l’accès public. Si un point de terminaison de service est activé sur la source de données, veillez à _autoriser les services Azure sur la liste des services approuvés_ à accéder à vos sources de données Azure. Le point de terminaison de service route le trafic provenant du réseau virtuel via un chemin optimal vers Azure. 

## <a name="option-2-use-private-endpoints"></a>Option 2 : Utiliser des points de terminaison privés 

Vous pouvez utiliser des [points de terminaison privés Azure](../private-link/private-endpoint-overview.md) pour vos comptes Azure Purview. Cette option est utile si vous devez faire une des choses suivantes :

- Analyser des sources de données Azure IaaS et PaaS dans des réseaux virtuels Azure, et des sources de données locales via une connexion privée.
- Autoriser des utilisateurs sur un réseau virtuel à accéder de façon sécurisée à Azure Purview via [Azure Private Link](../private-link/private-link-overview.md). 

À l’instar des autres solutions PaaS, Azure Purview ne prend pas en charge le déploiement direct dans un réseau virtuel. Vous ne pouvez donc pas utiliser certaines fonctionnalités réseau avec les ressources de l’offre, comme les groupes de sécurité réseau, les tables de routage ou d’autres appliances dépendantes du réseau, comme Pare-feu Azure. Au lieu de cela, vous pouvez utiliser des points de terminaison privés qui peuvent être activés sur votre réseau virtuel. Vous pouvez ensuite désactiver l’accès Internet public pour vous connecter de façon sécurisée à Azure Purview. 

Vous devez utiliser des points de terminaison privés pour votre compte Azure Purview si vous devez satisfaire une des conditions suivantes : 

- Vous devez avoir un isolement réseau de bout en bout pour les comptes Azure Purview et les sources de données. 

- Vous devez [bloquer l’accès public](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access) à vos comptes Azure Purview. 

- Vos sources de données PaaS sont déployées avec des points de terminaison privés et vous avez bloqué tous les accès via le point de terminaison public. 

- Vos sources de données locales ou IaaS ne peuvent pas atteindre des points de terminaison publics. 

### <a name="design-considerations"></a>Remarques relatives à la conception  

- Pour vous connecter à votre compte Azure Purview de façon privée et sécurisée, vous devez déployer un point de terminaison privé de compte et de portail. Par exemple, ce déploiement est nécessaire si vous prévoyez de vous connecter à Azure Purview via l’API ou d’utiliser Azure Purview Studio.

- Si vous devez vous connecter à Azure Purview Studio en utilisant des points de terminaison privés, vous devez déployer des points de terminaison privés de compte et de portail. 

- Pour analyser des sources de données via une connectivité privée, vous devez configurer au moins un compte et un point de terminaison privé d’ingestion pour Azure Purview. Vous devez configurer les analyses en utilisant un runtime d’intégration auto-hébergé via une méthode d’authentification autre qu’une identité managée Azure Purview. 

- Passez en revue [Matrice de prise en charge pour l’analyse des sources de données via un point de terminaison privé d’ingestion](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint) avant de configurer des analyses.

- Examinez la [Configuration requise du DNS](catalog-private-link-name-resolution.md#deployment-options). Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients doivent pouvoir résoudre le nom de domaine complet (FQDN) pour les points de terminaison du compte Azure Purview en l’adresse IP du point de terminaison privé. 

### <a name="integration-runtime-options"></a>Options de runtime d’intégration 

- Si vos sources de données se trouvent dans Azure, vous devez configurer et utiliser un runtime d’intégration auto-hébergé sur une machine virtuelle Windows déployée à l’intérieur du même réseau virtuel où les points de terminaison privés d’ingestion d’Azure Purview sont déployés. Le runtime d’intégration Azure ne va pas fonctionner avec des points de terminaison privés d’ingestion. 

- Pour analyser des sources de données locales, vous pouvez aussi installer un runtime d’intégration auto-hébergé sur une machine Windows locale ou sur une machine virtuelle au sein d’un réseau virtuel Azure. 

- Quand vous utilisez des points de terminaison privé avec Azure Purview, vous devez autoriser la connectivité réseau depuis les sources de données vers la machine virtuelle d’intégration auto-hébergée sur le réseau virtuel Azure où les points de terminaison privés Azure Purview sont déployés.  

- Nous recommandons d’autoriser la mise à niveau automatique du runtime d’intégration auto-hébergé. Veillez à ouvrir les règles de trafic sortant nécessaires dans votre réseau virtuel Azure ou sur votre pare-feu d’entreprise pour permettre la mise à niveau automatique. Pour plus d’informations, consultez la [configuration réseau requise du runtime d’intégration auto-hébergé](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Options d’authentification  

- Vous ne pouvez pas utiliser une identité managée Azure Purview pour analyser des sources de données via des points de terminaison privés d’ingestion. Utilisez un principal de service, une clé de compte ou l’authentification SQL, selon le type de la source de données.

- Vérifiez que vos informations d’identification sont stockées dans un coffre de clés Azure et inscrites dans Azure Purview.

- Vous devez créer des informations d’identification dans Azure Purview en fonction de chaque secret que vous créez dans le coffre de clés Azure. Vous devez affecter au minimum l’accès _obtenir_ et _lister_ pour les secrets pour Azure Purview sur la ressource Key Vault dans Azure. Sinon, les informations d’identification ne vont pas fonctionner dans le compte Azure Purview. 

### <a name="current-limitations"></a>Limites actuelles 

- L’analyse de plusieurs sources Azure en utilisant l’ensemble de l’abonnement ou du groupe de ressources via des points de terminaison privés d’ingestion et d’un runtime d’intégration auto-hébergé n’est pas prise en charge lors de l’utilisation de points de terminaison privés pour l’ingestion. À la place, vous devez inscrire et analyser les sources de données individuellement. 

- Pour les limitation liées aux points de terminaison privés Azure Purview, consultez [Limitations connues](catalog-private-link-troubleshoot.md#known-limitations).

- Pour les limitations liées au service Private Link, consultez [Limites d’Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits). 

### <a name="private-endpoint-scenarios"></a>Scénarios impliquant des points de terminaison privés 

#### <a name="single-virtual-network-single-region"></a>Un seul réseau virtuel, une seule région  

Dans ce scénario, toutes les sources de données Azure, toutes les machines virtuelles du runtime d’intégration auto-hébergé et tous les points de terminaison privés Azure Purview sont déployés dans le même réseau virtuel dans un abonnement Azure.   

Si des sources de données locales existent, la connectivité est assurée via une connexion VPN de site à site ou Azure ExpressRoute à un réseau virtuel Azure où les points de terminaison privés Azure Purview sont déployés. 

Cette architecture convient principalement aux petites organisations ou aux scénarios de développement, de test et de preuve de concept. 

:::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés dans un scénario avec un seul réseau virtuel."lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="single-region-multiple-virtual-networks"></a>Une seule région, plusieurs réseaux virtuels 

Pour interconnecter plusieurs réseaux virtuels dans Azure, vous pouvez utiliser le [peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md). Le trafic réseau entre les réseaux virtuels appairés est privé et conservé sur le réseau principal Azure. 

De nombreux clients créent leur infrastructure réseau dans Azure en utilisant l’architecture réseau hub-and-spoke, où : 

- Les services réseau partagés (comme les appliances virtuelles réseau, les passerelles ExpressRoute/VPN ou les serveurs DNS) sont déployés dans le réseau virtuel du hub. 
- Les réseaux virtuels spoke consomment ces services partagés via l’appairage de réseaux virtuels. 

Dans les architectures réseau hub-and-spoke, l’équipe de gouvernance des données de votre organisation peut bénéficier d’un abonnement Azure qui inclut un réseau virtuel (hub). Tous les services de données peuvent se trouver dans quelques autres abonnements connectés au réseau virtuel hub via un appairage de réseaux virtuels ou une connexion VPN de site à site. 

Dans une architecture hub-and-spoke, vous pouvez déployer Azure Purview et une ou plusieurs machines virtuelles du runtime d’intégration auto-hébergé dans l’abonnement hub et le réseau virtuel. Vous pouvez inscrire et analyser les sources de données d’autres réseaux virtuels de plusieurs abonnements dans la même région. 

Les machines virtuelles du runtime d’intégration auto-hébergé doivent se trouver dans le même réseau virtuel que le point de terminaison privé d’ingestion, mais elles peuvent se trouver dans un sous-réseau distinct.

:::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés dans un scénario avec plusieurs réseaux virtuels."lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

Si vous le souhaitez, vous pouvez déployer un autre runtime d’intégration auto-hébergé dans les réseaux virtuels spoke. Dans ce cas, vous devez déployer un point de terminaison privé de compte et d’ingestion supplémentaire dans le réseau virtuel spoke. 

#### <a name="multiple-regions-multiple-virtual-networks"></a>Plusieurs régions, plusieurs réseaux virtuels

Si vos sources de données sont réparties dans plusieurs régions Azure dans le cadre d’un ou plusieurs abonnements Azure, vous pouvez utiliser ce scénario.

Pour l’optimisation des performances et des coûts, nous recommandons fortement de déployer une ou plusieurs machines virtuelles du runtime d’intégration auto-hébergé dans chaque région où se trouvent des sources de données. Dans ce cas, vous devez déployer un point de terminaison privé de compte et d’ingestion supplémentaire pour le compte Azure Purview dans la région et le réseau virtuel où se trouvent les machines virtuelles du runtime d’intégration auto-hébergé.  

Si vous avez devez inscrire et analyser des ressources Azure Data Lake Storage (Gen2) d’autres régions, vous devez disposer d’une machine virtuelle du runtime d’intégration auto-hébergé locale dans la région où se trouve la source de données. 

:::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés dans un scénario avec plusieurs réseaux virtuels et plusieurs régions."lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3-use-both-private-and-public-endpoints"></a>Option 3 : Utiliser des points de terminaison privés et des points de terminaison publics

Vous pouvez choisir une option dans laquelle un sous-ensemble de vos sources de données utilise des points de terminaison privés et, en même temps, vous devez analyser un des éléments suivants :

- D’autres sources de données configurées avec un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md)
- Des sources de données qui ont un point de terminaison public accessible via Internet

Si vous devez analyser des sources de données en utilisant un point de terminaison privé d’ingestion et des sources de données qui utilisent des points de terminaison publics ou un point de terminaison de service, vous pouvez :

1. Utiliser des points de terminaison privés pour votre compte Azure Purview.
1. Définir **Accès au réseau public** sur **Autoriser** pour votre compte Azure Purview.

### <a name="integration-runtime-options"></a>Options de runtime d’intégration 

- Pour analyser une source de données Azure configurée avec un point de terminaison privé, vous devez configurer et utiliser un runtime d’intégration auto-hébergé sur une machine virtuelle Windows déployée au sein du même réseau virtuel où les points de terminaison privés d’ingestion d’Azure Purview sont déployés. 

  Quand vous utilisez un point de terminaison privé avec Azure Purview, vous devez autoriser la connectivité réseau depuis les sources de données vers une machine virtuelle d’intégration auto-hébergée sur le réseau virtuel Azure où les points de terminaison privés Azure Purview sont déployés.  

- Pour analyser une source de données Azure configurée pour autoriser un point de terminaison public, vous pouvez utiliser le runtime d’intégration Azure. 

- Pour analyser des sources de données locales, vous pouvez aussi installer un runtime d’intégration auto-hébergé sur une machine Windows locale ou sur une machine virtuelle au sein d’un réseau virtuel Azure. 

- Nous recommandons d’autoriser la mise à niveau automatique pour un runtime d’intégration auto-hébergé. Veillez à ouvrir les règles de trafic sortant nécessaires dans votre réseau virtuel Azure ou sur votre pare-feu d’entreprise pour permettre la mise à niveau automatique. Pour plus d’informations, consultez la [configuration réseau requise du runtime d’intégration auto-hébergé](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Options d’authentification  

- Pour analyser une source de données Azure configurée pour autoriser un point de terminaison public, vous pouvez utiliser n’importe quelle option d’authentification, selon le type de source de données.
  
- Si vous utilisez un point de terminaison privé d’ingestion pour analyser une source de données Azure configurée avec un point de terminaison privé :

  - Vous ne pouvez pas utiliser une identité managée Azure Purview. Au lieu de cela, utilisez un principal de service, une clé de compte ou l’authentification SQL, selon le type de la source de données. 
  
  - Vérifiez que vos informations d’identification sont stockées dans un coffre de clés Azure et inscrites dans Azure Purview.

  - Vous devez créer des informations d’identification dans Azure Purview en fonction de chaque secret que vous créez dans Azure Key Vault. Au minimum, affectez l’accès _obtenir_ et _lister_ pour les secrets pour Azure Purview sur la ressource Key Vault dans Azure. Sinon, les informations d’identification ne vont pas fonctionner dans le compte Azure Purview. 

## <a name="next-steps"></a>Étapes suivantes
-  [Utiliser des points de terminaison privés pour un accès sécurisé à Azure Purview](./catalog-private-link.md)
