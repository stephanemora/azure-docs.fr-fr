---
title: Réglage des performances de TCP/IP pour les machines virtuelles Azure | Microsoft Docs
description: Découvrez diverses TCP/IP performances paramétrage techniques courantes et leur relation avec les machines virtuelles Azure.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: c5d4f67e9c1e4e983133675c440b8c5b64183227
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851859"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP réglage des performances pour les machines virtuelles Azure

L’objectif de cet article est pour discuter des techniques de réglage de performances de TCP/IP courants et leurs considérations pour les machines virtuelles en cours d’exécution sur Microsoft Azure. Il convient tout d’abord avoir une compréhension élémentaire des concepts supplémentaires et commenterons comment ils peuvent être paramétrées.

## <a name="common-tcpip-tuning-techniques"></a>Techniques de paramétrage TCP/IP courantes

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>MTU, la fragmentation et envoyer déchargement important (LSO)

#### <a name="explanation-of-mtu"></a>Explication de MTU

L’unité de transmission maximale (MTU) est la plus grande taille de cadre (paquet), spécifiée en octets, qui peuvent être envoyés via une interface réseau. La taille MTU est un paramètre configurable et la valeur par défaut que MTU utilisé sur les machines virtuelles Azure, et par défaut sur la plupart des périphériques réseau dans le monde entier, est 1 500 octets.

#### <a name="explanation-of-fragmentation"></a>Explication de fragmentation

La fragmentation se produit lorsqu’un paquet est envoyé qui dépasse la taille MTU d’une interface réseau. La pile TCP/IP arrêtera le paquet en éléments plus petits (fragments) qui sont conformes aux interfaces MTU. La fragmentation se produit au niveau de la couche IP et est indépendante du protocole sous-jacent (par exemple, TCP). Lorsqu’un paquet d’octets 2000 est envoyé via une interface réseau avec une taille MTU de 1500, puis il est divisé en un seul paquet de 1500 octets et un seul paquet de 500 octets.

Périphériques réseau dans le chemin d’accès entre une source et la destination ont la possibilité de supprimer les paquets qui dépassent la taille MTU ou fragmenter le paquet en éléments plus petits.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>Le bit « ne pas fragmenter (DF) » dans un paquet IP

Ne pas fragmenter est un indicateur dans l’en-tête de protocole IP. Lorsque le bit DF est défini, il indique que les périphériques réseau intermédiaires sur le chemin d’accès entre l’expéditeur et le destinataire doivent fragment pas le paquet. Pourquoi ce bit peut être défini pour de nombreuses raisons (voir la section de la découverte de chemin d’accès ci-dessous pour obtenir un exemple). Quand un périphérique réseau reçoit un paquet avec le bit de ne pas fragmenter défini, ce paquet dépasse l’interface de périphériques MTU, puis le comportement standard est que l’appareil d’abandonner le paquet et envoient un paquet de « Fragmentation ICMP nécessaire » à la source d’origine de la paquet.

#### <a name="performance-implications-of-fragmentation"></a>Impact sur les performances de fragmentation

La fragmentation peut avoir des implications en matière de performances. Une des principales raisons de l’impact sur les performances est l’impact de processeur/mémoire de fragmentation et le réassemblage de paquets. Lorsqu’un périphérique réseau doit fragmenter un paquet, il devra allouer des ressources de processeur/mémoire pour effectuer la fragmentation. Les mêmes doivent se produire lorsque le paquet est sont assemblés à nouveau. Le périphérique réseau doit stocker tous les fragments jusqu'à ce que leur réception afin qu’il peut les réassembler dans le paquet d’origine. Ce processus de fragmentation/re-assembly peut également entraîner une latence en raison du processus de fragmentation/re-assembly.

Implication des performances négatif possible de fragmentation est que les paquets fragmentés peuvent arriver en désordre. Paquets en désordre peuvent entraîner certains types de périphériques réseau à supprimer les paquets en désordre - qui demandera à l’ensemble du paquet pour être retransmis. Les scénarios typiques afin de déplacer des fragments incluent des dispositifs de sécurité, notamment les pare-feu réseau ou lorsqu’un périphérique réseau de réception de mémoires tampons sont épuisées. Lorsqu’un périphérique réseau de réception de mémoires tampons sont épuisées, un périphérique réseau tente de réassembler un paquet fragmenté, mais n’a pas les ressources pour stocker et reassume le paquet.

La fragmentation peut être perçue comme une opération négatif, mais la prise en charge de la fragmentation est nécessaire pour la connexion de divers réseaux via Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Avantages et les conséquences de la modification de la taille MTU

Une déclaration générale, augmenter la taille MTU peut créer un réseau plus efficace. Tous les paquets transmis a des informations d’en-tête supplémentaires qui sont ajoutées au paquet d’origine. Paquet plus signifie plus en-tête surcharge, et le réseau est par conséquent moins efficace.

Par exemple, la taille d’en-tête Ethernet est 14 octets plus un 4 octets Frame vérifier FCS (séquence) pour assurer la cohérence de frame. Si un seul octet de 2000 paquet est envoyé, 18 octets de surcharge d’Ethernet est ajouté sur le réseau. Si le paquet est fragmenté dans un paquet de 1500 octets et un paquet de 500 octets, chaque paquet aura 18 octets de l’en-tête Ethernet - ou 36 octets. Tandis qu’un seul paquet de 2000 octets n’auront qu’un en-tête Ethernet de 18 octets.

Il est important de noter que l’augmentation de la taille MTU en soi ne crée pas nécessairement un réseau plus efficace. Si une application envoie uniquement les paquets de 500 octets, la même surcharge en-tête existe si la taille MTU est 1 500 octets ou 9 000 octets. Dans l’ordre du réseau être plus efficace, puis il doit également utiliser paquets plus volumineux qui est par rapport à la taille MTU.

#### <a name="azure-and-vm-mtu"></a>Azure et MTU de la machine virtuelle

La taille MTU de machines virtuelles Azure de la valeur par défaut est 1 500 octets. La pile de réseau virtuel Azure va tenter de fragment d’un paquet à 1 400 octets. Cependant, la pile de réseau virtuel Azure autorise les paquets 2006 octets lorsque le bit « Ne pas fragmenter » est défini dans l’en-tête IP.

Il est important de noter que cette fragmentation n’implique pas que la pile de réseau virtuel Azure est par nature inefficace, car les fragments de paquets au niveau des octets 1400 tandis que les machines virtuelles ont une taille MTU de 1500. La réalité est qu’un grand pourcentage des paquets réseau est beaucoup plus petit que 1 400 octets ou 1 500 octets.

#### <a name="azure-and-fragmentation"></a>La fragmentation et Azure

Pile de réseau virtuel d’Azure aujourd'hui est configurée pour supprimer « Fragments hors de la commande « - ce qui signifie que les paquets fragmentés qui n’arrivent pas dans leur ordre d’origine fragmenté. Ces paquets sont abandonnés principalement en raison d’une faille de sécurité réseau a annoncé en novembre 2018 appelé FragmentStack.

FragmentSmack est un défaut dans la manière dont le noyau Linux gérées réassemblage de paquets fragmentés de IPv4 et IPv6. Un attaquant distant pourrait utiliser cette faille pour des opérations de réassemblage de fragment coûteux déclencheur conduisant à processeur accru et un déni de service sur le système cible.

#### <a name="tune-the-mtu"></a>Régler la taille MTU

Les machines virtuelles Azure prend en charge une taille MTU configurable comme tout autre système d’exploitation. Toutefois, la fragmentation qui se produit au sein d’Azure et est détaillée ci-dessus, prenez en compte lors de la configuration de la taille MTU.

Azure n’Encouragez les clients à augmenter leur MTU VM. Cette discussion est destinée à expliquer en détail comment Azure implémente MTU et effectue la fragmentation dès aujourd'hui.

> [!IMPORTANT]
>Augmenter la taille MTU n’a pas indiqué pour améliorer les performances et peut avoir un effet négatif sur les performances de l’application.
>
>

#### <a name="large-send-offload-lso"></a>Déchargement d’envoi important (LSO)

Envoyer déchargement important (LSO) peut améliorer les performances réseau en déchargeant la segmentation des paquets à la carte Ethernet. Avec LSO activé, la pile TCP/IP crée un paquet TCP volumineux et envoyez-la à l’adaptateur Ethernet pour la segmentation avant de le transférer. L’avantage de LSO est qu’il peut libérer le processeur à partir de la segmentation de paquets en tailles de paquet qui sont conformes à la taille MTU et que le traitement à l’interface Ethernet où elle est effectuée dans le matériel de déchargement. Vous trouverez plus d’informations sur les avantages de LSO dans [performances dans la documentation de l’adaptateur de réseau Microsoft](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Lorsque LSO est activé, les clients Azure peuvent voir les tailles d’image de grande taille lorsque les captures de paquets performant. Ces tailles d’image volumineux peuvent entraîner certains clients à croire que la fragmentation ou un jumbo que MTU est utilisé lorsqu’il n’est pas. Avec LSO, la carte ethernet peut publier un MSS supérieure à la pile TCP/IP afin de créer une taille de paquet TCP. Cette trame non segmenté entière est ensuite transmise à la carte Ethernet et est visible dans une capture de paquets effectuée sur la machine virtuelle. Toutefois, le paquet est divisé en plus petit nombre de frames par l’adaptateur Ethernet en fonction de la taille MTU de la carte Ethernet.

### <a name="tcpmss-window-scaling-and-pmtud"></a>L’évolutivité de fenêtre TCP/MSS et PMTUD

#### <a name="explanation-of-tcp-mss"></a>Explication du MSS TCP

TCP Segment (MSS, Maximum Size) est un paramètre destiné à définir la taille de segment TCP maximale afin d’éviter la fragmentation des paquets TCP. Systèmes d’exploitation définira généralement MSS comme MSS = MTU - taille d’en-tête TCP & IP (20 octets ou total de 40 octets). Par conséquent, une interface avec une taille MTU de 1500 aura une MSS 1460. Toutefois, le MSS, n’est configurable.

Ce paramètre est accepté dans la négociation tridirectionnelle TCP quand une session TCP est configurée entre une source et de destination. Les deux côtés envoyer une valeur MSS et la plus petite des deux est utilisée pour la connexion TCP.

Périphériques intermédiaires du réseau, telles que des passerelles VPN, y compris Azure VPN Gateway, ont la possibilité d’ajuster la taille MTU indépendante de la source et la destination pour garantir des performances réseau optimales. Par conséquent, il convient de noter que la taille MTU de la source et de destination autonome n’est pas les facteurs unique dans la valeur réelle de MSS.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>Explication de la découverte MTU de chemin d’accès (PMTUD)

Pendant la négociation de MSS, il n’indique pas le MSS réelle qui peut servir d’autres périphériques réseau dans le chemin d’accès entre la source et de destination peut avoir une valeur de la taille MTU inférieure à la source et la destination. Dans ce cas, l’appareil dont MTU est plus petit que le paquet ignore le paquet et renvoyer un message de contrôle Message ICMP (Internet Protocol) la Fragmentation nécessité (Type 3, 4 de Code) contenant la MTU. Ce message ICMP permet à l’hôte source afin de réduire sa taille MTU de chemin d’accès en conséquence. Le processus est appelé découverte de MTU du chemin d’accès.

Le processus de PMTUD est par nature inefficace et a des implications en matière de performances du réseau. Envoi de paquets qui dépassent un chemins d’accès réseau MTU, les paquets doivent être retransmis avec un MSS inférieur. Si l’expéditeur ne reçoit pas le paquet ICMP Fragmentation requise, peut-être en raison d’un pare-feu de réseau dans le chemin d’accès (communément appelé PMTUD blocage), puis l’expéditeur ne sait pas qu'il lui faut réduire notablement le MSS et seront en permanence retransmettre le paquet. Pour cette raison, nous ne recommandons pas augmenter la MTU de la machine virtuelle Azure.

#### <a name="vpn-considerations-with-mtu"></a>Considérations sur les connexions VPN avec MTU

Les clients qui utilisent des machines virtuelles qui exécutent l’encapsulation (par exemple, IPSec, VPN) peuvent avoir des conséquences supplémentaires à la taille de paquet et MTU. Réseaux privés virtuels Ajouter des en-têtes supplémentaires seront ajoutés au paquet d’origine ainsi augmenter la taille de paquet et nécessitant un plus petit MSS.

La recommandation actuelle pour Azure consiste à définir clamping du MSS TCP à 1 350 octets et l’interface de tunnel MTU à 1400. Vous trouverez plus d’informations sur la [VPN des appareils et la page des paramètres IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latence, temps d’aller-retour et l’évolutivité de fenêtre TCP

#### <a name="latency-and-round-trip-time"></a>Temps de latence et aller-retour

Latence du réseau est régie par la vitesse de la lumière sur un réseau de fibre optique. La réalité est, débit du réseau du protocole TCP est également gérées efficacement (pratiques maximales) raison de l’heure aller-retour (RTT) entre deux périphériques réseau.

| | | | |
|-|-|-|-|
|Routage|Distance|Heure à sens unique|Temps d’aller-retour (RTT)|
|New York à San Francisco|4,148 km|21 ms|42 ms|
|New York à Londres|5,585 km|28 ms|56 ms|
|New York à Sydney|15,993 km|80 ms|160 ms|

Ce tableau montre la distance linéaire entre deux emplacements, toutefois, dans les réseaux, la distance est généralement plus longue que la distance linéaire. Une formule simple pour calculer le temps RTT minimale comme régie par la vitesse de la lumière est : RTT minimale = 2 * (kilomètres de Distance / la vitesse de propagation).

Une valeur standard de 200 peut être utilisée pour la vitesse de la propagation - valeur est que la distance en mètres clair est transmis en 1 milliseconde.

Dans l’exemple de New York à San Francisco, il est 4,148 kilomètres linéaire. Minimum RTT = 2 * (4,148 / 20). La sortie de l’équation sera en millisecondes.

Comme la distance physique entre deux emplacements est une réalité fixe, si les performances réseau maximales sont requise, l’option la plus logique consiste à sélectionner les destinations avec la plus petite distance entre eux. Accessoirement, les décisions de conception du réseau virtuel est possible d’optimiser le chemin d’accès du trafic et de réduire la latence. Ces considérations de réseau virtuel sont décrits dans la section Considérations relatives à la conception réseau ci-dessous.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Effets de temps de latence et aller-retour sur TCP

Aller-retour (RTT) de temps a un effet direct sur le débit TCP maximal. Le protocole TCP dispose d’un concept de taille de fenêtre. La taille de fenêtre est la quantité maximale de trafic qui peut être envoyé via une connexion TCP avant que l’expéditeur doit recevoir l’accusé de réception du destinataire. Si le TCP MSS est défini sur 1460 et la taille de fenêtre TCP est définie à 65535 l’émetteur peut envoyer des 45 paquets avant qu’il doit recevoir l’accusé de réception du destinataire. Si l’accusé de réception n’est pas reçu l’expéditeur sera retransmissions. Dans cet exemple, la taille de fenêtre TCP / TCP MSS = paquets envoyés. Ou 65535 / 1460 est arrondi jusqu'à 45.

Cet état « en attente d’accusé de réception », comme un mécanisme pour créer une remise fiable des données, est cause efficacement RTT affecter le débit TCP. Plus l’expéditeur attend un accusé de réception, plus il doit également patienter avant d’envoyer plus de données.

La formule de calcul du débit maximal d’une connexion TCP est comme suit : Taille de la fenêtre / (latence RTT en millisecondes / 1000) = Maximum octets par seconde. Le tableau ci-dessous est mis en forme en mégaoctets pour une meilleure lisibilité et montre la maximale en mégaoctets / débit par seconde d’une connexion TCP unique.

| | | | |
|-|-|-|-|
|Taille en octets de la fenêtre TCP|Latence de la durée des boucles<br/>en millisecondes|Maximale<br/>Mégaoctets débit par seconde|Maximale<br/> Mégabit débit par seconde|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1,09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

Si les paquets sont perdus, il réduit le débit maximal d’une connexion TCP tandis que l’expéditeur retransmet les données qu’il a déjà envoyées.

#### <a name="explanation-of-tcp-window-scaling"></a>Explication de l’évolutivité de fenêtre TCP

L’évolutivité de fenêtre TCP est un concept qui augmente dynamiquement la taille de fenêtre TCP autorisant plus de données à envoyer avant un accusé de réception est requis. Dans notre exemple précédent, les 45 paquets sera donc envoyés avant un accusé de réception a été nécessaire. Si le nombre de paquets qui sont envoyés avant l’augmentation de l’accusé de réception, puis le débit maximal TCP est également augmenté en réduisant le nombre de fois où un expéditeur est en attente d’accusé de réception.

Le débit TCP est illustré dans une table simple ci-dessous :

| | | | |
|-|-|-|-|
|Taille de fenêtre TCP<br/>en octets|Latence de la durée des boucles en millisecondes|Maximale<br/>Mégaoctets débit par seconde|Maximale<br/> Mégabit débit par seconde|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Toutefois, la valeur d’en-tête TCP pour la taille de fenêtre TCP est uniquement 2 octets de longs, ce qui signifie que la valeur maximale pour une fenêtre de réception est 65535. Afin d’augmenter la taille de fenêtre maximale, un facteur d’échelle de fenêtre TCP a été introduit.

Le facteur d’échelle est également un paramètre qui peut être configuré dans un système d’exploitation. La formule de calcul de la taille de fenêtre TCP à l’aide des facteurs d’échelle est la suivante : Taille de fenêtre TCP = taille en octets de la fenêtre TCP \* (2 ^ facteur d’échelle). Si le facteur d’échelle de fenêtre est 3 et la taille de la fenêtre de 65 535, calcul est comme suit : 65535 \* (2 ^ 3) = octets 262,140. Un facteur d’échelle de 14 entraîne une taille de fenêtre TCP de 14 (le décalage maximal autorisé), alors que la taille de fenêtre TCP seront 1,073,725,440 octets (8,5 Gigabit/s).

#### <a name="support-for-tcp-window-scaling"></a>Prise en charge de l’évolutivité de fenêtre TCP

Windows a la possibilité de définir différents facteurs de mise à l’échelle sur une par type de connexion - il existe plusieurs classes de connexions (centre de données internet et ainsi de suite). Vous pouvez voir la classification de connexion mise à l’échelle de fenêtre avec la commande powershell Get-NetTCPConnection.

```powershell
Get-NetTCPConnection
```

Vous pouvez voir les valeurs de chaque classe avec la commande powershell Get-NetTCPSetting.

```powershell
Get-NetTCPSetting
```

La taille de fenêtre TCP initiale et le facteur de mise à l’échelle de TCP peuvent être définie dans Windows via la commande powershell Set-NetTCPSetting. Vous trouverez plus d’informations sur la [NetTCPSetting de jeu de page](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

Les paramètres TCP efficaces pour AutoTuningLevel sont les suivantes.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Facteur d’échelle|Multiplicateur de mise à l’échelle|Formule à<br/>calculer la taille de fenêtre maximale|
|Désactivé|Aucun|Aucun|Taille de la fenêtre|
|Restreint|4|2^4|Taille de la fenêtre * (2 ^ 4)|
|Hautement restreint|2|2^2|Taille de la fenêtre * (2 ^ 2)|
|Normal|8|2^8|Taille de la fenêtre * (2 ^ 8)|
|Expérimental|14|2^14|Taille de la fenêtre * (2 ^ 14)|

Bien que ces paramètres soient les plus susceptibles d’affecter les performances de TCP, il convient de noter que beaucoup d’autres facteurs sur Internet, en dehors du contrôle d’Azure, peut également affecter les performances de TCP.

#### <a name="increase-mtu-size"></a>Augmenter la taille MTU

Poser une question logique est « peut augmenter la taille MTU augmenter les performances de TCP comme une taille MTU supérieure signifie une plus grande MSS » ? La réponse simple est – probablement pas. Comme indiqué, voici les avantages et inconvénients pour la taille du paquet qui s’appliquent au-delà de simplement le trafic TCP. Comme indiqué ci-dessus, les principaux facteurs qui affectent les performances de débit TCP sont taille de fenêtre TCP, perte de paquets et durée des boucles.

> [!IMPORTANT]
> Azure ne recommande pas que les clients Azure modifier la valeur de la taille MTU par défaut sur les Machines virtuelles.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Mise en réseau accélérée et du trafic entrant

#### <a name="accelerated-networking"></a>Mise en réseau accélérée

Fonctions de réseau de Machine virtuelle faisaient intensif sur l’ordinateur virtuel invité et l’hôte hyperviseur/de l’UC. Tous les paquets qui transitent via l’hôte sont traité dans le logiciel par l’hôte du processeur - y compris tous les le réseau virtuel encapsulation/fr-capsulation. Par conséquent, plus le trafic qui traverse l’hôte, puis plus la charge du processeur. Et, si l’hôte du processeur est occupé à d’autres opérations, puis qui affectera également débit du réseau et la latence. Ce problème a été résolu via la mise en réseau accélérée.

Mise en réseau accélérée fournit la latence du réseau de très faibles cohérentes d’Azure de matériel programmable en interne et de technologies telles que SR-IOV. En déplaçant une grande partie de la pile de mise en réseau définie par logiciel d’Azure désactivé les processeurs et en SmartNICs basée sur des FPGA, de calcul cycles sont récupérés par les applications de l’utilisateur final, placer une charge moindre sur la machine virtuelle, la diminution de l’instabilité et l’incohérence de latence. En d’autres termes, les performances peuvent être plus déterministe.

Mise en réseau accélérée permet d’obtenir des améliorations de performances en permettant à la machine virtuelle invitée contourner l’hôte et d’établir un chemin de données directement avec SmartNIC l’hôte. Avantages de la mise en réseau accélérée sont :

- **Réduire la latence / nombre supérieur de paquets par seconde (pps)**: la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l'hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.

- **Instabilité réduite**: le traitement du commutateur virtuel dépend de la stratégie à appliquer et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.

- **Réduit l’utilisation du processeur**: en contournant le commutateur virtuel de l'hôte, le processeur utilise moins de ressources pour traiter le trafic réseau.

Mise en réseau accélérée doit être explicitement activée sur une base par ordinateur virtuel. Instructions pour l’activation de mise en réseau accélérée sur une machine virtuelle sont disponibles sur le [créer une machine virtuelle Linux avec mise en réseau accélérée de page](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Trafic entrant (RSS)

Mise à l’échelle du côté de réception est une technologie de pilote de réseau qui distribue la réception du trafic réseau plus efficacement en distribuant le traitement de réception entre plusieurs processeurs dans un système multiprocesseur. En termes simples, RSS permet à un système traiter une quantité supérieure de trafic reçu, car elle utilise tous les processeurs disponibles au lieu d’un. Vous trouverez une discussion plus technique de RSS dans le [Introduction à l’échelle côté réception de page](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

RSS est nécessaire pour optimiser les performances lors de la mise en réseau accélérée est activée sur une machine virtuelle. Il peut être également avantageux dans à l’aide de RSS sur des machines virtuelles qui n’ont pas activée la fonction de mise en réseau accélérée. Une vue d’ensemble montrant comment déterminer si le partage RSS est activé et configuration pour l’activer, consultez le [débit du réseau optimiser pour les machines virtuelles page](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>Heure et délai d’attente TCP d’attente sera

Un autre problème courant qui affecte les performances des applications et de réseau est le paramètre de durée d’attente TCP. Sur les machines virtuelles actives qui sont d’ouverture et fermeture de sockets, soit en tant que client ou serveur (Source IP:Source Port + Port de Destination IP:Destination), pendant le fonctionnement normal du TCP, un socket donné peut arriver dans un état d’attente de temps pour un certain temps. Cet état « temps d’attente », est destiné à autoriser toutes les données supplémentaires doivent être remises sur un socket avant sa fermeture. Par conséquent, les piles TCP/IP empêchent généralement la réutilisation d’un socket en déposant silencieusement le paquet TCP SYN de clients.

Cette quantité de temps un socket est en état d’attente est configurable, mais peut aller de 30 secondes à 240 secondes de temps. Les sockets sont une ressource limitée, et le nombre de sockets qui peut être utilisé à un moment donné est configurable (le nombre est généralement environ 30 000 sockets potentiels). Si ce nombre est atteint, ou clients et serveurs ont des paramètres d’attente de temps qui ne correspondent pas, une machine virtuelle tente de réutiliser un socket dans un état d’attente de temps, puis nouvelles connexions échouera sont car les paquets TCP SYN sont supprimés de manière silencieuse.

En règle générale, la valeur de la plage de ports pour les sockets sortants, ainsi que les paramètres de durée d’attente TCP et réutilisation de socket sont configurables au sein de la pile TCP/IP du système d’exploitation. Modification de ces numéros pouvant potentiellement améliorer l’évolutivité, mais selon le scénario, pourrait induire des problèmes d’interopérabilité et doivent être modifiée avec prudence.

Une fonctionnalité appelée temps attendre sera a été introduite pour résoudre ce problème de limitation de mise à l’échelle. Temps attendre sera permet à un socket d’être réutilisé dans certains scénarios, comme lorsque le numéro de séquence dans le paquet IP de la nouvelle connexion dépasse le numéro de séquence du dernier paquet de la connexion précédente. Dans ce cas, le système d’exploitation permettra la nouvelle connexion est établie (accepter le nouveau SYN ACK) et force fermer la connexion précédente qui a été dans le temps état d’attente. Cette fonctionnalité est prise en charge sur les machines virtuelles Windows dans Azure dès aujourd'hui et prise en charge au sein d’autres machines virtuelles doit être examiné par les clients Azure avec les fournisseurs de système d’exploitation respectifs.

Documentation sur la configuration des paramètres de durée d’attente TCP et la plage de ports source est disponible sur le [paramètres qui peuvent être modifiées à la page d’améliorer les performances réseau](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Facteurs de réseau virtuels qui peuvent affecter les performances

### <a name="vm-maximum-outbound-throughput"></a>Débit sortant maximal de machine virtuelle

Azure offre une variété de tailles et de types de machines virtuelles, qui présentent une combinaison différente de capacités de performances. Une telle capacité de performances est réseau débit (ou la bande passante), mesurée en mégabits par seconde (Mbits/s). Étant donné que les machines virtuelles sont hébergées sur du matériel partagé, la capacité du réseau doit être répartie équitablement entre les machines virtuelles partageant le même matériel. Les machines virtuelles plus volumineuses bénéficient d’une plus grande bande passante que les machines plus petites.

La bande passante réseau allouée à chaque machine virtuelle est mesurée en fonction du trafic sortant de la machine virtuelle. L’ensemble du trafic réseau quittant la machine virtuelle est mesuré en fonction de la limite d’allocation, quelle que soit la destination. Par exemple, si une machine virtuelle a une limite de 1 000 Mbits/s, cette limite s’applique si le trafic sortant est destiné à une autre machine virtuelle dans le même réseau virtuel, ou en dehors d’Azure.
Le trafic en entrée n’est pas compté ou limité directement. Toutefois, il existe d’autres facteurs, tels que les limites en termes de processeur et de stockage, susceptibles d’affecter la capacité d’une machine virtuelle à traiter les données entrantes.

Mise en réseau accélérée est une fonctionnalité conçue pour améliorer les performances du réseau, notamment la latence, le débit et l’utilisation du processeur. Alors que la mise en réseau accélérée peut améliorer le débit d’une machine virtuelle, elle peut le faire uniquement jusqu'à la machine virtuelle alloués à la bande passante.

Les machines virtuelles Azure doivent être associées à une interface réseau minimum. La bande passante allouée à une machine virtuelle représente l’intégralité du trafic sortant sur l’ensemble des interfaces réseau associées à une machine virtuelle. En d’autres termes, la bande passante est allouée machine virtuelle par machine virtuelle, quel que soit le nombre d’interfaces réseau associées à cette machine.
 
Débit sortant attendu et le nombre d’interfaces réseau pris en charge par chaque taille de machine virtuelle est détaillée ici. Pour afficher le débit maximal, sélectionnez un type, comme à usage général, puis une gamme de taille sur la page résultante, telles que la série Dv2. Chaque série comporte une table avec la mise en réseau des spécifications dans la dernière colonne intitulée, cartes réseau Max / attendue des performances du réseau (Mbits/s).

Cette limite de débit s’applique à la machine virtuelle. Le débit n’est pas affecté par les facteurs suivants :

- **Nombre d’interfaces réseau**: La limite de bande passante est cumulative de tout le trafic sortant à partir de la machine virtuelle.

- **Mise en réseau accélérée**: Toutefois, la fonctionnalité peut être utile pour atteindre la limite publiée, elle ne modifie pas la limite.

- **Destination du trafic**: Toutes les destinations sont pris en compte la limite de sortie.

- **Protocole** : Tout le trafic sortant sur tous les protocoles est comptabilisée dans la limite.

Un [table de bande passante maximale par type de machine virtuelle peut être trouvée en consultant cette page](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) et en cliquant sur le type de machine virtuelle respectif. Dans chaque page de type, une table affichera les cartes réseau maximale et la bande passante maximale attendue du réseau.

Vous trouverez plus d’informations sur la bande passante du réseau de machine virtuelle à [la bande passante du réseau de machine virtuelle](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considérations sur les performances

Comme indiqué dans cet article, facteurs sur Internet et en dehors du contrôle d’Azure peuvent affecter les performances du réseau. Ces facteurs sont :

- **Latence**: Le temps d’aller-retour entre deux destinations peuvent être affecté à des problèmes sur les réseaux intermédiaires, le trafic ne prendre la distance « le plus court » chemins d’homologation chemin d’accès possible et non optimal

- **Perte de paquets**: Perte de paquets peut être dû de congestion du réseau, les problèmes de chemin d’accès physique et les performances sous les périphériques réseau

- **Taille MTU/Fragmentation**: La fragmentation sur le chemin peut entraîner des retards dans l’arrivée des données ou les paquets arrivant en désordre, qui peuvent affecter la remise de paquets

Détermination d’itinéraire est un bon outil pour mesurer les caractéristiques de performances réseau (par exemple, la perte de paquets et la latence) le long de chaque chemin d’accès réseau entre un appareil source et de destination.

### <a name="network-design-considerations"></a>Considérations de conception de réseau

Ainsi que les considérations ci-dessus, la topologie d’un réseau virtuel peut affecter les performances de réseau virtuel. Par exemple, un hub- and -spoke conception que le trafic de retours à charge dans le monde entier à un réseau virtuel hub unique sera introduire une latence du réseau et ainsi affecter les performances globales. De même, le nombre de périphériques réseau qui traverse de trafic réseau peut également affecter la latence globale. Par exemple, dans une conception hub- and -spoke, si le trafic traverse un rayon Appliance virtuelle réseau et une Appliance virtuelle Hub avant en transit à Internet, puis latence peut être introduite par les Appliances virtuelles réseau.

### <a name="azure-regions-virtual-networks-and-latency"></a>Régions Azure, de réseaux virtuels et de latence

Régions Azure sont constituées de plusieurs centres de données qui existent dans une zone géographique générale. Ces centres de données peut ne pas être physiquement en regard de chacun des autres et dans certains cas peuvent être séparés par autant que 10 kilomètres. Le réseau virtuel est un segment de recouvrement logique sur le réseau de centre de données physiques d’Azure et un réseau virtuel n’implique pas de n’importe quelle topologie de réseau spécifique au sein du centre de données. Par exemple, VM A VM B se trouvent dans le même réseau virtuel et sous-réseau, mais peut être dans différents racks, lignes ou des centres de données même. Ils peuvent être séparés par des pieds de câble à fibre optique ou kilomètres de câble à fibre optique. Cette réalité peut introduire une latence variable (différence à quelques millisecondes) entre différentes machines virtuelles.

Cet emplacement géographique et donc la latence entre deux machines virtuelles, peuvent être influencées par la configuration des groupes à haute disponibilité et des Zones de disponibilité, toutefois, la distance entre les centres de données dans une région spécifiques à une région et principalement influencé par topologie centre de données dans la région.

### <a name="source-nat-port-exhaustion"></a>Épuisement du port source NAT

Un déploiement dans Azure peut communiquer avec les points de terminaison en dehors d’Azure dans le réseau Internet public et/ou espace IP public. Quand une instance lance cette connexion sortante, Azure mappe dynamiquement l’adresse IP privée à une adresse IP publique. Une fois ce mappage créé, le trafic de retour pour ce flux sortant peut aussi atteindre l’adresse IP privée d’où provient le flux.

Pour chaque connexion sortante, l’équilibreur de charge Azure doit mettre à jour ce mappage pour un certain temps. Avec la nature de l’architecture mutualisée d’Azure, la maintenance de ce mappage pour chaque flux sortant pour chaque machine virtuelle peut être gourmandes en ressources. Par conséquent, il existe des limites qui sont définies et selon la configuration du réseau virtuel Azure. Ou mentionnés plus précisément - une machine virtuelle Azure peut uniquement établir un certain nombre de connexions sortantes à un moment donné. Lorsque ces limites sont épuisées, puis la machine virtuelle Azure ne pourra effectuer toutes les connexions sortantes supplémentaires.

Ce comportement est, toutefois, configurable. Pour plus d’informations sur [SNAT et SNAT port épuisement], consultez [cet article](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Mesurer les performances réseau sur Azure

Un nombre des valeurs maximales de performances dans cet article est lié à la latence du réseau / aller-retour (RTT) de temps entre deux machines virtuelles. Cette section fournit des suggestions pour tester la latence/RTT, ainsi que les performances de TCP et les performances réseau des machines virtuelles. Les valeurs de TCP/IP & réseau décrits ci-dessus peuvent être paramétrées et performances testé en utilisant les techniques décrites ci-dessous. Les valeurs de latence, MTU, MSS et la taille de la fenêtre peuvent être utilisés dans les calculs ci-dessus et les valeurs maximales théoriques peuvent être comparées aux valeurs réelles observés pendant le test.

### <a name="measure-round-trip-time-and-packet-loss"></a>Temps d’aller-retour de mesure et de perte de paquets

Performances TCP s’appuie fortement sur la durée des boucles et la perte de paquets. La façon la plus simple pour mesurer la durée des boucles et la perte de paquets est à l’aide de l’utilitaire ping disponible dans Windows et Linux. La sortie de la commande ping affiche Latence max/min/moy entre une source et destination ainsi que de pertes de paquets. Test ping utilise le protocole ICMP par défaut. Pour tester TCP RTT, puis PsPing peut être utilisé. Plus d’informations sur PsPing sont disponibles à l’adresse [ce lien](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mesurer le débit réel d’une connexion TCP

NTttcp est un outil qui permet de tester les performances de TCP d’un Linux ou d’une machine virtuelle Windows. Différents paramètres TCP peuvent être modifiés et les avantages testée à l’aide de NTttcp. Vous trouverez plus d’informations sur NTttcp sur les liens ci-dessous.

- [Bande passante/débit (NTttcp) de test](https://aka.ms/TestNetworkThroughput)

- [Utilitaire de NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mesurer la bande passante réelle d’une machine virtuelle

Test des performances de différents types de machines virtuelles, mise en réseau accélérée, etc., peut être testée à l’aide d’un outil appelé Iperf, également disponible sur Linux et Windows. Iperf peut utiliser TCP ou UDP pour tester le débit global du réseau. Les tests de débit TCP à l’aide de Iperf sont influencées par les facteurs décrits dans cet article (latence, temps RTT et ainsi de suite). Par conséquent, UDP peut-être produire de meilleurs résultats pour un débit maximal simple test.

Vous trouverez des informations supplémentaires ci-dessous :

- [Résolution des problèmes de performances du réseau Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Comment valider un débit VPN sur un réseau virtuel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Détecter les comportements TCP inefficaces

Les clients Azure peuvent voir des paquets TCP avec indicateurs TCP (SACK, DUP ACK, retransmission et retransmission rapide) dans les captures de paquets qui peuvent indiquer des problèmes de performances réseau. Ces paquets indiquent spécifiquement les inefficacités du réseau en raison de la perte de paquets. Toutefois, perte de paquets n’est pas nécessairement en raison de problèmes de performances Azure. Problèmes de performances peut être le résultat de l’application, de système d’exploitation ou d’autres problèmes qui ne peuvent pas être liés directement à la plateforme Azure. Il est également important de noter que certains retransmission ou les accusés de réception en double sur un réseau est normale – les protocoles TCP ont été conçus pour être fiables. Et, preuve de ces paquets TCP dans une capture de paquets n’indique pas nécessairement un problème réseau systémiques, sauf si elles sont excessifs.

Toutefois, il convient de préciser clairement que ces types de paquets sont des indications que le débit TCP n’a pas obtenu ses performances maximales : pour les raisons mentionnées dans d’autres sections.
