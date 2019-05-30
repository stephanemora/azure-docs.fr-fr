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
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8794a93cecb50774f30746c22931db31a9fa9194
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239619"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architecture réseau de SAP HANA (grandes instances)

L’architecture des services réseau Azure est un composant clé de la réussite du déploiement des applications SAP sur la grande instance HANA. En règle générale, les déploiements SAP HANA sur Azure (grandes instances) ont un plus grand paysage SAP avec plusieurs solutions SAP distinctes et tailles de bases de données variées, une consommation des ressources d’UC et une utilisation de la mémoire différentes. Il est probable que pas tous les systèmes informatiques sont trouvent déjà dans Azure. Votre paysage SAP est souvent hybride également à partir d’un point de SGBD et d’un point de vue SAP application à l’aide d’un mélange de NetWeaver et S/4HANA et SAP HANA et autres SGBD. Azure propose différents services qui vous permettent d’exécuter différents systèmes SGBD, NetWeaver et S/4HANA dans Azure. Azure également offres que technologie afin de donner Azure le réseau comme un centre de données virtuel pour vos déploiements de logiciel sur site

À moins que vos systèmes informatiques complètes sont hébergés dans Azure. Les fonctionnalités de mise en réseau Azure permet de connecter le monde en local avec vos ressources Azure afin de rendre Azure ressembler à un centre de données virtuel de la vôtre. La fonctionnalité de réseau Azure utilisée est : 

- Réseaux virtuels Azure sont connectés à la [ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuit qui se connecte à vos ressources de réseau local.
- Un circuit ExpressRoute qui se connecte en local à Azure doit avoir une bande passante minimale de [1 Gbit/s ou version ultérieure](https://azure.microsoft.com/pricing/details/expressroute/). La bande passante minimale permet une bande passante adéquate pour le transfert de données entre les systèmes locaux et les systèmes qui s’exécutent sur des machines virtuelles. Il permet également à la bande passante adéquate de se connecter aux systèmes Azure à partir d’utilisateurs locaux.
- Tous les systèmes SAP dans Azure sont configurés dans les réseaux virtuels pour communiquer entre eux.
- Active Directory et DNS hébergés en local sont étendus à Azure via ExpressRoute en local, ou sont en cours d’exécution complet dans Azure.

Dans le cas spécifique de l’intégration de grandes Instances HANA dans l’infrastructure de réseau de centre de données Azure, la technologie Azure ExpressRoute est également utilisée


> [!NOTE] 
> Qu’un seul abonnement Azure peut être lié à un seul locataire dans un tampon de grande Instance HANA dans une région Azure spécifique. À l’inverse, un seul locataire de tampon de grande Instance HANA peut être lié à un seul abonnement Azure. Cette exigence est cohérente avec d’autres objets facturables dans Azure.

Si SAP HANA sur Azure (grandes Instances) est déployé dans plusieurs régions Azure différentes, un abonné séparé est déployé dans le tampon de grande Instance HANA. Vous pouvez exécuter ces deux abonnés dans le même abonnement Azure tant que ces instances font partie du même paysage SAP. 

> [!IMPORTANT] 
> Seule la méthode de déploiement Azure Resource Manager est pris en charge avec SAP HANA sur Azure (grandes Instances).

 

## <a name="additional-virtual-network-information"></a>Informations supplémentaires relatives au réseau virtuel

Pour vous connecter à un réseau virtuel pour ExpressRoute, une passerelle Azure ExpressRoute doit être créée. Pour plus d’informations, consultez [Expressroute sur des passerelles pour ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Une passerelle Azure ExpressRoute est utilisée avec ExpressRoute à une infrastructure en dehors d’Azure ou à un tampon de grande Instance Azure. Vous pouvez vous connecter à la passerelle Azure ExpressRoute à un maximum de quatre circuits ExpressRoute différents, que ces connexions proviennent de différents routeurs Microsoft enterprise edge. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Le débit maximal, que vous pouvez obtenir avec une passerelle ExpressRoute est 10 Gbits/s à l’aide d’une connexion ExpressRoute. La copie de fichiers entre une machine virtuelle résidant dans un réseau virtuel et un système local (en tant que flux de copie unique) n’atteint pas le débit total des différentes références SKU des passerelles. Pour tirer parti de la bande passante complète de la passerelle ExpressRoute, utilisez plusieurs flux de données. Ou bien, vous devez copier des fichiers différents dans des flux parallèles d’un seul fichier.


## <a name="networking-architecture-for-hana-large-instance"></a>Architecture de mise en réseau pour la grande instance HANA
L’architecture de mise en réseau pour la grande instance HANA peut être divisée en quatre parties différentes :

- Mise en réseau locale et connexion ExpressRoute à Azure. Cette partie est le domaine des clients et est connectée à Azure via ExpressRoute. Ce circuit Expressroute est entièrement payé par vous en tant que client. La bande passante doit être suffisamment grande pour gérer le trafic réseau entre vos ressources locales et la région Azure que vous vous connectez sur. Consultez la partie inférieure droite de la figure suivante.
- Services réseau Azure, comme indiqués précédemment, les réseaux virtuels qui doivent à nouveau des passerelles ExpressRoute ajoutés. Il s’agit d’une zone dans laquelle vous devez rechercher les conceptions appropriées pour les exigences, la sécurité et les exigences de conformité de votre application. L’utilisation de la grande instance HANA ou pas est à prendre en considération en ce qui concerne le nombre de réseaux virtuels et les références SKU de la passerelle Azure à partir desquels choisir. Consultez la partie supérieure droite de la figure.
- Connectivité de la grande instance HANA via la technologie ExpressRoute dans Azure. Cette partie est déployée et gérée par Microsoft. Il vous suffit de fournir certaines plages d’adresses IP après la connexion du déploiement de vos ressources dans la grande instance HANA au circuit ExpressRoute pour les réseaux virtuels. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Il n’existe aucun frais supplémentaire pour vous en tant que client pour la connectivité entre la structure réseau du centre de données Azure et les unités de grande Instance HANA.
- Mise en réseau dans le tampon de grande Instance HANA, qui est essentiellement transparente pour vous.

![Réseau virtuel connecté à SAP HANA sur Azure (grandes instances) et en local](./media/hana-overview-architecture/image1-architecture.png)

L’obligation de connecter vos ressources locales via ExpressRoute à Azure perdure malgré l’utilisation de la grande instance HANA. La nécessité d’utiliser un ou plusieurs réseaux virtuels qui exécutent les machines virtuelles, hébergeant la couche Application qui se connecte aux instances HANA hébergées dans les unités de grande instance HANA ne change pas non plus. 

Les différences dans les déploiements SAP dans Azure sont les suivantes :

- Les unités de grande instance HANA de votre abonné client sont connectées via un autre circuit ExpressRoute à vos réseaux virtuels. Pour séparer les conditions de charge, le système local et circuits ExpressRoute de réseau virtuel Azure et les circuits entre réseaux virtuels Azure et les grandes Instances HANA ne partage pas les mêmes routeurs.
- Le profil de la charge de travail entre la couche Application SAP et la grande instance HANA est de nature différente, avec de nombreuses petites requêtes, et agit comme des transferts de données (jeux de résultats) à partir de SAP HANA dans la couche Application.
- L’architecture d’application SAP est plus sensible à la latence du réseau que les scénarios classiques dans lesquels les données sont échangées entre les machines locales et Azure.
- La passerelle Azure ExpressRoute a au moins deux connexions ExpressRoute. Un circuit est connecté à partir de sur site et qui est connecté à partir de grandes Instances HANA. Cela laisse uniquement d’espace pour un autre deux circuits supplémentaires à partir des msee différents pour vous connecter à sur la passerelle ExpressRoute. Cette restriction est indépendante de l’utilisation d’ExpressRoute rapide chemin d’accès. Tous les circuits connectés partagent la bande passante maximale pour les données entrantes de la passerelle ExpressRoute.

La latence du réseau rencontrée entre les machines virtuelles et les unités de grande instance HANA peut être supérieure à la latence standard aller-retour sur un réseau de machine virtuelle à machine virtuelle. Sur la région Azure, les valeurs mesurées peuvent dépasser la latence aller-retour de 0,7 ms classée en dessous de la moyenne dans [Remarque SAP n° 1100926 - FAQ : Performances réseau](https://launchpad.support.sap.com/#/notes/1100926/E). Selon la région Azure et l’outil utilisé pour mesurer la latence aller-retour entre une machine virtuelle Azure et une unité de grande instance SAP HANA, la latence mesurée peut avoisiner les 2 millisecondes maximum. Néanmoins, les clients déploient des applications SAP de production basées sur SAP HANA avec succès sur la grande instance SAP HANA. Vérifiez avoir testé minutieusement vos processus métier dans la Grande instance HANA Azure. Une nouvelle fonctionnalité, appelée ExpressRoute rapide chemin d’accès, est en mesure de réduire la latence réseau entre les grandes Instances HANA et la couche d’application dans Azure des machines virtuelles (voir ci-dessous). 

Pour offrir une latence réseau déterministe entre les machines virtuelles et de grande Instance HANA, le choix de la passerelle ExpressRoute référence (SKU) est essentiel. Contrairement aux modèles de trafic entre machines locales et machines virtuelles, le modèle de trafic entre les machines virtuelles et la grande instance HANA peut développer des pics courts mais élevés de requêtes et de volumes de données à transmettre. Pour bien gérer ces pics, nous vous recommandons fortement d’utiliser la référence SKU de passerelle UltraPerformance. Pour la classe Type II des références SKU de grande Instance HANA, l’utilisation de la référence SKU de passerelle UltraPerformance en tant qu’une passerelle Expressroute est obligatoire.

> [!IMPORTANT] 
> Étant donné le trafic réseau global entre les couches Base de données et Application SAP, seules les références SKU de passerelle HighPerformance et UltraPerformance pour les réseaux virtuels sont prises en charge pour la connexion à SAP HANA sur Azure (grandes instances). HANA grande Instance de Type II références (SKU), seule la passerelle UltraPerformance référence (SKU) est pris en charge comme une passerelle ExpressRoute. Exceptions s’appliquent lorsque vous utilisez ExpressRoute rapide chemin d’accès (voir ci-dessous)

### <a name="expressroute-fast-path"></a>Chemin d’accès rapide d’ExpressRoute
Pour réduire la latence, ExpressRoute rapide chemin d’accès a été commercialisée et publiée en mai 2019, la connectivité spécifique des grandes Instances HANA aux réseaux virtuels Azure, qui hébergent les machines virtuelles d’application SAP. La principale différence à la solution déployée pour l’instant, c'est-à-dire que les flux de données entre les machines virtuelles et les grandes Instances HANA ne sont pas plus routés via la passerelle ExpressRoute. À la place les machines virtuelles affectées dans l’ou les sous-réseaux du réseau virtuel Azure communiquent directement avec le routeur de périphérie d’entreprise dédiés. 

> [!IMPORTANT] 
> La fonctionnalité ExpressRoute rapide chemin d’accès requiert que les sous-réseaux de machines virtuelles d’une application SAP en cours d’exécution se trouvent dans le même réseau virtuel Azure qui a été connecté pour les grandes Instances HANA. Machines virtuelles situées dans des réseaux virtuels Azure qui sont homologués avec le réseau virtuel Azure connecté directement aux unités de grande Instance HANA ne bénéficient pas de ExpressRoute rapide chemin d’accès. Par conséquent hub- and -spoke réseau virtuel conceptions classiques, où les circuits ExpressRoute sont connectent par rapport à un réseau virtuel hub et les réseaux virtuels contenant la couche d’application SAP (spokes) sont bien homologués, l’optimisation par ExpressRoute Fast Chemin d’accès ne fonctionnera pas. En outre, ExpressRoute rapide chemin d’accès ne prend pas en charge les règles de routage défini par l’utilisateur (UDR) dès aujourd'hui. Pour plus d’informations, consultez [ExpressRoute virtuel réseau de passerelle et le chemin d’accès rapide](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Pour plus d’informations sur la configuration ExpressRoute rapide chemin d’accès, consultez le document [connecter un réseau virtuel aux grandes instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Une passerelle UltraPerformance ExpressRoute est nécessaire pour disposer d’ExpressRoute rapide chemin d’accès


## <a name="single-sap-system"></a>Système SAP unique

L’infrastructure locale présentée précédemment est connectée via ExpressRoute dans Azure. Le circuit ExpressRoute se connecte à un routeur Microsoft enterprise edge (MSEE). Pour plus d’informations, consultez [Vue d’ensemble technique d’ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Une fois l’itinéraire est établi, il se connecte au réseau principal Azure.

> [!NOTE] 
> Pour exécuter des paysages SAP dans Azure, connectez le routeur de périphérie d’entreprise le plus proche de la région Azure dans le paysage SAP. Les tampons de grande Instance HANA sont connectés via les périphériques du routeur de périphérie entreprise dédiés pour réduire la latence réseau entre les machines virtuelles dans Azure IaaS et les tampons de grande Instance HANA.

La passerelle ExpressRoute pour les machines virtuelles qui hébergent des instances de l’application SAP est connectée à un circuit ExpressRoute qui se connecte pour sur site. Le même réseau virtuel est connecté à un routeur de périphérie d’entreprise distinct dédié à la connexion aux tampons de grande Instance. Machines virtuelles à l’aide d’ExpressRoute rapide chemin d’accès, le flux de données à partir de grandes Instances HANA à la couche application SAP ne sont pas acheminées via la passerelle ExpressRoute plus et que de réduire le réseau de la latence de boucle.

Ce système est un exemple simple d’un système SAP unique. La couche Application SAP est hébergée dans Azure. La base de données SAP HANA s’exécute sur SAP HANA sur Azure (grandes instances). L’hypothèse est que la bande passante de passerelle ExpressRoute de débit de 2 Gbits/s ou de 10 Gbits/s ne représente pas un goulot d’étranglement.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Plusieurs systèmes SAP ou grands systèmes SAP

Si plusieurs systèmes SAP ou grands systèmes SAP sont déployés pour vous connecter à SAP HANA sur Azure (grandes Instances), le débit de la passerelle ExpressRoute peut devenir un goulot d’étranglement. Ou vous souhaitez isoler les systèmes de production et hors production dans différents réseaux virtuels Azure. Dans ce cas, divisez les couches Application en plusieurs réseaux virtuels. Vous pouvez également créer un réseau virtuel spécial qui se connecte à la grande instance HANA dans les cas suivants :

- Exécution de sauvegardes directement à partir des instances HANA dans la grande instance HANA vers une machine virtuelle dans Azure hébergeant des partages NFS.
- Copie de sauvegardes volumineuses ou d’autres fichiers des unités de grandes instances HANA vers l’espace disque géré dans Azure.

Utiliser un réseau virtuel distinct pour hébergent des machines virtuelles pour gérer le stockage de masse transfert de données entre les grandes Instances HANA et Azure. Ce procédé permet d’éviter les effets d’un fichier volumineux ou de transfert de données de grande Instance HANA sur Azure sur la passerelle ExpressRoute qui sert les machines virtuelles qui s’exécutent de la couche application SAP. 

Pour une architecture réseau plus évolutive :

- Utilisez plusieurs réseaux virtuels pour une couche Application SAP unique, plus grande.
- Déployez un réseau virtuel distinct pour chaque système SAP déployé, plutôt que de combiner ces systèmes SAP en sous-réseaux séparés dans le même réseau virtuel.

  Une architecture réseau plus évolutive pour SAP HANA sur Azure (grandes instances) :

![Déployer la couche Application SAP sur plusieurs réseaux virtuels](./media/hana-overview-architecture/image4-networking-architecture.png)

Dépendants sur les règles et restrictions, que vous souhaitez appliquer entre les différents réseaux virtuels héberge des ordinateurs virtuels de différents systèmes SAP, vous devez homologuer ces réseaux virtuels. Pour plus d’informations sur l’homologation du réseau virtuel, consultez [Homologation de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routage dans Azure

Par le déploiement par défaut, les trois considérations de routage de réseau sont importantes pour SAP HANA sur Azure (grandes Instances) :

* SAP HANA sur Azure (grandes Instances) est accessible uniquement par le biais de machines virtuelles Azure et la connexion ExpressRoute dédiée, pas directement depuis un site local. L’accès direct du réseau local aux unités de grande instance HANA, tel que fourni par Microsoft, n’est pas possible immédiatement. Les restrictions de routage transitives sont dues à l’architecture réseau Azure actuelle utilisée pour la grande instance SAP HANA. Certains clients d’administration et toutes les applications nécessitant un accès direct, tel que le gestionnaire de solutions SAP s’exécutant en local, ne peuvent pas se connecter à la base de données SAP HANA. Pour les exceptions, consultez la section « Routage Direct aux grandes Instances HANA ».

* Si vous avez des unités de grande Instance HANA déployées dans deux régions Azure différentes pour la récupération d’urgence, les mêmes restrictions de routage temporaires appliquées dans le passé. En d’autres termes, les adresses IP d’une unité de grande Instance HANA dans une région (par exemple, ouest des États-Unis) ont été pas routées vers une unité de grande Instance HANA déployée dans une autre région (par exemple, est des États-Unis). Cette restriction a été indépendante de l’utilisation du réseau Azure homologation entre des régions ou des interconnexions les circuits ExpressRoute qui se connectent les unités de grande Instance HANA aux réseaux virtuels. Pour obtenir une représentation graphique, consultez la figure de la section « Utiliser des unités de grande instance HANA dans plusieurs régions. » Cette restriction, qui est fourni avec l’architecture déployée, interdit l’utilisation immédiate de réplication de système HANA en tant que fonctionnalité de récupération d’urgence. Pour connaître les modifications récentes, recherchez la section « Unités utilisation HANA grande Instance dans plusieurs régions ». 

* SAP HANA sur des unités Azure (grandes Instances) ont une adresse IP à partir de la plage d’adresses serveur IP pool que vous avez envoyée lors de la demande le déploiement de grande Instance HANA. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette adresse IP est accessible via les abonnements Azure et le circuit qui connecte les réseaux virtuels Azure aux grandes Instances HANA. L’adresse IP assignée hors que plage d’adresses de pools IP du serveur est directement assignée à l’unité matérielle. Elle n’est *plus* assignée via la traduction d'adresses réseau, comme c’était le cas dans les premiers déploiements de cette solution. 

### <a name="direct-routing-to-hana-large-instances"></a>Routage aux grandes Instances HANA direct
Par défaut le routage transitives entre les unités de grande Instance HANA et sur site ou entre un routage grande Instance HANA qui sont déployés dans deux régions différentes ne fonctionne pas. Il existe plusieurs possibilités pour activer ces un routage transitif.

- Un proxy inverse pour router les données, dans un sens et dans l’autre. Par exemple, F5 BIG-IP, NGINX avec Traffic Manager déployé dans le réseau virtuel Azure qui se connecte aux grandes Instances HANA et en local en tant qu’une solution de routage de trafic/pare-feu virtuelle.
- Utilisation des [règles IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) sur une machine virtuelle Linux pour activer le routage entre des emplacements locaux et des unités de grande instance HANA ou entre des unités de grande instance HANA dans différentes régions. La machine virtuelle en cours d’exécution IPTables doit être déployé dans le réseau virtuel Azure qui se connecte aux grandes Instances HANA et sur site. La machine virtuelle doit être dimensionnés en conséquence, donc, que le débit du réseau de la machine virtuelle est suffisant pour le trafic réseau attendu. Pour plus d’informations sur la machine virtuelle de la bande passante réseau, consultez l’article [tailles des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Pare-feu Azure](https://azure.microsoft.com/services/azure-firewall/) serait une autre solution permettant de diriger le trafic entre les locaux et les unités HANA grande instance. 

Tout le trafic de ces solutions serait acheminé via un réseau virtuel Azure et par conséquent, le trafic peut être restreint par les appliances de manière réversible utilisés ou par Azure Network Security Groups, par conséquent, certaines adresses IP ou une adresse IP comprise entre sur site pourrait bloqué ou autorisé explicitement l’accès à grandes Instances HANA. 

> [!NOTE]  
> N’oubliez pas que l’implémentation et la prise en charge des solutions personnalisées qui impliquent des appliances réseau ou IPTables tierces n’est pas fournie par Microsoft. La prise en charge doit être réalisée par le fournisseur du composant utilisé ou de l’intégrateur. 

#### <a name="express-route-global-reach"></a>Express Route une portée mondiale
Microsoft a introduit une nouvelle fonctionnalité appelée [ExpressRoute de portée mondiale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Portée mondiale utilisable pour les grandes Instances HANA dans deux scénarios :

- Activer l’accès direct à partir de sur site pour vos unités de grande Instance HANA déployées dans différentes régions
- Activer la communication directe entre vos unités de grande Instance HANA déployées dans différentes régions


##### <a name="direct-access-from-on-premise"></a>Accès direct à partir en local
Dans les régions Azure où la portée mondiale est disponible, vous pouvez demander l’activation de la fonctionnalité de portée mondiale pour votre circuit ExpressRoute qui connecte votre réseau local au réseau virtuel Azure qui se connecte à vos unités de grande Instance HANA également. Il existe certaines implications en matière de coût pour le côté local de votre circuit ExpressRoute. Pour connaître les prix, vérifier les prix pour [Global de module complémentaire atteindre](https://azure.microsoft.com/pricing/details/expressroute/). Il n’existe aucun coût supplémentaire pour vous liés au circuit qui se connecte les unités de grande Instance HANA sur Azure. 

> [!IMPORTANT]  
> En cas d’utilisation de portée mondiale pour activer l’accès direct entre vos unités de grande Instance HANA et les ressources locales, le flux de données et de contrôle de réseau est **ne pas acheminé par le biais des réseaux virtuels Azure**, mais directement entre Microsoft routeurs d’entreprise exchange. Par conséquent les règles de groupe de sécurité réseau ou les ASG ou n’importe quel type de pare-feu, appliance virtuelle réseau ou de proxy que vous avez déployé dans un réseau virtuel Azure, ne sont pas bien touchées. **Si vous utilisez l’option ExpressRoute de portée mondiale pour activer l’accès direct à partir de local à des restrictions d’unités HANA grande instance et autorisations pour accéder aux unités HANA de grande Instance doivent être définis dans les pare-feux sur le côté local** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connexion des grandes Instances HANA dans différentes régions Azure
Dans la même façon, comme portée mondiale ExpressRoute peut être utilisé pour se connecter en local aux unités de grande Instance HANA, il peut être utilisé pour se connecter à deux que locataires de grande Instance HANA déployées dans deux régions différentes pour vous. L’isolation est les circuits ExpressRoute qui sont à l’aide de vos clients de grande Instance HANA pour se connecter à Azure dans les deux régions. Il n’existe pas de frais supplémentaires pour la connexion de deux clients de grande Instance HANA qui sont déployés dans deux régions différentes. 

> [!IMPORTANT]  
> Le flux de données et le flux de contrôle du trafic réseau entre les différents HANA grandes locataires de l’instance ne seront pas routées via les réseaux azure. Par conséquent vous ne pouvez pas utiliser les fonctionnalités d’Azure ou des appliances virtuelles réseau pour appliquer des restrictions de communication entre vos deux clients de grandes Instances HANA. 

Pour plus d’informations sur l’obtention de portée mondiale d’ExpressRoute est activé, lisez le document [connecter un réseau virtuel aux grandes instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Connectivité internet de la grande instance HANA
La grande instance HANA ne bénéficie *pas* d’une connectivité directe à Internet. Par exemple, cette limitation peut restreindre votre capacité à enregistrer l’image du système d’exploitation directement avec le fournisseur du système d’exploitation. Vous devrez peut-être travailler avec votre serveur d’outil de gestion des abonnements SUSE Linux Enterprise Server local ou d’un gestionnaire des abonnements Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Chiffrement des données entre des machines virtuelles et la grande instance HANA
Les données transférées entre la grande instance HANA et des machines virtuelles ne sont pas chiffrées. En revanche, vous pouvez activer le chiffrement du trafic pour l’échange simple entre le SGBD HANA et les applications basées sur JDBC/ODBC. Pour plus d’informations, consultez [cette documentation par SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utiliser des unités de grande instance HANA dans plusieurs régions

Pour réaliser un onduleur de jeu de récupération d’urgence, vous devez disposer d’unités de grande Instance de SHANA dans plusieurs régions Azure. Même avec l’utilisation d’Azure [Global Vnet Peering], le routage transitives par défaut ne fonctionne pas entre les locataires de grande Instance HANA dans deux régions différentes. Toutefois, portée mondiale s’ouvre la voie de communication entre les unités de grande Instance HANA que vous avez configuré dans deux régions différentes. Ce scénario d’utilisation de portée mondiale ExpressRoute permet :

 - Réplication de système HANA sans autres proxys ou pare-feu
 - Copie des sauvegardes entre les unités de grande Instance HANA dans deux régions différentes pour effectuer des copies du système ou actualisation du système


![Réseau virtuel connecté aux tampons de grande instance Azure dans différentes régions Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figure montre comment les différents réseaux virtuels dans les deux régions sont connectés à deux circuits ExpressRoute distincts qui sont utilisés pour se connecter à SAP HANA sur Azure (grandes Instances) dans les deux régions Azure (lignes grises). Ce deux interconnexions fait pour protéger contre une panne des msee sur chaque côté. Le flux de communication entre les deux réseaux virtuels dans les deux régions Azure est censé être géré via le [l’homologation globale](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) des deux réseaux virtuels dans les deux régions différentes (ligne en pointillés bleue). La ligne rouge épaisse décrit la connexion ExpressRoute de portée mondiale, qui autorise les unités de grande Instance HANA de vos locataires dans deux régions différentes à communiquer entre eux. 

> [!IMPORTANT] 
> Si plusieurs circuits ExpressRoute sont utilisés, les paramètres de préfixation AS PATH et Local Preference BGP doivent être utilisés pour garantir le routage correct du trafic.

**Étapes suivantes**
- Consultez [Architecture de stockage de SAP HANA (grandes instances)](hana-storage-architecture.md)