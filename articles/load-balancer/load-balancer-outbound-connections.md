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
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 738b54d9fcd86313c2581c5d0f055a7cca8230b8
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88706062"
---
# <a name="outbound-connections-in-azure"></a>Connexions sortantes dans Azure

Azure Load Balancer fournit une connectivité sortante via différents mécanismes. Cet article décrit les scénarios et la façon de les gérer. Si vous rencontrez des problèmes de connectivité sortante via un équilibreur de charge Azure, consultez le [guide de résolution des problèmes liés aux connexions sortantes](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>Cet article traite des déploiements Resource Manager. Microsoft recommande l’utilisation de Resource Manager pour les charges de travail de production.

## <a name="terminology"></a>Terminologie

| Terme | Protocole(s) applicable(s) | Détails|
| ---------|---------| -------|
| Traduction d’adresses réseau sources (SNAT) | TCP, UDP | Un déploiement dans Azure peut communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public. Quand une instance lance un flux sortant vers une destination située dans l’espace d’adressage IP public, Azure mappe dynamiquement l’adresse IP privée à une adresse IP publique. Une fois ce mappage créé, le trafic de retour pour ce flux sortant peut aussi atteindre l’adresse IP privée d’où provient le flux. Pour exécuter cette fonction, Azure utilise une **traduction d’adresses réseau sources (SNAT)** .|
| Masquage de port SNAT (PAT)| TCP, UDP |  Quand plusieurs adresses IP privées se dissimulent derrière une seule IP publique, Azure utilise une **traduction d’adresse de port (PAT)** pour masquer/cacher les adresses IP privées. Les ports éphémères utilisés pour la traduction d’adresse de port sont [préaffectés](#preallocatedports) en fonction de la taille du pool. Lorsqu’une ressource Load Balancer publique est associée à des instances de machine virtuelle qui n’ont pas d’adresse IP publique dédiée, la source de chaque connexion sortante est réécrite. La source est réécrite depuis l’espace d’adressage IP privé du réseau virtuel dans l’adresse IP publique frontend de l’équilibreur de charge. Dans l’espace d’adressage IP public, le 5-tuple du flux (adresse IP source, port source, protocole de transport IP, adresse IP de destination, port de destination) doit être unique. L’usurpation de ports SNAT est utilisable avec les protocoles TCP et UDP IP. Des ports éphémères (ports SNAT) sont utilisés à cette fin après réécriture de l’adresse IP privée source, car plusieurs flux proviennent d’une même adresse IP publique. L’algorithme SNAT de masquage de port alloue des ports SNAT différemment pour UDP et TCP.|
| Ports SNAT| TCP | Les ports SNAT sont des ports éphémères disponibles pour une adresse IP publique source donnée. Un seul port SNAT est utilisé par flux vers un port et adresse IP de destination uniques. En cas de flux TCP multiples vers les mêmes adresse IP, port et protocole de destination, chaque flux TCP utilise un seul port de traduction d’adresses réseau sources. Cela garantit que les flux sont uniques quand ils proviennent de la même adresse IP publique et sont dirigés vers les mêmes adresse IP, port et protocole de destination. Plusieurs flux, chacun dirigé vers une adresse IP, un port et un protocole de destination distincts, partagent un même port SNAT. L’adresse IP, le port et le protocole de destination rendent les flux uniques sans qu’il soit nécessaire de recourir à des ports sources supplémentaires pour distinguer les flux dans l’espace d’adressage IP public.|
|Ports SNAT | UDP | Les ports SNAT UDP sont gérés par un algorithme différent de celui qui gère les ports SNAT TCP.  Load Balancer utilise un algorithme appelé « NAT à cône restrictif sur les ports » pour UDP.  Un seul port SNAT est utilisé pour chaque flux, quels que soient le port et l’adresse IP de destination.|
| Épuisement | - | En cas d’épuisement des ressources de port SNAT, les flux sortants échouent tant que les flux existants ne libèrent pas des ports SNAT. L’équilibreur de charge récupère les ports de traduction d’adresses réseau sources lorsque le flux se ferme, et utilise un [délai d’inactivité de 4 minutes](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) pour récupérer les ports de traduction d’adresses réseau sources des flux inactifs. Les Ports SNAT UDP s’épuisent généralement beaucoup plus rapidement que les ports SNAT TCP, car l’algorithme utilisé n’est pas le même. Vous devez concevoir et adapter les tests en tenant compte de cette différence.|
| Comportement de mise à disposition de ports SNAT | TCP | Si le client/serveur envoie un paquet FINACK, le port SNAT est mis à disposition après un délai de 240 secondes. Si une instance RST est visible, le port SNAT est mis à disposition après un délai de 15 secondes. Si le délai d’inactivité est atteint, le port est mis à disposition.|
| Comportement de mise à disposition de ports SNAT | UDP |Si le délai d’inactivité est atteint, le port est mis à disposition.|
| Réutilisation des ports SNAT | TCP, UDP | Une fois qu’un port a été libéré, le port peut être réutilisé en fonction des besoins.  Vous pouvez considérer les ports SNAT sous la forme d’une série allant du moins jusqu’au plus disponible pour un scénario donné, et le premier port SNAT disponible est utilisé pour les nouvelles connexions.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algorithme d’allocation de port

Azure utilise un algorithme pour déterminer le nombre de ports SNAT préalloués disponibles en fonction de la taille du pool principal lorsque vous utilisez le PAT. Pour chaque adresse IP publique associée à un équilibreur de charge, 64 000 ports sont disponibles en tant que ports SNAT pour chacun des protocoles de transport IP. Un nombre identique de ports SNAT est préalloué pour UDP et TCP respectivement ; il est consommé indépendamment en suivant un protocole de transport IP.  Toutefois, l’utilisation de ports SNAT est différente selon que le flux est TCP ou UDP. Lorsque des flux sortants sont créés, ces ports sont consommés dynamiquement (jusqu’à la limite préallouée) et libérés lorsque le flux se ferme ou en cas d’expiration du [délai d’inactivité](../load-balancer/troubleshoot-outbound-connection.md#idletimeout). Les ports ne sont consommés que si cela permet de rendre les flux uniques.

#### <a name="default-snat-ports-allocated"></a><a name="snatporttable"></a> Ports SNAT par défaut alloués

Le tableau suivant présente les préaffectations de ports SNAT pour les différents niveaux de tailles de pool backend :

| Taille du pool (instances de machine virtuelle) | Ports de traduction d’adresses réseau sources préaffectés par configuration IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 |

La modification de la taille de votre pool principal peut affecter certains de vos flux établis :

- Si la taille du pool backend augmente et passe au niveau suivant, la moitié des ports SNAT préaffectés sont récupérés pendant la transition vers le niveau de pool backend supérieur suivant. Les flux associés à un port SNAT récupéré expirent et doivent être rétablis. En cas de tentative de lancement d’un nouveau flux, celui-ci réussit immédiatement du moment que des ports préaffectés sont disponibles.
- Si la taille du pool backend diminue et passe à un niveau inférieur, le nombre de ports SNAT disponibles augmente. Dans ce cas, les ports de traduction d’adresses réseau sources affectés existants et leurs flux respectifs ne sont pas concernés.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Vue d’ensemble des scénarios des connexions sortantes

| Scénario | Méthode | Protocoles IP | Description |
|  --- | --- | --- | --- |
|  1. Machine virtuelle avec une IP publique (avec ou sans Azure Load Balancer) | Traduction d’adresses réseau sources, masquage de port non utilisé | TCP, UDP, ICMP, ESP | Azure utilise l’IP publique attribuée à la configuration IP de la carte d’interface réseau de l’instance pour tous les flux sortants. L’instance a tous les ports éphémères disponibles. Peu importe si la machine virtuelle est équilibrée en charge ou non. Ce scénario prend le pas sur les autres. Une adresse IP publique affectée à une machine virtuelle constitue une relation 1:1 (non pas une relation 1-à-plusieurs) ; elle est implémentée comme un NAT 1:1 sans état. |
| 2. Équilibreur de charge public associé à une machine virtuelle (aucune IP publique sur l’instance ou la machine virtuelle) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) en utilisant des frontends Load Balancer | TCP, UDP | Dans ce scénario, la ressource d’équilibreur de charge doit être configurée avec une règle d’équilibreur de charge pour créer un lien entre le serveur frontal d’IP publique et le pool principal. Si vous n’effectuez pas cette configuration de règle, le comportement est celui décrit dans le scénario no 3. La règle n’a pas besoin d’un écouteur opérationnel dans le pool backend ou la sonde d’intégrité pour réussir. Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en IP publique du serveur frontal Load Balancer public via SNAT. Les ports éphémères de l’IP publique du serveur frontal de l’équilibreur de charge servent à faire la distinction entre les différents flux en provenance de la machine virtuelle. La traduction d’adresses réseau utilise dynamiquement des [ports éphémères préaffectés](#preallocatedports) lors de la création de flux sortants. Dans ce contexte, les ports éphémères utilisés pour la traduction d’adresses réseau sources sont appelés ports SNAT. Les ports SNAT sont préalloués comme décrit dans la table [Ports SNAT par défaut alloués](#snatporttable). |
| 3. Machine virtuelle (sans équilibreur de charge, aucune IP publique) ou machine virtuelle associée à l’équilibreur de charge interne de base | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) | TCP, UDP | Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique. L’IP publique **n’est pas configurable**, ne peut pas être réservée et n’entre pas en compte dans la limite de ressources IP publiques de l’abonnement. Si vous redéployez la machine virtuelle, le groupe à haute disponibilité ou le groupe de machines virtuelles identiques, cette adresse IP publique est libérée et une nouvelle adresse IP publique est demandée. N’utilisez pas ce scénario pour mettre en liste verte des adresses IP. Au lieu de cela, utilisez le scénario no 1 ou 2 dans lequel vous déclarez explicitement le comportement du trafic sortant. Les ports SNAT sont préalloués comme décrit dans la table [Ports SNAT par défaut alloués](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Règles de trafic sortant

 Les règles de trafic sortant facilitent la configuration de la traduction des adresses réseau sortantes publiques dans [Standard Load Balancer](load-balancer-standard-overview.md).  Vous pouvez contrôler entièrement la connectivité sortante de manière déclarative pour la mettre à l’échelle et l’adapter en fonction de vos besoins spécifiques. Cette section développe le scénario no 2 (B) décrit ci-dessus.

![Règles de trafic sortant dans Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Grâce aux règles de trafic sortant, vous pouvez utiliser Load Balancer pour définir la NAT de trafic sortant à partir de zéro. Vous pouvez également mettre à l’échelle et adapter le comportement de la NAT de trafic sortant.

Les règles de trafic sortant vous permettent de déterminer :

- quelles machines virtuelles doivent être traduites vers quelles adresses IP publiques,
- le mode d’allocation des ports SNAT sortants,
- les protocoles qui fournissent la traduction sortante,
- la durée à utiliser comme délai d’inactivité des connexions sortantes (de 4 à 120 minutes),
- la réinitialisation TCP au terme du délai d’inactivité.
- les protocoles de transport TCP et UDP avec une seule règle.

### <a name="outbound-rule-definition"></a>Définition de règle de trafic sortant

Comme toutes les règles de Load Balancer, les règles de trafic sortant suivent la syntaxe habituelle des règles d’équilibrage de charge et des règles NAT de trafic entrant : **serveur frontal** + **paramètres** + **pool principal**. Une règle de trafic sortant configure la NAT de trafic sortant pour que _toutes les machines virtuelles identifiées par le pool backend_ soient traduites sur le _frontend_.  Les _paramètres_ permettent de contrôler avec plus de précision l’algorithme de la NAT de trafic sortant.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Mettre à l’échelle la NAT de trafic sortant avec plusieurs adresses IP

Chaque adresse IP supplémentaire fournie par un serveur frontal met à disposition 64 000 ports éphémères que Load Balancer peut utiliser en tant que ports SNAT. Vous pouvez utiliser plusieurs adresses IP pour vos scénarios à grande échelle et utiliser des règles de trafic sortant afin de limiter les risques [d’épuisement de ports SNAT](troubleshoot-outbound-connection.md#snatexhaust).  

Vous pouvez utiliser un [préfixe d’IP publique](https://aka.ms/lbpublicipprefix) directement avec une règle de trafic sortant.  L’utilisation d’un préfixe d’adresse IP publique simplifie la mise à l’échelle et la mise sur liste verte des flux provenant de votre déploiement Azure. Vous pouvez configurer une configuration IP frontend au sein de la ressource Load Balancer pour référencer directement un préfixe d’adresse IP publique.  De cette manière, Load Balancer conserve un contrôle exclusif sur le préfixe d’adresse IP publique, et la règle de trafic sortant utilise automatiquement toutes les adresses IP publiques contenues dans le préfixe d’adresse IP publique pour les connexions sortantes.  Chacune des adresses IP du préfixe d’IP publique fournit 64 000 ports éphémères que Load Balancer peut utiliser en tant que ports SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a>Délai d’inactivité des flux sortants et réinitialisation TCP

Les règles de trafic sortant ont un paramètre de configuration qui permet de contrôler le délai d’inactivité des flux sortants et de l’ajuster en fonction des besoins de votre application. Le délai d’inactivité des flux sortants est de 4 minutes par défaut. Vous pouvez apprendre à [configurer des délais d’inactivité](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). Le comportement par défaut de Load Balancer consiste à supprimer silencieusement des flux quand le délai d’inactivité d’un flux sortant est atteint.  Avec le paramètre `enableTCPReset`, vous pouvez définir un comportement plus prévisible de l’application et contrôler s’il faut déclencher une réinitialisation TCP (TCP RST) bidirectionnelle quand le délai d’inactivité d’un flux sortant a expiré. Pour plus d’informations, y compris sur la disponibilité dans les régions, consultez [Réinitialisation TCP au terme du délai d’inactivité](https://aka.ms/lbtcpreset).

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prévention de toute connexion sortante

Les règles d’équilibrage de charge fournissent une programmation automatique de la NAT de trafic sortant. Toutefois, dans certains scénarios, il est préférable, ou obligatoire, de désactiver la programmation automatique de la NAT de trafic sortant par la règle d’équilibrage de charge pour que vous puissiez contrôler ou adapter vous-même le comportement.  
Vous pouvez utiliser ce paramètre de deux manières :

1. Suppression facultative de l’utilisation de l’adresse IP entrante pour la SNAT de trafic sortant via la désactivation de la SNAT de trafic sortant pour une règle d’équilibrage de charge.
  
2. Paramétrage de la SNAT de trafic sortant d’une adresse IP utilisée à la fois pour le trafic entrant et le trafic sortant.  La programmation automatique de la NAT de trafic sortant doit être désactivée pour permettre le contrôle par une règle de trafic sortant.  Par exemple, le paramètre `disableOutboundSnat` doit être défini sur true si vous souhaitez changer l’allocation de ports SNAT d’une adresse qui est également utilisée pour le trafic entrant.  Si vous essayez d’utiliser une règle de trafic sortant pour redéfinir les paramètres d’une adresse IP également utilisée pour le trafic entrant, mais que vous n’avez pas désactivé la programmation de la NAT de trafic sortant pour la règle d’équilibrage de charge, la configuration d’une règle de trafic sortant échoue.

>[!IMPORTANT]
> Votre machine virtuelle n’aura pas de connectivité sortante si vous définissez ce paramètre sur true et que vous n’avez pas créé de règle de trafic sortant pour définir la connectivité sortante.  Certaines opérations de votre machine virtuelle ou application peuvent avoir besoin d’une connectivité sortante. Assurez-vous de bien comprendre toutes les dépendances de votre scénario, ainsi que les conséquences d’un tel changement.

Dans certains cas, il est préférable de ne pas autoriser une machine virtuelle à créer un flux sortant. Il peut aussi exister une condition qui détermine les destinations qui peuvent être atteintes avec des flux sortants ou celles qui peuvent lancer des flux entrants. Dans ce cas, vous pouvez utiliser des [groupes de sécurité réseau](../virtual-network/security-overview.md) pour déterminer les destinations que la machine virtuelle peut atteindre. Vous pouvez aussi utiliser des groupes de sécurité réseau pour déterminer la destination publique qui peut lancer des flux entrants.

Quand vous appliquez un groupe de sécurité réseau à une machine virtuelle à charge équilibrée, vérifiez les [balises de service](../virtual-network/security-overview.md#service-tags) et les [règles de sécurité par défaut](../virtual-network/security-overview.md#default-security-rules). Vous devez vous assurer que la machine virtuelle peut recevoir des demandes d’analyse d’intégrité d’Azure Load Balancer.

Si un groupe de sécurité réseau bloque les demandes d’analyse d’intégrité depuis la balise par défaut AZURE_LOADBALANCER, votre analyse de l’intégrité de la machine virtuelle échoue et la machine virtuelle est marquée comme défaillante. L’équilibrage de charge arrête l’envoi de nouveaux flux vers cette machine virtuelle.

## <a name="scenarios-with-outbound-rules"></a>Scénarios avec règles de trafic sortant

| # | Scénario| Détails|
|---|---|---|
| I | Préparer les connexions sortantes dans un ensemble spécifique d’IP publiques| Vous pouvez utiliser une règle de trafic sortant pour préparer les connexions sortantes de sorte qu’elles semblent provenir d’un ensemble spécifique d’adresses IP publiques. Cette pratique facilite les scénarios de mise sur liste verte.  Cette adresse IP publique source peut être la même que celle utilisée par une règle d’équilibrage de charge ou être un ensemble d’adresses IP publiques différent de celui utilisé par une règle d’équilibrage de charge.  1. Créez un [préfixe d’IP publique](https://aka.ms/lbpublicipprefix) (ou plusieurs IP publiques à partir du préfixe d’IP publique). 2. Créez un équilibreur de charge standard public. 3. Créez des serveurs frontaux référençant le préfixe d’IP publique (ou les IP publiques) que vous souhaitez utiliser. 4. Réutilisez un pool principal existant ou créez-en un et placez les machines virtuelles dans un pool principal de l’équilibreur de charge public. 5. Configurez une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles utilisant les serveurs frontaux. Si vous ne souhaitez pas utiliser la règle d’équilibrage de charge pour le trafic sortant, vous devez désactiver la SNAT de trafic sortant sur la règle d’équilibrage de charge.
| II | Modifier l’allocation de ports SNAT| Vous pouvez utiliser des règles de trafic sortant pour paramétrer [l’allocation de ports SNAT automatique en fonction de la taille du pool backend](load-balancer-outbound-connections.md#preallocatedports). Par exemple, si vous avez deux machines virtuelles qui partagent une seule adresse IP publique pour la NAT de trafic sortant, vous pouvez augmenter le nombre de ports SNAT alloués (1 024 ports par défaut) en cas d’épuisement des ports SNAT. Chaque adresse IP publique peut fournir jusqu’à 64 000 ports éphémères.  Si vous configurez une règle de trafic sortant avec une seule adresse IP publique frontend, vous pouvez distribuer un total de 64 000 ports SNAT sur les machines virtuelles du pool backend.  Pour deux machines virtuelles, un maximum de 32 000 ports SNAT peut être alloué avec une règle de trafic sortant (2 x 32 000 = 64 000). Vous pouvez utiliser des règles de trafic sortant pour paramétrer les ports SNAT alloués par défaut. Vous allouez plus ou moins que l’allocation par défaut de ports SNAT ne le prévoit. Chaque IP publique de tous les serveurs frontaux d’une règle de trafic sortant fournit jusqu’à 64 000 ports éphémères en tant que ports SNAT.  Load Balancer alloue des ports SNAT par multiples de huit. Si vous indiquez une valeur non divisible par huit, l’opération de configuration est rejetée.  Si vous essayez d’allouer plus de ports SNAT qu’il n’y a de ports disponibles par rapport au nombre d’adresses IP publiques, l’opération de configuration est rejetée.  Par exemple, si vous allouez 10 000 ports par machine virtuelle et que sept machines virtuelles d’un pool principal partagent la même adresse IP publique, la configuration est rejetée (7 x 10 000 ports SNAT > 64 000 ports SNAT).  Dans ce scénario, vous devrez donc ajouter des adresses IP publiques supplémentaires sur le frontend de la règle de trafic sortant. Vous pouvez revenir à une [allocation de ports SNAT par défaut en fonction de la taille du pool principal](load-balancer-outbound-connections.md#preallocatedports) en spécifiant 0 comme nombre de ports. Dans ce cas, les 50 premières instances de machine virtuelle obtiennent 1 024 ports, les instances de machine virtuelle 51 à 100 obtiennent 512 ports, et ainsi de suite conformément à ce qui est indiqué dans la [table](#snatporttable).|
| III| Activer pour le trafic sortant uniquement | Vous pouvez utiliser un équilibreur de charge standard public afin de fournir la NAT de trafic sortant dans un groupe de machines virtuelles. Dans ce scénario, vous pouvez utiliser une règle de trafic sortant uniquement, sans avoir besoin d’autres règles.|
| IV | NAT de trafic sortant pour les machines virtuelles uniquement (pas en entrée) | Définissez un équilibreur de charge standard public, placez les machines virtuelles dans le pool backend, et configurez une règle de trafic sortant pour programmer la NAT de trafic sortant et préparer les connexions sortantes de sorte qu’elles semblent provenir d’une adresse IP publique spécifique. Vous pouvez également utiliser un préfixe d’adresse IP publique pour simplifier la mise sur liste verte des connexions sortantes sources. 1. Créez un équilibreur de charge standard public. 2. Créez un pool backend et placez les machines virtuelles dans un pool backend de l’équilibreur de charge public. 3. Configurez une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles.
| V| NAT de trafic sortant dans les scénarios avec un équilibreur de charge standard interne| Lorsque vous utilisez un équilibreur de charge standard interne, la NAT de trafic sortant n’est pas disponible tant que la connectivité sortante n’a pas été explicitement déclarée. Vous pouvez définir la connectivité sortante à l’aide d’une règle de trafic sortant pour fournir la connectivité sortante requise par les machines virtuelles placées derrière un équilibreur de charge standard interne comme suit : 1. Créez un équilibreur de charge standard public. 2. Créez un pool backend et placez les machines virtuelles dans un pool backend de l’équilibreur de charge public en plus de l’équilibreur de charge interne. 3. Configurez une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles. Pour plus d’informations sur ce scénario, consultez [cet exemple](https://docs.microsoft.com/azure/load-balancer/egress-only). |
| VI | Activer les protocoles TCP et UDP pour la NAT de trafic sortant avec un équilibreur de charge standard public | Quand vous utilisez un équilibreur de charge standard public, la programmation automatique de la NAT de trafic sortant utilise le protocole de transport de la règle d’équilibrage de charge. 1. Désactivez la NAT de trafic sortant sur la règle d’équilibrage de charge. 2. Configurez une règle de trafic sortant sur le même équilibreur de charge. 3. Réutilisez le pool backend de vos machines virtuelles. 4. Spécifiez "protocol": "All" dans la définition de la règle de trafic sortant. Si vous utilisez uniquement des règles de NAT de trafic entrant, aucune NAT de trafic sortant n’est fournie. 1. Placez les machines virtuelles dans un pool backend. 2. Définissez une ou plusieurs configurations IP de serveur frontal avec une ou plusieurs IP publiques ou avec un préfixe d’IP publique. 3. Configurez une règle de trafic sortant sur le même équilibreur de charge. 4. Spécifiez "protocol": "All" dans la définition de la règle de trafic sortant |


## <a name="limitations"></a>Limites

- Le nombre maximal de ports éphémères utilisables par adresse IP frontend est de 64 000.
- Le délai d’inactivité défini pour un flux sortant doit être compris entre 4 et 120 minutes (240 à 7 200 secondes).
- L’équilibreur de charge ne prend pas en charge ICMP pour la NAT de trafic sortant.
- Les règles de trafic sortant peuvent uniquement être appliquées à la configuration d’adresse IP principale d’une carte réseau.  Vous ne pouvez pas créer une règle de trafic sortant pour l’adresse IP secondaire d’une machine virtuelle ou d’une appliance virtuelle réseau. La présence de plusieurs cartes réseau est prise en charge.
- Les rôles de travail web sans réseau virtuel et d’autres services de plateforme Microsoft peuvent être accessibles alors que seul un équilibreur de charge Standard interne est utilisé, en raison d’un effet secondaire du fonctionnement des services pré-réseau virtuel et des autres services de plateforme. Ne comptez pas sur cet effet secondaire, car le service lui-même ou la plateforme sous-jacente peuvent changer sans préavis. Vous devez toujours supposer que vous devez créer explicitement des connectivités sortantes si cela est souhaitable lors de l’utilisation d’un équilibreur de charge interne standard uniquement. Le scénario no 3 décrit dans cet article n’est pas disponible.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[équilibreur de charge standard](load-balancer-standard-overview.md).
- Consultez notre [foire aux questions relative à Azure Load Balancer](load-balancer-faqs.md).
- En savoir plus sur les [règles de trafic sortant](load-balancer-outbound-rules-overview.md) pour l’équilibreur de charge Standard public.
- En savoir plus sur [Load Balancer](load-balancer-overview.md).
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/security-overview.md).
- En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.
