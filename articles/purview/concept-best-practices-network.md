---
title: Architecture et meilleures pratiques pour les réseaux Purview
description: Cet article fournit des exemples des options d’architecture réseau Azure Purview et décrit les meilleures pratiques.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: b49709a5fb31e597b6a6f85ac0648018ea35a744
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661855"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Architecture et meilleures pratiques pour les réseaux Purview

Azure Purview est une solution de gouvernance des données PaaS (Platform as a Service). 

Les comptes Azure Purview incluent des points de terminaison publics accessibles sur Internet pour se connecter à Purview. Toutefois, tous les points de terminaison sont sécurisés à l’aide de connexions AAD et RBAC avec un contrôle d’accès affiné.
Pour un niveau de sécurité supplémentaire, vous pouvez également créer des points de terminaison privés pour votre compte Azure Purview. Cela permet d’affecter une adresse IP privée à partir de votre réseau virtuel dans Azure au compte Purview et à ses ressources managées. Cela permet de restreindre tout le trafic entre votre réseau virtuel et le compte Purview à une liaison privée dans les cas où les utilisateurs interagissent avec les API, Purview Studio, ou pour l’analyse et l’ingestion. Actuellement, le pare-feu Azure Purview fournit un contrôle d’accès pour le point de terminaison public de votre compte Purview. Vous pouvez utiliser le pare-feu pour autoriser ou bloquer tout l’accès via le point de terminaison public quand des points de terminaison privés sont utilisés. 

En fonction de vos besoins en matière de réseau et de sécurité, vous pouvez configurer et gérer des comptes Azure Purview pour accéder aux services sous-jacents Purview ou à l’ingestion, en fonction des options de réseau suivantes : 

- Utiliser des [points de terminaison publics Azure](#option-1---use-public-endpoints). 
- Utiliser des [points de terminaison privés](#option-2---use-private-endpoints). 
- Utiliser des [points de terminaison privés et autoriser l’accès public sur le même compte Purview](#option-3---use-both-private-endpoint-and-public-endpoints). 

Pour identifier l’option la plus adaptée à votre environnement, nous vous suggérons de commencer par ce qui suit : 

- Examinez la topologie et les conditions requises de sécurité de votre réseau avant d’inscrire et d’analyser des sources de données dans Azure Purview. Pour plus d’informations, voir [Définir une topologie réseau Azure](/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology). 

- Définissez votre [modèle de connectivité réseau pour les services PaaS](/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services). 

## <a name="intended-audience"></a>Public concerné  

- Équipe en charge de l’architecture des données 
- Équipe réseau  
- Équipe de sécurité des données 

## <a name="why-do-you-need-to-consider-defining-a-networking-design-part-of-azure-purview-architecture"></a>Pourquoi devez-vous envisager de définir une conception de réseau dans le cadre de l’architecture Azure Purview ? 

Utilisez ce guide des meilleures pratiques pour définir et préparer votre environnement réseau pour Azure Purview, afin d’accéder à Purview et d’analyser des sources de données à partir de différents emplacements de votre réseau ou cloud, en fonction des conditions requises liées au réseau, à la connectivité et à la sécurité.

Nous avons documenté les scénarios d’architecture réseau les plus courants pour Azure Purview dans ce guide. Toutefois, les options disponibles ne sont pas limitées à celles décrites dans ce document. Examinez les limitations de Purview lors de la planification du réseau pour vos comptes Purview. 

## <a name="option-1---use-public-endpoints"></a>Option 1 - Utiliser des points de terminaison publics 

Par défaut, vous pouvez utiliser des comptes Azure Purview via des points de terminaison publics accessibles sur Internet. Autorisez le réseau public dans votre compte Purview, si vous avez les conditions requises suivantes : 

- Aucune connectivité privée n’est requise lors de l’analyse ou de la connexion aux points de terminaison Azure Purview. 
- Toutes les sources de données sont des applications SaaS uniquement. 
- Toutes les sources de données ont un point de terminaison public, accessible sur Internet. 
- Les utilisateurs professionnels ont besoin d’accéder au compte Purview et à Purview Studio sur Internet. 

### <a name="integration-runtime-options"></a>Options de runtime d’intégration 

Pour analyser des sources de données alors que le pare-feu de compte Purview est configuré pour autoriser l’accès public, vous pouvez utiliser le runtime d’intégration Azure et le [runtime d’intégration auto-hébergé](./manage-integration-runtimes.md), en fonction de votre [prise en charge des sources de données](manage-data-sources.md).  

- Vous pouvez utiliser le runtime d’intégration Azure ou un runtime d’intégration auto-hébergé pour analyser les sources de données Azure telles qu’Azure SQL Database ou le Stockage Blob Azure.  

- Il est recommandé d’utiliser le runtime d’intégration Azure pour analyser les sources de données Azure lorsque cela est possible, afin de réduire les coûts et la surcharge administrative. 
  
- Pour analyser plusieurs sources de données Azure, utilisez le réseau public et le runtime d’intégration Azure. Les étapes suivantes montrent le flux de communication à un niveau très élevé, lorsque le runtime d’intégration Azure est utilisé pour analyser une source de données dans Azure :

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Capture d’écran montrant le flux de connexion entre Azure Purview, le runtime et les sources de données."lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. Une analyse manuelle ou automatique est lancée à partir du mappage de données Purview à l’aide du runtime d’intégration Azure. 
   
  2. Le runtime d’intégration Azure se connecte à la source de données pour extraire les métadonnées.

  3. Les métadonnées sont mises en file d’attente dans le stockage managé d’Azure Purview et stockées dans le Stockage Blob Azure. 

  4. Les métadonnées sont envoyées au mappage de données Azure Purview. 

- L’analyse des sources de données locale et basée sur une machine virtuelle requiert toujours l’utilisation d’un runtime d’intégration auto-hébergé. Le runtime d’intégration Azure n’est pas pris en charge pour ces sources de données. Les étapes suivantes montrent le flux de communication à un niveau très élevé, lorsque le runtime d’intégration auto-hébergé est utilisé pour analyser une source de données :

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Capture d’écran montrant le flux de connexion entre Azure Purview, le runtime auto-hébergé et les sources de données."lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. Une analyse manuelle ou automatique est déclenchée. Azure Purview se connecte à Azure Key Vault pour récupérer les informations d’identification permettant d’accéder à une source de données.
   
  2. L’analyse est lancée à partir du mappage de données Purview à l’aide d’un runtime d’intégration auto-hébergé. 
   
  3. Le service du runtime d’intégration auto-hébergé de la machine virtuelle se connecte à la source de données pour extraire les métadonnées.

  4. Les métadonnées sont traitées dans la mémoire de la machine virtuelle du runtime d’intégration auto-hébergé. Les métadonnées sont mises en file d’attente dans le stockage managé d’Azure Purview, puis stockées dans le Stockage Blob Azure. 

  5. Les métadonnées sont envoyées au mappage de données Azure Purview. 

### <a name="authentication-options"></a>Options d’authentification  

Lors de l’analyse d’une source de données dans Azure Purview, vous devez fournir des informations d’identification pour qu’Azure Purview puisse lire les métadonnées des ressources à l’aide du runtime d’intégration Azure dans la source de données de destination. À l’aide d’un réseau public, les options d’authentification et les conditions requises varient en fonction des facteurs suivants : 

- Type de source de données. Par exemple, s’il s’agit d’une source de données Azure SQL Database, vous devez utiliser une authentification SQL avec l’accès db_datareader à chaque base de données. Il peut s’agir d’une identité managée d’utilisateur ou Azure Purview, ou d’un principal de service dans votre instance Azure Active Directory ajouté à Azure SQL Database en tant que db_datareader. S’il s’agit d’une source de données Stockage Blob Azure, vous pouvez utiliser l’identité managée Azure Purview ou un principal de service dans votre instance Azure Active Directory ajouté comme rôle Lecteur de données Blob Stockage sur le compte Stockage Azure, ou simplement utiliser la clé du compte de stockage.  

- Il est recommandé d’utiliser l’identité managée Azure Purview pour analyser les sources de données Azure lorsque cela est possible, afin de réduire la surcharge administrative. Pour tous les autres types d’authentification dont vous avez besoin pour [configurer les informations d’identification pour l’authentification source dans Azure Purview](manage-credentials.md) : 

  - Générez un secret dans un coffre Azure Key Vault. 
  - Inscrivez le coffre de clés dans Azure Purview.  
  - Dans Azure Purview, créez des informations d’identification à l’aide du secret enregistré dans le coffre Azure Key Vault. 

- Type du runtime utilisé dans l’analyse. Actuellement, vous ne pouvez pas utiliser une identité managée Azure Purview avec le runtime d’intégration auto-hébergé. 

### <a name="additional-considerations"></a>Considérations supplémentaires  

- Si vous choisissez d’analyser des sources de données à l’aide de points de terminaison publics, vos sources de données locales ou basées sur des machines virtuelles doivent disposer d’une connectivité sortante aux points de terminaison Azure. 

- Vos machines virtuelles du runtime d’intégration auto-hébergé doivent disposer d’une [connectivité sortante aux points de terminaison Azure](manage-integration-runtimes.md#networking-requirements). 

- Vos sources de données Azure doivent autoriser l’accès public. Toutefois, si le point de terminaison de service est activé sur la source de données, veillez à _autoriser les services Azure dans la liste des services approuvés_ à accéder à vos sources de données Azure. Le point de terminaison de service achemine le trafic à partir du réseau virtuel via un chemin d’accès optimal vers Azure. 

## <a name="option-2---use-private-endpoints"></a>Option 2 - Utiliser des points de terminaison privés 

Vous pouvez utiliser des [points de terminaison privés Azure](../private-link/private-endpoint-overview.md) pour vos comptes Azure Purview, si vous devez analyser des sources de données Azure IaaS et PaaS dans des réseaux virtuels Azure et des sources de données locales via une connexion privée ou pour permettre aux utilisateurs d’un réseau virtuel d’accéder en toute sécurité à Azure Purview via une [liaison privée](../private-link/private-link-overview.md). 

À l’instar des autres solutions PaaS (Platform as a Service), Azure Purview ne prend pas en charge le déploiement direct sur un réseau virtuel. Pour cette raison, vous ne pouvez pas tirer parti de certaines fonctionnalités réseau avec les ressources de l’offre telles que les groupes de sécurité réseau, les tables de routage ou les appliances dépendantes du réseau, telles qu’un Pare-feu Azure. Au lieu de cela, vous pouvez utiliser des points de terminaison privés qui peuvent être activés sur votre réseau virtuel et désactiver l’accès à l’Internet public pour vous connecter en toute sécurité à Purview. 

Vous devez utiliser des points de terminaison privés pour votre compte Azure Purview, si vous devez satisfaire l’une des conditions suivantes : 

- Vous devez disposer d’un isolement réseau de bout en bout pour les comptes Purview et les sources de données. 

- Vous devez [bloquer l’accès public](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access) à vos comptes Purview. 

- Vos sources de données PaaS sont déployées avec des points de terminaison privés et vous avez bloqué tous les accès via le point de terminaison public. 

- Vos sources de données locales ou IaaS ne peuvent pas atteindre des points de terminaison publics. 

### <a name="design-considerations"></a>Remarques relatives à la conception  

- Pour vous connecter à votre compte Purview de manière privée et sécurisée, vous devez déployer un point de terminaison privé de compte et de portail. (Par exemple, si vous souhaitez vous connecter à Purview via l’API ou lancer Azure Purview Studio). 

- Si vous devez vous connecter à Purview Studio à l’aide de points de terminaison privés, vous devez déployer des points de terminaison privés de compte et de portail. 

- Pour analyser des sources de données via la connectivité privée, vous devez configurer au moins un compte et un point de terminaison privé d’ingestion pour Purview. Les analyses doivent être configurées via un runtime d’intégration auto-hébergé à l’aide d’une méthode d’authentification autre que l’identité managée Azure Purview. 

- Examinez la [Matrice de prise en charge pour l’analyse des sources de données via un point de terminaison privé d’ingestion](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint) avant de configurer des analyses.

- Examinez la [Configuration requise du DNS](catalog-private-link-name-resolution.md#deployment-options). Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients doivent pouvoir résoudre le nom de domaine complet des points de terminaison de compte Purview sur l’adresse IP du point de terminaison privé. 

### <a name="integration-runtime-options"></a>Options de runtime d’intégration 

- Si vos sources de données se trouvent dans Azure, vous devez configurer et utiliser un runtime d’intégration auto-hébergé sur une machine virtuelle Windows déployée à l’intérieur du même réseau virtuel dans lequel les points de terminaison privés d’ingestion d’Azure Purview sont déployés. Le runtime d’intégration Azure ne fonctionne pas avec les points de terminaison privés d’ingestion. 

- Pour analyser des sources de données locales, vous pouvez également installer un runtime d’intégration auto-hébergé sur une machine Windows locale ou une machine virtuelle à l’intérieur d’un réseau virtuel Azure. 

- Lorsque vous utilisez un point de terminaison privé avec Azure Purview, vous devez autoriser la connectivité réseau à partir des sources de données vers la machine virtuelle d’intégration auto-hébergée sur le réseau virtuel Azure sur lequel les points de terminaison privés Purview sont déployés.  

- Nous vous recommandons d’autoriser la mise à niveau automatique du runtime d’intégration auto-hébergé. Veillez à ouvrir les règles de trafic sortant requises dans votre réseau virtuel Azure ou sur votre pare-feu d’entreprise pour permettre la mise à niveau automatique. Pour plus d’informations, consultez la [configuration réseau requise du runtime d’intégration auto-hébergé](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Options d’authentification  

- Vous ne pouvez pas utiliser l’identité managée Azure Purview pour analyser des sources de données par le biais de points de terminaison privés d’ingestion. Utilisez le principal de service, la clé du compte ou l’authentification SQL en fonction du type de la source de données.

- Vérifiez que vos informations d’identification sont stockées dans un coffre Azure Key Vault et inscrites dans Azure Purview.

- Vous devez créer des informations d’identification dans Azure Purview en fonction de chaque secret que vous créez dans le coffre Azure Key Vault. Vous devez attribuer au minimum l’accès _Get_ et _List_ pour les secrets de votre compte Azure Purview sur la ressource Key Vault dans Azure. Sinon, les informations d’identification ne fonctionneront pas dans le compte Azure Purview. 

### <a name="current-limitations"></a>Limites actuelles 

- L’analyse de plusieurs sources Azure à l’aide de l’ensemble de l’abonnement ou du groupe de ressources via des points de terminaison privés d’ingestion et du runtime d’intégration auto-hébergé n’est pas prise en charge lors de l’utilisation de points de terminaison privés pour l’ingestion. À la place, vous devez inscrire et analyser les sources de données individuellement. 

- Pour la limitation liée aux points de terminaison privés Azure Purview, consultez les [Limitations connues](catalog-private-link-troubleshoot.md#known-limitations). 

- Pour connaître les limitations liées au service Private Link, consultez [Limites Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits). 

### <a name="private-endpoint-scenarios"></a>Scénarios impliquant des points de terminaison privés 

#### <a name="scenario-1-single-vnet--single-region"></a>Scénario 1 : un seul réseau virtuel - une seule région  

Dans ce scénario, toutes les sources de données Azure, les machines virtuelles du runtime d’intégration auto-hébergé et les points de terminaison privés Purview sont déployés dans le même réseau virtuel dans un abonnement Azure.   

Si des sources de données locales existent, la connectivité est assurée via une connexion VPN de site à site ou ExpressRoute au réseau virtuel Azure sur lequel les points de terminaison privés Azure Purview sont déployés. 

Cette architecture convient principalement aux petites organisations ou aux scénarios de développement, de test et de preuve de concept. 

  :::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés dans un scénario impliquant un seul réseau virtuel."lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="scenario-2-single-region--multiple-vnets"></a>Scénario 2 : une seule région – plusieurs réseaux virtuels 

Pour interconnecter plusieurs réseaux virtuels dans Azure, vous pouvez utiliser le [peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md). Le trafic réseau entre les réseaux virtuels appairés est privé et conservé sur le réseau principal Azure. 

De nombreux clients créent leur infrastructure réseau dans Azure à l’aide de l’architecture de réseau Hub and Spoke, où : 

- Les services partagés de mise en réseau (tels que les appliances virtuelles réseau, les passerelles ExpressRoute/VPN ou les serveurs DNS) sont déployés dans le réseau virtuel du hub (réseau virtuel) 
- Les réseaux virtuels Spoke consomment ces services partagés via le peering de réseaux virtuels. 

Dans les architectures de réseau Hub and spoke, l’équipe en charge de la gouvernance des données dans votre organisation peut recevoir un abonnement Azure, lequel inclut un réseau virtuel (Hub) et tous les services de données peuvent se trouver dans quelques autres abonnements connectés au réseau virtuel Hub par le biais d’un peering de réseaux virtuels ou d’une connexion VPN de site à site. Dans cette architecture, vous pouvez déployer Azure Purview et une ou plusieurs machines virtuelles du runtime d’intégration auto-hébergé dans l’abonnement Hub et le réseau virtuel, et inscrire et analyser les sources de données d’autres réseaux virtuels à partir de plusieurs abonnements dans la même région. 

Les machines virtuelles du runtime d’intégration auto-hébergé doivent se trouver dans le même réseau virtuel que le point de terminaison privé d’ingestion, mais peuvent se trouver dans un sous-réseau distinct.

  :::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés dans un scénario impliquant plusieurs réseaux virtuels."lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

Vous pouvez éventuellement déployer un autre runtime d’intégration auto-hébergé dans les réseaux virtuels Spoke. Dans ce cas, un point de terminaison privé de compte et d’ingestion supplémentaire doit être déployé dans le réseau virtuel Spoke. 

#### <a name="scenario-3-multiple-regions--multiple-vnets"></a>Scénario 3 : plusieurs régions - plusieurs réseaux virtuels

Si vos sources de données sont réparties dans plusieurs régions Azure dans le cadre d’un ou plusieurs abonnements Azure, vous pouvez utiliser ce scénario.

Pour des raisons d’optimisation des performances et des coûts, il est fortement recommandé de déployer une ou plusieurs machines virtuelles du runtime d’intégration auto-hébergé dans chaque région où se trouvent des sources de données. Dans ce cas, vous devez déployer un point de terminaison privé de compte et d’ingestion supplémentaires pour le compte Azure Purview dans la région et le réseau virtuel où se trouvent les machines virtuelles du runtime d’intégration auto-hébergé.  

Si vous avez besoin d’inscrire et d’analyser des ressources Azure Data Lake Storage (Gen2) d’autres régions, vous devez disposer d’une machine virtuelle du runtime d’intégration auto-hébergé locale dans la région où se trouve la source de données. 

  :::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés dans un scénario impliquant plusieurs réseaux virtuels et plusieurs régions."lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3---use-both-private-endpoint-and-public-endpoints"></a>Option 3 : utiliser un point de terminaison privé et des points de terminaison publics

Cette option est nécessaire lorsqu’un sous-ensemble de vos sources de données utilise des points de terminaison privés et en même temps, vous devez analyser d’autres sources de données configurées avec un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) ou des sources de données qui ont un point de terminaison public accessible sur Internet.

Vous pouvez utiliser des points de terminaison privés pour votre compte Azure Purview et définir l’**Accès réseau public** sur _Autoriser_ sur votre compte Purview, si vous devez satisfaire l’une des conditions suivantes :
  
- Vous devez analyser certaines sources de données qui utilisent le point de terminaison privé d’ingestion et certaines sources de données qui utilisent les points de terminaison publics ou le point de terminaison de service.

### <a name="integration-runtime-options"></a>Options de runtime d’intégration 

- Pour analyser une source de données Azure configurée avec un point de terminaison privé, vous devez configurer et utiliser un runtime d’intégration auto-hébergé sur une machine virtuelle Windows déployée à l’intérieur du même réseau virtuel dans lequel les points de terminaison privés d’ingestion d’Azure Purview sont déployés. Lorsque vous utilisez un point de terminaison privé avec Azure Purview, vous devez autoriser la connectivité réseau à partir des sources de données vers la machine virtuelle d’intégration auto-hébergée sur le réseau virtuel Azure sur lequel les points de terminaison privés Purview sont déployés.  

- Pour analyser une source de données Azure configurée pour autoriser un point de terminaison public, vous pouvez utiliser le runtime d’intégration Azure. 

- Pour analyser des sources de données locales, vous pouvez également installer un runtime d’intégration auto-hébergé sur une machine Windows locale ou une machine virtuelle à l’intérieur d’un réseau virtuel Azure. 

- Nous vous recommandons d’autoriser la mise à niveau automatique du runtime d’intégration auto-hébergé. Veillez à ouvrir les règles de trafic sortant requises dans votre réseau virtuel Azure ou sur votre pare-feu d’entreprise pour permettre la mise à niveau automatique. Pour plus d’informations, consultez la [configuration réseau requise du runtime d’intégration auto-hébergé](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Options d’authentification  

- Pour analyser une source de données Azure configurée pour autoriser un point de terminaison public, vous pouvez utiliser n’importe quelle option d’authentification basée sur le type de source de données.
  
- Utilisation d’un point de terminaison privé d’ingestion pour analyser une source de données Azure configurée avec un point de terminaison privé :
  - Vous ne pouvez pas utiliser l’identité managée Azure Purview. À la place, utilisez le principal de service, la clé du compte ou l’authentification SQL en fonction du type de source de données. 
  
  - Vérifiez que vos informations d’identification sont stockées dans un coffre Azure Key Vault et inscrites dans Azure Purview.

  - Vous devez créer des informations d’identification dans Azure Purview en fonction de chaque secret que vous créez dans le coffre Azure Key Vault. Vous devez attribuer au minimum l’accès _Get_ et _List_ pour les secrets de votre compte Azure Purview sur la ressource Key Vault dans Azure. Sinon, les informations d’identification ne fonctionneront pas dans le compte Azure Purview. 

## <a name="next-steps"></a>Étapes suivantes
-  [Utiliser des points de terminaison privés pour un accès sécurisé à Purview](./catalog-private-link.md)
