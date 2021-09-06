---
title: Architecture réseau de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez l’architecture réseau pour le déploiement de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5add931f71dfdb5034e614b3d6c3ddc8703293a2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461569"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architecture réseau de SAP HANA (grandes instances)

Cet article décrit l’architecture réseau pour le déploiement de SAP HANA sur Azure (grandes instances) (également appelée BareMetal Infrastructure). 

L’architecture des services réseau Azure est un composant clé de la réussite du déploiement des applications SAP sur la grande instance HANA. Généralement, les déploiements de SAP HANA sur Azure (grandes instances) impliquent un paysage SAP plus vaste. Ils incluent probablement plusieurs solutions SAP avec des tailles de bases de données, une consommation des ressources du processeur et une utilisation de la mémoire pouvant varier. 

Il est probable que certains systèmes informatiques ne trouvent pas encore dans Azure. Votre paysage SAP peut également être hybride. Votre système de gestion de base de données (SGBD) et votre application SAP peuvent utiliser un mélange de NetWeaver, S/4HANA et SAP HANA. Votre application SAP peut même utiliser un autre SGBD.

Azure offre différents services qui vous permettent d’exécuter des systèmes SGBD, NetWeaver et S/4HANA dans Azure. Azure offre une technologie réseau qui lui permet de se présenter comme un centre de données virtuel pour vos déploiements de logiciels locaux. La fonctionnalité réseau Azure comprend les éléments suivants : 

- Des réseaux virtuels Azure connectés au circuit [ExpressRoute](https://azure.microsoft.com/services/expressroute/) qui se connecte à vos ressources réseau locales.
- Un circuit ExpressRoute se connectant en local à Azure avec une bande passante minimale de [1 Gbit/s](https://azure.microsoft.com/pricing/details/expressroute/). Ce circuit permet une bande passante adéquate pour le transfert de données entre les systèmes locaux et les systèmes qui s’exécutent sur des machines virtuelles (VM). Il permet également à la bande passante adéquate de se connecter aux systèmes Azure à partir d’utilisateurs locaux.
- Tous les systèmes SAP dans Azure configurés dans des réseaux virtuels pour communiquer entre eux.
- Le répertoire Active Directory et le DNS hébergés en local sont étendus à Azure via ExpressRoute, en local. Ils peuvent également s’exécuter entièrement dans Azure.

En cas d’intégration de Grandes instances HANA dans la structure réseau du centre de données Azure, la technologie Azure ExpressRoute est également utilisée.


> [!NOTE] 
> Un seul abonnement Azure ne peut être lié qu’à un seul locataire dans un tampon de Grande instance HANA situé dans une région Azure spécifique. À l’inverse, un seul locataire de tampon de Grande instance HANA ne peut être lié qu’à un seul abonnement Azure. Cette exigence est cohérente avec d’autres objets facturables dans Azure.

Si SAP HANA sur Azure (Grandes instances) est déployé dans plusieurs régions Azure, un locataire séparé est déployé dans le tampon de Grande instance HANA. Vous pouvez exécuter ces deux locataires dans le même abonnement Azure tant que ces instances font partie du même paysage SAP. 

> [!IMPORTANT] 
> Seule la méthode de déploiement Azure Resource Manager est prise en charge avec SAP HANA sur Azure (Grandes instances).

## <a name="extra-virtual-network-information"></a>Informations complémentaires relatives au réseau virtuel

Pour connecter un réseau virtuel à ExpressRoute, une passerelle Azure ExpressRoute doit être créée. Pour plus d’informations, voir [À propos des passerelles ExpressRoute pour ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md). 

Une passerelle Azure ExpressRoute est utilisée avec ExpressRoute sur une infrastructure située hors d’Azure ou sur un tampon de Grande instance Azure. Vous pouvez connecter la passerelle Azure ExpressRoute à un maximum de quatre circuits ExpressRoute, mais uniquement si ces connexions proviennent de routeurs Microsoft Enterprise Edge (MSEE) distincts. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md). 

> [!NOTE] 
> Le débit maximal pouvant être atteint avec une passerelle ExpressRoute est de 10 Gbits/s, à l’aide d’une connexion ExpressRoute. La copie de fichiers entre une machine virtuelle résidant dans un réseau virtuel et un système local (en tant que flux de copie unique) n’atteint pas le débit total des différentes références SKU des passerelles. Pour tirer parti de l’intégralité de la bande passante de la passerelle ExpressRoute, utilisez plusieurs flux ou copiez différents fichiers dans des flux parallèles d’un seul fichier.


## <a name="networking-architecture-for-hana-large-instance"></a>Architecture de mise en réseau pour la grande instance HANA
L’architecture de mise en réseau pour grandes instances HANA peut être divisée en quatre parties :

- Mise en réseau locale et connexion ExpressRoute à Azure. Cette partie est votre domaine (celui du client) et est connectée à Azure via ExpressRoute. Ce circuit ExpressRoute est entièrement payé par vous. La bande passante doit être suffisante pour gérer le trafic réseau entre vos ressources locales et la région Azure avec laquelle vous vous connectez. Consultez la partie inférieure droite de la figure suivante.
- Services réseau Azure, comme indiqué précédemment, avec des réseaux virtuels qui ont besoin de l’ajout de passerelles ExpressRoute. Pour cette partie, vous devez créer les conceptions appropriées pour répondre à vos exigences en matière d’application, de sécurité et de conformité. Déterminez s’il convient d’utiliser des grandes instances HANA compte tenu du nombre de réseaux virtuels et de références SKU de passerelle Azure à choisir. Consultez la partie supérieure droite de la figure.
- Connectivité de votre grande instance HANA via ExpressRoute dans Azure. Cette partie est déployée et gérée par Microsoft. Il vous suffit de fournir certaines plages d’adresses IP après le déploiement de vos ressources dans la grande instance HANA et la connexion du circuit ExpressRoute aux réseaux virtuels. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md). Vous ne payez aucun frais supplémentaire pour la connectivité entre la structure réseau du centre de données Azure et les unités de Grande instance HANA.
- Mise en réseau dans le tampon de Grande instance HANA, essentiellement transparente pour vous.

![Réseau virtuel connecté à SAP HANA sur Azure (grandes instances) et en local](./media/hana-overview-architecture/image1-architecture.png)

Les deux conditions suivantes sont toujours valables, même si vous utilisez des grandes instances Hana :
- Vos ressources locales doivent se connecter via ExpressRoute à Azure.
- Vous avez besoin d’un ou de plusieurs réseaux virtuels qui exécutent vos machines virtuelles. Ces machines virtuelles hébergent la couche d’application qui se connecte aux instances HANA hébergées dans des grandes instances HANA.

Les différences dans les déploiements SAP dans Azure sont les suivantes :

- Les grandes instances HANA de votre locataire sont connectées via un autre circuit ExpressRoute à vos réseaux virtuels. Les circuits ExpressRoute entre les réseaux locaux et le réseau virtuel Azure, ainsi qu’entre les réseaux virtuels Azure et les Grandes instances HANA ne partagent pas les mêmes routeurs. Leurs conditions de charge restent séparées.
- Le profil de charge de travail est de nature différente entre la couche d’application SAP et la Grande instance HANA. SAP HANA génère de nombreuses petites requêtes et rafales comme les transferts de données (jeux de résultats) dans la couche d’application.
- L’architecture d’application SAP est plus sensible à la latence du réseau que les scénarios classiques dans lesquels les données sont échangées entre les machines locales et Azure.
- La passerelle Azure ExpressRoute possède au moins deux connexions ExpressRoute. Un circuit est connecté à partir depuis l’emplacement local et un autre est connecté depuis une Grande instance HANA. Cette configuration ne laisse de la place que pour deux autres circuits de différents MSEE pour se connecter à la passerelle ExpressRoute. Cette restriction est indépendante de l’utilisation d’ExpressRoute FastPath. Tous les circuits connectés partagent la bande passante maximale pour les données entrantes en provenance de la passerelle ExpressRoute.

Avec la Révision 3 des tampons de Grande instance HANA, la latence du réseau entre les machines virtuelles et les unités de grande instance HANA peut être supérieure aux latences standard aller-retour sur un réseau de machine virtuelle à machine virtuelle. En fonction de la région Azure, les valeurs mesurées peuvent dépasser une latence aller-retour de 0,7 ms, classée en dessous de la moyenne dans [Remarque SAP n° 1100926 - FAQ : performances du réseau](https://launchpad.support.sap.com/#/notes/1100926/E). Selon la région Azure et l’outil utilisé pour mesurer la latence aller-retour entre une machine virtuelle Azure et une grande instance SAP HANA, la latence peut être de 2 millisecondes maximum. Toutefois, les clients peuvent déployer avec succès des applications SAP de production basées sur SAP HANA sur des Grandes instances SAP HANA. Assurez-vous d’avoir testé minutieusement vos processus métier avec les Grandes instances HANA Azure. Une nouvelle fonctionnalité, nommée ExpressRoute FastPath, permet de réduire considérablement la latence réseau entre les Grandes instances HANA et les machines virtuelles de la couche d’application dans Azure (voir ci-dessous). 

La Révision 4 des tampons de Grande instance HANA améliore la latence du réseau entre les machines virtuelles Azure déployées à proximité du tampon de Grande instance HANA. La latence appartient à la classification moyenne ou la dépasse, comme indiqué dans [Remarque SAP n° 1100926 - FAQ : performances du réseau](https://launchpad.support.sap.com/#/notes/1100926/E) si Azure ExpressRoute FastPath est configuré (voir ci-dessous). 

Pour déployer des machines virtuelles Azure sur des Grandes instances HANA de la Révision 4, vous devez appliquer des [groupes de placements de proximité Azure](../../co-location.md). Les groupes de placement de proximité peuvent être utilisés pour localiser la couche d’application SAP dans le même centre de données Azure que les Grandes instances hébergées de Révision 4. Pour plus d’informations, consultez [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP](sap-proximity-placement-scenarios.md).

Afin d’offrir une latence réseau déterministe entre les machines virtuelles Azure et une Grande instance HANA, l’utilisation de la référence SKU de la passerelle ExpressRoute est essentielle. Contrairement aux modèles de trafic entre machines locales et machines virtuelles, les modèles de trafic entre les machines virtuelles et les grandes instances HANA peut développer des rafales courtes mais élevées de requêtes et de volumes de données. Pour gérer ces rafales, nous vous recommandons fortement d’utiliser la référence SKU de passerelle UltraPerformance. Pour les références SKU de Grande instance HANA de classe Type II, l’utilisation de la référence SKU de la passerelle UltraPerformance en tant que passerelle ExpressRoute est obligatoire.

> [!IMPORTANT] 
> Étant donné le trafic réseau global entre les couches Base de données et Application SAP, seules les références SKU de passerelle HighPerformance et UltraPerformance pour les réseaux virtuels sont prises en charge pour la connexion à SAP HANA sur Azure (grandes instances). Pour les références SKU de Grande instance HANA de Type II, seule la référence SKU de passerelle UltraPerformance est prise en charge en tant que passerelle ExpressRoute. Des exceptions s’appliquent en cas d’utilisation d’ExpressRoute FastPath (voir ci-dessous).

### <a name="expressroute-fastpath"></a>ExpressRoute FastPath
En mai 2019, nous avons publié ExpressRoute FastPath. FastPath réduit la latence entre les Grandes instances HANA et les réseaux virtuels Azure qui hébergent les machines virtuelles d’application SAP. Avec FastPath, les flux de données entre les machines virtuelles et les Grandes instance HANA ne sont plus acheminés via la passerelle ExpressRoute. Les machines virtuelles affectées dans les sous-réseaux du réseau virtuel Azure communiquent directement avec le routeur de périphérie d’entreprise dédié. 

> [!IMPORTANT] 
> ExpressRoute FastPath nécessite que les sous-réseaux exécutant les machines virtuelles d’application SAP se trouvent dans le réseau virtuel Azure qui est connecté aux Grandes instances HANA. Les machines virtuelles situées dans des réseaux virtuels Azure appairés au réseau virtuel Azure connecté directement aux unités de Grande instance HANA ne bénéficient pas d’ExpressRoute FastPath. En conséquence, pour les conceptions de réseau virtuel hub-and-spoke (en étoile) typiques, où les circuits ExpressRoute se connectent à un réseau virtuel faisant office de hub, et où les réseaux virtuels contenant la couche Application SAP (spokes) sont appairés, l’optimisation par ExpressRoute FastPath ne fonctionne pas. ExpressRoute FastPath ne prend pas non plus en charge les règles d’acheminement définies par l’utilisateur (UDR). Pour plus d’informations, voir [Passerelle de réseau virtuel ExpressRoute et FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


Pour plus d’informations sur la configuration d’ExpressRoute FastPath, voir [Connecter un réseau virtuel à de Grandes instances HANA](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> Une passerelle ExpressRoute UltraPerformance est nécessaire pour le fonctionnement d’ExpressRoute FastPath.


## <a name="single-sap-system"></a>Système SAP unique

L’infrastructure locale présentée précédemment est connectée via ExpressRoute dans Azure. Le circuit ExpressRoute se connecte à un MSEE. Pour plus d’informations, consultez [Vue d’ensemble technique d’ExpressRoute](../../../expressroute/expressroute-introduction.md). Une fois l’itinéraire établi, il se connecte au routeur principal Azure.

> [!NOTE] 
> Pour exécuter des paysages SAP dans Azure, connectez le routeur de périphérie d’entreprise le plus proche de la région Azure dans le paysage SAP. Les tampons de Grande instance HANA sont connectés via les routeurs dédiés de périphérie d’entreprise afin de réduire la latence réseau entre les machines virtuelles dans Azure IaaS et les tampons de Grande instance HANA.

La passerelle ExpressRoute pour les machines virtuelles qui hébergent des instances d’application SAP est connectée à un circuit ExpressRoute qui se connecte au réseau local. Le même réseau virtuel est connecté à un routeur de périphérie d’entreprise distinct. Ce routeur de périphérie est dédié à la connexion aux tampons de grande instance. Là encore, avec FastPath, le flux de données entre les Grandes instances HANA et les machines virtuelles de la couche d’application SAP n’est pas acheminé via la passerelle ExpressRoute. Cette configuration réduit la latence aller-retour du réseau.

Ce système est un exemple simple d’un système SAP unique. La couche Application SAP est hébergée dans Azure. La base de données SAP HANA s’exécute sur SAP HANA sur Azure (grandes instances). Nous supposons que le débit de 2 ou 10 Gbit/s de la bande passante de la passerelle ExpressRoute ne constitue pas un goulot d’étranglement.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Plusieurs systèmes SAP ou grands systèmes SAP

Si vous déployez plusieurs systèmes SAP ou de grands systèmes SAP se connectant à SAP HANA (Grandes instances), le débit de la passerelle ExpressRoute peut constituer un goulot d’étranglement. Dans ce cas, divisez les couches d’application en plusieurs réseaux virtuels. Vous pouvez aussi diviser les couches d’application si vous souhaitez isoler les systèmes dédiés à la production et ceux qui ne le sont pas dans des réseaux virtuels Azure distincts. 

Vous pouvez créer un réseau virtuel spécial qui se connecte aux Grandes instances HANA dans les cas suivants :

- Exécution de sauvegardes directement à partir des instances HANA dans une Grande instance HANA vers une machine virtuelle dans Azure hébergeant des partages NFS.
- Copie de sauvegardes volumineuses ou d’autres fichiers des Grandes instances HANA vers l’espace disque géré dans Azure.

Utilisez un réseau virtuel distinct pour héberger les machines virtuelles qui gèrent le stockage pour le transfert en masse de données entre les Grandes instances HANA et Azure. Ce procédé permet d’éviter le transfert de données ou de fichiers volumineux depuis de Grandes instances HANA vers Azure sur la passerelle ExpressRoute qui sert les machines virtuelles exécutant la couche d’application SAP. 

Pour une architecture réseau plus extensible :

- Utilisez plusieurs réseaux virtuels pour une couche Application SAP unique plus grande.
- Déployez un réseau virtuel distinct pour chaque système SAP déployé, plutôt que de combiner ces systèmes SAP en sous-réseaux séparés dans le même réseau virtuel.

    Le diagramme suivant montre une architecture de mise en réseau plus extensible pour SAP HANA sur Azure (grandes instances) :

![Déployer la couche Application SAP sur plusieurs réseaux virtuels](./media/hana-overview-architecture/image4-networking-architecture.png)

En fonction des règles et restrictions que vous souhaitez appliquer entre les différents réseaux virtuels hébergeant des machines virtuelles de différents systèmes SAP, vous devez appairer ces réseaux virtuels. Pour plus d’informations sur le peering du réseau virtuel, consultez [Peering de réseaux virtuels](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Routage dans Azure

Pour un déploiement par défaut, trois points importants sont à prendre en compte en matière de routage pour SAP HANA sur Azure (Grandes instances) :

- L’accès à SAP HANA sur Azure (grandes instances) se fait uniquement via des machines virtuelles Azure dans la connexion ExpressRoute dédiée, non directement via le système local. L’accès direct du réseau local aux unités de grande instance HANA, tel que fourni par Microsoft, n’est pas possible immédiatement. Les restrictions de routage transitives sont dues à l’architecture réseau Azure actuelle utilisée pour les Grandes instances SAP HANA. Certains clients d’administration et toutes les applications nécessitant un accès direct, tel que le gestionnaire de solutions SAP s’exécutant en local, ne peuvent pas se connecter à la base de données SAP HANA. Pour les exceptions, voir la section suivante [Routage direct vers de Grandes instances HANA](#direct-routing-to-hana-large-instances).

- Si vous avez des unités de Grande instance HANA déployées dans deux régions Azure différentes à des fins de récupération d’urgence, les mêmes restrictions de routage temporaire s’appliquent que dans le passé. En d’autres termes, les adresses IP d’une Grande instance HANA dans une région (par exemple, USA Ouest) n’étaient pas routées vers une Grande instance HANA déployée dans une autre région (par exemple, USA Est). Cette restriction est indépendante de l’utilisation du peering de réseau Azure entre les différentes régions ou des interconnexions de circuits ExpressRoute qui connectent les Grandes instances HANA aux réseaux virtuels. Pour obtenir une représentation graphique, consultez la figure de la section [Utiliser des unités de grande instance HANA dans plusieurs régions](#use-hana-large-instance-units-in-multiple-regions). Cette restriction, qui résultait de l’architecture déployée, empêchait l’utilisation immédiate de la réplication du système HANA pour la récupération d’urgence. Pour les modifications récentes, à nouveau, consultez la section [Utiliser des unités de Grande instance HANA dans plusieurs régions](#use-hana-large-instance-units-in-multiple-regions). 

- SAP HANA sur Azure (grandes instances) comporte une adresse IP assignée à partir de la plage d’adresses du pool d’adresses IP du serveur que vous avez envoyée lors de la demande du déploiement de Grande instance HANA. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md). Cette adresse IP est accessible via les abonnements Azure et le circuit qui connecte les réseaux virtuels Azure à HANA - Grandes instances. L’adresse IP assignée hors que plage d’adresses de pools IP du serveur est directement assignée à l’unité matérielle. Elle n’est *plus* assignée via la traduction d’adresses réseau (NAT), comme c’était le cas dans les premiers déploiements de cette solution. 

### <a name="direct-routing-to-hana-large-instances"></a>Routage direct vers de Grandes Instances HANA

Par défaut, le routage transitif ne fonctionne pas dans les scénarios suivants :

- Entre les unités de grande instance HANA et un déploiement local.

- Entre les unités de grande instance HANA déployées dans différentes régions.

Il existe trois façons d’activer le routage transitif dans ces scénarios :

- Un proxy inverse pour router les données, dans un sens et dans l’autre. Par exemple, F5 BIG-IP, NGINX avec Traffic Manager déployé dans le réseau virtuel Azure qui se connecte aux Grandes instances HANA et au réseau local en tant que solution de routage de pare-feu/trafic virtuel.
- Utilisation des règles IPTables sur une machine virtuelle Linux pour activer le routage entre des emplacements locaux et des unités de grande instance HANA ou entre des unités de grande instance HANA dans différentes régions. La machine virtuelle exécutant IPTables doit être déployée sur le réseau virtuel Azure qui se connecte aux Grandes instances HANA et au réseau local. La machine virtuelle doit être dimensionnée de sorte que son débit réseau soit suffisant pour le trafic réseau attendu. Pour plus d’informations sur la bande passante réseau machines virtuelles, voir l’article [Tailles des machines virtuelles Linux dans Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Un [Pare-feu Azure](https://azure.microsoft.com/services/azure-firewall/) serait une autre solution pour permettre un trafic direct entre le réseau local et les unités de Grande instance HANA. 

Tout le trafic de ces solutions est acheminé via un réseau virtuel Azure. Par conséquent, le trafic pourrait également être limité par les appliances logicielles utilisées ou par les groupes de sécurité réseau Azure. De cette façon, des adresses IP ou des plages d’adresses IP locales spécifiques pourraient être bloquées ou explicitement autorisées à accéder aux Grandes instances HANA. 

> [!NOTE]  
> N’oubliez pas que l’implémentation et la prise en charge des solutions personnalisées qui impliquent des appliances réseau ou IPTables tierces n’est pas fournie par Microsoft. La prise en charge doit être réalisée par le fournisseur du composant utilisé ou par l’intégrateur. 

#### <a name="express-route-global-reach"></a>ExpressRoute Global Reach
Microsoft a introduit une nouvelle fonctionnalité appelée [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md). Global Reach est utilisable pour les Grandes instances HANA dans deux scénarios :

- Permettre l’accès direct à partir du réseau local à vos unités de Grande instance HANA déployées dans différentes régions.
- Permettre une communication directe entre vos unités de Grande instance HANA déployées dans différentes régions.


##### <a name="direct-access-from-on-premises"></a>Accès direct à partir du réseau local
Dans les régions Azure proposant Global Reach, vous pouvez demander d’activer Global Reach pour votre circuit ExpressRoute. Ce circuit connecte votre réseau local au réseau virtuel Azure qui se connecte à vos Grandes instances HANA. Le côté local de votre circuit ExpressRoute est soumis à des coûts. Pour plus d’informations, voir les tarifs liés au [module complémentaire Global Reach](https://azure.microsoft.com/pricing/details/expressroute/). Aucun coût supplémentaire n’est associé au circuit qui connecte les Grandes instances HANA à Azure. 

> [!IMPORTANT]  
> Lors de l’utilisation de Global Reach pour permettre l’accès direct entre vos unités de Grande instance HANA et vos ressources locales, les données réseau et le flux de contrôle ne sont **pas acheminés via des réseaux virtuels Azure**. Au lieu de cela, les données réseau et le flux de contrôle sont acheminés directement entre les routeurs Microsoft Exchange d’entreprise. Aucune règle de groupe de sécurité réseau ou de groupe de sécurité d’application, ni aucun type de pare-feu, de groupe de sécurité réseau ou de proxy que vous avez déployés dans un réseau virtuel Azure, ne sont donc concernés. **Si vous utilisez ExpressRoute Global Reach pour permettre un accès direct du réseau local aux unités de Grande instance HANA, les restrictions et autorisations d’accès aux unités de Grande instance HANA doivent être définies dans des pare-feu côté local.** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connexion de Grandes Instances HANA dans différentes régions Azure
De même, ExpressRoute Global Reach peut être utilisé pour connecter deux locataires de Grande instance HANA déployés dans deux régions différentes. L’isolation est assurée par les circuits ExpressRoute que vos locataires de Grande instance HANA utilisent pour se connecter à Azure dans les deux régions. La connexion de deux locataires de Grande instance HANA déployés dans des régions différentes n’occasionne pas de frais supplémentaires. 

> [!IMPORTANT]  
> Les flux de données et de contrôle du trafic réseau entre les locataires de Grande instance HANA ne sont pas acheminés via des réseaux Azure. Vous ne pouvez donc pas utiliser de fonctionnalité Azure ou d’appliances virtuelles réseau pour appliquer des restrictions de communication entre vos locataires de Grande instance HANA. 

Pour plus d’informations sur l’activation d’ExpressRoute Global Reach, voir [Connecter un réseau virtuel à de Grandes instances HANA](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Connectivité internet de la grande instance HANA
Les grandes instances HANA ne bénéficient *pas* d’une connectivité directe à Internet. Par exemple, cette limitation peut restreindre votre capacité à enregistrer l’image du système d’exploitation directement avec le fournisseur du système d’exploitation. Vous devrez peut-être travailler avec votre serveur d’outil de gestion des abonnements SUSE Linux Enterprise Server local ou d’un gestionnaire des abonnements Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Chiffrement des données entre des machines virtuelles et la grande instance HANA
Les données transférées entre les grandes instances HANA et les machines virtuelles ne sont pas chiffrées. Vous pouvez cependant activer le chiffrement du trafic simplement pour l’échange entre le SGBD HANA et les applications basées sur JDBC/ODBC. Pour plus d’informations, consultez [Secure Communication Between SAP HANA and JDBC/ODBC Clients](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html) (Sécuriser la communication entre SAP HANA et les clients JDBC/ODBC).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utiliser des unités de grande instance HANA dans plusieurs régions

Pour la récupération d’urgence, vous devez disposer d’unités de Grande instance HANA dans plusieurs régions Azure. En cas d’utilisation d’Azure [Global Vnet Peering](../../../virtual-network/virtual-network-peering-overview.md) uniquement, par défaut, le routage transitif ne fonctionne pas entre locataires de Grandes instances HANA situés dans différentes régions. Global Reach ouvre toutefois une communication directe entre unités de Grande instance HANA situées dans différentes régions Ce scénario d’utilisation d’ExpressRoute Global Reach permet d’effectuer les opérations suivantes :

 - Réplication de système HANA sans proxy ni pare-feu supplémentaires.
 - Copie des sauvegardes entre les unités de grande instance HANA dans des régions différentes pour effectuer des copies du système ou des actualisations du système.


![Réseau virtuel connecté aux tampons de grande instance Azure dans différentes régions Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figure précédente illustre la connexion des réseaux virtuels des deux régions à deux circuits ExpressRoute. Les circuits sont utilisés pour se connecter à SAP HANA sur Azure (Grandes instances) dans les deux régions Azure (lignes grises). Ces deux interconnexions ont pour but d’offrir une protection contre les pannes de routeur de périphérie d’entreprise Microsoft (MSEE) de chaque côté. Le flux de communication entre les deux réseaux virtuels dans les deux régions Azure est censé être géré via le [peering mondial](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) des deux réseaux virtuels dans les deux régions (ligne pointillée bleue). La ligne rouge épaisse décrit la connexion de Global Reach ExpressRoute. Cette connexion permet aux unités de Grande instance HANA de vos locataires situés dans différentes régions de communiquer entre elles. 

> [!IMPORTANT] 
> Si vous avez utilisé plusieurs circuits ExpressRoute, utilisez les paramètres de préfixation AS PATH et Local Preference BGP pour garantir le routage correct du trafic.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’architecture de stockage de SAP HANA (grandes instances).

> [!div class="nextstepaction"]
> [Architecture de stockage de SAP HANA (Grandes instances)](hana-storage-architecture.md)
