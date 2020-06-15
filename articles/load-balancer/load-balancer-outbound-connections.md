---
title: Connexions sortantes dans Azure
titleSuffix: Azure Load Balancer
description: Cet article explique comment Azure permet aux machines virtuelles de communiquer avec les services Internet publics.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0e46905ad280cd76d66befb1156e428b23f35664
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235644"
---
# <a name="outbound-connections-in-azure"></a>Connexions sortantes dans Azure

Azure Load Balancer fournit une connectivité sortante pour les déploiements clients via plusieurs mécanismes différents. Cet article décrit les scénarios, les situations où ils s’appliquent, comment ils fonctionnent et comment les gérer. Si vous rencontrez des problèmes de connectivité sortante via un équilibreur Azure Load Balancer, consultez le [guide de résolution des problèmes liés aux connexions sortantes] (../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>Cet article traite des déploiements Resource Manager uniquement. Consultez [Connexions sortantes dans Azure (Classic)](load-balancer-outbound-connections-classic.md) pour découvrir l’ensemble des scénarios de déploiement Classic dans Azure.

Un déploiement dans Azure peut communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public. Quand une instance lance un flux sortant vers une destination située dans l’espace d’adressage IP public, Azure mappe dynamiquement l’adresse IP privée à une adresse IP publique. Une fois ce mappage créé, le trafic de retour pour ce flux sortant peut aussi atteindre l’adresse IP privée d’où provient le flux.

Pour exécuter cette fonction, Azure utilise une traduction d’adresses réseau sources. Quand plusieurs adresses IP privées se dissimulent derrière une adresse IP publique, Azure utilise une [traduction d’adresse de port](#pat) pour masquer les adresses IP privées. Les ports éphémères utilisés pour la traduction d’adresse de port sont [préaffectés](#preallocatedports) en fonction de la taille du pool.

Il existe plusieurs [scénarios sortants](#scenarios). Vous pouvez éventuellement combiner ces scénarios. Examinez-les soigneusement pour comprendre les fonctionnalités, les contraintes et les structures qui s’appliquent à vos modèle de déploiement et scénario d’application. Prenez connaissance des conseils relatifs à la [gestion de ces scénarios](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer et les adresses IP Standard introduisent de nouvelles fonctionnalités et des comportements différents pour la connectivité sortante.  Ils ne sont pas identiques aux références SKU De base.  Si vous souhaitez une connectivité sortante lorsque vous travaillez avec des références SKU Standard, vous devez explicitement la définir avec des adresses IP publiques Standard ou l’équilibreur de charge public Standard.  Cela inclut la création de la connectivité sortante lors de l’utilisation d’un équilibreur de charge Standard interne.  Nous vous recommandons de toujours utiliser des règles de trafic sortant sur un équilibreur de charge public Standard.  Le [scénario 3](#defaultsnat) n’est pas disponible avec la référence SKU Standard.  Cela signifie que lorsqu’un équilibreur de charge interne Standard est utilisé, vous devez prendre des mesures pour créer une connectivité sortante pour les machines virtuelles dans le pool principal si la connectivité sortante est souhaitée.  Dans le contexte de la connectivité sortante, une seule machine virtuelle autonome, toutes les machines virtuelles d’un groupe à haute disponibilité, toutes les instances dans un VMSS se comportent comme un groupe. Cela signifie que, si une seule machine virtuelle d’un groupe à haute disponibilité est associée à une référence SKU Standard, toutes les instances de machine virtuelle au sein de ce groupe à haute disponibilité se comportent maintenant selon les mêmes règles que si elles étaient associées avec la référence SKU Standard, même si une instance individuelle n’est pas directement associée. Ce comportement est également observé dans le cas d’une machine virtuelle autonome avec plusieurs cartes d’interface réseau attachées à un équilibreur de charge. Si une carte réseau est ajoutée comme composant autonome, elle aura le même comportement. Lisez attentivement la totalité de ce document pour comprendre les concepts généraux, passer en revue [Standard Load Balancer](load-balancer-standard-overview.md) pour connaître les différentes entre les références SKU et les [règles de trafic sortant](load-balancer-outbound-rules-overview.md).  L’utilisation de règles de trafic sortant vous permet un contrôle précis de tous les aspects de la connectivité sortante.

## <a name="scenario-overview"></a><a name="scenarios"></a>Vue d’ensemble des scénarios

Azure Load Balancer et les ressources associées sont définis explicitement quand vous utilisez [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Actuellement, Azure offre trois méthodes différentes pour obtenir une connexion sortante pour les ressources Azure Resource Manager. 

| Références (SKU) | Scénario | Méthode | Protocoles IP | Description |
| --- | --- | --- | --- | --- |
| Standard, De base | [1. Machine virtuelle avec une adresse IP publique de niveau d’instance (avec ou sans Load Balancer)](#ilpip) | Traduction d’adresses réseau sources, masquage de port non utilisé | TCP, UDP, ICMP, ESP | Azure utilise l’adresse IP publique affectée à la configuration IP de la carte d’interface réseau de l’instance. L’instance a tous les ports éphémères disponibles. Lors de l’utilisation de Standard Load Balancer, les [règles de trafic sortant](load-balancer-outbound-rules-overview.md) ne sont pas prises en charge si une IP publique est attribuée à la machine virtuelle. |
| Standard, De base | [2. Load Balancer public associé à une machine virtuelle (aucune adresse IP publique sur l’instance)](#lb) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) en utilisant des frontends Load Balancer | TCP, UDP |Azure partage l’adresse IP publique des frontends Load Balancer publics avec plusieurs adresses IP privées. Azure utilise les ports éphémères des frontends pour la traduction d’adresse de port. Quand vous utilisez Standard Load Balancer, vous devez utiliser des [règles de trafic sortant](load-balancer-outbound-rules-overview.md) pour définir explicitement la connectivité sortante. |
| Aucune ou De base | [3. Machine virtuelle autonome (sans Load Balancer, sans adresse IP publique)](#defaultsnat) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) | TCP, UDP | Azure désigne automatiquement une adresse IP publique pour la traduction d’adresses réseau sources, partage cette adresse IP publique avec plusieurs adresses IP privées du groupe à haute disponibilité, puis utilise les ports éphémères de cette adresse IP publique. Ce scénario est une solution de secours pour les scénarios précédents. Nous vous le déconseillons si vous avez besoin de visibilité et de contrôle. |

Si vous voulez empêcher une machine virtuelle de communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public, vous pouvez utiliser des groupes de sécurité réseau (NSG) pour bloquer l’accès comme il se doit. La section [Empêchement des connexions sortantes](#preventoutbound) traite de façon plus détaillée des groupes de sécurité réseau. Les conseils sur la conception, l’implémentation et la gestion d’un réseau virtuel sans accès sortant n’entrent pas dans le cadre de cet article.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scénario 1 : Machine virtuelle avec adresse IP publique

Dans ce scénario, une adresse IP publique est affectée à la machine virtuelle. En ce qui concerne les connexions sortantes, il importe peu que la machine virtuelle soit à charge équilibrée ou non. Ce scénario prend le pas sur les autres. Quand une adresse IP publique est utilisée, la machine virtuelle utilise l’adresse IP publique pour tous les flux sortants.  

Une adresse IP publique affectée à une machine virtuelle constitue une relation 1:1 (non pas une relation 1-à-plusieurs) ; elle est implémentée comme un NAT 1:1 sans état.  Le masquage de port (traduction d’adresse de port) n’est pas utilisé et la machine virtuelle peut utiliser tous les ports éphémères.

Si votre application lance de nombreux flux sortants et que vous subissez un épuisement des ports de traduction d’adresses réseau sources, envisagez d’affecter une [adresse IP publique pour atténuer les contraintes de traduction d’adresses réseau sources](../load-balancer/troubleshoot-outbound-connection.md#assignilpip). Lisez [Gestion de l’épuisement de la traduction d’adresses réseau sources](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) dans son intégralité.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scénario 2 : Machine virtuelle à charge équilibrée sans adresse IP publique

Dans ce scénario, la machine virtuelle fait partie d’un pool principal d’équilibreurs de charge public. La machine virtuelle n’a pas d’adresse IP publique affectée. La ressource d’équilibreur de charge doit être configurée avec une règle d’équilibreur de charge pour créer un lien entre le serveur frontal d’adresse IP publique et le pool backend.

Si vous n’effectuez pas cette configuration de règle, le comportement est celui décrit dans le scénario pour [Machine virtuelle autonome sans adresse IP publique](#defaultsnat). La règle n’a pas besoin d’un écouteur opérationnel dans le pool backend ou la sonde d’intégrité pour réussir.

Lorsque la machine virtuelle à charge équilibrée crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique du frontend d’équilibrage de charge public. Azure utilise la traduction d’adresses réseau sources pour exécuter cette fonction. Azure utilise aussi la [traduction d’adresse de port](#pat) pour masquer plusieurs adresses IP privées derrière une adresse IP publique. 

Les ports éphémères du frontend d’adresse IP publique de l’équilibreur de charge servent à faire la distinction entre les différents flux en provenance de la machine virtuelle. La traduction d’adresses réseau utilise dynamiquement des [ports éphémères préaffectés](#preallocatedports) lors de la création de flux sortants. Dans ce contexte, les ports éphémères utilisés pour la traduction d’adresses réseau sources sont appelés ports SNAT.

Les ports SNAT sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat). Il s’agit d’une ressource limitée sujette à épuisement. Il est important de comprendre comment ils sont [consommés](#pat). Pour savoir comment concevoir en fonction de cette consommation et d’en atténuer éventuellement les effets, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

Si [plusieurs adresses IP (publiques) sont associées à un équilibreur de charge de base](load-balancer-multivip-overview.md), toutes ces adresses sont candidates pour les flux sortants, mais une seule d’entre elles est sélectionnée au hasard.  

Pour surveiller l’intégrité des connexions sortantes avec l’équilibreur de charge de base, vous pouvez utiliser les [journaux d'activité Azure Monitor pour Load Balancer](load-balancer-monitor-log.md) et les [journaux des événements d’alerte](load-balancer-monitor-log.md#alert-event-log) pour surveiller les messages signalant l’épuisement des ports SNAT.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scénario 3 : Machine virtuelle autonome sans adresse IP publique

Dans ce scénario, la machine virtuelle ne fait pas partie d’un pool Load Balancer public (et non plus d’un pool Standard Load Balancer interne) et aucune adresse IP publique (ILPIP) n’y est assignée. Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique. L’adresse IP publique utilisée pour ce flux sortant n’est pas configurable et n’entre pas en compte dans la limite de ressource IP publique de l’abonnement. Cette adresse IP publique ne vous appartient pas, et ne peut pas être réservée. Si vous redéployez la machine virtuelle, le groupe à haute disponibilité ou le groupe de machines virtuelles identiques, cette adresse IP publique est libérée et une nouvelle adresse IP publique est demandée. N’utilisez pas ce scénario pour mettre en liste verte des adresses IP. Utilisez plutôt l’un des deux autres scénarios dans lequel vous déclarez explicitement le scénario sortant et l’adresse IP publique à utiliser pour la connectivité sortante.

>[!IMPORTANT] 
>Ce scénario s’applique également lorsque __seul__ un équilibreur de charge interne de base est joint. Le scénario 3 est __non disponible__ lorsqu’un équilibreur de charge interne standard est joint à une machine virtuelle.  Vous devez créer explicitement [scénario 1](#ilpip) ou [scénario 2](#lb) en plus pour utiliser un équilibreur de charge interne standard.

Pour exécuter cette fonction, Azure utilise la traduction d’adresses réseau sources avec masquage de port ([traduction d’adresse de port](#pat)). Ce scénario est similaire au [scénario 2](#lb), sauf qu’il n’existe aucun contrôle de l’adresse IP utilisée. Il s’agit d’un scénario de secours quand les scénarios 1 et 2 n’existent pas. Nous vous déconseillons ce scénario si vous voulez exercer un contrôle sur l’adresse sortante. Si les connexions sortantes sont un élément essentiel de votre application, choisissez plutôt un autre scénario.

Les ports SNAT sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat).  Le nombre de machines virtuelles qui partagent un groupe à haute disponibilité détermine quel niveau de pré-allocation s’applique.  Une machine virtuelle autonome sans un groupe à haute disponibilité est effectivement un pool de 1 dans le cadre de la détermination de la pré-allocation (ports SNAT 1024). Les ports SNAT sont une ressource limitée qui peut être épuisée. Il est important de comprendre comment ils sont [consommés](#pat). Pour savoir comment concevoir en fonction de cette consommation et d’en atténuer éventuellement les effets, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Plusieurs scénarios combinés

Vous pouvez combiner les scénarios décrits dans les sections précédentes pour obtenir un résultat particulier. En présence de plusieurs scénarios, un ordre de priorité s’applique : le [scénario 1](#ilpip) est prioritaire sur les [scénarios 2](#lb) et [3](#defaultsnat). Le [scénario 2](#lb) remplace le [scénario 3](#defaultsnat).

Un exemple est un déploiement Azure Resource Manager où l’application dépend fortement des connexions sortantes à un nombre limité de destinations, mais reçoit également des flux entrants sur un serveur frontal d’équilibreur de charge. Dans ce cas, vous pouvez combiner les scénarios 1 et 2 pour alléger la charge. Pour d’autres modèles, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a> Plusieurs serveurs frontaux pour les flux sortants

#### <a name="standard-load-balancer"></a>Standard Load Balancer

Standard Load Balancer utilise tous les candidats pour les flux sortants en même temps lorsque [plusieurs serveurs frontaux IP (publics)](load-balancer-multivip-overview.md) sont présents. Chaque serveur frontal multiplie le nombre de ports SNAT pré-alloués disponibles si une règle d’équilibrage de charge est activée pour les connexions sortantes.

Vous pouvez choisir de supprimer l’utilisation d’une adresse IP de serveur frontal pour les connexions sortantes avec une nouvelle option de règle d’équilibrage de charge :

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalement, l’option `disableOutboundSnat` par défaut est _false_ et signifie que cette règle programme le SNAT sortant pour les machines virtuelles associées dans le pool principal de la règle d’équilibrage de charge. Cette option `disableOutboundSnat` peut être définie sur _true_ pour empêcher l’équilibreur de charge d’utiliser l’adresse IP du serveur frontal associée pour des connexions sortantes pour la machine virtuelle dans le pool principal de cette règle d’équilibrage de charge.  Vous pouvez toutefois toujours désigner une adresse IP spécifique pour les flux sortants comme décrit dans [Scénarios combinés multiples](#combinations).

#### <a name="load-balancer-basic"></a>Équilibreur de charge de base

L’équilibreur de charge de base choisit un seul frontend à utiliser pour les flux sortants quand [plusieurs frontends d’adresses IP (publiques)](load-balancer-multivip-overview.md) sont candidats pour les flux sortants. Cette sélection n’est pas configurable, et vous devez considérer l’algorithme de sélection comme étant aléatoire. Vous pouvez désigner une adresse IP spécifique pour les flux sortants comme décrit dans [Scénarios combinés multiples](#combinations).

### <a name="availability-zones"></a><a name="az"></a> Zones de disponibilité

Lorsque vous utilisez [Équilibreur de charge standard avec zones de disponibilité](load-balancer-standard-availability-zones.md), les serveurs frontaux redondants interzone peuvent fournir des connexions SNAT sortantes redondantes interzone et la programmation du SNAT survit à l’échec de la zone.  Lorsque vous utilisez des serveurs frontaux de zones, les connexions sortantes SNAT connaissent le même sort que la zone à laquelle ils appartiennent.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Masquage de la traduction d’adresses réseau sources du port (traduction d’adresse de port)

Lorsqu’une ressource Load Balancer publique est associée à des instances de machine virtuelle qui n’ont pas d’adresse IP publique dédiée, la source de chaque connexion sortante est réécrite. La source est réécrite depuis l’espace d’adressage IP privé du réseau virtuel dans l’adresse IP publique frontend de l’équilibreur de charge. Dans l’espace d’adressage IP public, le 5-tuple du flux (adresse IP source, port source, protocole de transport IP, adresse IP de destination, port de destination) doit être unique. L’usurpation de ports SNAT est utilisable avec les protocoles TCP et UDP IP.

Des ports éphémères (ports SNAT) sont utilisés à cette fin après réécriture de l’adresse IP privée source, car plusieurs flux proviennent d’une même adresse IP publique. L’algorithme SNAT de masquage de port alloue des ports SNAT différemment pour UDP et TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Ports SNAT TCP

Un seul port SNAT est utilisé par flux vers un port et adresse IP de destination uniques. En cas de flux TCP multiples vers les mêmes adresse IP, port et protocole de destination, chaque flux TCP utilise un seul port de traduction d’adresses réseau sources. Cela garantit que les flux sont uniques quand ils proviennent de la même adresse IP publique et sont dirigés vers les mêmes adresse IP, port et protocole de destination. 

Plusieurs flux, chacun dirigé vers une adresse IP, un port et un protocole de destination distincts, partagent un même port SNAT. L’adresse IP, le port et le protocole de destination rendent les flux uniques sans qu’il soit nécessaire de recourir à des ports sources supplémentaires pour distinguer les flux dans l’espace d’adressage IP public.

#### <a name="udp-snat-ports"></a><a name="udp"></a>Ports SNAT UDP

Les ports SNAT UDP sont gérés par un algorithme différent de celui qui gère les ports SNAT TCP.  Load Balancer utilise un algorithme appelé « NAT à cône restrictif sur les ports » pour UDP.  Un seul port SNAT est utilisé pour chaque flux, quels que soient le port et l’adresse IP de destination.

#### <a name="snat-port-reuse"></a>Réutilisation des ports SNAT

Une fois qu’un port a été libéré, le port peut être réutilisé en fonction des besoins.  Vous pouvez considérer les ports SNAT sous la forme d’une série allant du moins jusqu’au plus disponible pour un scénario donné, et le premier port SNAT disponible est utilisé pour les nouvelles connexions. 
 
#### <a name="exhaustion"></a>Épuisement

En cas d’épuisement des ressources de port SNAT, les flux sortants échouent tant que les flux existants ne libèrent pas des ports SNAT. L’équilibreur de charge récupère les ports de traduction d’adresses réseau sources lorsque le flux se ferme, et utilise un [délai d’inactivité de 4 minutes](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) pour récupérer les ports de traduction d’adresses réseau sources des flux inactifs.

Les Ports SNAT UDP s’épuisent généralement beaucoup plus rapidement que les ports SNAT TCP, car l’algorithme utilisé n’est pas le même. Vous devez concevoir et adapter les tests en tenant compte de cette différence.

Pour découvrir des modèles permettant d’atténuer les conditions qui aboutissent généralement à un épuisement des ports SNAT, consultez la section [Gestion de l’épuisement de la traduction d’adresses réseau sources](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Préaffectation de port éphémère pour la traduction d’adresses réseau sources (traduction d’adresse de port) pour le masquage de port

Azure utilise un algorithme pour déterminer le nombre de ports de traduction d’adresses réseau sources préaffectés disponibles en fonction de la taille du pool principal lorsque vous utilisez une traduction d’adresses réseau sources ([traduction d’adresse de port](#pat)) pour le masquage de port. Les ports SNAT sont des ports éphémères disponibles pour une adresse IP publique source donnée. Pour chaque adresse IP publique associée à un équilibreur de charge, 64 000 ports sont disponibles en tant que ports SNAT pour chacun des protocoles de transport IP.

Un nombre identique de ports SNAT est préalloué pour UDP et TCP respectivement ; il est consommé indépendamment en suivant un protocole de transport IP.  Toutefois, l’utilisation de ports SNAT est différente selon que le flux est TCP ou UDP.

>[!IMPORTANT]
>La programmation SKU SNAT standard s’effectue par protocole de transport IP et est dérivée de la règle d’équilibrage de charge.  Si seulement une règle d’équilibrage de charge TCP existe, SNAT est disponible exclusivement pour TCP. Si vous posséder uniquement une règle d’équilibrage de charge TCP et avez besoin d’une instance SNAT sortante pour UDP, créez une règle d’équilibrage de charge UDP du même pool frontal vers le même pool principal.  Cette opération déclenchera la programmation SNAT pour UDP.  Aucune règle de travail ou sonde d’intégrité ne sont requises.  La programmation SKU SNAT de base programme toujours SNAT pour les 2 protocoles de transport IP, indépendamment du protocole de transport spécifié dans la règle d’équilibrage de charge.

Azure préaffecte des ports de traduction d’adresses réseau sources à la configuration IP de la carte d’interface réseau de chaque machine virtuelle. Quand une configuration IP est ajoutée au pool, les ports de traduction d’adresses réseau sources sont préaffectés pour cette configuration IP en fonction de la taille du pool principal. Au moment où les flux sortants sont créés, la [traduction d’adresse de port](#pat) consomme (jusqu’à la limite préaffectée) et libère dynamiquement ces ports quand le flux se ferme ou en cas de [délais d’inactivité](../load-balancer/troubleshoot-outbound-connection.md#idletimeout).

Le tableau suivant présente les préaffectations de ports SNAT pour les différents niveaux de tailles de pool backend :

| Taille du pool (instances de machine virtuelle) | Ports de traduction d’adresses réseau sources préaffectés par configuration IP|
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 |

>[!NOTE]
> Lorsque vous utilisez l’équilibreur de charge standard avec [plusieurs serveurs frontaux](load-balancer-multivip-overview.md), chaque adresse IP de serveur frontal multiplie le nombre de ports SNAT disponibles dans la table précédente. Par exemple, un pool de back-ends de 50 machines virtuelles avec deux règles d’équilibrage de charge, chacun avec une adresse IP front-end séparée, utilise 2048 ports SNAT (2 x 1024) par règle. Affichez les détails pour [plusieurs serveurs frontaux](#multife).

Ne perdez pas de vue que le nombre de ports SNAT disponibles ne se traduit pas directement en nombre de flux. Un port de traduction d’adresses réseau sources peut être réutilisé pour plusieurs destinations uniques. Les ports ne sont consommés que si cela permet de rendre les flux uniques. Pour obtenir des conseils concernant la conception et l’atténuation, consultez la section qui explique [comment gérer cette ressource épuisable](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust), ainsi que la section qui décrit la [traduction d’adresse de port](#pat).

La modification de la taille de votre pool backend peut affecter certains de vos flux établis. Si la taille du pool backend augmente et passe au niveau suivant, la moitié des ports SNAT préaffectés sont récupérés pendant la transition vers le niveau de pool backend supérieur suivant. Les flux associés à un port SNAT récupéré expirent et doivent être rétablis. En cas de tentative de lancement d’un nouveau flux, celui-ci réussit immédiatement du moment que des ports préaffectés sont disponibles.

Si la taille du pool backend diminue et passe à un niveau inférieur, le nombre de ports SNAT disponibles augmente. Dans ce cas, les ports de traduction d’adresses réseau sources affectés existants et leurs flux respectifs ne sont pas concernés.

Les allocations de ports SNAT sont spécifiques au protocole de transport IP (TCP et UDP sont gérés séparément) et sont mis à disposition selon les conditions suivantes :

### <a name="tcp-snat-port-release"></a>Mis à disposition du port TCP SNAT

- Si le client/serveur envoie un paquet FINACK, le port SNAT est mis à disposition après un délai de 240 secondes.
- Si une instance RST est visible, le port SNAT est mis à disposition après un délai de 15 secondes.
- Si le délai d’inactivité est atteint, le port est mis à disposition.

### <a name="udp-snat-port-release"></a>Mis à disposition du port UDP SNAT

- Si le délai d’inactivité est atteint, le port est mis à disposition.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Découverte de l’adresse IP publique utilisée par une machine virtuelle
Il existe de nombreuses manières de déterminer l’adresse IP source publique d’une connexion sortante. OpenDNS fournit un service qui peut vous indiquer l’adresse IP publique votre machine virtuelle. 

La commande nslookup vous permet d’envoyer une requête DNS sur le nom myip.opendns.com au programme de résolution OpenDNS. Le service retourne l’adresse IP source qui a été utilisée pour envoyer la requête. Quand vous exécutez la requête suivante à partir de votre machine virtuelle, la réponse est l’adresse IP publique utilisée pour cette machine virtuelle :

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prévention de toute connexion sortante
Dans certains cas, il est préférable de ne pas autoriser une machine virtuelle à créer un flux sortant. Il peut aussi exister une condition qui détermine les destinations qui peuvent être atteintes avec des flux sortants ou celles qui peuvent lancer des flux entrants. Dans ce cas, vous pouvez utiliser des [groupes de sécurité réseau](../virtual-network/security-overview.md) pour déterminer les destinations que la machine virtuelle peut atteindre. Vous pouvez aussi utiliser des groupes de sécurité réseau pour déterminer la destination publique qui peut lancer des flux entrants.

Quand vous appliquez un groupe de sécurité réseau à une machine virtuelle à charge équilibrée, vérifiez les [balises de service](../virtual-network/security-overview.md#service-tags) et les [règles de sécurité par défaut](../virtual-network/security-overview.md#default-security-rules). Vous devez vous assurer que la machine virtuelle peut recevoir des demandes d’analyse d’intégrité d’Azure Load Balancer. 

Si un groupe de sécurité réseau bloque les demandes d’analyse d’intégrité depuis la balise par défaut AZURE_LOADBALANCER, votre analyse de l’intégrité de la machine virtuelle échoue et la machine virtuelle est marquée comme défaillante. L’équilibrage de charge arrête l’envoi de nouveaux flux vers cette machine virtuelle.

## <a name="connections-to-azure-storage-in-the-same-region"></a>Connexions au Stockage Azure dans la même région

Il n’est pas nécessaire de disposer d’une connectivité sortante via les scénarios ci-dessus pour vous connecter au Stockage Azure dans la même région que la machine virtuelle. Si vous n’en souhaitez pas, utilisez les groupes de sécurité réseau (NSG), comme expliqué ci-dessus. Pour la connectivité vers le Stockage Azure dans d’autres régions, une connectivité sortante est requise. Lorsque vous vous connectez au Stockage Azure à partir d’une machine virtuelle dans la même région, l’adresse IP source dans les journaux de diagnostic de stockage est une adresse de fournisseur interne, et non l’adresse IP publique de votre machine virtuelle. Si vous souhaitez restreindre l’accès à votre compte de stockage aux machines virtuelles dans un ou plusieurs sous-réseaux du réseau virtuel dans la même région, utilisez des [points de terminaison de service du réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) et non votre adresse IP publique lors de la configuration de votre pare-feu de compte de stockage. Une fois les points de terminaison de service configurés, l’adresse IP privée de votre réseau virtuel apparaît dans vos journaux de diagnostic de stockage, mais pas l’adresse interne du fournisseur.

## <a name="azure-load-balancer-outbound-rules"></a><a name="outboundrules"></a>Règles de trafic sortant dans Azure Load Balancer

Azure Load Balancer fournit la connectivité sortante à partir d’un réseau virtuel en plus de la connectivité entrante.  Les règles de trafic sortant facilitent la configuration de la traduction des adresses réseau sortantes publiques dans [Standard Load Balancer](load-balancer-standard-overview.md).  Vous pouvez contrôler entièrement la connectivité sortante de manière déclarative pour la mettre à l’échelle et l’adapter en fonction de vos besoins spécifiques.

![Règles de trafic sortant dans Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Avec des règles de trafic sortant, vous pouvez utiliser Load Balancer pour : 
- définir la NAT de trafic sortant à partir de zéro ;
- mettre à l’échelle et adapter le comportement de la NAT de trafic sortant existante. 

Les règles de trafic sortant vous permettent de déterminer :
- quelles machines virtuelles doivent être traduites vers quelles adresses IP publiques, 
- le mode d’allocation des [ports SNAT sortants](load-balancer-outbound-connections.md#snat),
- les protocoles qui fournissent la traduction sortante,
- la durée à utiliser comme délai d’inactivité des connexions sortantes (de 4 à 120 minutes),
- la réinitialisation TCP au terme du délai d’inactivité.

Les règles de trafic sortant étendent le [scénario 2](load-balancer-outbound-connections.md#lb) décrit dans l’article sur les [connexions sortantes](load-balancer-outbound-connections.md), mais la priorité du scénario reste la même.

### <a name="outbound-rule"></a>Règle de trafic sortant

Comme toutes les règles dans Load Balancer, les règles de trafic sortant suivent la syntaxe habituelle des règles d’équilibrage de charge et des règles de NAT de trafic entrant :

**frontend** + **parameters** + **backend pool**

Une règle de trafic sortant configure la NAT de trafic sortant pour que _toutes les machines virtuelles identifiées par le pool backend_ soient traduites sur le _frontend_.  Les _paramètres_ permettent de contrôler avec plus de précision l’algorithme de la NAT de trafic sortant.

Dans l’API version « 2018-07-01 », une définition de règle de trafic sortant a la structure suivante :

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>La configuration de la NAT de trafic sortant effective est composée à partir de l’ensemble des règles de trafic sortant et règles d’équilibrage de charge. Les règles de trafic sortant sont incrémentielles pour les règles d’équilibrage de charge. Pour savoir comment gérer la NAT de trafic sortant effective quand plusieurs règles s’appliquent à une machine virtuelle, consultez [Désactiver la NAT de trafic sortant pour une règle d’équilibrage de charge](#disablesnat). Vous devez [désactiver la SNAT de trafic sortant](#disablesnat) quand vous définissez une règle de trafic sortant qui utilise la même adresse IP publique qu’une règle d’équilibrage de charge.

#### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Mettre à l’échelle la NAT de trafic sortant avec plusieurs adresses IP

Une règle de trafic sortant peut être utilisée avec une seule adresse IP publique, mais les règles de trafic sortant facilitent la configuration de la mise à l’échelle de la NAT de trafic sortant. Vous pouvez utiliser plusieurs adresses IP pour vos scénarios à grande échelle et utiliser des règles de trafic sortant afin de limiter les risques [d’épuisement de ports SNAT](troubleshoot-outbound-connection.md#snatexhaust).  

Chaque adresse IP supplémentaire fournie par un frontend met à disposition 64 000 ports éphémères que Load Balancer peut utiliser en tant que ports SNAT. Alors que les règles d’équilibrage de charge ou les règles NAT de trafic entrant ont un seul frontend, la règle de trafic sortant étend la notion de frontend et permet l’utilisation de plusieurs frontends par règle.  Avec plusieurs frontends par règle, le nombre de ports SNAT disponibles est multiplié avec chaque adresse IP publique, ce qui rend possible la prise en charge de scénarios à grande échelle.

De plus, vous pouvez utiliser un [préfixe d’adresse IP publique](https://aka.ms/lbpublicipprefix) directement avec une règle de trafic sortant.  L’utilisation d’un préfixe d’adresse IP publique simplifie la mise à l’échelle et la mise sur liste verte des flux provenant de votre déploiement Azure. Vous pouvez configurer une configuration IP frontend au sein de la ressource Load Balancer pour référencer directement un préfixe d’adresse IP publique.  De cette manière, Load Balancer conserve un contrôle exclusif sur le préfixe d’adresse IP publique, et la règle de trafic sortant utilise automatiquement toutes les adresses IP publiques contenues dans le préfixe d’adresse IP publique pour les connexions sortantes.  Chacune des adresses IP dans la plage du préfixe d’adresse IP publique fournit 64 000 ports éphémères que Load Balancer peut utiliser en tant que ports SNAT.   

Avec cette option, vous ne pouvez pas créer des ressources d’adresse IP publique de manière individuelle à partir du préfixe d’adresse IP publique, car la règle de trafic sortant doit avoir un contrôle total sur le préfixe d’adresse IP publique.  Si vous souhaitez un contrôle plus précis, créez une ressource d’adresse IP publique individuelle à partir du préfixe d’adresse IP publique et assignez plusieurs adresses IP publiques individuellement au frontend d’une règle de trafic sortant.

#### <a name="tune-snat-port-allocation"></a><a name="snatports"></a> Paramétrer l’allocation de ports SNAT

Vous pouvez utiliser des règles de trafic sortant pour paramétrer [l’allocation de ports SNAT automatique en fonction de la taille du pool backend](load-balancer-outbound-connections.md#preallocatedports) et pour changer le nombre de ports SNAT alloués automatiquement.

Utilisez le paramètre suivant pour allouer 10 000 ports SNAT par machine virtuelle (configuration IP de carte réseau).
 

          "allocatedOutboundPorts": 10000

Chaque adresse IP publique de tous les frontends d’une règle de trafic sortant fournit jusqu’à 64 000 ports éphémères en tant que ports SNAT.  Load Balancer alloue des ports SNAT par multiples de huit. Si vous indiquez une valeur non divisible par huit, l’opération de configuration est rejetée.  Si vous essayez d’allouer plus de ports SNAT qu’il n’y a de ports disponibles par rapport au nombre d’adresses IP publiques, l’opération de configuration est rejetée.  Par exemple, si vous allouez 10 000 ports par machine virtuelle et que sept machines virtuelles d’un pool principal partagent la même adresse IP publique, la configuration est rejetée (7 x 10 000 ports SNAT > 64 000 ports SNAT).  Dans ce scénario, vous devrez donc ajouter des adresses IP publiques supplémentaires sur le frontend de la règle de trafic sortant.

Vous pouvez revenir à une [allocation de ports SNAT automatique en fonction de la taille du pool backend](load-balancer-outbound-connections.md#preallocatedports) en spécifiant 0 comme nombre de ports. Dans ce cas, les 50 premières instances de machine virtuelle obtiennent 1 024 ports, les instances de machine virtuelle 51 à 100 obtiennent 512 ports, et ainsi de suite selon la table.

#### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a> Contrôler le délai d’inactivité des flux sortants

Les règles de trafic sortant ont un paramètre de configuration qui permet de contrôler le délai d’inactivité des flux sortants et de l’ajuster en fonction des besoins de votre application.  Le délai d’inactivité des flux sortants est de 4 minutes par défaut.  Le paramètre peut être défini sur une valeur comprise entre 4 et 120, selon le nombre de minutes du délai d’inactivité nécessaire pour les flux auxquels cette règle particulière s’applique.

Pour définir le délai d’inactivité des flux sortants à une (1) heure, utilisez le paramètre suivant :

          "idleTimeoutInMinutes": 60

#### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a> <a name="tcpreset"></a> Activer la réinitialisation TCP au terme du délai d’inactivité

Le comportement par défaut de Load Balancer consiste à supprimer silencieusement des flux quand le délai d’inactivité d’un flux sortant est atteint.  Avec le paramètre enableTCPReset, vous pouvez définir un comportement plus prévisible de l’application et contrôler s’il faut déclencher une réinitialisation TCP (TCP RST) bidirectionnelle quand le délai d’inactivité d’un flux sortant est atteint. 

Pour activer la réinitialisation TCP sur une règle de trafic sortant, utilisez le paramètre suivant :

           "enableTcpReset": true

Pour plus d’informations, y compris sur la disponibilité dans les régions, consultez [Réinitialisation TCP au terme du délai d’inactivité](https://aka.ms/lbtcpreset).

#### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a> Prendre en charge les protocoles de transport TCP et UDP avec une seule règle

Vous choisirez probablement l’option "All" pour le protocole de transport de la règle de trafic sortant, mais vous pouvez également appliquer la règle de trafic sortant à un protocole de transport spécifique, par choix ou par nécessité.

Pour définir le protocole sur TCP ou UDP, utilisez le paramètre suivant :

          "protocol": "All"

#### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a> Désactiver la NAT de trafic sortant pour une règle d’équilibrage de charge

Comme indiqué précédemment, les règles d’équilibrage de charge fournissent une programmation automatique de la NAT de trafic sortant. Toutefois, dans certains scénarios, il est préférable, ou obligatoire, de désactiver la programmation automatique de la NAT de trafic sortant par la règle d’équilibrage de charge pour que vous puissiez contrôler ou adapter vous-même le comportement.  Dans certains scénarios de règles de trafic sortant, il est important de désactiver la programmation automatique de la NAT de trafic sortant.

Vous pouvez utiliser ce paramètre de deux manières :
- Suppression facultative de l’utilisation de l’adresse IP entrante pour la NAT de trafic sortant.  Les règles de trafic sortant sont incrémentielles pour les règles d’équilibrage de charge et, avec ce paramètre défini, la règle de trafic sortant est contrôlée.
  
- Paramétrage de la NAT de trafic sortant d’une adresse IP utilisée à la fois pour le trafic entrant et le trafic sortant.  La programmation automatique de la NAT de trafic sortant doit être désactivée pour permettre le contrôle par une règle de trafic sortant.  Par exemple, ce paramètre doit être défini sur true si vous souhaitez changer l’allocation de ports SNAT d’une adresse qui est également utilisée pour le trafic entrant.  Si vous essayez d’utiliser une règle de trafic sortant pour redéfinir les paramètres d’une adresse IP également utilisée pour le trafic entrant, mais que vous n’avez pas désactivé la programmation de la NAT de trafic sortant pour la règle d’équilibrage de charge, la configuration d’une règle de trafic sortant échoue.

>[!IMPORTANT]
> Votre machine virtuelle n’aura pas de connectivité sortante si vous définissez ce paramètre sur true et que vous n’avez pas créé de règle de trafic sortant (ou un [scénario d’adresse IP publique de niveau d’instance](load-balancer-outbound-connections.md#ilpip)) pour définir la connectivité sortante.  Certaines opérations de votre machine virtuelle ou application peuvent avoir besoin d’une connectivité sortante. Assurez-vous de bien comprendre toutes les dépendances de votre scénario, ainsi que les conséquences d’un tel changement.

Pour désactiver la SNAT de trafic sortant sur la règle d’équilibrage de charge, définissez ce paramètre de configuration :

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Le paramètre disableOutboundSNAT est défini sur false par défaut, ce qui signifie que la règle d’équilibrage de charge **fournit** une NAT de trafic sortant automatique comme image miroir de la configuration de la règle d’équilibrage de charge.  

Si vous définissez disableOutboundSnat sur true pour la règle d’équilibrage de charge, celle-ci redonne le contrôle à la place de la programmation automatique de la NAT de trafic sortant.  En conséquence, la SNAT de trafic sortant est désactivée.

#### <a name="reuse-existing-or-define-new-backend-pools"></a>Réutiliser des pools backend existants ou en définir de nouveaux

Les règles de trafic sortant n’introduisent pas de nouveau concept pour la définition du groupe de machines virtuelles auquel la règle doit s’appliquer.  À la place, elles reprennent le concept de pool backend, qui est également utilisé pour les règles d’équilibrage de charge. Cela simplifie la configuration, car vous pouvez ainsi soit réutiliser une définition de pool backend existant, soit créer un pool backend spécifique pour une règle de trafic sortant.

### <a name="scenarios"></a>Scénarios

#### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a> Grouper les connexions sortantes dans un ensemble spécifique d’adresses IP publiques

Vous pouvez utiliser une règle de trafic sortant pour préparer les connexions sortantes de sorte qu’elles semblent provenir d’un ensemble spécifique d’adresses IP publiques. Cette pratique facilite les scénarios de mise sur liste verte.  Cette adresse IP publique source peut être la même que celle utilisée par une règle d’équilibrage de charge ou être un ensemble d’adresses IP publiques différent de celui utilisé par une règle d’équilibrage de charge.  

1. Créer un [préfixe d’adresse IP publique](https://aka.ms/lbpublicipprefix) (ou plusieurs adresses IP publiques à partir du préfixe d’adresse IP publique)
2. Créer un équilibreur de charge standard public
3. Créer des frontends référençant le préfixe d’adresse IP publique (ou les adresses IP publiques) que vous souhaitez utiliser
4. Réutiliser un pool backend existant ou créer un pool backend et placer les machines virtuelles dans un pool backend de l’équilibreur de charge public
5. Configurer une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles utilisant les frontends
   
Si vous ne souhaitez pas utiliser la règle d’équilibrage de charge pour le trafic sortant, vous devez [désactiver la SNAT de trafic sortant](#disablesnat) sur la règle d’équilibrage de charge.

#### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a> Modifier l’allocation de ports SNAT

Vous pouvez utiliser des règles de trafic sortant pour paramétrer [l’allocation de ports SNAT automatique en fonction de la taille du pool backend](load-balancer-outbound-connections.md#preallocatedports).

Par exemple, si vous avez deux machines virtuelles qui partagent une seule adresse IP publique pour la NAT de trafic sortant, vous pouvez augmenter le nombre de ports SNAT alloués (1 024 ports par défaut) en cas d’épuisement des ports SNAT. Chaque adresse IP publique peut fournir jusqu’à 64 000 ports éphémères.  Si vous configurez une règle de trafic sortant avec une seule adresse IP publique frontend, vous pouvez distribuer un total de 64 000 ports SNAT sur les machines virtuelles du pool backend.  Pour deux machines virtuelles, un maximum de 32 000 ports SNAT peut être alloué avec une règle de trafic sortant (2 x 32 000 = 64 000).

Consultez l’article [Connexions sortantes](load-balancer-outbound-connections.md) et la section sur l’allocation et l’utilisation des ports [SNAT](load-balancer-outbound-connections.md#snat).

#### <a name="enable-outbound-only"></a><a name="outboundonly"></a> Activer pour le trafic sortant uniquement

Vous pouvez utiliser un équilibreur de charge standard public afin de fournir la NAT de trafic sortant dans un groupe de machines virtuelles. Dans ce scénario, vous pouvez utiliser une règle de trafic sortant uniquement, sans avoir besoin d’autres règles.

##### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT de trafic sortant pour les machines virtuelles uniquement (pas en entrée)

Définissez un équilibreur de charge standard public, placez les machines virtuelles dans le pool backend, et configurez une règle de trafic sortant pour programmer la NAT de trafic sortant et préparer les connexions sortantes de sorte qu’elles semblent provenir d’une adresse IP publique spécifique. Vous pouvez également utiliser un préfixe d’adresse IP publique pour simplifier la mise sur liste verte des connexions sortantes sources.

1. Créez un équilibreur de charge standard public.
2. Créez un pool backend et placez les machines virtuelles dans un pool backend de l’équilibreur de charge public.
3. Configurez une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles.

##### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT de trafic sortant dans les scénarios avec un équilibreur de charge standard interne

Lorsque vous utilisez un équilibreur de charge standard interne, la NAT de trafic sortant n’est pas disponible tant que la connectivité sortante n’a pas été explicitement déclarée. Vous pouvez définir la connectivité sortante à l’aide d’une règle de trafic sortant pour fournir la connectivité sortante requise par les machines virtuelles placées derrière un équilibreur de charge standard interne comme suit :

1. Créez un équilibreur de charge standard public.
2. Créez un pool backend et placez les machines virtuelles dans un pool backend de l’équilibreur de charge public en plus de l’équilibreur de charge interne.
3. Configurez une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles.

##### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Activer les protocoles TCP et UDP pour la NAT de trafic sortant avec un équilibreur de charge standard public

- Quand vous utilisez un équilibreur de charge standard public, la programmation automatique de la NAT de trafic sortant utilise le protocole de transport de la règle d’équilibrage de charge.  

   1. Désactivez la NAT de trafic sortant sur la règle d’équilibrage de charge.
   2. Configurez une règle de trafic sortant sur le même équilibreur de charge.
   3. Réutilisez le pool backend de vos machines virtuelles.
   4. Spécifiez "protocol": "All" dans la définition de la règle de trafic sortant.

- Si vous utilisez uniquement des règles de NAT de trafic entrant, aucune NAT de trafic sortant n’est fournie.

   1. Placez les machines virtuelles dans un pool backend.
   2. Définissez une ou plusieurs configurations IP frontend avec une ou plusieurs adresses IP publiques ou avec un préfixe d’adresse IP publique.
   3. Configurez une règle de trafic sortant sur le même équilibreur de charge.
   4. Spécifiez "protocol": "All" dans la définition de la règle de trafic sortant


## <a name="limitations"></a>Limites
- Le nombre maximal de ports éphémères utilisables par adresse IP frontend est de 64 000.
- Le délai d’inactivité défini pour un flux sortant doit être compris entre 4 et 120 minutes (240 à 7 200 secondes).
- L’équilibreur de charge ne prend pas en charge ICMP pour la NAT de trafic sortant.
- Les règles de trafic sortant peuvent uniquement être appliquées à la configuration d’adresse IP principale d’une carte réseau.  La présence de plusieurs cartes réseau est prise en charge.
- Les rôles de travail web sans réseau virtuel et d’autres services de plateforme Microsoft peuvent être accessibles alors que seul un équilibreur de charge Standard interne est utilisé, en raison d’un effet secondaire du fonctionnement des services pré-réseau virtuel et des autres services de plateforme. Ne comptez pas sur cet effet secondaire, car le service lui-même ou la plateforme sous-jacente peuvent changer sans préavis. Vous devez toujours supposer que vous devez créer explicitement des connectivités sortantes si cela est souhaitable lors de l’utilisation d’un équilibreur de charge interne standard uniquement. Le scénario [SNAT par défaut](#defaultsnat) 3 décrit dans cet article n’est pas disponible.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[équilibreur de charge standard](load-balancer-standard-overview.md).
- En savoir plus sur les [règles de trafic sortant](load-balancer-outbound-rules-overview.md) pour l’équilibreur de charge Standard public.
- En savoir plus sur [Load Balancer](load-balancer-overview.md).
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/security-overview.md).
- En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.
