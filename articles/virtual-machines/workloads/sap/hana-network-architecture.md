---
title: Architecture réseau de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Architecture réseau du déploiement de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011580"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architecture réseau de SAP HANA (grandes instances)

L’architecture des services réseau Azure est un composant clé de la réussite du déploiement des applications SAP sur la grande instance HANA. En règle générale, les déploiements SAP HANA sur Azure (grandes instances) ont un plus grand paysage SAP avec plusieurs solutions SAP distinctes et tailles de bases de données variées, une consommation des ressources d’UC et une utilisation de la mémoire différentes. Il est probable que tous les systèmes SAP ne soient pas basés sur SAP HANA. Votre paysage SAP est probablement un hybride qui utilise :

- Des systèmes SAP déployés en local. En raison de leur taille, ces systèmes ne peuvent pas actuellement être hébergés dans Azure. Par exemple, un système ERP SAP qui s’exécute sur SQL Server (en tant que base de données) et qui nécessite plus de ressources UC ou de mémoire que les machines virtuelles peuvent offrir.
- Des systèmes SAP basés sur SAP HANA déployés en local.
- Des systèmes SAP déployés dans des machines virtuelles. Ces systèmes peuvent être des instances de développement, de test, de bac à sable ou de production pour toutes les applications basées sur SAP NetWeaver qui peuvent être déployées correctement dans Azure (sur des machines virtuelles), en fonction de la demande de mémoire et de consommation de ressources. Ces systèmes peuvent également reposer sur des bases de données telles que SQL Server. Pour plus d’informations, consultez [SAP Support Note #1928533 – applications SAP sur Azure : Produits et types de machines virtuelles Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533/E). Et ces systèmes peuvent également reposer sur des bases de données telles que SAP HANA. Pour plus d’informations, consultez [Plateformes IaaS certifiées SAP HANA](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Des serveurs d’applications SAP déployés dans Azure (sur des machines virtuelles) qui utilisent SAP HANA sur Azure (grandes instances) dans des tampons de grande instance Azure.

Un paysage SAP hybride avec quatre différents scénarios de déploiement ou plus est typique. Il existe également de nombreux cas client de paysages SAP complets qui s’exécutent dans Azure. Les machines virtuelles devenant plus puissantes, le nombre de clients qui passent toutes leurs solutions SAP vers Azure augmente.

La mise en réseau Azure dans le contexte des systèmes SAP déployés dans Azure n’est pas difficile. Elle est basée sur les principes suivants :

- Les réseaux virtuels Azure doivent être connectés au circuit ExpressRoute qui se connecte à un réseau local.
- Un circuit ExpressRoute se connectant en local à Azure doit généralement avoir une bande passante de 1 Gbit/s ou plus. La bande passante minimale permet une bande passante adéquate pour le transfert de données entre les systèmes locaux et les systèmes qui s’exécutent sur des machines virtuelles. Il permet également à la bande passante adéquate de se connecter aux systèmes Azure à partir d’utilisateurs locaux.
- Tous les systèmes SAP dans Azure doivent être configurés dans les réseaux virtuels pour communiquer entre eux.
- Le répertoire Active Directory et le DNS hébergés en local sont étendus à Azure via ExpressRoute, en local.


> [!NOTE] 
> Du point de vue de la facturation, un seul abonnement Azure peut être lié à un seul abonné dans un tampon de grande Instance dans une région Azure spécifique. À l’inverse, un seul abonné de tampon de grande Instance peut être lié à un seul abonnement Azure. Cette exigence est cohérente avec d’autres objets facturables dans Azure.

Si SAP HANA sur Azure (grandes instances) est déployé dans plusieurs régions Azure différentes, un abonné séparé est déployé dans le tampon de grande instance. Vous pouvez exécuter ces deux abonnés dans le même abonnement Azure tant que ces instances font partie du même paysage SAP. 

> [!IMPORTANT] 
> Seul le déploiement Azure Resource Manager est pris en charge avec SAP HANA sur Azure (grandes instances).

 

## <a name="additional-virtual-network-information"></a>Informations supplémentaires relatives au réseau virtuel

Pour vous connecter à un réseau virtuel sur ExpressRoute, une passerelle Azure doit être créée. Pour en savoir plus, consultez [À propos des passerelles de réseau virtuel pour ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Une passerelle Azure peut être utilisée avec ExpressRoute sur une infrastructure en dehors de Azure ou sur un tampon de grande instance Azure. Une passerelle Azure peut également être utilisée pour se connecter entre des réseaux virtuels. Pour plus d’informations, consultez [Configurer une connexion réseau au réseau pour le Gestionnaire des ressources à l’aide de PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vous pouvez connecter la passerelle Azure à un maximum de quatre connexions ExpressRoute différentes, tant que celles-ci proviennent de routeurs de périphérie d’entreprise Microsoft distincts. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Le débit fourni par une passerelle Azure est différent pour les deux cas d’usage. Pour plus d’informations, consultez [À propos de la passerelle VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Le débit maximal pouvant être atteint avec une passerelle de réseau virtuel est de 10 Gbits/s, à l’aide d’une connexion ExpressRoute. La copie de fichiers entre une machine virtuelle résidant dans un réseau virtuel et un système local (en tant que flux de copie unique) n’atteint pas le débit total des différentes références SKU des passerelles. Pour tirer parti de la bande passante complète de la passerelle de réseau virtuel, utilisez plusieurs flux de données. Ou bien, vous devez copier des fichiers différents dans des flux parallèles d’un seul fichier.


## <a name="networking-architecture-for-hana-large-instance"></a>Architecture de mise en réseau pour la grande instance HANA
L’architecture de mise en réseau pour la grande instance HANA peut être divisée en quatre parties différentes :

- Mise en réseau locale et connexion ExpressRoute à Azure. Cette partie est le domaine des clients et est connectée à Azure via ExpressRoute. Consultez la partie inférieure droite de la figure suivante.
- Services réseau Azure, comme indiqué précédemment, avec des réseaux virtuels, qui ont à nouveau des passerelles. Il s’agit d’une zone dans laquelle vous devez rechercher les conceptions appropriées pour les exigences, la sécurité et les exigences de conformité de votre application. L’utilisation de la grande instance HANA ou pas est à prendre en considération en ce qui concerne le nombre de réseaux virtuels et les références SKU de la passerelle Azure à partir desquels choisir. Consultez la partie supérieure droite de la figure.
- Connectivité de la grande instance HANA via la technologie ExpressRoute dans Azure. Cette partie est déployée et gérée par Microsoft. Il vous suffit de fournir certaines plages d’adresses IP après la connexion du déploiement de vos ressources dans la grande instance HANA au circuit ExpressRoute pour les réseaux virtuels. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Mise en réseau dans la grande instance HANA, essentiellement transparente pour vous.

![Réseau virtuel connecté à SAP HANA sur Azure (grandes instances) et en local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

L’obligation de connecter vos ressources locales via ExpressRoute à Azure perdure malgré l’utilisation de la grande instance HANA. La nécessité d’utiliser un ou plusieurs réseaux virtuels qui exécutent les machines virtuelles, hébergeant la couche Application qui se connecte aux instances HANA hébergées dans les unités de grande instance HANA ne change pas non plus. 

Les différences dans les déploiements SAP dans Azure sont les suivantes :

- Les unités de grande instance HANA de votre abonné client sont connectées via un autre circuit ExpressRoute à vos réseaux virtuels. Afin de séparer les conditions de charge, les liens ExpressRoute entre les réseaux locaux et virtuels et les liens entre les réseaux virtuels Azure et la grande instance HANA ne partagent pas les mêmes routeurs.
- Le profil de la charge de travail entre la couche Application SAP et la grande instance HANA est de nature différente, avec de nombreuses petites requêtes, et agit comme des transferts de données (jeux de résultats) à partir de SAP HANA dans la couche Application.
- L’architecture d’application SAP est plus sensible à la latence du réseau que les scénarios classiques dans lesquels les données sont échangées entre les machines locales et Azure.
- La passerelle de réseau virtuel possède au moins deux connexions ExpressRoute. Les deux connexions partagent la bande passante maximale pour les données entrantes de la passerelle du réseau virtuel.

La latence du réseau rencontrée entre les machines virtuelles et les unités de grande instance HANA peut être supérieure à la latence standard aller-retour sur un réseau de machine virtuelle à machine virtuelle. Sur la région Azure, les valeurs mesurées peuvent dépasser la latence aller-retour de 0,7 ms classée en dessous de la moyenne dans [Remarque SAP n° 1100926 - FAQ : Performances réseau](https://launchpad.support.sap.com/#/notes/1100926/E). Selon la région Azure et l’outil utilisé pour mesurer la latence aller-retour entre une machine virtuelle Azure et une unité de grande instance SAP HANA, la latence mesurée peut avoisiner les 2 millisecondes maximum. Néanmoins, les clients déploient des applications SAP de production basées sur SAP HANA avec succès sur la grande instance SAP HANA. Vérifiez avoir testé minutieusement vos processus métier dans la Grande instance HANA Azure.
 
Afin d’offrir une latence réseau déterministe entre les machines virtuelles Azure et une grande instance HANA, le choix de la référence SKU de passerelle de réseau virtuel est essentiel. Contrairement aux modèles de trafic entre machines locales et machines virtuelles, le modèle de trafic entre les machines virtuelles et la grande instance HANA peut développer des pics courts mais élevés de requêtes et de volumes de données à transmettre. Pour bien gérer ces pics, nous vous recommandons fortement d’utiliser la référence SKU de passerelle UltraPerformance. Pour les références SKU de grande instance HANA de classe Type II, l’utilisation de la référence SKU de la passerelle UltraPerformance en tant que passerelle de réseau virtuel est obligatoire.

> [!IMPORTANT] 
> Étant donné le trafic réseau global entre les couches Base de données et Application SAP, seules les références SKU de passerelle HighPerformance et UltraPerformance pour les réseaux virtuels sont prises en charge pour la connexion à SAP HANA sur Azure (grandes instances). Pour les références SKU de grande instance HANA de Type II, seule la référence SKU de la passerelle UltraPerformance est prise en charge en tant que passerelle de réseau virtuel.



## <a name="single-sap-system"></a>Système SAP unique

L’infrastructure locale présentée précédemment est connectée via ExpressRoute dans Azure. Le circuit ExpressRoute se connecte à un routeur de périphérie d’entreprise. Pour plus d’informations, consultez [Vue d’ensemble technique d’ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Une fois l’itinéraire établi, il se connecte au serveur principal Azure et toutes les régions Azure sont accessibles.

> [!NOTE] 
> Pour exécuter des paysages SAP dans Azure, connectez le routeur de périphérie d’entreprise le plus proche de la région Azure dans le paysage SAP. Les tampons de grande instance Azure sont connectés via les périphériques du routeur de périphérie d’entreprise dédiés pour réduire la latence réseau entre les machines virtuelles dans Azure IaaS et les tampons de grande instance.

La passerelle du réseau virtuel pour les machines virtuelles qui hébergent des instances de l’application SAP est connectée au circuit ExpressRoute. Le même réseau virtuel est connecté à un routeur de périphérie d’entreprise distinct dédié à la connexion aux tampons de grande Instance.

Ce système est un exemple simple d’un système SAP unique. La couche Application SAP est hébergée dans Azure. La base de données SAP HANA s’exécute sur SAP HANA sur Azure (grandes instances). Nous supposons que le fait que le débit de la bande passante de la passerelle de réseau virtuel soit de 2 Gbits/s ou de 10 Gbit/s ne représente pas un goulot d’étranglement.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Plusieurs systèmes SAP ou grands systèmes SAP

Si plusieurs systèmes SAP ou si de grands systèmes SAP sont déployés pour se connecter à SAP HANA sur Azure (grandes instances), le débit de la passerelle de réseau virtuel peut devenir un goulot d’étranglement. Dans ce cas, divisez les couches Application en plusieurs réseaux virtuels. Vous pouvez également créer un réseau virtuel spécial qui se connecte à la grande instance HANA dans les cas suivants :

- Exécution de sauvegardes directement à partir des instances HANA dans la grande instance HANA vers une machine virtuelle dans Azure hébergeant des partages NFS.
- Copie de sauvegardes volumineuses ou d’autres fichiers des unités de grandes instances HANA vers l’espace disque géré dans Azure.

Utilisez un réseau virtuel distinct pour héberger des machines virtuelles qui gèrent le stockage. Ce procédé permet d’éviter les effets d’un fichier volumineux ou de transfert de données à partir de la grande instance HANA vers Azure sur la passerelle de réseau virtuel qui sert les machines virtuelles qui exécutent la couche Application SAP. 

Pour une architecture réseau plus évolutive :

- Utilisez plusieurs réseaux virtuels pour une couche Application SAP unique, plus grande.
- Déployez un réseau virtuel distinct pour chaque système SAP déployé, plutôt que de combiner ces systèmes SAP en sous-réseaux séparés dans le même réseau virtuel.

  Une architecture réseau plus évolutive pour SAP HANA sur Azure (grandes instances) :

![Déployer la couche Application SAP sur plusieurs réseaux virtuels](./media/hana-overview-architecture/image4-networking-architecture.png)

La figure montre la couche Application SAP ou des composants, déployés sur plusieurs réseaux virtuels. Cette configuration a introduit la surcharge de latence inévitable qui s’est produite pendant la communication entre les applications hébergées dans ces réseaux virtuels. Par défaut, le trafic réseau entre les machines virtuelles situées dans différents réseaux virtuels passe par les routeurs de périphérie d’entreprise dans cette configuration. La méthode d’optimisation et de réduction de la latence de communication entre deux réseaux virtuels consiste à homologuer des réseaux virtuels dans la même région. Cette méthode fonctionne même si ces réseaux virtuels sont dans des abonnements différents. Grâce à l’homologation des réseaux virtuels, la communication entre des machines virtuelles de deux réseaux virtuels différents peut utiliser le réseau Azure principal pour communiquer directement entre elles. La latence s’affiche comme si les machines virtuelles étaient dans le même réseau virtuel. Le trafic d’adressage des plages d’adresses IP connectées via la passerelle du réseau virtuel Azure est acheminé via la passerelle de réseau virtuel individuelle du réseau virtuel. 

Pour plus d’informations sur l’homologation du réseau virtuel, consultez [Homologation de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routage dans Azure

Trois points importants sont à prendre en compte en matière de routage pour SAP HANA sur Azure (grandes instances) :

* L’accès à SAP HANA sur Azure (grandes instances) se fait uniquement via des machines virtuelles dans la connexion ExpressRoute dédiée et non directement via le système local. L’accès direct du réseau local aux unités de grande instance HANA, tel que fourni par Microsoft, n’est pas possible immédiatement. Les restrictions de routage transitives sont dues à l’architecture réseau Azure actuelle utilisée pour la grande instance SAP HANA. Certains clients d’administration et toutes les applications nécessitant un accès direct, tel que le gestionnaire de solutions SAP s’exécutant en local, ne peuvent pas se connecter à la base de données SAP HANA.

* Si vous avez des unités de grande instance HANA déployées dans deux régions Azure différentes à des fins de récupération d’urgence, les mêmes restrictions de routage temporaires s’appliquent. En d’autres termes, les adresses IP d’une unité de grande instance HANA dans une région (par exemple, États-Unis de l’Ouest) ne seront pas routées vers une unité de grande instance HANA déployée dans une autre région (par exemple, États-unis de l’Est). Cette restriction est indépendante de l’utilisation de l’homologation de réseau Azure entre les différentes régions ou des interconnexions de circuits ExpressRoute qui connectent les unités de grande instance HANA aux réseaux virtuels. Pour obtenir une représentation graphique, consultez la figure de la section « Utiliser des unités de grande instance HANA dans plusieurs régions. » Cette restriction, qui provient de l’architecture déployée, empêche l’utilisation immédiate de la réplication du système HANA en tant que fonctionnalité de récupération d’urgence.

* Les unités SAP HANA sur Azure (grandes instances) ont une adresse IP assignée à partir de la plage d’adresses de pools IP du serveur que vous avez envoyée. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette adresse IP est accessible via les abonnements Azure et ExpressRoute qui connectent des réseaux virtuels à HANA sur Azure (grandes instances). L’adresse IP assignée hors que plage d’adresses de pools IP du serveur est directement assignée à l’unité matérielle. Elle n’est *plus* assignée via la traduction d'adresses réseau, comme c’était le cas dans les premiers déploiements de cette solution. 

> [!NOTE]
> Pour surmonter la restriction de routage temporaire, comme expliqué dans les premiers éléments de la liste ci-dessus, utilisez des composants supplémentaires pour le routage. Les composants qui peuvent être utilisés pour surmonter la restriction peuvent être :
> 
> * Un proxy inverse pour router les données, dans un sens et dans l’autre. Par exemple, F5 BIG-IP, NGINX avec Traffic Manager déployé dans Azure en tant que solution de routage de trafic/pare-feu virtuelle.
> * Utilisation des [règles IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) sur une machine virtuelle Linux pour activer le routage entre des emplacements locaux et des unités de grande instance HANA ou entre des unités de grande instance HANA dans différentes régions.
> 
> N’oubliez pas que l’implémentation et la prise en charge des solutions personnalisées qui impliquent des appliances réseau ou IPTables tierces n’est pas fournie par Microsoft. La prise en charge doit être réalisée par le fournisseur du composant utilisé ou de l’intégrateur. 

## <a name="internet-connectivity-of-hana-large-instance"></a>Connectivité internet de la grande instance HANA
La grande instance HANA ne bénéficie *pas* d’une connectivité directe à Internet. Par exemple, cette limitation peut restreindre votre capacité à enregistrer l’image du système d’exploitation directement avec le fournisseur du système d’exploitation. Vous devrez peut-être travailler avec votre serveur d’outil de gestion des abonnements SUSE Linux Enterprise Server local ou d’un gestionnaire des abonnements Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Chiffrement des données entre des machines virtuelles et la grande instance HANA
Les données transférées entre la grande instance HANA et des machines virtuelles ne sont pas chiffrées. En revanche, vous pouvez activer le chiffrement du trafic pour l’échange simple entre le SGBD HANA et les applications basées sur JDBC/ODBC. Pour plus d’informations, consultez [cette documentation par SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utiliser des unités de grande instance HANA dans plusieurs régions

Vous pouvez avoir d’autres raisons de déployer SAP HANA sur Azure (grandes instances) dans plusieurs régions Azure, en plus de la récupération d’urgence. Vous voulez peut-être accéder à la grande instance HANA à partir de chaque machine virtuelle déployée dans les différents réseaux virtuels dans les régions. Les adresses IP assignées aux différentes unités de grande instance HANA ne sont pas propagées au-delà des réseaux virtuels qui sont directement connectés via leur passerelle vers les instances. Par conséquent, une légère modification est introduite dans la conception du réseau virtuel. Une passerelle de réseau virtuel peut gérer quatre circuits ExpressRoute différents hors des routeurs de périphérie d’entreprise différents. Chaque réseau virtuel connecté à l’un des tampons de grande Instance peut être connecté au tampon de grande instance dans une autre région Azure.

![Réseau virtuel connecté aux tampons de grande instance Azure dans différentes régions Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figure illustre comment les différents réseaux virtuels dans les deux régions sont connectés à deux circuits ExpressRoute distincts utilisés pour se connecter à SAP HANA sur Azure (grandes instances) dans les deux régions Azure. Les connexions nouvellement introduites sont représentées par les lignes rouges rectangulaires. Avec ces connexions, en dehors des réseaux virtuels Azure, les machines virtuelles s’exécutant dans l’un de ces réseaux virtuels peuvent accéder à chacune des différentes unités de grande instance HANA déployées dans les deux régions. Comme le montre la figure, il est supposé que vous disposiez de deux connexions ExpressRoute en local vers les deux régions Azure. Cette configuration est recommandée à des fins de récupération d’urgence.

> [!IMPORTANT] 
> Si plusieurs circuits ExpressRoute sont utilisés, les paramètres de préfixation AS PATH et Local Preference BGP doivent être utilisés pour garantir le routage correct du trafic.

**Étapes suivantes**
- Consultez [Architecture de stockage de SAP HANA (grandes instances)](hana-storage-architecture.md)