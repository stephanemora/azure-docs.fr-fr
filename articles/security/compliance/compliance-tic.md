---
title: Recommandations relatives à l’initiative Trusted Internet Connections pour Azure
description: Recommandations relatives à l’initiative Trusted Internet Connections pour les services Azure et SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: d52785dd7569560f4b6986080b14723762537ec8
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388307"
---
# <a name="trusted-internet-connections-guidance"></a>Recommandations relatives à l’initiative Trusted Internet Connections

Cet article explique comment des organismes publics peuvent utiliser Microsoft Azure Government pour se mettre en conformité avec l’initiative Trusted Internet Connections (TIC). Il décrit comment utiliser Azure Government en lien avec des offres IaaS (infrastructure as a service) et PaaS (platform as a service) Azure.

## <a name="trusted-internet-connections-overview"></a>Vue d’ensemble de l’initiative Trusted Internet Connections

L’objectif de l’initiative TIC (Trustes Internet Connections) est d’optimiser et de normaliser la sécurité des connexions réseau externes individuelles qu’utilisent actuellement les agences fédérales. La stratégie est décrite dans le [Mémorandum M-08-05 du Bureau de la gestion et du budget (OMB, Office of Management and Budget)](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

En novembre 2007, l’OMB a établi le programme TIC afin d’améliorer la sécurité du périmètre réseau fédéral et des fonctions de réponse aux incidents. L’initiative TIC est conçue pour effectuer une analyse réseau de tout le trafic .gov entrant et sortant, afin d’identifier les données basées sur un modèle et des signatures spécifiques. Elle permet de détecter des anomalies comportementales, telles qu’une activité de botnet. Les agences sont mandatées pour consolider leurs connexions réseau externes et router tout le trafic via un dispositif d’appareils de détection et de prévention des intrusions appelé EINSTEIN. Ces appareils sont hébergés sur un nombre limité de points de terminaison réseau appelés _connexions Internet sécurisées_.

L’objectif de l’initiative TIC est que les agences sachent :
- Qui se trouve sur leur réseau (de manière autorisée ou non).
- Quand un accès à leur réseau se produit et pourquoi.
- Quelles ressources sont accessibles.

Toutes les connexions externes des agences doivent actuellement passer par une TIC approuvée par l’OMB. Les agences fédérales doivent obligatoirement participer au programme TIC en tant fournisseur d’accès TIC (TICAP, TIC Access Provider), soit en sous-traitant les services auprès d’un des fournisseurs de services Internet majeur de niveau 1. Ces fournisseurs sont appelés fournisseurs MTIPS (Managed Trusted Internet Protocol Service). L’initiative TIC inclut des fonctionnalités critiques obligatoires assurées par l’agence et le fournisseur MTIPS. Dans la version actuelle du programme TIC, les appareils de détection des intrusions EINSTEIN version 2 et de prévention des intrusions EINSTEIN version 3 accélérée (3A) sont déployés au niveau de chaque fournisseur TICAP et MTIPS. L’agence établit un « mémorandum d’entente » avec le Département de la sécurité intérieure (DHS, Department of Homeland Security) pour déployer les fonctionnalités EINSTEIN dans les systèmes fédéraux.

Dans le cadre de sa responsabilité de protection du réseau .gov, le DHS exige les flux réseau de données brutes de l’agence pour corréler les incidents survenant au sein de l’organisation fédérale et effectuer des analyses à l’aide d’outils spécialisés. Les routeurs DHS permettent de collecter le trafic réseau IP quand il entre ou sort d’une interface. Les administrateurs réseau peuvent analyser les données de flux réseau pour déterminer la source et la destination du trafic, la classe de service, etc. Les données de flux réseau sont considérées comme des « données hors contenu », telles que des en-têtes, des adresses IP sources et de destination, etc. Les données hors contenu permettent au DHS de connaître le contenu : qui a fait quoi et pendant combien de temps.

L’initiative inclut également des stratégies de sécurité, des instructions et des infrastructures qui supposent l’existence d’une infrastructure locale. À mesure que les organismes publics migrent vers le cloud à des fins d’économie, d’efficacité opérationnelle et d’innovation, les exigences d’implémentation de l’initiative TIC peuvent ralentir le trafic réseau. La vitesse et l’agilité avec lesquelles les fonctionnaires peuvent accéder à leurs données dans le cloud sont ainsi bridées.

## <a name="azure-networking-options"></a>Options de mise en réseau Azure

Il existe trois options principales pour la connexion aux services Azure :

- Connexion Internet directe : accès aux services Azure directement via une connexion Internet ouverte. Le vecteur et la connexion sont publics. Le chiffrement au niveau du transport et de l’application est responsable de la confidentialité. La bande passante est limitée par la connectivité du site à Internet. Utilisez plusieurs fournisseurs actifs pour garantir la résilience.
- Réseau privé virtuel (VPN, Virtual private network) : connexion confidentielle au réseau virtuel Azure via une passerelle VPN.
Le vecteur est public parce qu’il transite par une connexion Internet standard d’un site, mais la connexion est chiffrée dans un tunnel afin de garantir la confidentialité. La bande passante est limitée en fonction des périphériques VPN et de la configuration choisie. Les connexions point à site Azure sont généralement limitées à 100 Mbits/s, tandis que les connexions site à site sont limitées à 1,25 Gbits/s.
- Azure ExpressRoute : ExpressRoute est une connexion directe aux services Microsoft. Établie via un Fiber Channel isolé, la connexion peut être publique ou privée, selon la configuration utilisée. La bande passante est généralement limitée à un maximum de 10 Gbits/s.

Il existe plusieurs façons de remplir les exigences de l’Annexe H du programme TIC (considérations relatives au cloud), telles que spécifiées dans le document « Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0 » publié par le DHS. Dans cet article, les conseils du DHS en lien avec le TIC sont appelés **TIC 2.0**.

Pour connecter le **Département ou l’Agence (D/A)** à Azure ou à Office 365 sans router le trafic via le TIC du D/A, le D/A doit utiliser un tunnel chiffré ou une connexion dédiée au fournisseur de services cloud (CSP, Cloud Service Provider). Les services CSP peuvent garantir que la connectivité aux ressources cloud du D/A n’est pas offerte à l’Internet public pour l’accès direct du personnel de l’agence.

Office 365 se conforme à l’Annexe H du programme TIC 2.0 en utilisant ExpressRoute avec l’[homologation Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) activée ou une connexion Internet qui chiffre tout le trafic à l’aide de TLS 1.2. Les utilisateurs finaux du D/A sur le réseau D/A peuvent se connecter par le biais du réseau de leur agence et de l’infrastructure TIC via internet. Tout l’accès Internet à distance à Office 365 est bloqué et transite par l’agence. Le D/A peut également se connecter à Office 365 sur une connexion ExpressRoute avec une homologation Microsoft (un type d’homologation publique) activée.  

Pour Azure uniquement, la deuxième option (VPN) et la troisième option (ExpressRoute) peuvent remplir ces exigences quand elles sont utilisées conjointement avec des services qui limitent l’accès à Internet.

![Trusted Internet Connection (TIC) Microsoft](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Offres IaaS Azure

La mise en conformité avec une stratégie TIC à l’aide d’Azure IaaS est relativement simple, car les clients Azure gèrent leur propre routage de réseau virtuel.

La principale exigence pour garantir la conformité avec l’architecture de référence TIC consiste à s’assurer que le réseau virtuel est une extension privée du réseau de D/A. Pour que l’extension soit _privée_, la stratégie exige que le trafic ne quitte votre réseau que par le biais de la connexion réseau TIC locale. Ce processus est appelé _tunneling forcé_. Pour la conformité au programme TIC, le processus route tout le trafic provenant d’un système quelconque dans l’environnement CSP via une passerelle locale sur le réseau d’une organisation, puis vers Internet via le programme TIC.

La mise en conformité avec TIC Azure IaaS est divisée en deux grandes étapes :

- Étape 1 : configuration.
- Étape 2 : audit.

### <a name="azure-iaas-tic-compliance-configuration"></a>Conformité avec TIC Azure IaaS : configuration

Pour configurer une architecture conforme à TIC avec Azure, vous devez commencer par empêcher tout accès Internet direct à votre réseau virtuel, puis forcer le trafic Internet à transiter par le réseau local.

#### <a name="prevent-direct-internet-access"></a>Empêcher l’accès Internet direct

La mise en réseau Azure IaaS s’effectue par le biais de réseaux virtuels constitués de sous-réseaux, auxquels sont associés les contrôleurs réseau NIC (Network Interface Controller) des machines virtuelles.

Le scénario le plus simple pour prendre en charge la conformité TIC consiste à s’assurer qu’une machine virtuelle, ou une collection de machines virtuelles, ne peut se connecter à aucune ressource externe. Assurez la déconnexion des réseaux externes en utilisant des groupes de sécurité réseau (NSG). Utilisez des NSG pour contrôler le trafic vers un ou plusieurs NIC ou sous-réseaux de votre réseau virtuel. Un groupe de sécurité réseau contient les règles de contrôle d’accès qui autorisent ou refusent le trafic en fonction de la direction du trafic, du protocole, de l’adresse et du port source ainsi que de l’adresse et du port de destination. Vous pouvez modifier les règles d’un NSG à tout moment, et les modifications sont appliquées à toutes les instances associées. Pour en savoir plus sur la façon de créer un NSG, voir [Filtrer le trafic réseau avec un groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Forcer le transit du trafic Internet par un réseau local

Azure crée des itinéraires système automatiquement et assigne les itinéraires pour chaque sous-réseau dans un réseau virtuel. Vous ne pouvez pas créer ou supprimer des itinéraires système, mais vous pouvez en remplacer certains par des itinéraires personnalisés. Azure crée des itinéraires système par défaut pour chaque sous-réseau. Azure ajoute des itinéraires par défaut facultatifs à des sous-réseaux spécifiques ou à chaque sous-réseau lorsque vous utilisez des fonctionnalités Azure spécifiques. Ce type de routage offre les garanties suivantes :
- Le trafic circulant au sein du réseau virtuel reste dans celui-ci.
- Les espaces d’adressage privés désignés par l’IANA (Internet Assigned Numbers Authority), tels que 10.0.0.0/8, sont abandonnés, sauf s’ils sont inclus dans l’espace d’adressage du réseau virtuel.
- Routage en « Dernier recours » de 0.0.0.0/0 vers le point de terminaison Internet de réseau virtuel.

![Tunneling forcé TIC](media/tic-diagram-c.png)

Tout le trafic quittant le réseau virtuel doit être routé via la connexion locale pour s’assurer que tout le trafic traverse le TIC du D/A. Vous pouvez établir des itinéraires personnalisés en créant des itinéraires définis par l’utilisateur ou en échangeant des itinéraires utilisant le protocole de passerelle frontière (BGP, Border Gateway Protocol) entre votre passerelle réseau locale et une passerelle VPN Azure. Pour plus d’informations sur les itinéraires définis par l’utilisateur, voir [Routage du trafic réseau virtuel : itinéraires définis par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Pour plus d’informations sur le protocole BGP, voir [Routage du trafic réseau virtuel : protocole de passerelle frontière (BGP, Border Gateway Protocol)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Ajouter des Itinéraires définis par l’utilisateur

Si vous utilisez une passerelle de réseau virtuel basée sur un itinéraire, vous pouvez forcer le tunneling dans Azure. Ajoutez un itinéraire défini par l’utilisateur (UDR) qui route le trafic 0.0.0.0/0 vers un **tronçon suivant** de votre passerelle de réseau virtuel. Azure priorise les itinéraires définis par l’utilisateur par rapport aux itinéraires définis par le système. Tout trafic réseau non virtuel est envoyé à votre passerelle de réseau virtuel, qui peut ensuite router le trafic vers le réseau local. Après avoir établi l’itinéraire défini par l’utilisateur, associez-le à des sous-réseaux existants ou nouveaux dans tous les réseaux virtuels de votre environnement Azure.

![Itinéraires définis par l’utilisateur et TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Utiliser le protocole BGP

Si vous utilisez ExpressRoute ou une passerelle de réseau virtuel compatible avec le protocole BGP, celui-ci est le mécanisme préconisé pour l’annonce des itinéraires. Avec un itinéraire BGP annoncé 0.0.0.0/0, ExpressRoute et les passerelles de réseau virtuel compatibles avec le protocole BGP veillent à que l’itinéraire par défaut s’applique à tous les sous-réseaux de vos réseaux virtuels.

### <a name="azure-iaas-tic-compliance-auditing"></a>Conformité avec TIC Azure IaaS : audit

Azure offre plusieurs moyens d’auditer la conformité avec TIC.

#### <a name="view-effective-routes"></a>Afficher les itinéraires effectifs

Confirmez que votre itinéraire par défaut est propagé en observant les _itinéraires effectifs_ pour une machine virtuelle particulière, un NIC spécifique ou une table d’itinéraires définis par l’utilisateur dans le [portail Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) ou dans [. Azure PowerShell ](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). Les **Itinéraires effectifs** présentent les itinéraires définis par l’utilisateur pertinents, les itinéraires BGP annoncés et les itinéraires système qui s’appliquent à l’entité appropriée, comme illustré ci-dessous :

![Itinéraires effectifs](media/tic-screen-1.png)

> [!NOTE]
> Vous ne pouvez pas afficher les itinéraires effectifs pour un NIC, sauf si celui-ci est associé à une machine virtuelle en cours d’exécution.

#### <a name="use-azure-network-watcher"></a>Utiliser Azure Network Watcher

Azure Network Watcher offre plusieurs outils pour l’audit de la conformité au TIC. Pour plus d’informations, voir [cette présentation de Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Capturer les journaux de flux de groupe de sécurité réseau 

Utilisez Network Watcher pour capturer les journaux de flux réseau qui indiquent les métadonnées entourant le trafic IP. Les journaux de flux réseau contiennent les adresses sources et de destination des cibles, ainsi que d’autres données. Vous pouvez utiliser ces données avec les journaux de la passerelle de réseau virtuel, des périphériques de périmètre locaux ou le TIC pour surveiller tous les itinéraires de trafic locaux. 

## <a name="azure-platform-as-a-service-offerings"></a>Offres Azure PaaS

Les services Azure PaaS tels que Stockage Azure sont accessibles via une URL accessible sur Internet. Toute personne disposant d’informations d’identification approuvées peut accéder à la ressource (telle qu’un compte de stockage) à partir de n’importe quel emplacement sans traverser de TIC. C’est pourquoi de nombreux clients publics concluent à tort que les services Azure PaaS ne sont pas conformes aux stratégies TIC. Bon nombre de services Azure PaaS peuvent être conformes à une stratégie TIC. Un service est conforme quand son architecture est identique à celle de l’environnement IaaS compatible TIC ([comme décrit précédemment](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) et quand il est joint à un réseau virtuel Azure.

Quand des services Azure PaaS sont intégrés avec un réseau virtuel, le service est accessible de façon confidentielle à partir de ce réseau virtuel. Vous pouvez appliquer un routage personnalisé pour 0.0.0.0/0 via des itinéraires définis par l’utilisateur ou le protocole BGP. Un routage personnalisé garantit que tous les itinéraires de trafic liés à Internet transitent pas le TIC. Intégrez les services Azure dans des réseaux virtuels à l’aide des modèles suivants :

- **Déployer une instance dédiée d’un service** : un nombre croissant de services PaaS sont déployables en tant qu’instances dédiées avec des points de terminaison attachés au réseau virtuel. Vous pouvez déployer un environnement App Service pour PowerApps en mode « Isolé » pour permettre la limitation du point de terminaison réseau à un réseau virtuel. L’environnement App Service peut alors héberger de nombreux services Azure PaaS, tels qu’Azure Web Apps, Gestion des API Azure et Azure Functions.
- **Utiliser des points de terminaison de service de réseau virtuel** : un nombre croissant de services PaaS autorisent le déplacement de leur point de terminaison vers une adresse IP privée de réseau virtuel au lieu d’une adresse publique.

Depuis mai 2018, les services qui prennent en charge le déploiement d’instances dédiées dans un réseau virtuel ou l’utilisation de points de terminaison de service sont répertoriés dans les tableaux suivants.

> [!Note]
> L’état de disponibilité correspond aux services Azure disponibles commercialement. L’état de disponibilité des services Azure dans Azure Government est en attente.

### <a name="support-for-service-endpoints"></a>Support pour les points de terminaison de service

|de diffusion en continu                        |Disponibilité      |
|-------------------------------|------------------|
|Azure Key Vault                | Préversion privée  |
|Azure Cosmos DB                | Préversion privée  |
|Services d’identité              | Préversion privée  |
|Azure Data Lake                | Préversion privée  |
|Azure Database pour PostgreSQL  | Préversion privée  |
|Azure Database pour MySQL       | Préversion privée  |
|Azure SQL Data Warehouse       | Préversion publique   |
|Azure SQL Database             | Disponibilité générale |
|Stockage Azure                  | GA               |

### <a name="support-for-virtual-network-injection"></a>Prise en charge pour l’injection de réseau virtuel

|de diffusion en continu                               |Disponibilité      |
|--------------------------------------|------------------|
|Azure SQL Database Managed Instance   | Préversion publique   |
|Service Azure Kubernetes (AKS)        | Préversion publique   |
|Azure Service Fabric                  | GA               |
|Gestion des API Azure                  | GA               |
|Azure Active Directory                | GA               |
|Azure Batch                           | GA               |
|Environnement App Service               | GA               |
|Cache Redis Azure                     | GA               |
|Azure HDInsight                       | GA               |
|Jeu de mise à l’échelle de machine virtuelle             | GA               |
|Services cloud Azure                  | GA               |


### <a name="virtual-network-integration-details"></a>Détails d’intégration de réseau virtuel

Le diagramme suivant illustre le flux réseau général pour l’accès aux services PaaS. L’accès est illustré à partir d’une injection de réseau virtuel et d’un tunneling de service de réseau virtuel. Pour plus d’informations sur les passerelles de service réseau, les réseaux virtuels et les balises de service, voir [Groupes de sécurité réseau et d’application : balises de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![Options de connectivité PaaS pour TIC](media/tic-diagram-e.png)

1. Une connexion privée est établie à Azure à l’aide d’ExpressRoute. Une homologation privée ExpressRoute avec tunneling forcé est utilisée pour forcer tout le trafic de réseau virtuel client sur ExpressRoute, puis de nouveau localement. Une homologation Microsoft n’est pas nécessaire.
2. Une passerelle VPN Azure, utilisée conjointement avec ExpressRoute et une homologation Microsoft, peut recouvrir le chiffrement IPSec de bout en bout entre le réseau virtuel client et le périmètre local. 
3. La connectivité au réseau virtuel client est contrôlée à l’aide de groupes de sécurité réseau qui permettent aux clients d’autoriser ou de refuser l’accès en fonction de l’adresse IP, du port et du protocole.
4. Le réseau virtuel client s’étend au service PaaS en créant un point de terminaison de service pour le service du client.
5. Le point de terminaison de service PaaS est sécurisé de façon à **tout rejeter par défaut** et à autoriser l’accès uniquement à partir des sous-réseaux spécifiés du réseau virtuel client. Les rejets par défaut incluent également les connexions provenant d’Internet.
6. Les autres services Azure ayant besoin d’accéder aux ressources du réseau virtuel client doivent être :  
   - Déployés directement dans le réseau virtuel.
   - Autorisés de manière sélective conformément aux recommandations du service Azure concerné.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Option A : déployer une instance dédiée d’un service (injection de réseau virtuel)

Une injection de réseau virtuel permet aux clients de déployer de manière sélective des instances dédiées d’un service Azure donné, tel que HDInsight, dans leur propre réseau virtuel. Les instances de service sont déployées dans un sous-réseau dédié du réseau virtuel d’un client. L’injection de réseau virtuel permet d’accéder aux ressources de service via les adresses routables non-Internet. Les instances locales utilisent ExpressRoute ou un VPN de site à site pour accéder directement aux instances de service via l’espace d’adressage de réseau virtuel, au lieu d’ouvrir un pare-feu à l’espace d’adressage Internet public. Quand une instance dédiée est attachée à un point de terminaison, vous pouvez utiliser les mêmes stratégies que pour la conformité au TIC IaaS. Le routage par défaut garantit que le trafic Internet est redirigé vers une passerelle de réseau virtuel liée en local. Vous pouvez contrôler davantage l’accès entrant et sortant via des groupes de sécurité réseau pour le sous-réseau donné.

![Vue d’ensemble de l’injection de réseau virtuel](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Option B : utiliser des points de terminaison de service de réseau virtuel (tunnel de service)

Un nombre croissant de services mutualisés Azure offrent des « points de terminaison de service ». Les points de terminaison de service sont une méthode alternative pour l’intégration à des réseaux virtuels Azure. Des points de terminaison de service de réseau virtuel étendent votre espace d’adressage IP de réseau virtuel et l’identité de votre réseau virtuel au service via une connexion directe. Le trafic du réseau virtuel vers le service Azure reste toujours à l’intérieur du réseau principal Azure. 

Après avoir activé un point de terminaison de service pour un service, utilisez des stratégies exposées par le service pour restreindre les connexions de service à ce réseau virtuel. Des contrôles d’accès sont appliqués dans la plateforme par le service Azure. L’accès à une ressource verrouillée est accordé uniquement si la demande provient du réseau ou sous-réseau virtuel autorisé, ou des deux adresses IP utilisées pour identifier votre trafic local si vous utilisez ExpressRoute. Utilisez cette méthode pour empêcher efficacement le trafic entrant/sortant de quitter directement le service PaaS.

![Vue d’ensemble des points de terminaison de service](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Outils cloud natifs pour la conscience situationnelle du réseau

Azure fournit des outils natifs pour s’assurer que vous ayez la conscience situationnelle requise pour comprendre les flux de trafic de votre réseau. Les outils ne sont pas requis pour la conformité avec la stratégie TIC. Les outils peuvent améliorer considérablement vos fonctionnalités de sécurité.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](https://azure.microsoft.com/services/azure-policy/) est un service Azure qui permet à votre organisation de mieux auditer et appliquer des initiatives de conformité. Azure Policy est actuellement disponible en préversion publique dans les services Azure disponibles commercialement. Azure Policy n’est pas encore disponible dans Azure Government. Les clients peuvent planifier et tester leurs règles Azure Policy maintenant pour s’assurer de la conformité future à TIC. 

Azure Policy est ciblé au niveau d’abonnement. Le service fournit une interface centralisée dans laquelle vous pouvez effectuer les tâches de conformité suivantes :
- Gérer des initiatives
- Configurer des définitions de stratégie
- Auditer la conformité
- Appliquer la conformité
- Gérer les exceptions

En plus des nombreuses définitions intégrées, les administrateurs peuvent élaborer leurs propres définitions personnalisées à l’aide de modèles JSON simples. Microsoft recommande autant que possible de préférer l’audit à l’application.

Les exemples de stratégies suivants peuvent être utiles pour des scénarios de conformité au TIC :

|Stratégie  |Exemple de scénario  |Modèle  |
|---------|---------|---------|
|Appliquez une table d’itinéraires définie par l’utilisateur. | Vérifiez que l’itinéraire par défaut sur tous les réseaux virtuels pointe vers une passerelle de réseau virtuel approuvée pour le routage local.    | Commencez à utiliser ce [modèle](https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table). |
|Effectuez un audit pour vérifier si Network Watcher n’est pas activé pour une région.  | Assurez-vous que Network Watcher est activé pour toutes les régions utilisées.  | Commencez à utiliser ce [modèle](https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled). |
|Groupe de sécurité réseau x sur chaque sous-réseau.  | Assurez-vous qu’un groupe de sécurité réseau (ou un ensemble de groupes de sécurité réseau approuvés) avec trafic Internet bloqué est appliqué à tous les sous-réseaux de chaque réseau virtuel. | Commencez à utiliser ce [modèle](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet). |
|Groupe de sécurité réseau x sur chaque NIC. | Assurez-vous qu’un groupe de sécurité réseau avec trafic Internet bloqué est appliqué à tous les NIC sur toutes les machines virtuelles. | Commencez à utiliser ce [modèle](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic). |
|Utilisez un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle.  | Assurez-vous que tous les NIC se trouvent sur un réseau virtuel approuvé. | Commencez à utiliser ce [modèle](https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics). |
|Emplacements autorisés. | Assurez-vous que toutes les ressources sont déployées dans des régions avec une configuration de réseaux virtuels et de Network Watcher conforme.  | Commencez à utiliser ce [modèle](https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs). |
|Types de ressources non autorisés, tels que **PublicIPs**. | Interdisez le déploiement de types de ressources qui n’ont pas de plan de conformité. Utilisez cette stratégie pour interdire le déploiement de ressources d’adresses IP publiques. Bien que les règles de groupe de sécurité réseau puissent servir à bloquer efficacement le trafic Internet entrant, empêcher l’utilisation d’adresses IP publiques réduit davantage la surface d’attaque.   | Commencez à utiliser ce [modèle](https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type).  |

### <a name="network-watcher-traffic-analytics"></a>Analyse du trafic par Network Watcher

L’[analyse du trafic](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) par Network Watcher utilise des données de journal de flux et d’autres journaux pour fournir une vue d’ensemble du trafic réseau. Ces données sont utiles pour l’audit de conformité TIC et pour identifier des points problématiques. Vous pouvez utiliser le tableau de bord de haut niveau pour filtrer rapidement les machines virtuelles qui communiquent avec Internet et obtenir une liste ciblée pour le routage TIC.

![Traffic Analytics](media/tic-traffic-analytics-1.png)

Utilisez la **Carte géographique** pour identifier rapidement les destinations physiques probables du trafic Internet pour vos machines virtuelles. Vous pouvez identifier et trier des emplacements suspects ou des changements de modèle :

![Carte géographique](media/tic-traffic-analytics-2.png)

Utilisez la **topologie de réseaux virtuels** pour examiner rapidement des réseaux virtuels existants :

![Carte topologique de réseau](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Tests de tronçon suivant Network Watcher

Les réseaux dans les régions surveillées par Network Watcher peuvent effectuer des tests de tronçon suivant. Dans le portail Azure, vous pouvez entrer une source et une destination pour un exemple de flux réseau afin que Network Watcher résolve la destination du tronçon suivant. Exécutez ce test sur des machines virtuelles et des exemples d’adresses Internet pour vous assurer que la destination du tronçon suivant est la passerelle virtuelle réseau attendue.

![Tests de tronçon suivant](media/tic-network-watcher.png)

## <a name="conclusions"></a>Conclusions

Vous pouvez aisément configurer l’accès pour Microsoft Azure, Office 365 et Dynamics 365 afin de faciliter la mise en conformité avec les recommandations de l’annexe H de TIC 2.0 rédigées et définies en mai 2018. Microsoft reconnaît que les recommandations TIC sont susceptibles de changer. Microsoft s’efforce d’aider les clients respecter ces recommandations en temps opportun à mesure que de nouvelles recommandations sont publiées.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Annexe : modèles de connexions Internet de confiance pour les charges de travail courantes

| Catégorie | Charge de travail | IaaS | PaaS dédié / Injection de réseau virtuel  | Points de terminaison de service  |
|---------|---------|---------|---------|--------|
| Calcul | Machines virtuelles Linux Azure | Oui | | |
| Calcul | Machines virtuelles Windows Azure | Oui | | |
| Calcul | Groupes identiques de machines virtuelles  | Oui | | |
| Calcul | Azure Functions | | Environnement App Service | |
| Web et mobile | Application web interne | | Environnement App Service| |
| Web et mobile | Application mobile interne | | Environnement App Service | |
| Web et mobile | Applications API | | Environnement App Service | |
| Containers | Azure Container Service | | | Oui |
| Containers | Azure Kubernetes Service (AKS) \* | | | Oui |
| Base de données | Azure SQL Database | | Azure SQL Database Managed Instance \* | Azure SQL |
| Base de données | Azure Database pour MySQL | | | Oui |
| Base de données | Azure Database pour PostgreSQL | | | Oui |
| Base de données | Azure SQL Data Warehouse | | | Oui |
| Base de données | Azure Cosmos DB | | | Oui |
| Base de données | Cache Redis Azure | | Oui | |
| Stockage | Stockage d'objets blob Azure | Oui | | |
| Stockage | Azure Files | Oui | | |
| Stockage | Stockage File d’attente Azure | Oui | | |
| Stockage | Stockage de tables Azure | Oui | | |
| Stockage | Stockage sur disque Azure | Oui | | |

\* Préversion publique dans Azure Government, mai 2018.
