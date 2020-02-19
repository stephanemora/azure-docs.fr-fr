---
title: 'Architecture : Migration vers Azure Virtual WAN'
description: Découvrez comment migrer vers Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062959"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migration vers Azure Virtual WAN

Azure Virtual WAN permet aux entreprises de simplifier leur connectivité internationale afin de tirer parti de l’étendue du réseau mondial de Microsoft. Cet article fournit des détails techniques pour les entreprises souhaitant migrer d’une topologie hub-and-spoke existante managée par le client à une conception tirant parti des hubs Virtual WAN managés par Microsoft.

Pour plus d’informations sur les avantages offerts par Azure Virtual WAN pour les entreprises adoptant un réseau international d’entreprise moderne centré sur le cloud, consultez [Architecture du réseau de transit global Virtual WAN](virtual-wan-global-transit-network-architecture.md).

![hub and spoke](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
**Figure : Azure Virtual WAN**

Le modèle de connectivité hub-and-spoke d’Azure Virtual Datacenter (VDC) a été adopté par des milliers de clients pour tirer parti du comportement de routage transitif par défaut d’Azure Networking afin de créer des réseaux cloud simples et évolutifs. Azure Virtual WAN est basé sur ces concepts et introduit de nouvelles fonctionnalités qui permettent des topologies de connectivité internationale (pas uniquement entre des emplacements locaux et Azure) et qui permettent aussi aux clients de tirer parti de toute l’étendue du réseau Microsoft pour augmenter leurs réseaux mondiaux existants.

Cet article explique comment migrer un environnement hybride existant vers Virtual WAN.

## <a name="scenario"></a>Scénario

Contoso est une organisation financière internationale qui possède des bureaux en Europe et en Asie. Cette organisation envisage de déplacer ses applications existantes depuis un centre de données local vers Azure et a développé une conception de base qui s’appuie sur l’architecture VDC, y compris des réseaux virtuels de type hub managés par les clients régionaux pour la connectivité hybride. Dans le cadre du passage aux technologies informatiques, l’équipe réseau a été chargée de veiller à ce que la connectivité soit optimisée pour les activités à venir de l’organisation.

La figure suivante illustre une vue générale du réseau mondial existant, y compris la connectivité à plusieurs régions Azure.

![Topologie du réseau existant de Contoso](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
**Figure : La topologie du réseau existant chez Contoso**

Dans la topologie de réseau existante, on peut noter les points suivants :

- Une topologie de réseau en étoile (hub-and-spoke) est utilisée dans plusieurs régions, notamment dans les circuits ExpressRoute Premium pour la connectivité à un réseau étendu privé commun.

- Certains de ces sites disposent également de tunnels VPN vers Azure pour atteindre les applications hébergées dans le cloud Microsoft.

## <a name="requirements"></a>Spécifications

L’équipe qui s’occupe du réseau a été chargée de mettre en œuvre un modèle de réseau global capable de prendre en charge la migration de Contoso vers le cloud et d’optimiser les coûts, la mise à l’échelle et les performances. Voici un résumé des exigences à satisfaire :

- Fournir au siège social (HQ) et aux filiales un chemin optimisé vers les applications hébergées dans le cloud
- Supprimer la dépendance aux centres de données locaux existants afin d’arrêter le VPN tout en conservant les chemins de connectivité suivants :
  - **Branche vers réseau virtuel** : Les bureaux connectés à un VPN doivent être en mesure d’accéder aux applications migrées vers le cloud dans la région Azure locale.
  - **Branche vers hub à hub vers réseau virtuel** : Les bureaux connectés à un VPN doivent être en mesure d’accéder aux applications migrées vers le cloud dans la région Azure distante.
  - **Branche vers branche** : Les bureaux régionaux connectés à un VPN doivent être en mesure de communiquer entre eux et avec les sites HQ/DC ExpressRoute connectés.
  - **Branche vers hub à hub vers branche** : Les bureaux distants connectés à un VPN doivent être en mesure de communiquer entre eux et avec tous les sites HQ/DC ExpressRoute connectés.
  - **Branche vers Internet** : Les sites connectés doivent être en mesure de communiquer avec Internet. Ce trafic doit être filtré et journalisé.
  - **Réseau virtuel vers réseau virtuel** : Les réseaux virtuels de type spoke d’une même région doivent être en mesure de communiquer entre eux.
  - **Réseau virtuel vers hub à hub vers réseau virtuel** : Les réseaux virtuels de type spoke de régions différentes doivent être en mesure de communiquer entre eux.
- Offrir aux utilisateurs itinérants de Contoso (ordinateur portable et téléphone) la possibilité d’accéder aux ressources de l’entreprise, même sans connexion au réseau de l’entreprise.

## <a name="architecture"></a>Architecture d’Azure Virtual WAN

La figure suivante illustre une vue d’ensemble de la topologie cible mise à jour à l’aide d’Azure Virtual WAN pour répondre aux exigences décrites dans la section précédente.

![Architecture du WAN virtuel de Contoso](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
**Figure : Architecture d’Azure Virtual WAN**

Résumé :

- Le siège social en Europe reste connecté à ExpressRoute, tandis que les contrôleurs de domaine locaux en Europe ont été entièrement migrés vers Azure, puis désactivés.
- Le contrôleur de domaine et le siège social d’Asie restent connectés à un WAN privé. Azure Virtual WAN est désormais utilisé pour augmenter le réseau de l’opérateur local et fournir une connectivité mondiale.
- Des hubs Azure Virtual WAN ont été déployés dans les régions Azure Europe Ouest et Asie Sud-Est pour fournir un hub de connectivité aux appareils connectés à ExpressRoute et au VPN.
- Les hubs fournissent également un arrêt VPN pour les utilisateurs itinérants sur plusieurs types de clients à l’aide de la connectivité OpenVPN au réseau de maillage global. Ainsi, ils peuvent accéder non seulement aux applications migrées vers Azure, mais également à toutes les ressources locales.
- Connectivité Internet pour les ressources au sein d’un réseau virtuel fournie par Azure Virtual WAN.

Connectivité Internet pour les sites distants également fournie par Azure Virtual WAN. Local Internet Breakout pris en charge via l’intégration des partenaires pour un accès optimisé aux services SaaS tels qu’Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrer vers Virtual WAN

Cette section présente les différentes étapes de la migration vers Azure Virtual WAN.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Étape 1 : VDC hub-and-spoke à une seule région

Passez en revue l’architecture. La figure suivante illustre une topologie à une seule région pour Contoso avant le lancement d’Azure Virtual WAN :

![Topologie à une seule région](./media/migrate-from-hub-spoke-topology/figure1.png)
**Figure 1 : VDC hub-and-spoke à une seule région**

Conformément à l’approche du centre de données virtuel (VDC), le réseau virtuel hub managé par le client contient plusieurs blocs de fonctions :

- Services partagés (toute fonction commune requise par plusieurs spokes). Exemple : Contoso utilise des contrôleurs de domaine Windows Server sur des machines virtuelles IaaS (infrastructure-as-a-service).
- Les services de pare-feu d’adresse IP/de routage sont fournis par une appliance virtuelle de réseau tierce, ce qui permet un routage d’adresse IP de couche 3 de type « spoke à spoke ».
- Services d’entrée/sortie Internet incluant la passerelle Azure Application pour les requêtes HTTPS entrantes et les services proxy tiers exécutés sur des machines virtuelles pour un accès sortant filtré aux ressources Internet.
- Passerelle de réseau virtuel VPN et ExpressRoute pour la connectivité aux réseaux locaux.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Étape 2 : Déploiement des hubs Virtual WAN

Déployez un hub Virtual WAN dans chaque région. Configurez le hub Virtual WAN avec la passerelle VPN et la passerelle ExpressRoute, comme décrit dans les articles suivants :

- [Tutoriel : Créer une connexion de site à site à l’aide d’Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Tutoriel : Créer une association ExpressRoute avec Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure Virtual WAN doit utiliser la référence SKU Standard pour activer certains des chemins de trafic indiqués dans cet article.

![Déploiement des hubs Virtual WAN](./media/migrate-from-hub-spoke-topology/figure2.png)
**Figure 2 : Migration du VDC hub-and-spoke vers Virtual WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Étape 3 : Connexion des sites distants (ExpressRoute et VPN) à Virtual WAN

Connectez le hub Virtual WAN aux circuits ExpressRoute existants et configurez des VPN de site à site via Internet vers toutes les branches distantes.

> [!NOTE]
> Les circuits ExpressRoute doivent être mis à niveau vers le type de référence SKU Premium pour se connecter au hub Virtual WAN.

![Connexion des sites distants à Virtual WAN](./media/migrate-from-hub-spoke-topology/figure3.png)
**Figure 3 : Migration du VDC hub-and-spoke vers Virtual WAN**

À ce stade, l’équipement réseau local commencera à recevoir des itinéraires reflétant l’espace d’adressage IP attribué au réseau virtuel hub managé par Virtual WAN. À ce stade, les branches distantes connectées via un VPN voient deux chemins d’accès à toutes les applications existantes sur les réseaux virtuels spoke. Ces appareils doivent être configurés pour continuer à utiliser le tunnel vers le hub VDC pour garantir un routage symétrique pendant la phase de transition.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Étape 4 : Test de la connectivité hybride par le biais de Virtual WAN

Avant d’utiliser le hub Virtual WAN managé pour la connectivité en production, nous vous recommandons de configurer un réseau virtuel spoke de test et une connexion de réseau virtuel Virtual WAN. Vérifiez que les connexions à cet environnement de test fonctionnent via ExpressRoute et le VPN de site à site avant de passer aux étapes suivantes.

![Test de la connectivité hybride par le biais de Virtual WAN](./media/migrate-from-hub-spoke-topology/figure4.png)
**Figure 4 : Migration du VDC hub-and-spoke vers Virtual WAN**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Étape 5 : Transition de connectivité vers le hub Virtual WAN

![Transition de connectivité vers le hub Virtual WAN](./media/migrate-from-hub-spoke-topology/figure5.png)
**Figure 5 : Migration du VDC hub-and-spoke vers Virtual WAN**

**a**. Supprimez les connexions de Peering existantes des réseaux virtuels spoke vers l’ancien hub VDC. L’accès aux applications sur les réseaux virtuels spoke n’est pas possible tant que les étapes a à c ne sont pas terminées.

**b**. Connectez les réseaux virtuels spoke au hub Virtual WAN par le biais de connexions de réseau virtuel.

**c**. Supprimez tous les itinéraires définis par l’utilisateur précédemment utilisés sur les réseaux virtuels spoke pour les communications de type spoke à spoke. Ce chemin est désormais accessible par le routage dynamique disponible dans le hub Virtual WAN.

**d**. Les passerelles VPN et ExpressRoute existantes dans le hub VDC sont maintenant désactivées pour autoriser le passage à l’étape suivante (e).

**e**. Connectez l’ancien hub VDC (réseau virtuel hub) au hub Virtual WAN par le biais d’une nouvelle connexion de réseau virtuel.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Étape 6 : L’ancien hub devient un spoke de services partagés

Nous avons maintenant repensé notre réseau Azure pour que le hub Virtual WAN soit le point central de notre nouvelle topologie.

![L’ancien hub devient un spoke de services partagés](./media/migrate-from-hub-spoke-topology/figure6.png)
**Figure 6 : Migration du VDC hub-and-spoke vers Virtual WAN**

Étant donné que le hub Virtual WAN est une entité managée et qu’il n’autorise pas le déploiement de ressources personnalisées telles que des machines virtuelles, le bloc de services partagés existe désormais en tant que réseau virtuel spoke et héberge des fonctions telles que l’entrée Internet par le biais de la passerelle Azure Application ou de l’appliance virtualisée de réseau. Le trafic entre l’environnement de services partagés et les machines virtuelles back-end transite désormais par le hub managé par Virtual WAN.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Étape 7 : Optimisation de la connectivité locale pour exploiter pleinement Virtual WAN

À ce niveau, Contoso a migré la plupart de ses applications métier dans le cloud Microsoft. Il ne reste plus que quelques applications héritées dans le contrôleur de domaine local.

![Optimisation de la connectivité locale pour exploiter pleinement Virtual WAN](./media/migrate-from-hub-spoke-topology/figure7.png)
**Figure 7 : Migration du VDC hub-and-spoke vers Virtual WAN**

Pour tirer parti de toutes les fonctionnalités d’Azure Virtual WAN, Contoso a décidé de désactiver ses connexions VPN locales héritées. Toutes les branches qui continuent d’accéder aux réseaux du siège social ou du contrôleur de domaine sont en mesure de transiter par le réseau global de Microsoft à l’aide du routage de transit intégré à Azure Virtual WAN.

> [!NOTE]
> Les clients qui souhaitent tirer parti de l’infrastructure principale de Microsoft pour compléter leurs WAN privés existants peuvent aussi utiliser ExpressRoute Global Reach.

## <a name="end-state-architecture-and-traffic-paths"></a>Architecture finalisée et chemins du trafic

![Architecture finalisée et chemins du trafic](./media/migrate-from-hub-spoke-topology/figure8.png)
**Figure : Virtual WAN à deux régions**

Cette section fournit un résumé de la façon dont cette topologie répond aux exigences d’origine en examinant des exemples de flux de trafic.

### <a name="path-1"></a>Chemin 1

Le chemin 1 affiche le flux du trafic entre une branche connectée VPN site à site en Asie et un réseau virtuel Azure dans la région Asie Sud-Est.

Le trafic est routé comme suit :

- La branche Asie est connectée par le biais de tunnels BGP site à site résilients dans le hub Virtual WAN de la région Asie Sud-Est.

- Le hub Virtual WAN en Asie achemine le trafic localement vers le réseau virtuel connecté.

![Flux 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Chemin 2

Le chemin 2 affiche le flux du trafic entre le siège en Europe connecté via ExpressRoute et le réseau virtuel Azure de la région Asie Sud-Est.

Le trafic est routé comme suit :

- Le siège européen est connecté via le circuit ExpressRoute Premium au hub Virtual WAN de la région Europe Ouest.

- La connectivité globale de hub à hub Virtual WAN permet un transit du trafic vers le réseau virtuel connecté dans une région distante.

![Flux 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Chemin 3

Le chemin 3 affiche le flux du trafic entre le centre de données local en Asie connecté au WAN privé jusqu’à une branche européenne connectée site à site.

Le trafic est routé comme suit :

- Le contrôleur de domaine en Asie est connecté à l’opérateur du WAN privé local.

- Le circuit ExpressRoute se termine localement là où le WAN privé se connecte au hub Virtual WAN de la région Asie Sud-Est.

- La connectivité mondiale hub à hub Virtual WAN permet le transit du trafic.

![Flux 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Chemin 4

Le chemin 4 affiche le flux du trafic entre un réseau virtuel Azure de la région Asie Sud-Est et un réseau virtuel Azure de la région Europe Ouest.

Le trafic est routé comme suit :

- La connectivité globale de hub à hub Virtual WAN permet le transit natif de tous les réseaux virtuels Azure connectés sans configuration utilisateur supplémentaire.

![Flux 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Chemin 5

Le chemin 5 affiche le flux du trafic entre les utilisateurs VPN (P2S) itinérants et un réseau virtuel Azure de la région Europe Ouest.

Le trafic est routé comme suit :

- Les utilisateurs d’ordinateurs portables et d’appareils mobiles utilisent le client OpenVPN pour une connectivité transparente vers la passerelle VPN P2S de la région Europe Ouest.

- Le hub Virtual WAN de la région Europe Ouest achemine le trafic localement vers le réseau virtuel connecté.

![Flux 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Sécurité et contrôle de stratégie via le pare-feu Azure

Contoso dispose maintenant d’une connectivité validée entre toutes les branches et tous les réseaux virtuels, qui est conforme aux exigences abordées précédemment dans cet article. Pour répondre à ses exigences en matière de contrôle de sécurité et d’isolement réseau, l’entreprise doit continuer à séparer et à enregistrer le trafic via le réseau hub. Auparavant, cette fonction était exécutée par une appliance virtuelle réseau (NVA). Contoso souhaite également désactiver ses services proxy existants et utiliser les services Azure natifs pour le filtrage Internet sortant.

![Sécurité et contrôle de stratégie via Pare-feu Azure](./media/migrate-from-hub-spoke-topology/security-policy.png)
**Figure : Pare-feu Azure dans Virtual WAN (hub virtuel sécurisé)**

Les étapes générales suivantes sont requises pour introduire Pare-feu Azure dans les hubs Virtual WAN afin d’obtenir un point unifié de contrôle des stratégies. Pour plus d’informations sur ce processus et le concept de hubs virtuels sécurisés, consultez [Azure Firewall Manager](../firewall-manager/index.yml).

1. Créez une stratégie de pare-feu Azure.
2. Liez la stratégie de pare-feu au hub Azure Virtual WAN. Cette étape permet au hub Virtual WAN existant de fonctionner en tant que hub virtuel sécurisé et déploie les ressources Pare-feu Azure nécessaires.

> [!NOTE]
> Si Pare-feu Azure est déployé dans un hub Virtual WAN Standard (SKU : Standard) : Les stratégies de pare-feu V2V, B2V, V2I et B2I sont uniquement appliquées sur le trafic provenant des réseaux virtuels et des branches connectés au hub spécifique sur lequel le pare-feu Azure est déployé (hub sécurisé). Le trafic provenant de branches et de réseaux virtuels distants qui sont joints à d’autres hubs Virtual WAN sur le même Virtual WAN ne passera pas par le pare-feu, même si ces branches et réseaux virtuels distants sont interconnectés via des liens hub à hub Virtual WAN. La prise en charge du pare-feu entre les hubs est inscrite sur la feuille de route Azure Virtual WAN et Firewall Manager.

Les chemins d’accès suivants montrent les chemins de connectivité activés grâce aux hubs virtuels sécurisés Azure :

### <a name="path-6"></a>Chemin 6

Le chemin 6 affiche le flux du trafic sécurisé entre les réseaux virtuels de la même région.

Le trafic est routé comme suit :

- Les réseaux virtuels connectés au même hub virtuel sécurisé acheminent désormais le trafic via le pare-feu Azure.

- Le pare-feu Azure peut appliquer une stratégie à ces flux.

![Flux 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Chemin 7

Le chemin 7 affiche le flux du trafic entre un réseau virtuel Azure et Internet ou un service de sécurité tiers.

Le trafic est routé comme suit :

- Les réseaux virtuels connectés au hub virtuel sécurisé peuvent envoyer du trafic au public (des destinations sur Internet) en utilisant le hub sécurisé comme point central d’accès à Internet.

- Ce trafic peut être filtré localement à l’aide des règles FQDN de Pare-feu Azure ou envoyé à un service de sécurité tiers à des fins d’inspection.

![Flux 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Chemin 8

Le chemin 8 affiche le flux du trafic d’une branche vers Internet ou un service de sécurité tiers.

Le trafic est routé comme suit :

- Les branches connectées au hub virtuel sécurisé peuvent envoyer du trafic vers des destinations publiques sur Internet en utilisant le hub sécurisé comme point central d’accès à Internet.

- Ce trafic peut être filtré localement à l’aide des règles FQDN de Pare-feu Azure ou envoyé à un service de sécurité tiers à des fins d’inspection.

![Flux 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Azure Virtual WAN](virtual-wan-about.md)
