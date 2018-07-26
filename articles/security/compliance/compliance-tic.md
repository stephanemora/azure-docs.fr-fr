---
title: Conseils relatifs à Trusted Internet Connection pour Azure
description: Conseils relatifs à Trusted Internet Connection pour les services Azure et SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 9d71efa35713500911c67d1df15612b64c8e97da
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990827"
---
# <a name="trusted-internet-connection-guidance"></a>Conseils relatifs à Trusted Internet Connection

## <a name="background"></a>Arrière-plan

L’objectif de l’initiative TIC (Trusted Internet Connections) consiste à optimiser et à normaliser la sécurité des connexions réseau externes utilisées actuellement par les agences fédérales. La stratégie est décrite dans le [Mémorandum M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) de l’OMB (Office of Management and Budget).

En novembre 2007, l’OMB a établi le programme TIC afin d’améliorer la sécurité du périmètre réseau fédéral et des fonctions de réponse aux incidents. TIC a été conçu pour effectuer une analyse réseau de tout le trafic .gov entrant et sortant, afin d’identifier les données basées sur un modèle et des signatures spécifiques, et de découvrir les anomalies comportementales, telles que les activités de botnet. Il a été demandé aux agences de consolider leurs connexions réseau externe et d’acheminer tout le trafic par le biais de périphériques de détection et de prévention des intrusions (appelés EINSTEIN) hébergés sur un nombre limité de points de terminaison réseau (appelés Trusted Internet Connections).

En bref, l’objectif de TIC est que les agences sachent :
- Qui se trouve sur leur réseau (de manière autorisée ou non).
- Quand un accès à leur réseau se produit et pourquoi.
- Quelles sont les ressources sollicitées.

Aujourd'hui, toutes les connexions externes des agences doivent être acheminées par le biais d’une TIC approuvée par l’OMB. Les agences fédérales doivent obligatoirement participer au programme TIC soit comme fournisseur d’accès TIC (TICAP, TIC Access Provider), soit en déléguant les services à l’un des fournisseurs de services Internet majeurs de niveau 1 appelés fournisseurs MTIPS (Managed Trusted Internet Protocol Service).  TIC inclut des fonctionnalités critiques obligatoires qui sont effectuées aujourd'hui par l’agence et le fournisseur MTIPS. Dans la version actuelle de TIC, les périphériques de détection d’intrusion EINSTEIN version 2 et les périphériques de prévention d’intrusion EINSTEIN version 3 accélérée (3A) sont déployés sur chaque TICAP et MTIPS, et l’agence établit un protocole d’accord avec le DHS (Department of Homeland Security) afin de déployer les fonctionnalités EINSTEIN sur les systèmes fédéraux.

Dans le cadre de sa responsabilité de protection du réseau .gov, le DHS nécessite des flux de données bruts de données Netflow d’agence pour mettre en corrélation les incidents à l’échelle de l’entreprise fédérale et effectuer des analyses à l’aide d’outils spécialisés. Les routeurs DHS permettent de collecter le trafic réseau IP quand il entre ou sort d’une interface. En analysant les données de flux nettes, un administrateur réseau peut déterminer des éléments tels que la source et la destination du trafic, la classe de service, et ainsi de suite. Les données de flux nettes sont considérées comme des « données de non-contenu » (par exemple, en-tête, adresse IP source, adresse IP de destination, et ainsi de suite) et permettent au DHS de connaître certaines informations relatives au contenu, à savoir qui faisait quoi et pendant combien de temps.

L’initiative inclut également des stratégies de sécurité, des instructions et des infrastructures qui supposent l’existence d’une infrastructure locale. À mesure que les agences gouvernementales basculent vers le cloud afin de réaliser des économies de coûts et d’accroître l’efficacité opérationnelle et l’innovation, les exigences de mise en œuvre de TIC ralentissent dans certains cas le trafic réseau et limitent la vitesse et l’agilité avec laquelle les utilisateurs de ces agences peuvent accéder à leurs données dans le cloud.

Cet article explique comment les agences gouvernementales peuvent utiliser Microsoft Azure Government pour aider à atteindre le niveau de conformité requis avec la stratégie TIC à l’échelle des services IaaS et PaaS.

## <a name="summary-of-azure-networking-options"></a>Synthèse des options de mise en réseau Azure

Il existe trois options principales lors de la connexion aux services Azure :

1. Connexion Internet directe : vous vous connectez aux services Azure directement par le biais d’une connexion Internet ouverte. Le support et la connexion sont publics. Le chiffrement au niveau du transport et de l’application est responsable de la confidentialité. La bande passante est limitée par la connectivité à Internet d’un site, et plusieurs fournisseurs actifs peuvent être utilisés pour garantir la résilience.
1. Réseau privé virtuel : vous vous connectez à votre réseau virtuel Azure de manière privée à l’aide d’une passerelle VPN.
Le support est public, puisqu’il exploite la connexion Internet standard d’un site, mais la connexion est chiffrée dans un tunnel afin de garantir la confidentialité. La bande passante est limitée en fonction des périphériques VPN et de la configuration choisie. Les connexions point à site Azure sont généralement limitées à 100 Mbits/s, alors que les connexions site à site sont limitées à 1,25 Gbits/s.
1. Microsoft ExpressRoute : ExpressRoute est une connexion directe aux services Microsoft. La connectivité s’effectuant par le biais d’un canal fibre isolé, la connexion peut être publique ou privée en fonction de la configuration utilisée. La bande passante est généralement limitée à un maximum de 10 Gbits/s.

Il existe plusieurs façons de répondre aux exigences de l’Annexe H de Trusted Internet Connection (Considérations relatives au cloud), qui figurent dans le document « Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0 » publié par le Department of Homeland Security. Tout au long de ce document, le terme TIC 2.0 sera utilisé pour faire référence aux conseils relatifs à TIC DHS.

Pour activer la connexion du Département ou de l’Agence (D/A) vers Azure ou Office 365, sans acheminer le trafic par le biais de la TIC D/A, le D/A doit utiliser un tunnel chiffré et/ou une connexion dédiée au fournisseur de services cloud (CSP, Cloud Service Provider). Les services CSP peuvent garantir que la connectivité aux ressources cloud du D/A n’est pas proposée à l’internet public pour l’accès direct du personnel de l’agence.

Office 365 est conforme à l’Annexe H de la TIC 2.0 à l’aide d’Express Route avec l’[homologation Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) activée ou d’une connexion internet qui chiffre tout le trafic à l’aide de TLS 1.2.  Les utilisateurs finaux du D/A sur le réseau D/A peuvent se connecter par le biais du réseau de leur agence et de l’infrastructure TIC via internet. Tout l’accès internet à distance à Office 365 est bloqué et acheminé par le biais de l’agence. Le D/A peut également se connecter à Office 365 sur une connexion Express Route avec l’homologation Microsoft (qui est un type d’homologation publique) activée.  

Pour Azure uniquement, les options 2 (VPN) et 3 (ExpressRoute) peuvent répondre à ces exigences quand elles sont utilisées conjointement avec des services qui limitent l’accès à Internet.

![Diagramme Microsoft Trusted Internet Connection (TIC)](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Comment les offres Azure Infrastructure as a Service peuvent aider avec la mise en conformité TIC

Respecter la stratégie TIC à l’aide d’IaaS est relativement simple, puisque les clients Azure gèrent leur propre routage de réseau virtuel.

La principale exigence pour aider à garantir la conformité avec l’architecture de référence TIC consiste à vous assurer que votre réseau virtuel (VNet) devient une extension privée du réseau de l’Agence ou du Département. Pour que le VNet devienne une extension _privée_, la stratégie exige que le trafic ne quitte votre réseau que par le biais de la connexion réseau TIC locale. Ce processus est connu sous le nom de « tunneling forcé ». En cas d’utilisation pour la conformité TIC, il consiste à faire en sorte que tout le trafic provenant d’un système dans l’environnement CSP soit acheminé par le biais d’une passerelle locale sur le réseau d’une organisation vers Internet par le biais de la TIC.

La conformité TIC Azure IaaS peut être divisée en deux étapes principales :

1. Configuration
1. Audit

### <a name="azure-iaas-tic-compliance-configuration"></a>Configuration de la conformité TIC Azure IaaS

Pour configurer une architecture conforme à TIC avec Azure, vous devez d’abord empêcher tout accès internet direct à votre réseau virtuel, puis forcer le trafic internet à transiter par le réseau local.

#### <a name="prevent-direct-internet-access"></a>Empêcher l’accès Internet direct

La mise en réseau Azure IaaS s’effectue par le biais de réseaux virtuels constitués de sous-réseaux, auxquels sont associés les contrôleurs d’interface réseau des machines virtuelles.

Le scénario le plus simple pour prendre en charge la conformité TIC consiste à s’assurer qu’une machine virtuelle (ou une collection de machines virtuelles) ne peut se connecter à aucune ressource externe. La déconnexion des réseaux externes peut être assurée à l’aide de groupes de sécurité réseau, qui peuvent être utilisés pour contrôler le trafic vers un ou plusieurs sous-réseaux ou contrôleurs d’interface réseau de votre réseau virtuel. Un groupe de sécurité réseau contient les règles de contrôle d’accès qui autorisent ou refusent le trafic en fonction de la direction du trafic, du protocole, de l’adresse et du port source ainsi que de l’adresse et du port de destination. Les règles d'un groupe de sécurité réseau peuvent être modifiées à tout moment et les modifications sont appliquées à toutes les instances associées.  Pour en savoir plus sur la façon de créer un groupe de sécurité réseau, consultez cet article sur la [création d’un groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>Forcer le trafic Internet à transiter par un réseau local

Azure crée des itinéraires système automatiquement et assigne les itinéraires pour chaque sous-réseau dans un réseau virtuel. Vous ne pouvez pas créer ou supprimer des itinéraires système, mais vous pouvez remplacer certains itinéraires système par des itinéraires personnalisés. Azure crée des itinéraires système par défaut pour chaque sous-réseau et ajoute des itinéraires par défaut facultatifs supplémentaires à des sous-réseaux spécifiques, ou à chaque sous-réseau, quand vous utilisez des fonctionnalités Azure spécifiques. Ce routage garantit que le trafic destiné à l’intérieur du réseau virtuel reste sur le réseau virtuel, que les espaces d’adressage privés désignés par l’IANA, tels que 10.0.0.0/8, sont supprimés (à moins d’être inclus dans l’espace d’adressage du réseau virtuel), ainsi que le routage de « dernier ressort » de 0.0.0.0/0 vers le point de terminaison Internet du réseau virtuel.

![Tunneling forcé TIC](media/tic-diagram-c.png)

Pour s’assurer que tout le trafic traverse la TIC de D/A, tout le trafic quittant le réseau virtuel doit être acheminé par le biais de la connexion locale.  Vous pouvez créer des itinéraires personnalisés en créant des itinéraires définis par l’utilisateur ou en échangeant les itinéraires BGP (Border Gateway Protocol) entre votre passerelle de réseau local et une passerelle de réseau virtuel Azure. Vous trouverez plus d’informations sur les itinéraires définis par l’utilisateur à l’adresse https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. Vous trouverez plus d’informations sur le protocole BGP à l’adresse https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Itinéraires définis par l’utilisateur

Si vous utilisez une passerelle de réseau virtuel basée sur des itinéraires, le tunneling forcé est possible dans Azure en ajoutant un itinéraire défini par l’utilisateur faisant en sorte que le trafic 0.0.0.0/0 soit acheminé vers un « tronçon suivant » de votre passerelle de réseau virtuel. Étant donné qu’Azure accorde une priorité plus élevée aux itinéraires définis par l’utilisateur qu’aux itinéraires définis par le système, cela se traduit par l’envoi de tout le trafic autre que le trafic du réseau virtuel à votre passerelle de réseau virtuel, qui peut ensuite l’acheminer localement. Une fois défini, cet itinéraire défini par l’utilisateur doit être associé à tous les sous-réseaux existants ou nouvellement créés au sein de tous les réseaux virtuels dans votre environnement Azure.

![Itinéraires définis par l’utilisateur et TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Protocole BGP

Si vous utilisez ExpressRoute ou une passerelle de réseau virtuel compatible avec le protocole BGP, ce dernier est le mécanisme recommandé pour la publication des itinéraires. Avec un itinéraire BGP publié 0.0.0.0/0, ExpressRoute et les passerelles de réseau virtuel prenant en charge BGP garantissent que cet itinéraire par défaut est appliqué à tous les sous-réseaux de vos réseaux virtuels.

### <a name="azure-iaas-tic-compliance-auditing"></a>Audit de la conformité TIC Azure IaaS

Azure offre plusieurs moyens d’auditer la conformité TIC.

#### <a name="effective-routes"></a>Itinéraires effectifs

Pour confirmer que votre itinéraire par défaut a été propagé, vous pouvez observer les « routages effectifs » d’une machine virtuelle spécifique, d’un contrôleur d’interface réseau spécifique ou d’une table d’itinéraires définie par l’utilisateur. Vous pouvez pour cela utiliser le portail Azure comme décrit dans https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal, ou PowerShell comme décrit dans https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell. Le panneau Routages effectifs vous permet de voir les itinéraires définis par l’utilisateur pertinents, les itinéraires BGP publiés et les itinéraires système qui s’appliquent à l’entité appropriée, comme indiqué ci-dessous.

![capture d’écran des itinéraires](media/tic-screen-1.png)

**Remarque** : Vous ne pouvez pas afficher les itinéraires effectifs pour un contrôleur d’interface réseau, sauf s’il est associé à une machine virtuelle en cours d’exécution.

#### <a name="network-watcher"></a>Network Watcher

Azure Network Watcher offre plusieurs outils qui peuvent être utilisés pour l’audit de conformité TIC.  Pour en savoir plus sur Network Watcher, consultez https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview.

##### <a name="network-security-groups-flow-logs"></a>Journaux des flux de groupes de sécurité réseau 

Azure Network Watcher permet de capturer des journaux de flux réseau indiquant les métadonnées qui entourent le trafic IP. En plus des autres données, les journaux de flux réseau contiennent les adresses sources et de destination des cibles. Ceci, combiné aux journaux de la passerelle de réseau virtuel, des périphériques de périmètre locaux, et/ou de la TIC, permet de vérifier que tout le trafic est effectivement acheminé localement. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Comment les offres Azure Platform as a Service peuvent aider avec la mise en conformité TIC

Les services PaaS Azure tels que Stockage Azure sont accessibles par le biais d’une URL accessible par Internet. Toute personne disposant d’informations d’identification approuvées peut accéder à la ressource (telle qu’un compte de stockage) à partir de n’importe quel emplacement sans traverser de TIC. Pour cette raison, de nombreux clients d’agences gouvernementales concluent à tort que les services PaaS Azure ne sont pas conformes aux stratégies TIC. En fait, de nombreux services Azure PaaS peuvent être conformes à la stratégie TIC en faisant appel à la même architecture qu’un environnement IaaS conforme à la TIC décrit ci-dessus s’ils peuvent être attachés à un réseau virtuel (VNet). L’intégration des services PaaS Azure à un réseau virtuel Azure permet au service d’être accessible de manière privée à partir de ce réseau virtuel, et permet au routage personnalisé pour 0.0.0.0/0 d’être appliqué par le biais d’itinéraires définis par l’utilisateur ou du protocole BGP, garantissant ainsi que tout le trafic à destination d’Internet est routé localement de façon à traverser la TIC.  Certains services Azure peuvent être intégrés à des réseaux virtuels à l’aide des modèles suivants :

- **Déployer une instance dédiée du service** : un nombre croissant de services PaaS peuvent être déployés en tant qu’instances dédiées avec des points de terminaison attaché à un réseau virtuel. Par exemple, un environnement ASE (App Service Environment) peut être déployé en mode « Isolé », ce qui permet de limiter son point de terminaison réseau à un réseau virtuel. Cet environnement ASE peut ensuite héberger de nombreux services PaaS Azure, tels que des applications web, des API et des fonctions.
- **Points de terminaison de service de réseau virtuel** : un nombre croissant de services PaaS autorisent le déplacement de leur point de terminaison vers une adresse IP de réseau virtuel privée au lieu d’une adresse publique.

Les services qui prennent en charge le déploiement d’instances dédiées sur un réseau virtuel ou des points de terminaison de service à compter de mai 2018 sont répertoriés ci-dessous : *(la disponibilité représente la disponibilité Azure Commercial, Azure Government en attente).

### <a name="service-endpoints"></a>Points de terminaison de service

|de diffusion en continu                   |Statut            |
|--------------------------|------------------|
|Azure KeyVault            | Version préliminaire privée  |
|Cosmos DB                 | Version préliminaire privée  |
|Identité                  | Version préliminaire privée  |
|Azure Data Lake           | Version préliminaire privée  |
|Sql Postgress/Mysql       | Version préliminaire privée  |
|Azure SQL Data Warehouse  | Version préliminaire publique   |
|Azure SQL                 | GA               |
|Stockage                   | GA               |

### <a name="vnet-injection"></a>Injection de réseau virtuel

|de diffusion en continu                            |Statut            |
|-----------------------------------|------------------|
|Instance managée SQL               | Version préliminaire publique   |
|Azure Container Service (AKS)       | Version préliminaire publique   |
|Service Fabric                     | GA               |
|Gestion des API                     | GA               |
|Azure Active Directory             | GA               |
|Azure Batch                        | GA               |
|ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|Groupe de machines virtuelles identiques de calcul  | GA               |
|Service cloud de calcul              | GA               |

### <a name="vnet-integration-details"></a>Détails de l’intégration de réseau virtuel

Le diagramme ci-dessous présente le flux réseau général pour l’accès aux services PaaS à l’aide de l’injection de réseau virtuel et du tunneling de service de réseau virtuel.  Vous trouverez des informations supplémentaires sur les passerelles de service réseau et les balises de réseau virtuel et de service à l’adresse https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![Options de connectivité PaaS pour TIC](media/tic-diagram-e.png)

1. Connexion privée à Azure à l’aide d’ExpressRoute L’homologation privée ExpressRoute avec tunneling forcé est utilisée pour forcer le retour de tout le trafic de réseau virtuel client sur ExpressRoute vers le réseau local. L’homologation Microsoft n’est pas nécessaire.
2. La passerelle VPN Azure utilisée conjointement avec l’homologation Microsoft ExpressRoute peut servir à superposer le chiffrement IPSec de bout en bout entre le réseau virtuel client et le périmètre local. 
3. La connectivité au réseau virtuel client est contrôlée à l’aide de groupes de sécurité réseau permettant aux clients d’autoriser/refuser l’accès en fonction de l’adresse IP, du port et du protocole.
4. Le réseau virtuel client est étendu au service PaaS en créant un point de terminaison de service pour le service du client.
5. Le point de terminaison de service PaaS est sécurisé de façon à refuser tout accès par défaut et à autoriser uniquement l’accès à partir des sous-réseaux spécifiés sur le réseau virtuel client.  Le refus par défaut inclut également les connexions en provenance d’Internet.
6. Tout autre service Azure ayant besoin d’accéder aux ressources du réseau virtuel client doit être :  
  a. Déployé directement sur le réseau virtuel.  
  b. Autorisé de manière sélective en respectant les recommandations applicables au service Azure en question.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>Option 1 : « Injection de réseau virtuel » pour instance dédiée

Avec l’injection de réseau virtuel, les clients peuvent déployer de manière sélective des instances dédiées d’un service Azure donné, tel que HDInsight, sur leur propre réseau virtuel. Les instances de service sont déployées sur un sous-réseau dédié appartenant au réseau virtuel d’un client. L’injection de réseau virtuel permet aux ressources de service d’être accessibles par le biais des adresses routables non-Internet.  Les instances locales peuvent accéder à ces instances de service par le biais de l’espace d’adressage de réseau virtuel directement via ExpressRoute ou un VPN site à site, au lieu d’ouvrir le pare-feu à l’espace d’adressage Internet public. Avec une instance dédiée attachée à un point de terminaison, il est possible d’utiliser les mêmes stratégies que pour la conformité IaaS TIC, avec un routage par défaut garantissant que le trafic à destination d’Internet est redirigé vers une passerelle de réseau virtuel liée pour le réseau local. L’accès entrant et sortant peut être davantage contrôlé par le biais des groupes de sécurité réseau pour le sous-réseau donné.

![Diagramme de vue d’ensemble de l’injection de réseau virtuel](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>Option 2 : Points de terminaison de service de réseau virtuel 

Un nombre croissant de services multilocataires Azure offrent une fonctionnalité de « Point de terminaison de service », une autre méthode d’intégration à des réseaux virtuels Azure. Les points de terminaison de service de réseau virtuel étendent votre espace d’adressage IP de réseau virtuel et l’identité de votre réseau virtuel au service via une connexion directe.  Le trafic du réseau virtuel vers le service Azure reste toujours sur le réseau principal Azure. Une fois qu’un point de terminaison de service est activé pour un service, les connexions au service peuvent être limitées à ce réseau virtuel par le biais de stratégies exposées par le service. Des contrôles d’accès sont appliqués sur la plateforme par le service Azure, et l’accès aux ressources verrouillées est accordé uniquement si la demande provient du réseau virtuel/sous-réseau autorisé et/ou des deux adresses IP utilisées pour identifier votre trafic local si vous utilisez ExpressRoute. Ceci peut servir à empêcher le trafic entrant/sortant de quitter directement le service PaaS.

![Diagramme de vue d’ensemble des points de terminaison de service](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>Utilisation d’outils cloud natifs pour la reconnaissance du réseau

Azure fournit des outils natifs pour vous aider à appréhender les situations et mieux comprendre le flux de trafic de votre réseau. Ils ne doivent pas obligatoirement être conformes à la stratégie TIC, mais ils peuvent améliorer considérablement vos fonctionnalités de sécurité.

### <a name="azure-policy"></a>Azure Policy

Azure Policy (https://azure.microsoft.com/services/azure-policy/) est un service Azure qui permet à votre organisation de mieux auditer et appliquer des initiatives de conformité.  Actuellement disponible en préversion publique dans les clouds Azure Commercial, mais pas encore dans Microsoft Azure Government, les clients soucieux de TIC peuvent commencer à planifier et à tester leurs règles Azure Policy pour l’assurance de la conformité future. Azure Policy cible le niveau abonnement et fournit une interface centralisée pour la gestion des initiatives, des définitions de stratégie, de l’audit et des résultats de mise en œuvre, ainsi que la gestion des exceptions. Outre les nombreuses définitions Azure Policy intégrées, les administrateurs peuvent définir leurs propres définitions personnalisées grâce à la création de modèles json simples. Pour de nombreux clients, Microsoft recommande d’accorder dans la mesure du possible une priorité plus élevée à l’audit qu’à l’application.

Les exemples de stratégies suivants peuvent être utiles pour les scénarios de conformité TIC :

|Stratégie  |Exemple de scénario  |Modèle de départ  |
|---------|---------|---------|
|Appliquer une table de routage définie par l’utilisateur |     Vérifier que l’itinéraire par défaut sur tous les réseaux virtuels pointe vers une passerelle de réseau virtuel approuvée pour le routage local. | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Vérifier si Network Watcher n’est pas activé pour une région.  | Vérifier que Network Watcher est activé pour toutes les régions utilisées.  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|NSG X sur chaque sous-réseau  | Vérifier qu’un groupe de sécurité réseau (ou un ensemble de groupes de sécurité réseau approuvés) avec trafic Internet bloqué est appliqué à tous les sous-réseaux sur chaque réseau virtuel. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|NSG X sur chaque carte réseau | Vérifier qu’un groupe de sécurité réseau avec trafic Internet bloqué est appliqué à tous les contrôleurs d’interface réseau sur toutes les machines virtuelles. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle  | Vérifier que tous les contrôleurs d’interface réseau se trouvent sur un réseau virtuel approuvé. | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Emplacements autorisés | Vérifier que toutes les ressources sont déployées dans des régions avec une configuration de réseaux virtuels et Network Watcher conforme.  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Types de ressources non autorisés, tels que les adresses IP publiques  | Interdire le déploiement des types de ressources qui n’ont pas de plan de conformité. Cette stratégie pourrait par exemple être utilisée pour interdire le déploiement de ressources d’adresses IP publiques. Bien que les règles de groupe de sécurité réseau puissent servir à bloquer efficacement le trafic Internet entrant, empêcher l’utilisation d’adresses IP publiques réduit davantage la surface d’attaque.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [Traffic Analytics](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

Traffic Analytics d’Azure Network Watcher consomme des données de journaux de flux et d’autres journaux pour fournir une vue d’ensemble du trafic réseau. Ces données peuvent être utiles pour l’audit de conformité TIC et pour identifier les zones à problème. Un tableau de bord global permet de savoir rapidement quelles machines virtuelles communiquent avec Internet, ce qui vous procure une liste détaillée pour le routage TIC.

![Capture d’écran de Traffic Analytics](media/tic-traffic-analytics-1.png)

Vous pouvez utiliser une « carte géographique » afin d’identifier rapidement les destinations physiques probables du trafic Internet pour vos machines virtuelles, ce qui vous permet d’identifier et de trier les emplacements suspects ou les changements de modèle.

![Capture d’écran de Traffic Analytics](media/tic-traffic-analytics-2.png)

Vous pouvez utiliser un mappage de topologie de réseau pour passer en revue les réseaux virtuels existants :

![Capture d’écran de Traffic Analytics](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Segment suivant Azure Network Watcher

Les réseaux dans des régions surveillées par Network Watcher peuvent mener des tests « Tronçon suivant », facilitant l’accès basé sur le portail au type dans une source et une destination pour un exemple de flux réseau, pour lequel Network Watcher résoudra la destination « Tronçon suivant » sur le portail. Vous pouvez ensuite appliquer ces informations à des machines virtuelles et des exemples d’adresses Internet pour vous assurer que le « tronçon suivant » est bien la passerelle de réseau virtuel.

![Tronçon suivant Network Watcher](media/tic-network-watcher.png)

## <a name="conclusions"></a>Conclusions

L’accès à Microsoft Azure, Office 365 et Dynamics 365 peut être facilement configuré pour aider à se conformer aux recommandations de l’annexe H de TIC 2.0 applicables au mois de mai 2018.  Microsoft est conscient que ce guide est susceptible de changer, et s’efforcera d’aider les clients à répondre à ces exigences en temps voulu à mesure que de nouvelles recommandations seront publiées.

## <a name="appendix-tic-patterns-for-common-workloads"></a>Annexe : Modèles TIC pour les charges de travail courantes

| Category | Charge de travail | IaaS | PaaS dédié / Injection de réseau virtuel  | Points de terminaison de service  |
|---------|---------|---------|---------|--------|
| Calcul | Machines virtuelles Linux | Oui | | |
| Calcul | Machines virtuelles Windows | Oui | | |
| Calcul | Virtual Machine Scale Sets | Oui | | |
| Calcul | Azure Functions | | via App Service Environment (ASE) | |
| Web et mobilité | Application web interne | | via App Service Environment (ASE) | |
| Web et mobilité | Applications mobiles internes | | via App Service Environment (ASE) | |
| Web et mobilité | Applications API | | via App Service Environment (ASE) | |
| Containers | Azure Container Service (ACS) | | | Oui |
| Containers | Azure Container Service (AKS)* | | | Oui |
| Base de données | SQL Database | | Azure SQL Database Managed Instance* | Azure SQL |
| Base de données | Azure Database pour MySQL | | | Oui |
| Base de données | Azure Database pour PostgreSQL | | | Oui |
| Base de données | SQL Data Warehouse | | | Oui |
| Base de données | Azure Cosmos DB | | | Oui |
| Base de données | Cache Redis | | Oui | |
| Stockage | Objets blob | Oui | | |
| Stockage | Fichiers | Oui | | |
| Stockage | Files d’attente | Oui | | |
| Stockage | Tables | Oui | | |
| Stockage | Disques | Oui | | |

* : Préversion publique dans Azure Government à compter de mai 2018  
** : Préversion publique privée dans Azure Government à compter de mai 2018
