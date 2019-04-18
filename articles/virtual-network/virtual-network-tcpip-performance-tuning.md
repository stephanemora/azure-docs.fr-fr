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
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 1e8605a41cbe610c971b891309b2149d221b8b27
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426441"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP réglage des performances pour les machines virtuelles Azure

Cet article décrit les techniques de réglage de performances de TCP/IP courants et quelques éléments à prendre en compte lorsque vous les utilisez pour les machines virtuelles s’exécutant sur Azure. Il fournissent une vue d’ensemble des techniques et découvrez comment ils peuvent être paramétrées.

## <a name="common-tcpip-tuning-techniques"></a>Techniques de paramétrage TCP/IP courantes

### <a name="mtu-fragmentation-and-large-send-offload"></a>Déchargement d’envoi important, la fragmentation et la taille MTU

#### <a name="mtu"></a>MTU

L’unité de transmission maximale (MTU) est la plus grande taille de cadre (paquet), spécifiée en octets, qui peuvent être envoyés via une interface réseau. La taille MTU est un paramètre configurable. La valeur par défaut MTU utilisé sur les machines virtuelles Azure, et par défaut sur la plupart des périphériques réseau dans le monde entier, est de 1 500 octets.

#### <a name="fragmentation"></a>Fragmentation

La fragmentation se produit lorsqu’un paquet est envoyé qui dépasse la taille MTU d’une interface réseau. La pile TCP/IP arrêtera le paquet en éléments plus petits (fragments) qui se conforment à la MTU de l’interface. La fragmentation se produit au niveau de la couche IP et est indépendante du protocole sous-jacent (par exemple, TCP). Lorsqu’un paquet de 2 000 octets est envoyé via une interface réseau avec une taille MTU de 1 500, le paquet est divisé en un seul paquet de 1 500 octets et un seul paquet de 500 octets.

Périphériques réseau dans le chemin d’accès entre une source et la destination peuvent soit ignorer les paquets qui dépassent la taille MTU ou un fragment du paquet en éléments plus petits.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Ne pas fragmenter de bits dans un paquet IP

Le bit de ne pas fragmenter (DF) est un indicateur dans l’en-tête de protocole IP. Le bit DF indique que les périphériques réseau sur le chemin d’accès entre l’expéditeur et le destinataire doivent fragment pas le paquet. Ce bit peut être défini pour de nombreuses raisons. (Voir la section « Découverte de MTU de chemin d’accès » de cet article pour obtenir un exemple.) Quand un périphérique réseau reçoit un paquet avec le bit de ne pas fragmenter défini et que ce paquet dépasse l’interface de l’unité MTU, le comportement standard est sur l’appareil supprimer le paquet. L’appareil envoie un message ICMP Fragmentation requise à la source d’origine du paquet.

#### <a name="performance-implications-of-fragmentation"></a>Impact sur les performances de fragmentation

La fragmentation peut avoir des implications en matière de performances. Une des principales raisons de l’effet sur les performances est l’impact de processeur/mémoire de la fragmentation et le réassemblage de paquets. Lorsqu’un périphérique réseau doit fragmenter un paquet, il devra allouer des ressources de processeur/mémoire pour effectuer la fragmentation.

La même chose se produit lorsque le paquet est sont assemblés à nouveau. Le périphérique réseau doit stocker tous les fragments jusqu'à ce que leur réception afin qu’il peut les réassembler dans le paquet d’origine. Ce processus de fragmentation et réassemblage peut également entraîner des temps de latence.

Implication des performances négatif possible de fragmentation est que les paquets fragmentés peuvent arriver dans le désordre. Lorsque les paquets sont reçus en désordre, certains types de périphériques réseau peuvent les supprimer. Lorsque cela se produit, le paquet entier doit être retransmis.

Fragments sont généralement supprimés par les appareils de sécurité, notamment les pare-feu réseau ou lorsqu’un périphérique réseau de réception de mémoires tampons sont épuisées. Lorsqu’un périphérique réseau de réception de mémoires tampons sont épuisées, un périphérique réseau tente de réassembler un paquet fragmenté, mais n’a pas les ressources pour stocker et reassume le paquet.

La fragmentation peut être considérée comme une opération négatif, mais la prise en charge de la fragmentation est nécessaire lorsque vous vous connectez divers réseaux via internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Avantages et les conséquences de la modification de la taille MTU

En règle générale, vous pouvez créer un réseau plus efficace en augmentant la taille MTU. Tous les paquets transmis contient des informations d’en-tête qui sont ajoutées au paquet d’origine. Lorsque la fragmentation crée davantage de paquets, plus un en-tête de surcharge, et qui rend le réseau moins efficace.

Voici un exemple. La taille d’en-tête Ethernet est de 14 octets ainsi qu’une séquence de vérification de la frame de 4 octets pour garantir la cohérence de frame. Si un seul paquet de 2 000 octets est envoyé, 18 octets de surcharge d’Ethernet est ajouté sur le réseau. Si le paquet est fragmenté dans un paquet de 1 500 octets et un paquet de 500 octets, chaque paquet aura 18 octets de l’en-tête Ethernet, un total de 36 octets.

N’oubliez pas que l’augmentation de la taille MTU ne créez pas nécessairement un réseau plus efficace. Si une application envoie uniquement les paquets de 500 octets, la surcharge d’en-tête même existera si la taille MTU est 1 500 octets ou 9 000 octets. Le réseau devient plus efficace que si elle utilise des paquets plus volumineux qui est affectés par la taille MTU.

#### <a name="azure-and-vm-mtu"></a>Azure et MTU de la machine virtuelle

La taille MTU de machines virtuelles Azure de la valeur par défaut est 1 500 octets. La pile de réseau virtuel Azure va tenter de fragment d’un paquet à 1 400 octets. Mais la pile de réseau virtuel permet de paquets jusqu'à 2,006 octets lorsque l’option ne pas fragmenter est défini dans l’en-tête IP.

Notez que la pile de réseau virtuel n’est pas intrinsèquement inefficace, car il fragmente les paquets à 1 400 octets même si les machines virtuelles ont une taille MTU de 1 500. Un grand pourcentage des paquets réseau sont beaucoup plus petite que 1 400 ou 1 500 octets.

#### <a name="azure-and-fragmentation"></a>La fragmentation et Azure

Pile de réseau virtuel est configuré pour supprimer « fragments en désordre », autrement dit, les paquets fragmentés qui n’arrivent pas dans leur ordre d’origine fragmenté. Ces paquets sont perdus principalement en raison d’une faille de sécurité réseau a annoncé en novembre 2018 appelé FragmentSmack.

FragmentSmack est un défaut dans la manière dont le noyau Linux gérées réassemblage de paquets fragmentés de IPv4 et IPv6. Un attaquant distant pourrait utiliser cette faille pour des opérations de réassemblage de fragment coûteux déclencheur qui pourraient accrue du processeur et un déni de service sur le système cible.

#### <a name="tune-the-mtu"></a>Régler la taille MTU

Vous pouvez configurer une taille MTU de machine virtuelle Azure, comme vous pouvez le faire dans tout autre système d’exploitation. Mais vous devez envisager la fragmentation se produit dans Azure, décrit ci-dessus, lorsque vous configurez une taille MTU.

Nous n’Encouragez les clients à augmenter la MTU de la machine virtuelle. Cette discussion est censée expliquent en détail comment Azure implémente MTU et effectue la fragmentation.

> [!IMPORTANT]
>Augmenter la taille MTU n’est pas connu pour améliorer les performances et peut avoir un effet négatif sur les performances de l’application.
>
>

#### <a name="large-send-offload"></a>Déchargement d’envoi important

Déchargement d’envoi important (LSO) peut améliorer les performances réseau en déchargeant la segmentation des paquets à la carte Ethernet. Lorsque LSO est activée, la pile TCP/IP crée un paquet TCP volumineux et l’envoie à la carte Ethernet pour la segmentation avant de le transférer. L’avantage de LSO est qu’il peut libérer le processeur à partir de la segmentation des paquets dans des tailles qui sont conformes à la taille MTU et que le traitement à l’interface Ethernet où elle est effectuée dans le matériel de déchargement. Pour en savoir plus sur les avantages de LSO, consultez [envoi important de prendre en charge le déchargement](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Lorsque LSO est activé, les utilisateurs Azure peuvent afficher tailles d’image de grande taille lorsqu’ils effectuent les captures de paquets. Ces tailles d’image volumineux peuvent entraîner des clients à comprendre la fragmentation se produit ou qu’une MTU agrandie est utilisée lorsqu’il n’est pas. Avec LSO, la carte Ethernet peut publier une plus grande taille de segment maximale (MSS) à la pile TCP/IP afin de créer une taille de paquet TCP. Cette trame non segmenté entière est ensuite transmise à la carte Ethernet et est visible dans une capture de paquets effectuée sur la machine virtuelle. Mais le paquet est divisé en plus petit nombre de frames par l’adaptateur Ethernet, en fonction de la taille MTU de la carte Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>L’évolutivité de fenêtre TCP MSS et PMTUD

#### <a name="tcp-maximum-segment-size"></a>Taille de segment maximale TCP

Taille de segment maximale TCP (MSS) est un paramètre qui limite la taille des segments TCP, ce qui évite la fragmentation des paquets TCP. Systèmes d’exploitation est en général, utilisez cette formule pour définir MSS :

`MSS = MTU - (IP header size + TCP header size)`

L’en-tête IP et l’en-tête TCP sont 20 octets ou total de 40 octets. Par conséquent, une interface avec une taille MTU de 1 500 aura une MSS 1,460. Mais le MSS est configurable.

Ce paramètre est accepté dans la négociation tridirectionnelle TCP quand une session TCP est configurée entre une source et une destination. Les deux côtés envoyer une valeur MSS, et la plus petite des deux est utilisée pour la connexion TCP.

N’oubliez pas que les MTU de la source et la destination ne sont pas les seuls facteurs qui déterminent la valeur MSS. Appareils réseau intermédiaires, tels que les passerelles VPN, y compris Azure VPN Gateway, peuvent ajuster la taille MTU indépendamment de la source et la destination pour garantir des performances réseau optimales.

#### <a name="path-mtu-discovery"></a>Découverte MTU de chemin d’accès

MSS est négocié, mais il y a peut-être pas le MSS réelle qui peut être utilisé. Il s’agit, car les autres périphériques réseau dans le chemin d’accès entre la source et la destination peuvent avoir une valeur de la taille MTU inférieure à la source et la destination. Dans ce cas, l’appareil dont MTU est plus petit que le paquet supprimera le paquet. L’appareil renverra un message ICMP Fragmentation requise (Type 3, 4 de Code) qui contient sa MTU. Ce message ICMP permet à l’hôte source afin de réduire sa taille MTU de chemin d’accès en conséquence. Le processus est appelé découverte de MTU de chemin d’accès (PMTUD).

Le processus PMTUD est inefficace et affecte les performances du réseau. Lorsque les paquets sont envoyés qui dépassent la taille MTU d’un chemin d’accès réseau, les paquets doivent être retransmis avec un MSS inférieur. Si l’expéditeur ne reçoit pas le message ICMP Fragmentation requise, peut-être en raison d’un pare-feu de réseau dans le chemin d’accès (communément appelé un *PMTUD blocage*), l’expéditeur ne sait pas il lui faut réduire notablement le MSS et sera en continu retransmettre le paquet. C’est pourquoi nous ne vous recommandons d’augmenter la MTU de la machine virtuelle Azure.

#### <a name="vpn-and-mtu"></a>MTU et VPN

Si vous utilisez des machines virtuelles qui exécutent l’encapsulation (tels que IPsec, VPN), il existe des considérations supplémentaires concernant la taille de paquet et MTU. Réseaux privés virtuels ajouter davantage d’en-têtes des paquets, ce qui augmente la taille de paquet et nécessite un plus petit MSS.

Pour Azure, nous vous recommandons de définir clamping du MSS TCP à 1 350 octets et de tunnel interface MTU de 1 400. Pour plus d’informations, consultez le [VPN des appareils et la page des paramètres IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latence, temps d’aller-retour et l’évolutivité de fenêtre TCP

#### <a name="latency-and-round-trip-time"></a>Temps de latence et aller-retour

Latence du réseau est régie par la vitesse de la lumière sur un réseau de fibre optique. Débit du réseau du protocole TCP est également déployer efficacement régi par le temps d’aller-retour (RTT) entre deux périphériques réseau.

| | | | |
|-|-|-|-|
|**Itinéraire**|**Distance**|**Heure à sens unique**|**RTT**|
|New York à San Francisco|4,148 km|21 ms|42 ms|
|New York à Londres|5,585 km|28 ms|56 ms|
|New York à Sydney|15,993 km|80 ms|160 ms|

Ce tableau indique la distance linéaire entre deux emplacements. Dans les réseaux, la distance est généralement plus longue que la distance linéaire. Voici une formule pour calculer le temps RTT minimale comme régie par la vitesse de la lumière simple :

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Vous pouvez utiliser 200 de la vitesse de propagation. Il s’agit de la distance, en mètres, que la lumière est transmis en 1 milliseconde.

Prenons New York à San Francisco comme un exemple. La distance linéaire est 4,148 km. Entrez cette valeur dans l’équation, nous obtenons les éléments suivants :

`Minimum RTT = 2 * (4,148 / 200)`

La sortie de l’équation est exprimée en millisecondes.

Si vous souhaitez obtenir les meilleures performances réseau, l’option logique consiste à sélectionner les destinations de la distance la plus courte entre eux. Vous devez également concevoir votre réseau virtuel pour optimiser le chemin d’accès du trafic et de réduire la latence. Pour plus d’informations, consultez la section « Considérations de conception de réseau » de cet article.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Effets de temps de latence et aller-retour sur TCP

Temps d’aller-retour a un effet direct sur le débit TCP maximal. Dans le protocole TCP, *taille de la fenêtre* est la quantité maximale de trafic qui peut être envoyé via une connexion TCP avant que l’expéditeur doit recevoir l’accusé de réception du destinataire. Si le TCP MSS est défini sur 1,460 et la taille de fenêtre TCP est définie à 65 535, l’expéditeur peut envoyer des 45 paquets avant qu’il doit recevoir l’accusé de réception du destinataire. Si l’expéditeur ne vous retrouverez pas accusé de réception, il sera retransmettre les données. Voici la formule :

`TCP window size / TCP MSS = packets sent`

Dans cet exemple, 65 535 / 1,460 est arrondi jusqu'à 45.

Cet état « en attente d’accusé de réception », un mécanisme pour garantir la remise fiable des données, est la cause de RTT affecter le débit TCP. Plus l’expéditeur attend un accusé de réception, plus il doit attendre avant d’envoyer plus de données.

Voici la formule pour calculer le débit maximal d’une connexion TCP unique :

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Le tableau suivant répertorie les mégaoctets maximum / débit par seconde d’une connexion TCP unique. (Pour une meilleure lisibilité, en mégaoctets est utilisé pour l’unité de mesure).

| | | | |
|-|-|-|-|
|**Taille de fenêtre TCP (octets)**|**Latence du temps RTT (ms)**|**Maximale en mégaoctets par seconde de débit**|**Maximale mégabits par seconde de débit**|
|65 535|1|65.54|524.29|
|65 535|30|2.18|17.48|
|65 535|60|1,09|8.74|
|65 535|90|.73|5.83|
|65 535|120|.55|4.37|

Si les paquets sont perdus, le débit maximal d’une connexion TCP sera réduit tandis que l’expéditeur retransmet les données qu’il a déjà envoyées.

#### <a name="tcp-window-scaling"></a>L’évolutivité de fenêtre TCP

L’évolutivité de fenêtre TCP est une technique qui augmente dynamiquement la taille de fenêtre TCP pour autoriser davantage de données à envoyer avant un accusé de réception est requis. Dans l’exemple précédent, les 45 paquets sera donc envoyés avant un accusé de réception a été nécessaire. Si vous augmentez le nombre de paquets qui peuvent être envoyés avant un accusé de réception est nécessaire, vous réduisez le nombre de fois où qu'un expéditeur attend un accusé de réception, ce qui augmente le débit maximal de TCP.

Le tableau suivant illustre ces relations :

| | | | |
|-|-|-|-|
|**Taille de fenêtre TCP (octets)**|**Latence du temps RTT (ms)**|**Maximale en mégaoctets par seconde de débit**|**Maximale mégabits par seconde de débit**|
|65 535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Mais la valeur d’en-tête TCP pour la taille de fenêtre TCP est uniquement 2 octets de longs, ce qui signifie que la valeur maximale pour une fenêtre de réception est de 65 535. Pour augmenter la taille de fenêtre maximale, un facteur d’échelle de fenêtre TCP a été introduit.

Le facteur d’échelle est également un paramètre que vous pouvez configurer dans un système d’exploitation. Voici la formule de calcul de la taille de fenêtre TCP à l’aide des facteurs d’échelle :

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Voici le calcul pour un facteur d’échelle de fenêtre de 3 et une taille de fenêtre de 65 535 :

`65,535 \* (2^3) = 262,140 bytes`

Un facteur d’échelle de 14 entraîne une taille de fenêtre TCP de 14 (le décalage maximal autorisé). La taille de fenêtre TCP sera 1,073,725,440 octets (8,5 Gigabit/s).

#### <a name="support-for-tcp-window-scaling"></a>Prise en charge de l’évolutivité de fenêtre TCP

Windows peut définir différents facteurs de mise à l’échelle pour différents types de connexions. (Les classes de connexions incluent le centre de données, internet et ainsi de suite.) Vous utilisez la `Get-NetTCPConnection` commande PowerShell pour afficher la fenêtre de mise à l’échelle de type de connexion :

```powershell
Get-NetTCPConnection
```

Vous pouvez utiliser la `Get-NetTCPSetting` commande PowerShell pour afficher les valeurs de chaque classe :

```powershell
Get-NetTCPSetting
```

Vous pouvez définir la taille de fenêtre TCP initiale et le facteur d’échelle TCP dans Windows à l’aide de la `Set-NetTCPSetting` commande PowerShell. Pour plus d’informations, consultez [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Ce sont les paramètres TCP efficaces pour `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Facteur d’échelle**|**Multiplicateur de mise à l’échelle**|**Formule à<br/>calculer la taille de fenêtre maximale**|
|Désactivé|Aucun|Aucun|Taille de la fenêtre|
|Restreint|4|2^4|Taille de la fenêtre * (2 ^ 4)|
|Hautement restreint|2|2^2|Taille de la fenêtre * (2 ^ 2)|
|Normal|8|2^8|Taille de la fenêtre * (2 ^ 8)|
|Expérimental|14|2^14|Taille de la fenêtre * (2 ^ 14)|

Ces paramètres sont les plus susceptibles d’affecter les performances de TCP, mais n’oubliez pas que beaucoup d’autres facteurs sur internet, en dehors du contrôle d’Azure, peut également affecter les performances de TCP.

#### <a name="increase-mtu-size"></a>Augmenter la taille MTU

Car une taille MTU supérieure signifie une plus grande MSS, vous vous demandez peut-être si l’augmentant la taille MTU peut augmenter les performances de TCP. Probablement pas. Il existe des avantages et inconvénients pour la taille du paquet simplement le trafic TCP. Comme indiqué précédemment, les facteurs les plus importants qui affectent les performances de débit TCP sont taille de fenêtre TCP, perte de paquets et durée des boucles.

> [!IMPORTANT]
> Nous ne recommandons pas que les clients Azure modifier la valeur de la taille MTU par défaut sur les machines virtuelles.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Mise en réseau accélérée et du trafic entrant

#### <a name="accelerated-networking"></a>Mise en réseau accélérée

Fonctions de réseau de machine virtuelle faisaient intensif sur l’ordinateur virtuel invité et l’hôte hyperviseur/de l’UC. Tous les paquets qui transitent via l’hôte sont traitée dans les logiciels par l’hôte du processeur, y compris toute l’encapsulation de réseau virtuel décapsulation. Donc plus le trafic qui traverse l’hôte, plus le processeur de charge. Et si l’hôte du processeur est occupé avec d’autres opérations, ce qui affectera également de débit du réseau et la latence. Azure résout ce problème avec la mise en réseau accélérée.

Mise en réseau accélérée fournit la latence du réseau ultra basse cohérente via le matériel programmable en interne d’Azure et de technologies telles que SR-IOV. Mise en réseau accélérée déplace une grande partie de la Azure défini par logiciel pile réseau désactivé les processeurs et en SmartNICs basée sur des FPGA. Cette modification permet à des applications de l’utilisateur final de récupérer les cycles de calcul, qui place une charge moindre sur la machine virtuelle, diminuant instabilité et l’incohérence dans la latence. En d’autres termes, les performances peuvent être plus déterministe.

Mise en réseau accélérée améliore les performances en permettant à l’ordinateur virtuel de contourner l’hôte et d’établir un chemin de données directement avec SmartNIC l’hôte invité. Voici quelques avantages de la mise en réseau accélérée :

- **Réduire la latence / nombre supérieur de paquets par seconde (pps)**: Suppression du commutateur virtuel à partir du chemin de données élimine le temps que consacrent des paquets dans l’hôte pour le traitement de la stratégie et augmente le nombre de paquets qui peuvent être traitées dans la machine virtuelle.

- **Instabilité réduite**: Traitement de commutateur virtuel dépend de la quantité de stratégie qui doit être appliquée et la charge de travail de l’UC qui effectue le traitement. Le déchargement des stratégies pour le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en éliminant la communication de machine virtuelle hôte et toutes les interruptions de logiciel et les changements de contexte.

- **Réduit l’utilisation du processeur**: en contournant le commutateur virtuel de l'hôte, le processeur utilise moins de ressources pour traiter le trafic réseau.

Pour utiliser la mise en réseau accélérée, vous devez activer explicitement sur chaque ordinateur virtuel souhaité. Consultez [créer une machine virtuelle Linux avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) pour obtenir des instructions.

#### <a name="receive-side-scaling"></a>Trafic entrant

Mise à l’échelle (RSS) est une technologie de pilote de réseau qui distribue la réception du trafic réseau plus efficacement en distribuant le traitement de réception entre plusieurs processeurs dans un système multiprocesseur de côté de la réception. En termes simples, RSS permet à un système à traiter plus de trafic reçu, car elle utilise tous les processeurs disponibles au lieu d’un. Pour obtenir une description plus technique de RSS, consultez [Introduction à du trafic entrant](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Pour obtenir les meilleures performances lors de la mise en réseau accélérée est activée sur une machine virtuelle, vous devez activer RSS. RSS peut également fournir les avantages sur des machines virtuelles qui n’utilisent pas de mise en réseau accélérée. Pour une vue d’ensemble de la façon de déterminer si le partage RSS est activé et comment l’activer, consultez [optimiser un débit réseau des machines virtuelles Azure](http://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT et TIME_WAIT sera

TCP TIME_WAIT est un autre paramètre commun qui affecte les performances des applications et de réseau. Sur les machines virtuelles actives qui ouvrant et fermant de sockets, en tant que clients ou en tant que serveurs (Source IP:Source Port + Port de Destination IP:Destination), pendant le fonctionnement normal du TCP, un socket donné peut arriver dans un état TIME_WAIT pendant une longue période. L’état TIME_WAIT est destiné à autoriser toutes les données supplémentaires doivent être remises sur un socket avant sa fermeture. Par conséquent, les piles TCP/IP empêchent généralement la réutilisation d’un socket en supprimant des paquets de TCP SYN du client en mode silencieux.

La durée pendant laquelle qu'un socket est dans TIME_WAIT est configurable. Elle peut aller de 30 secondes à 240 secondes. Les sockets sont une ressource limitée, et le nombre de sockets qui peut être utilisé à un moment donné est configurable. (Le nombre de sockets disponibles est généralement environ 30 000). Si les sockets disponibles sont consommées, ou si les clients et serveurs ont des paramètres TIME_WAIT, et une machine virtuelle tente de réutiliser un socket dans un état TIME_WAIT, les nouvelles connexions échouera en tant que TCP SYN silencieusement les paquets sont abandonnés.

La valeur de la plage de ports pour les sockets sortants est généralement configurable dans la pile TCP/IP du système d’exploitation. La même chose est vrai pour les paramètres TCP TIME_WAIT et réutilisation du socket. Modification de ces numéros pouvant potentiellement améliorer l’évolutivité. Toutefois, selon la situation, ces modifications pourraient provoquer des problèmes d’interopérabilité. Il se peut que vous devez être prudent si vous modifiez ces valeurs.

Vous pouvez utiliser les sera TIME_WAIT pour contourner cette limitation de mise à l’échelle. Sera de TIME_WAIT permet à un socket d’être réutilisé dans certaines situations, comme lorsque le numéro de séquence dans le paquet IP de la nouvelle connexion dépasse le numéro de séquence du dernier paquet de la connexion précédente. Dans ce cas, le système d’exploitation permettra la nouvelle connexion est établie (il acceptera le nouveaux SYN/ACK) et force fermer la connexion précédente qui était dans un état TIME_WAIT. Cette fonctionnalité est prise en charge sur les machines virtuelles Windows dans Azure. Pour en savoir plus sur la prise en charge dans les autres machines virtuelles, vérifiez auprès du fournisseur du système d’exploitation.

Pour en savoir plus sur la configuration des paramètres de TCP TIME_WAIT et la plage de ports source, consultez [paramètres qui peuvent être modifiés pour améliorer les performances réseau](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Facteurs de réseau virtuel qui peuvent affecter les performances

### <a name="vm-maximum-outbound-throughput"></a>Débit sortant maximal de machine virtuelle

Azure fournit une variété de tailles de machine virtuelle et les types, chacun avec une combinaison différente de capacités de performances. Une de ces fonctionnalités est d’un débit réseau (ou la bande passante), qui est mesurée en mégabits par seconde (Mbits/s). Étant donné que les ordinateurs virtuels sont hébergés sur du matériel partagé, la capacité du réseau doit être partagés équitablement entre les machines virtuelles utilisant le même matériel. Les machines virtuelles plus grandes sont allouées à plus de bande passante que les machines virtuelles plus petites.

La bande passante réseau allouée à chaque machine virtuelle est mesurée en fonction du trafic sortant de la machine virtuelle. L’ensemble du trafic réseau quittant la machine virtuelle est mesuré en fonction de la limite d’allocation, quelle que soit la destination. Par exemple, si une machine virtuelle a une limite de 1 000 Mbits/s, cette limite s’applique si le trafic sortant est destiné à une autre machine virtuelle dans le même réseau virtuel ou l’un en dehors d’Azure.

Le trafic en entrée n’est pas compté ou limité directement. Mais il existe d’autres facteurs, tels que les limites de processeur et de stockage, qui peuvent affecter les capacité de la machine virtuelle pour traiter les données entrantes.

Mise en réseau accélérée est conçu pour améliorer les performances du réseau, notamment la latence, le débit et l’utilisation du processeur. Mise en réseau accélérée peut améliorer le débit d’une machine virtuelle, mais cela passe uniquement jusqu'à la bande passante allouée à la machine virtuelle.

Machines virtuelles Azure possèdent au moins une interface réseau qui leur sont attachée. Ils peuvent avoir plusieurs. La bande passante allouée à une machine virtuelle est la somme de tout le trafic sortant pour toutes les interfaces réseau attachées à la machine. En d’autres termes, la bande passante est allouée sur une base par machine virtuelle, quel que soit le nombre d’interfaces réseau est connecté à l’ordinateur.

Débit sortant attendu et le nombre d’interfaces réseau pris en charge par chaque taille de machine virtuelle sont détaillés dans [machines virtuelles de tailles pour Windows dans Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour afficher le débit maximal, sélectionnez un type, par exemple **à usage général**, puis recherchez la section relative à la série de taille sur la page résultante (par exemple, « série Dv2 »). Pour chaque série, il existe une table qui fournit les spécifications de mise en réseau dans la dernière colonne, intitulée « cartes réseau Max / attendue de la bande passante du réseau (Mbits/s). »

Cette limite de débit s’applique à la machine virtuelle. Débit n’est pas affecté par ces facteurs :

- **Nombre d’interfaces réseau**: La limite de bande passante s’applique à la somme de tout le trafic sortant à partir de la machine virtuelle.

- **Mise en réseau accélérée**: Toutefois, cette fonctionnalité peut être utile pour atteindre la limite publiée, elle ne change pas la limite.

- **Destination du trafic**: Toutes les destinations sont pris en compte la limite de sortie.

- **Protocole** : Tout le trafic sortant sur tous les protocoles est comptabilisée dans la limite.

Pour plus d’informations, consultez [la bande passante du réseau de machine virtuelle](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considérations sur les performances

Comme indiqué dans cet article, facteurs sur internet et en dehors du contrôle d’Azure peuvent affecter les performances du réseau. Voici quelques-unes de ces facteurs :

- **Latence**: Le temps d’aller-retour entre deux destinations peuvent être affecté par des problèmes sur les réseaux intermédiaires, par le trafic qui ne prend pas le chemin d’accès distance « le plus court » et par les chemins d’homologation non optimales.

- **Perte de paquets**: Perte de paquets peut être dû de congestion du réseau, des problèmes de chemin d’accès physique et les périphériques réseau peu performantes.

- **Taille MTU/Fragmentation**: La fragmentation sur le chemin peut entraîner des retards d’arrivée des données ou dans les paquets arrivant en désordre, ce qui peuvent affecter la remise de paquets.

Détermination d’itinéraire est un bon outil pour mesurer les caractéristiques de performances réseau (par exemple, la perte de paquets et la latence) le long de chaque chemin d’accès réseau entre un appareil source et un périphérique de destination.

### <a name="network-design-considerations"></a>Considérations de conception de réseau

Ainsi que les considérations présentées plus haut dans cet article, la topologie d’un réseau virtuel peut affecter les performances du réseau. Par exemple, une conception hub-and-spoke que retours à charge du trafic dans le monde entier à un réseau virtuel unique-hub introduira la latence du réseau, ce qui affectera les performances globales du réseau.

Le nombre de périphériques réseau qui traverse de trafic réseau peut également affecter la latence globale. Par exemple, dans une conception hub-and-spoke, si le trafic transite par l’appliance virtuelle réseau spoke et une appliance virtuelle hub avant en transit à internet, les appliances virtuelles réseau peuvent introduire une latence.

### <a name="azure-regions-virtual-networks-and-latency"></a>Régions Azure, de réseaux virtuels et de latence

Régions Azure sont constituées de plusieurs centres de données qui existent dans une zone géographique générale. Ces centres de données peut ne pas être physiquement en regard de chacun des autres. Dans certains cas, ils sont classés par autant que 10 kilomètres. Le réseau virtuel est un segment de recouvrement logique sur le réseau de centre de données physique Azure. Un réseau virtuel n’implique pas n’importe quelle topologie de réseau spécifique au sein du centre de données.

Par exemple, deux machines virtuelles qui se trouvent dans le même réseau virtuel et le sous-réseau peuvent être dans différents racks, lignes ou même centres de données. Ils pourraient être séparés en mètres de câble à fibre optique ou en kilomètres de câble à fibre optique. Cette variation peut introduire une latence variable (différence à quelques millisecondes) entre différentes machines virtuelles.

L’emplacement géographique des machines virtuelles et la latence résultant potentielle entre deux machines virtuelles, peuvent être influencées par la configuration des groupes à haute disponibilité et des Zones de disponibilité. Mais la distance entre les centres de données dans une région spécifiques à une région et principalement influencé par topologie centre de données dans la région.

### <a name="source-nat-port-exhaustion"></a>Épuisement du port source NAT

Un déploiement dans Azure peut communiquer avec les points de terminaison en dehors d’Azure sur l’internet public et/ou dans l’espace IP public. Quand une instance lance une connexion sortante, Azure mappe dynamiquement l’adresse IP privée à une adresse IP publique. Une fois que Azure crée ce mappage, le trafic de retour pour le flux d’origine sortant peut également atteindre l’adresse IP privée d'où provient le flux.

Pour chaque connexion sortante, l’équilibreur de charge Azure doit conserver ce mappage pour un certain temps. Avec la nature multi-locataires de Azure, la maintenance de ce mappage pour chaque flux sortant pour chaque machine virtuelle peut consommer beaucoup de ressources. Par conséquent, il existe des limites qui sont définies et selon la configuration du réseau virtuel Azure. Ou, pour indiquer que plus précisément, une machine virtuelle Azure peut uniquement effectuer un certain nombre de connexions sortantes à un moment donné. Lorsque ces limites sont atteintes, la machine virtuelle ne sont pas être en mesure d’établir des connexions sortantes plus.

Mais ce comportement est configurable. Pour plus d’informations sur la SNAT et SNAT port épuisement, consultez [cet article](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Mesurer les performances réseau sur Azure

Un nombre des valeurs maximales de performances dans cet article est lié à la latence du réseau / aller-retour (RTT) de temps entre deux machines virtuelles. Cette section fournit des suggestions pour le test de latence/RTT et comment tester les performances de TCP et les performances réseau des machines virtuelles. Vous pouvez paramétrer et testez les performances les valeurs de TCP/IP et réseau abordés précédemment en utilisant les techniques décrites dans cette section. Vous pouvez brancher latence MTU, MSS, valeurs et de fenêtre de taille dans les calculs fournis précédemment et comparer les limite maximale théorique aux valeurs réelles que vous observez pendant le test.

### <a name="measure-round-trip-time-and-packet-loss"></a>Temps d’aller-retour de mesure et de perte de paquets

Performances de TCP s’appuie sur la durée des boucles et la perte de paquets. L’utilitaire PING disponible dans Windows et Linux est le moyen le plus simple pour mesurer la perte de paquets et la durée des boucles. La sortie de la commande PING affiche la latence minimale/maximale/moyenne entre une source et de destination. Il affiche également la perte de paquets. Test PING utilise le protocole ICMP par défaut. Vous pouvez utiliser PsPing pour tester TCP RTT. Pour plus d’informations, consultez [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mesurer le débit réel d’une connexion TCP

NTttcp est un outil pour tester les performances de TCP de Linux ou de la machine virtuelle Windows. Vous pouvez modifier différents paramètres TCP et ensuite tester les avantages à l’aide de NTttcp. Pour plus d’informations, consultez ces ressources :

- [Bande passante/débit (NTttcp) de test](https://aka.ms/TestNetworkThroughput)

- [Utilitaire de NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mesurer la bande passante réelle d’une machine virtuelle

Vous pouvez tester les performances de différents types de machines virtuelles, accélérée de mise en réseau et ainsi de suite, à l’aide d’un outil appelé iPerf. iPerf est également disponible sur Linux et Windows. iPerf peut utiliser TCP ou UDP pour tester le débit global du réseau. tests de débit TCP iPerf sont influencées par les facteurs décrits dans cet article (par exemple, la latence et la durée des boucles). Par conséquent, UDP peut offrent de meilleurs résultats si vous souhaitez uniquement tester le débit maximal.

Pour plus d’informations, voir les articles suivants :

- [Résolution des problèmes de performances du réseau Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Comment valider un débit VPN sur un réseau virtuel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Détecter les comportements TCP inefficaces

Dans les captures de paquets, les clients Azure peuvent voir des paquets TCP avec des indicateurs TCP (SACK, DUP ACK, retransmission et retransmission rapide) qui peut indiquer des problèmes de performances réseau. Ces paquets indiquent plus précisément les inefficacités réseau qui résultent de la perte de paquets. Mais perte de paquets n’est pas nécessairement due à des problèmes de performances Azure. Problèmes de performances peut résulter de problèmes d’application, les problèmes de système d’exploitation ou d’autres problèmes qui ne peuvent pas être liés directement à la plateforme Azure.

En outre, n’oubliez pas que certains retransmission et les accusés de réception en double sont normales sur un réseau. Les protocoles TCP ont été conçus pour être fiables. Preuve de ces paquets TCP dans une capture de paquets n’indique pas forcément un problème réseau systémiques, sauf s’ils figurent excessive.

Cependant, ces types de paquets sont des indications que le débit TCP n’atteint pas ses performances maximales, pour les raisons mentionnées dans d’autres sections de cet article.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris TCP/IP réglage des performances des machines virtuelles Azure, vous souhaiterez en savoir plus sur d’autres considérations pour [planification de réseaux virtuels](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) ou [en savoir plus sur la connexion et la configuration de réseaux virtuels ](https://docs.microsoft.com/en-us/azure/virtual-network/).
