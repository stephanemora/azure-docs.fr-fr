---
title: Vue d’ensemble et architecture de SAP HANA et Azure (grandes instances) | Microsoft Docs
description: Vue d’ensemble architectural du déploiement de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3342f3057917202d81359a27accf47ba288b128
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Qu’est-ce que SAP HANA sur Azure (grandes instances) ?

SAP HANA sur Azure (grandes instances) est une solution Azure unique. En plus de fournir des machines virtuelles à des fins de déploiement et d’exécution de SAP HANA, Azure vous offre la possibilité d’exécuter et de déployer SAP HANA sur des serveurs nus qui vous sont dédiés. La solution SAP HANA sur Azure (grandes instances) s’appuie sur un matériel nu de type serveur/hôte non partagé, qui vous est assigné. Le matériel de serveur est incorporé dans des tampons plus volumineux qui contiennent l’infrastructure de calcul/serveur, de réseau et de stockage. En tant que combinaison, il s’agit d’une intégration de centre de données adaptée (TDI) à HANA certifiée. SAP HANA sur Azure (grandes instances) propose différentes références SKU ou tailles de serveurs. Les unités peuvent avoir 72 UC et 768 Go de mémoire et aller jusqu’à 960 UC et 20 To de mémoire.

L’isolation du client dans le tampon d’infrastructure s’effectue dans le cadre des abonnés, de la manière suivante :

- **Mise en réseau** : isolation des clients au sein de la pile d’infrastructures au travers de réseaux virtuels par abonné assigné à un client. Un locataire est assigné à un seul client. Un client peut avoir plusieurs locataires. L’isolation réseau des abonnés interdit la communication réseau entre les abonnés au niveau du tampon d’infrastructure, même si les abonnés appartiennent au même client.
- **Composants de stockage** : isolation par le biais de machines virtuelles de stockage auxquelles des volumes de stockage sont assignés. Les volumes de stockage ne peuvent être assignés qu’à une seule machine virtuelle de stockage. Une machine virtuelle de stockage est assignée exclusivement à un seul locataire dans la pile d’infrastructure certifiée SAP HANA TDI. Par conséquent, les volumes de stockage assignés à une machine virtuelle de stockage sont accessibles dans un seul abonné associé. Et ils ne sont pas visibles entre les différents abonnés déployés.
- **Serveur ou hôte** : une unité de serveur ou hôte n’est pas partagée entre les clients ou les abonnés. Un serveur ou un hôte déployé pour un client constitue une unité de calcul nue atomique qui est assignée à un seul locataire. *Aucun* partitionnement matériel ou logiciel utilisé ne peut vous conduire à partager un hôte ou un serveur avec un autre client. Les volumes de stockage qui sont assignés à la machine virtuelle de stockage du locataire spécifique sont montés sur ce type de serveur. Une ou plusieurs unités de serveur de différentes références SKU peuvent être exclusivement assignées à un seul locataire.
- Au sein d’une solution SAP HANA sur le tampon d’infrastructure Azure (grandes instances), plusieurs abonnés différents sont déployés et isolés les uns par rapport aux autres, au travers des concepts d’abonné sur les niveaux de mise en réseau, de stockage et de calcul. 


Ces unités de serveur nues exécutent uniquement SAP HANA. La couche Application SAP ou la couche intermédiaire de la charge de travail est en cours d’exécution dans les machines virtuelles. Les tampons d’infrastructure qui exécutent SAP HANA sur des unités Azure (grandes instances) sont connectés aux principaux fournisseurs de services réseau Azure. De cette façon, la connectivité à faible latence entre des unités SAP HANA sur Azure (grandes instances) et des machines virtuelles est fournie.

Ce document fait partie des nombreux documents qui traitent de SAP HANA sur Azure (grandes instances). Ce document présente l’architecture de base, les responsabilités et les services fournis par la solution. Il présente également les capacités de haut niveau de la solution. Pour la plupart des autres domaines, tels que la mise en réseau et la connectivité, quatre autres documents couvrent les détails et les informations déroulantes. La documentation de SAP HANA sur Azure (grandes instances) n’aborde pas les aspects de l’installation de SAP NetWeaver ni les déploiements de SAP NetWeaver dans des machines virtuelles. SAP NetWeaver sur Azure est couvert dans des documents distincts disponibles dans le même conteneur de documentation Azure. 


Les différents documents de grande instance HANA couvrent les domaines suivants :

- [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installer et configurer SAP HANA (grandes instances) sur Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Résolution des problèmes et analyse de SAP HANA (grandes instances) sur Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Configuration de la haute disponibilité dans SUSE à l’aide de STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Sauvegarde et restauration du système d’exploitation pour les références (SKU) de type II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Définitions

Plusieurs définitions communes sont largement utilisées dans ce guide sur l’architecture et le déploiement technique. Veuillez noter les termes suivants et leur signification :

- **IaaS** : Infrastructure as a service.
- **PaaS** : Platform as a service.
- **SaaS** : Software as a service.
- **Composant SAP** : une application SAP spécifique, tel que ERP Central Component (ECC), Business Warehouse (BW), Gestionnaire de solutions ou Enterprise Portal (EP). Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
- **Environnement SAP** : un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier telle que le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
- **Paysage SAP** : fait référence à l’ensemble des ressources SAP dans votre paysage informatique. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
- **Système SAP** : la combinaison de couche SGBD/couche Application, tel que celui d’un système de développement SAP ERP, d’un système de test SAP BW, d’un système de production SAP CRM. Les déploiements Azure ne prennent pas en charge la séparation de ces deux couches entre les sites locaux et Azure. Un système SAP est déployé en local ou dans Azure. Vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local. Pour SAP HANA sur Azure (grandes instances), vous devez héberger la couche Application SAP des systèmes SAP dans des machines virtuelles et l’instance SAP HANA sur une unité dans le tampon SAP HANA sur Azure (grandes instances).
- **Tampon de grande instance** : une pile d’infrastructure matérielle certifiée TDI SAP HANA et dédiée à l’exécution des instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes instances) :** nom officiel de l’offre dans Azure permettant d’exécuter des instances HANA sur le matériel SAP HANA certifié TDI qui est déployé dans des tampons de grande instance dans différentes régions Azure. Le terme associé *Grande instance HANA* est la forme abrégée de *SAP HANA sur Azure (grandes instances)* largement utilisée dans ce guide de déploiement technique.
- **Entre différents locaux :** décrit un scénario dans lequel les machines virtuelles sont déployées vers un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, Azure Active Directory/OpenLDAP local et le DNS local à Azure. Le paysage local est étendu aux ressources Azure des abonnements Azure. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. 

   Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles (tels que les services SGBD). La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure sont possibles. Il s’agit du scénario classique dans lequel la plupart des ressources SAP sont déployées. Pour plus d’informations, consultez [Planifier et concevoir pour la passerelle VPN Azure](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [Créer un réseau virtuel avec une connexion de site à site à l’aide du Portail Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Abonné** : un client déployé dans le tampon de grande instance HANA est isolé dans un *abonné.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles sur le niveau de tampon de grande instance HANA. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires sur le niveau de tampon de grande instance HANA.
- **Catégorie de référence SKU** : pour la grande instance HANA, les deux catégories suivantes de références SKU sont proposées :
    - **Classe de type I** : S72, S72m, S144, S144m, S192 et S192m
    - **Classe de type II** : S384, S384m, S384xm, S576m, S768m et S960m


De nombreuses ressources supplémentaires sont disponibles sur le déploiement d’une charge de travail SAP dans le cloud. Si vous planifiez un déploiement de SAP HANA dans Azure, vous devez être expérimenté, connaitre les principes d’Azure IaaS et le déploiement des charges de travail SAP sur Azure IaaS. Avant de continuer, consultez [Utiliser des solutions SAP sur des machines virtuelles Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations. 

## <a name="certification"></a>Certification

Outre la certification NetWeaver, SAP requiert une certification spéciale pour SAP HANA, afin de prendre en charge SAP HANA sur certaines infrastructures, telles que Azure IaaS.

La Remarque SAP principale sur NetWeaver, et dans une certaine mesure sur la certification SAP HANA, est [Remarque SAP n° 1928533 - Applications SAP sur Azure : types de machines virtuelles Azure et produits pris en charge](https://launchpad.support.sap.com/#/notes/1928533).

La [Remarque SAP n° 2316233 - SAP HANA sur Microsoft Azure (grandes instances)](https://launchpad.support.sap.com/#/notes/2316233/E) est également importante. Elle aborde la solution décrite dans ce guide. En outre, vous pouvez exécuter SAP HANA dans le type de machine virtuelle GS5 d’Azure. Les informations relatives à cette situation sont disponibles sur [le site Web SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La solution SAP HANA sur Azure (grandes instances) à laquelle il est fait référence dans la Remarque SAP n° 2316233 fournit aux clients SAP et Microsoft la possibilité de déployer les ressources volumineuses SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA ou d’autres charges de travail SAP HANA dans Azure. La solution est basée sur le tampon matériel dédié et certifié SAP-HANA ([SAP HANA Tailored Datacenter Integration – TDI](https://scn.sap.com/docs/DOC-63140)). Si vous exécutez une solution TDI configurée de SAP HANA, toutes les applications SAP HANA (tels que SAP Business Suite sur SAP HANA, SAP BW sur SAP HANA, S4/HANA et BW4/HANA) fonctionne sur l’infrastructure matérielle.

Par rapport à SAP HANA en cours d’exécution dans des machines virtuelles, cette solution offre un avantage. Elle fournit des volumes de grande capacité mémoire. Pour activer cette solution, vous devez comprendre les aspects clés suivants :

- La couche Application SAP et les applications autres que SAP s’exécutent dans des machines virtuelles hébergées dans les tampons matériels Azure habituels.
- Les déploiements d’applications, de centres de données et d’infrastructures locaux du client sont connectés à la plateforme cloud via ExpressRoute (recommandé) ou un réseau privé virtuel (VPN). Le répertoire Active Directory et le DNS sont également étendus dans Azure.
- L’instance de base de données SAP HANA pour les charges de travail HANA s’exécute sur SAP HANA sur Azure (grandes instances). Le tampon de grande instance est connecté au réseau Azure, afin que les logiciels s’exécutant dans des machines virtuelles puissent interagir avec l’instance HANA s’exécutant dans la grande instance HANA.
- Le matériel de SAP HANA sur Azure (grandes instances) est un matériel dédié fourni dans une Infrastructure as a Service (IaaS) avec SUSE Linux Enterprise Server ou Red Hat Enterprise Linux préinstallé. Comme pour les machines virtuelles, les mises à jour et la maintenance supplémentaires du système d’exploitation relèvent de votre responsabilité.
- L’installation de HANA ou tous les composants supplémentaires nécessaires à l’exécution de SAP HANA sur les unités de la grande instance HANA est de votre responsabilité. Toutes les opérations en cours respectives et l’administration de SAP HANA sur Azure sont également de votre responsabilité.
- Outre les solutions décrites ici, vous pouvez installer d’autres composants dans votre abonnement Azure qui se connecte à SAP HANA sur Azure (grandes instances). Par exemple, les composants qui permettent la communication directement avec la base de données SAP HANA, serveurs jump, serveurs RDP, SAP HANA Studio, scénarios SAP Data Services for SAP BI ou des solutions de surveillance réseau.
- Comme dans Azure, la grande instance HANA prend en charge des fonctionnalités de haute disponibilité et de récupération d’urgence.

## <a name="architecture"></a>Architecture

À un niveau élevé, SAP HANA sur la solution Azure (grandes instances) dispose de la couche Application SAP résidant dans des machines virtuelles. La couche de bases de données réside sur le matériel configuré TDI SAP situé dans un tampon de grande Instance dans la même région Azure connectée à Azure IaaS.

> [!NOTE]
> Déployez la couche Application SAP dans la même région Azure que la couche SGBD SAP. Cette règle est bien documentée dans les informations publiées sur les charges de travail SAP sur Azure. 

L’architecture globale de SAP HANA sur Azure (grandes instances) fournit une configuration matérielle certifiée TDI SAP, c’est-à-dire un serveur non virtualisé, nu et hautes performances pour la base de données SAP HANA. Elle fournit également la capacité et la flexibilité d’Azure pour mettre à l’échelle les ressources pour que la couche Application SAP réponde à vos besoins.

![Présentation de l’architecture de SAP HANA sur Azure (grandes instances)](./media/hana-overview-architecture/image1-architecture.png)

L’architecture présentée est divisée en trois sections :

- **À droite** : affiche une infrastructure sur site qui exécute différentes applications dans les centres de données afin que les utilisateurs finaux puissent accéder à des applications métier, telles que SAP. Dans l’idéal, cette infrastructure locale est ensuite connectée à Azure avec [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Au centre** : montre Azure IaaS et, dans ce cas, l’utilisation des machines virtuelles pour héberger SAP ou d’autres applications qui utilisent SAP HANA en tant que système SGBD. Les instances HANA plus petites qui fonctionnent avec la mémoire que les machines virtuelles fournissent sont déployées dans des machines virtuelles avec leur couche Application. Pour plus d’informations sur les machines virtuelles, consultez[Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/).

   Les services réseau Azure permettent de regrouper des systèmes SAP ainsi que d’autres applications dans des réseaux virtuels. Ces réseaux virtuels se connectent à des systèmes locaux et à SAP HANA sur Azure (grandes instances).

   Pour les bases de données et les applications SAP NetWeaver prises en charge pour être exécutées dans Azure, consultez [Remarque sur la prise en charge SAP n° 1928533 - Applications SAP sur Azure : types de machines virtuelles Azure et produits pris en charge](https://launchpad.support.sap.com/#/notes/1928533). Pour la documentation sur le déploiement des solutions SAP sur Azure, consultez :

  -  [Utiliser SAP sur des machines virtuelles Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Utiliser des solutions SAP sur des machines virtuelles Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **À gauche** : montre le matériel certifié TDI SAP HANA dans le tampon de grande instance Azure. Les unités de grande instance HANA sont connectées aux réseaux virtuels de votre abonnement à l’aide de la même technologie que la connectivité du système local à Azure.

Le tampon de grande instance Azure combine les composants suivants :

- **Calcul** : serveurs basés sur des processeurs Intel Xeon E7-8890v3 ou Intel Xeon E7-8890v4 qui fournissent les capacités de calcul nécessaires et sont certifiés SAP HANA.
- **Réseau** : une structure réseau haut débit unifiée qui relie le calcul, le stockage et les composants LAN.
- **Stockage** : une infrastructure de stockage accessible via une structure réseau unifiée. La capacité de stockage spécifique fournie varie selon la configuration SAP HANA sur Azure (grandes instances) spécifique déployée. Une capacité de stockage supplémentaire est disponible pour un coût mensuel supérieur.

Dans l’infrastructure multilocataire du tampon de grande instance, les clients sont déployés en tant que locataires isolés. Au moment du déploiement de l’abonné, nommez un abonnement Azure au sein de votre inscription Azure. Cet abonnement Azure correspond à la grande instance HANA facturée. Ces locataires ont une relation 1:1 avec l’abonnement Azure. En termes de réseau, il est possible d’accéder à une unité de grande instance HANA déployée sur un abonné dans une région Azure à partir de différents réseaux virtuels qui appartiennent à différents abonnements Azure. Ces abonnements Azure doivent appartenir à la même inscription Azure. 

Comme avec les machines virtuelles, SAP HANA sur Azure (grandes instances) est proposé dans plusieurs régions Azure. Pour offrir des fonctionnalités de récupération d’urgence, vous pouvez choisir d’accepter. Les différents tampons de grande instance d’une même région géopolitique sont connectés entre eux. Par exemple, les tampons de grande instance HANA des États-unis de l’Ouest et des États-Unis de l’Est sont connectés via une liaison réseau dédiée à des fins de réplication de la récupération d’urgence. 

Tout comme vous avez le choix entre différents types de machines virtuelles avec des machines virtuelles Azure, vous pouvez choisir parmi différentes références SKU de la grande instance HANA adaptées aux divers types de charges de travail de SAP HANA. SAP applique la mémoire aux rapports de socket de processeur pour différentes charges de travail en fonction des générations de processeurs Intel. Le tableau qui suit affiche les types de références SKU proposées.

À compter de juillet 2017, SAP HANA sur Azure (grandes instances) est disponible dans plusieurs configurations dans les régions Azure des États-Unis de l’Ouest et de l’Est, de l’Australie de l’Est et du Sud-Est, d’Europe de l'Ouest et d’Europe du Nord.

| Solution SAP | UC | Mémoire | Stockage | Disponibilité |
| --- | --- | --- | --- | --- |
| Optimisée pour OLAP : SAP BW, BW/4HANA<br /> ou SAP HANA pour les charges de travail OLAP génériques | SAP HANA sur Azure S72<br /> - 2 x processeurs Intel® Xeon® E7-8890 v3<br /> 36 cœurs et 72 threads d’UC |  768 Go |  3 To | Disponible |
| --- | SAP HANA sur Azure S144<br /> - 4 x processeurs Intel® Xeon® E7-8890 v3<br /> 72 cœurs et 144 threads d’UC |  1,5 To |  6 To | Plus proposé |
| --- | SAP HANA sur Azure S192<br /> - 4 x processeurs Intel® Xeon® E7-8890 v4<br /> 96 cœurs et 192 threads d’UC |  2 TO |  8 To | Disponible |
| --- | SAP HANA sur Azure S384<br /> - 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  4 TO |  16 TO | Disponible |
| Optimisée pour OLTP : SAP Business Suite<br /> sur SAP HANA ou S/4HANA (OLTP),<br /> OLTP générique | SAP HANA sur Azure S72m<br /> - 2 x processeurs Intel® Xeon® E7-8890 v3<br /> 36 cœurs et 72 threads d’UC |  1,5 To |  6 To | Disponible |
|---| SAP HANA sur Azure S144m<br /> - 4 x processeurs Intel® Xeon® E7-8890 v3<br /> 72 cœurs et 144 threads d’UC |  3 TO |  12 To | Plus proposé |
|---| SAP HANA sur Azure S192m<br /> - 4 x processeurs Intel® Xeon® E7-8890 v4<br /> 96 cœurs et 192 threads d’UC  |  4 TO |  16 TO | Disponible |
|---| SAP HANA sur Azure S384m<br /> - 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  6,0 To |  18 To | Disponible |
|---| SAP HANA sur Azure S384xm<br /> - 8 x processeurs Intel® Xeon® E7-8890 v4<br /> 192 cœurs et 384 threads d’UC |  8,0 To |  22 To |  Disponible |
|---| SAP HANA sur Azure S576m<br /> - 12 x processeurs Intel® Xeon® E7-8890 v4<br /> 288 cœurs et 576 threads d’UC |  12,0 To |  28 To | Disponible |
|---| SAP HANA sur Azure S768m<br /> - 16 x processeurs Intel® Xeon® E7-8890 v4<br /> 384 cœurs et 768 threads d’UC |  16,0 To |  36 To | Disponible |
|---| SAP HANA sur Azure S960m<br /> - 20 x processeurs Intel® Xeon® E7-8890 v4<br /> 480 cœurs et 960 threads d’UC |  20,0 To |  46 To | Disponible |

- Cœurs d’unité centrale = somme des cœurs d’UC non multithreads dérivée de la somme des processeurs de l’unité de serveur.
- Threads d’UC = somme des threads de calcul fournis par les cœurs d’UC multithreads dérivée de la somme des processeurs de l’unité de serveur. Toutes les unités sont configurées par défaut pour utiliser la technologie Hyper-Threading.


Les configurations spécifiques choisies dépendent de la charge de travail, des ressources d’UC et de la mémoire souhaitée. La charge de travail OLTP est en mesure d’utiliser des références SKU optimisées pour les charges de travail OLAP. 

La base matérielle de toutes les offres est certifiée TDI SAP HANA. Deux classes de matériel différentes divisent les références SKU comme suit :

- S72, S72m, S144, S144m, S192 et S192m, nommées références SKU de « classe de type I ».
- S384, S384m, S384xm, S576m, S768m et S960m, nommées références SKU de « classe de type II ».

Un tampon de grande instance HANA complet n’est pas exclusivement alloué à l’utilisation d’un client unique. Cela s’applique également aux racks de ressources de calcul et de stockage connectés par le biais d’une structure réseau déployée dans Azure. L’infrastructure de grande instance HANA, par exemple Azure, déploie plusieurs &quot;abonnés&quot; client isolés les uns des autres selon les trois niveaux suivants :

- **Réseau** : isolation par le biais des réseaux virtuels dans le tampon de grande instance HANA.
- **Stockage** : isolation par le biais de machines virtuelles auxquelles des volumes de stockage sont assignés et qui isolent les volumes de stockage entre les abonnés.
- **Calcul** : assignation dédiée des unités de serveur à un seul abonné. Aucun partitionnement matériel ou logiciel des unités de serveur. Aucun partage d’une unité de serveur ou hôte unique entre les locataires. 

Les déploiements d’unités de grande instance HANA entre les différents abonnés ne sont pas visibles entre eux. Les unités de grande instance HANA déployées dans différents abonnés ne peuvent pas communiquer directement entre elles au niveau du tampon de grande instance HANA. Seules les unités de grande instance HANA d’un abonné unique peuvent communiquer entre elles au niveau du tampon de grande instance HANA.

Un abonné déployé dans le tampon de grande instance est assigné à un abonnement Azure dans le cadre de la facturation. En termes de réseau, il est accessible à partir des réseaux virtuels d’autres abonnements Azure dans la même inscription Azure. Si vous effectuez un déploiement avec un autre abonnement Azure dans la même région Azure, vous pouvez également demander à obtenir un locataire de grande instance HANA distinct.

Il existe des différences importantes entre l’exécution de SAP HANA sur la grande instance HANA et l’exécution de SAP HANA sur des machines virtuelles déployées dans Azure :

- Il n’existe aucune couche Virtualisation pour SAP HANA sur Azure (grandes instances). Vous bénéficiez des performances du matériel nu sous-jacent.
- Contrairement à Azure, le serveur SAP HANA sur Azure (grandes instances) est dédié à un client spécifique. Il est impossible qu’une unité de serveur ou un hôte fasse l’objet d’un partitionnement matériel ou logiciel. Par conséquent, une unité de grande instance HANA est assignée dans son intégralité à un abonné, et donc à vous en tant que client. Un redémarrage ou un arrêt du serveur n’entraîne pas automatiquement le déploiement du système d’exploitation et de SAP HANA sur un autre serveur. (Pour les références SKU de classe de type I, la seule exception est lorsqu’un serveur rencontre des problèmes et qu’un redéploiement doit être effectué sur un autre serveur.)
- Contrairement à Azure, où les types de processeurs hôtes sont sélectionnés en fonction du meilleur rapport prix/performances, les types de processeurs choisis pour SAP HANA sur Azure (grandes instances) sont les plus performants de la gamme de processeurs Intel E7v3 et E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Exécution de plusieurs instances SAP HANA sur une unité de grande instance HANA
Il est possible d’héberger plusieurs instances SAP HANA actives sur les unités de grande instance HANA. Afin de fournir les fonctionnalités de captures instantanées du stockage et de récupération d’urgence, une telle configuration requiert un volume défini par instance. Actuellement, les unités de grande instance HANA peuvent être classées comme suit :

- **S72, S72m, S144, S192** : par incréments de 256 Go avec l’unité de départ la plus petite qui fait 256 Go. Des incréments différents, comme 256 Go, 512 Go, etc., peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.
- **S144m et S192m** : par incréments de 256 Go avec la plus petite unité qui fait 512 Go. Des incréments différents, comme 512 Go et 768 Go peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.
- **Classe Type II** : par incréments de 512 Go avec l’unité de départ la plus petite qui fait 2 To. Des incréments différents, comme 512 Go, 1 Go et 1,5 To peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.

Voici quelques exemples de ce à quoi peut ressembler l’exécution de plusieurs instances SAP HANA.

| SKU | Taille de la mémoire | Taille de stockage | Tailles avec plusieurs bases de données |
| --- | --- | --- | --- |
| S72 | 768 Go | 3 To | 1 instance HANA de 768 Go<br /> ou 1 instance de 512 Go + 1 instance de 256 Go<br /> ou 3 instances de 256 Go | 
| S72m | 1,5 To | 6 To | 3 instances HANA de 512 Go<br />ou 1 instance de 512 Go + 1 instance de 1 To<br />ou 6 instances de 256 Go<br />ou 1 x instance de 1,5 To | 
| S192m | 4 To | 16 TO | 8 instances de 512 Go<br />ou 4 instances de 1 To<br />ou 4 instances de 512 Go + 2 instances de 1 To<br />ou 4 instances de 768 Go + 2 instances de 512 Go<br />ou 1 instance de 4 To |
| S384xm | 8 To | 22 To | 4 instances de 2 To<br />ou 2 instances de 4 To<br />ou 2 instances de 3 To + 1 instance de 2 To<br />ou 2 instances de 2,5 To + 1 instance de 3 To<br />ou 1 instance de 8 To |


Il existe d’autres variantes. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Utiliser une hiérarchisation des données et des nœuds d’extension SAP HANA
SAP prend en charge un modèle de hiérarchisation des données pour SAP BW pour différentes versions SAP NetWeaver et pour SAP BW/4HANA. Pour plus d’informations sur le modèle de hiérarchisation des données, consultez le document SAP [SAP BW/4HANA et SAP BW sur HANA avec nœuds d’extension SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
La grande instance HANA vous permet d’utiliser la configuration de l’option 1 des nœuds d’extension SAP HANA, comme détaillé dans la FAQ et les documents de blog SAP. Les configurations de l’option 2 peuvent être définies avec les références SKU de grandes instances HANA suivantes : S72m, S192, S192m, S384 et S384m. 

Les avantages ne sont pas forcément visibles immédiatement lorsque l’on parcourt la documentation. Toutefois, en examinant les instructions portant sur le redimensionnement de SAP, vous pouvez trouver un avantage à utiliser les nœuds d’extension SAP HANA avec l’option 1 et l’option 2. Voici quelques exemples :

- Les instructions de dimensionnement de SAP HANA exigent généralement de doubler la quantité du volume de données en mémoire. Lorsque vous exécutez votre instance SAP HANA avec les données chaudes, vous n’avez que 50 % ou moins de la mémoire maximum remplis par les données. Ce qui reste de la mémoire est en principe conservé pour permettre à SAP HANA de faire son travail.
- Autrement dit, dans une unité S192 de grande instance HANA, dotée de 2 To de mémoire et exécutant une base de données SAP BW, vous ne disposez que de 1 To en volume de données.
- Si vous utilisez un nœud d’extension supplémentaire SAP HANA de l’option 1, ainsi qu’une référence SKU S192 de grande instance HANA, vous obtenez une capacité supplémentaire de 2 To pour le volume de données. Dans la configuration de l’option 2, vous disposez de 4 To supplémentaires pour le volume de données tièdes. Par rapport au nœud à accès rapide, la capacité de mémoire totale du nœud d’extension « à accès moyen » peut servir au stockage des données pour l’option 1. Le doublage de la mémoire peut être utilisé pour le volume de données dans la configuration du nœud d’extension SAP HANA de l’option 2.
- Vous vous retrouvez avec une capacité de 3 To pour vos données et d’un rapport accès rapide/accès moyen de 1:2 pour l’option 1. Vous avez 5 To de données et un rapport de 1:4 avec la configuration de nœud d’extension de l’option 2.

Plus le volume de données est important par rapport à la mémoire, plus la probabilité est forte que les données tièdes que vous demandez soient stockées dans le stockage sur disque.


## <a name="operations-model-and-responsibilities"></a>Responsabilités et modèle opérationnel

Le service fourni avec SAP HANA sur Azure (grandes instances) est aligné avec les services Azure IaaS. Vous bénéficiez d’une grande instance HANA avec un système d’exploitation installé optimisé pour SAP HANA. Comme avec les machines virtuelles Azure IaaS, la plupart des tâches de renforcement du SE, d’installation des logiciels supplémentaires, d’installation de HANA, le fonctionnement du SE et de HANA et la mise à jour du SE et de HANA relèvent de votre responsabilité. Microsoft ne vous impose pas de mises à jour du SE, ni de HANA.

![Responsabilités relatives à SAP HANA sur Azure (grandes instances)](./media/hana-overview-architecture/image2-responsibilities.png)

Comme indiqué dans le diagramme, SAP HANA sur Azure (grandes instances) est une offre IaaS mutualisée. Et par conséquent, la répartition des responsabilités se trouve à la limite entre le SE et l’infrastructure. Microsoft se charge de tous les aspects du service sous la ligne du système d’exploitation. Vous êtes responsable de tous les aspects du service au-dessus de la ligne. Le système d’exploitation est sous votre responsabilité. Vous pouvez continuer à utiliser les méthodes locales les plus récentes que vous employez peut-être pour la conformité, la sécurité, la gestion des applications, la base et la gestion du système d’exploitation. Les systèmes sont considérés comme faisant partie de votre réseau, à tous les égards.

Ce service étant optimisé pour SAP HANA, vous devez collaborer avec Microsoft dans certains domaines pour utiliser les capacités de l’infrastructure sous-jacente et obtenir de meilleurs résultats.

La liste suivante fournit plus de détails sur chacune des couches et vos responsabilités :

**Mise en réseau** : tous les réseaux internes pour le tampon de grande Instance exécutant SAP HANA. Votre responsabilité inclut l’accès au stockage, la connectivité entre les instances (pour la montée en puissance et d’autres fonctions), la connectivité avec le paysage et la connectivité à Azure, à l’endroit où la couche Application SAP est hébergée dans les machines virtuelles Azure. Elle inclut également la connectivité WAN entre les centres de données Azure pour la réplication à des fins de récupération d’urgence. Tous les réseaux sont partitionnés par l’abonné et la qualité de service est appliquée.

**Stockage**: le stockage virtualisé partitionné pour tous les volumes requis par les serveurs de SAP HANA, ainsi que pour les captures instantanées. 

**Serveurs** : les serveurs physiques dédiés à l’exécution des bases de données SAP HANA assignées aux abonnés. Les serveurs de classe Type I des références SKU font abstraction du matériel. Avec ces types de serveurs, la configuration du serveur est collectée et gérée dans des profils, qui peuvent être déplacés d’un matériel physique à un autre. Ce déplacement (manuel) d’un profil par des opérations peut être comparé à la réparation des services Azure. Les serveurs des références SKU de classe Type II n’offrent pas ce type de fonction.

**SDDC** : le logiciel de gestion utilisé pour gérer les centres de données comme des entités à définition logicielle. Il permet à Microsoft de regrouper des ressources à des fins de mise à l’échelle, de disponibilité et de performances.

**Système d’exploitation** : le SE que vous choisissez (SUSE Linux ou Red Hat Linux) et qui s’exécute sur les serveurs. Les images du système d’exploitation que vous obtenez sont les images fournies par chaque fournisseur Linux à Microsoft pour l’exécution de SAP HANA. Vous devez être abonné au fournisseur Linux de l’image optimisée pour SAP HANA spécifique. Vous êtes responsable de l’enregistrement des images auprès du fournisseur du SE. 

À partir du point de transfert de Microsoft, vous êtes également responsable des différentes mises à jour correctives du système d’exploitation Linux. Cette mise à jour corrective inclut des packages supplémentaires qui peuvent être nécessaires pour réussir l’installation de SAP HANA et qui ne sont pas inclus par le fournisseur Linux spécifique dans ses images de SE optimisé pour SAP HANA. (Pour plus d’informations, consultez la documentation d’installation HANA de SAP et les Remarques SAP.) 

Vous êtes responsable de la mise à jour corrective du système d’exploitation en raison de mauvais fonctionnement ou d’optimisation du système d’exploitation et de ses pilotes par rapport à du matériel de serveur spécifique. Vous êtes également responsable de la sécurité ou de la mise à jour corrective fonctionnelle du système d’exploitation. 

Vous êtes également responsable de l’analyse et de la planification de la capacité des éléments suivants :

- consommation des ressources d’UC
- consommation de la mémoire
- volumes de disques liés à l’espace libre, à l’IOPS et à la latence
- trafic en volume du réseau entre la grande instance HANA et la couche Application SAP.

L’infrastructure sous-jacente de la grande instance HANA fournit une fonction de sauvegarde et de restauration du volume du SE. L’utilisation de cette fonctionnalité relève également de votre responsabilité.

**Intergiciel** : l’instance SAP HANA principalement. L’administration, les opérations et la surveillance relèvent de votre responsabilité. Vous pouvez utiliser la fonction fournie pour utiliser des captures instantanées de stockage à des fins de récupération d’urgence, de restauration et de sauvegarde. Ces fonctionnalités sont fournies par l’infrastructure. Vos responsabilités incluent également la conception d’une haute disponibilité ou de récupération d’urgence avec ces fonctionnalités, leur utilisation et l’analyse pour déterminer si les captures instantanées de stockage sont correctement exécutées.

**Données** : vos données managées par SAP HANA et d’autres données telles que les fichiers de sauvegarde situés sur des volumes ou des partages de fichiers. Vos responsabilités incluent l’analyse de l’espace libre du disque et la gestion du contenu sur les volumes. Vous êtes également chargé d’analyser de l’exécution réussie des sauvegardes de volumes de disque et des captures instantanées de stockage. L’exécution réussie de la réplication de données pour les sites de récupération d’urgence est sous la responsabilité de Microsoft.

**Applications :** les instances d’application SAP ou, dans le cas d’applications autres que SAP, la couche Application de ces applications. Vos responsabilités incluent le déploiement, l’administration, les opérations et l’analyse de ces applications. Vous êtes responsable de la planification de la capacité de la consommation des ressources de l’UC, la consommation de mémoire, la consommation du stockage Azure et la consommation de la bande passante réseau dans les réseaux virtuels. Vous êtes également chargé de la planification de la capacité pour la consommation des ressources à partir de réseaux virtuels vers SAP HANA sur Azure (grandes instances).

**WAN** : les connexions que vous établissez en local vers des déploiements Azure pour des charges de travail. Tous les clients disposant de la grande instance HANA utilisent Azure ExpressRoute pour la connectivité. Cette connexion ne fait pas partie de la solution SAP HANA sur solution Azure (grandes instances). Vous êtes responsable de l’installation de cette connexion.

**Archive** : vous préférerez peut-être archiver des copies de données à l’aide de vos propres méthodes dans des comptes de stockage. L’archivage inclut de la gestion, de la conformité, des coûts et des opérations. Vous êtes chargé de générer des copies de l’archive et des sauvegardes sur Azure et de les stocker d’une manière conforme.

Consultez le [SLA pour SAP HANA sur des grandes instances Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Dimensionnement

Le redimensionnement de la grande instance HANA n’est pas différent du redimensionnement HANA en général. Pour les systèmes déployés et existants, si vous souhaitez les déplacer à partir d’autres SGBDR vers HANA, SAP fournit plusieurs rapports qui s’exécutent sur vos systèmes SAP existants. Si la base de données est déplacée vers HANA, ces rapports vérifient les données et calculent la mémoire requise pour l’instance HANA. Pour obtenir plus d’informations sur l’exécution de ces rapports et obtenir leurs mises à jour correctives ou versions les plus récentes, lisez les Remarques SAP suivantes :

- [Note de support SAP #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Dimensionnement de SAP Suite sur HANA)
- [Note de support SAP #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Rapport de dimensionnement de Suite sur HANA et S/4 HANA)
- [Remarque SAP n° 2121330 - FAQ : rapport de redimensionnement SAP BW sur HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Remarque SAP n° 1736976 - Rapport de redimensionnement de BW sur HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Remarque SAP n°2296290 - Nouveau rapport de redimensionnement de BW sur HANA](https://launchpad.support.sap.com/#/notes/2296290)

Pour les nouvelles implémentations, SAP Quick Sizer permet de calculer les besoins en mémoire de l’implémentation du logiciel SAP sur HANA.

La mémoire requise pour HANA augmente à mesure que le volume de données augmente. Faites attention à votre consommation de mémoire actuelle afin de mieux prévoir ce qui va se passer dans l’avenir. Selon la mémoire requise, vous pouvez mapper votre demande à l’une des références SKU de la grand instance HANA.

## <a name="requirements"></a>Configuration requise

Cette liste indique la configuration requise pour l’exécution de SAP HANA sur Azure (plus grandes instances).

**Microsoft Azure**

- Un abonnement Azure qui peut être lié à SAP HANA sur Azure (grandes instances).
- Contrat de support Premier Microsoft. Pour plus d’informations sur l’exécution de SAP dans Azure, consultez [Remarque sur la prise en charge SAP n° 2015553 – SAP sur Microsoft Azure : composants requis de la prise en charge](https://launchpad.support.sap.com/#/notes/2015553). Si vous utilisez des unités de grande instance HANA incluant 384 UC et plus, vous devez également étendre le contrat de support Premier pour inclure Azure Rapid Response.
- Reconnaissance des références de la grande instance HANA dont vous avez besoin après avoir effectué un exercice de redimensionnement avec SAP.

**Connectivité réseau**

- ExpressRoute entre le système local et Azure : pour connecter votre centre de données local à Azure, assurez-vous de commander au moins une connexion de 1 Gbit/s à votre ISP. 

**Système d’exploitation**

- Licences pour SUSE Linux Enterprise Server 12 pour les applications SAP.

   > [!NOTE] 
   > Le système d’exploitation livré par Microsoft n’est pas enregistré avec SUSE. Il n’est pas connecté à une instance de l’outil de gestion des abonnements.

- L’outil de gestion des abonnements SUSE Linux déployé dans Azure sur une machine virtuelle. Cet outil offre la capacité pour SAP HANA sur Azure (grandes instances) d’être inscrit et respectivement mis à jour par SUSE. (Il n’existe aucun accès à internet dans le centre de données de la grande instance HANA.) 
- Licences pour Red Hat Enterprise Linux 6.7 ou 7.2 pour SAP HANA.

   > [!NOTE]
   > Le système d’exploitation livré par Microsoft n’est pas enregistré avec Red Hat. Il n’est pas connecté à une instance de l’outil de gestion des abonnements Red Hat.

- Gestionnaire des abonnements Red Hat déployé dans Azure sur une machine virtuelle. Le gestionnaire des abonnements Red Hat permet à SAP HANA sur Azure (grandes instances) d’être inscrit et respectivement mis à jour par Red Hat. (Il n’existe aucun accès direct à internet à partir de l’abonné déployé sur le tampon de grande instance Azure.)
- Le système SAP requiert également un contrat de support avec votre fournisseur Linux. Cette exigence n’est pas supprimée par la solution de la grande instance HANA ni par le fait de l’exécution de Linux dans Azure. Contrairement à certaines images de la galerie Linux Azure, les frais de service ne sont *pas* inclus dans l’offre de solution de la grande instance HANA. Vous êtes tenu de respecter les exigences de SAP en matière de contrat de support avec le distributeur Linux. 
   - Pour connaître les exigences en matière de contrat de support avec SUSE Linux, consultez [Remarque SAP n° 1984787 - SUSE LINUX Enterprise Server 12 : remarques d’installation](https://launchpad.support.sap.com/#/notes/1984787) et [Remarque SAP n ° 1056161 - Support prioritaire SUSE pour les applications SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Pour Red Hat Linux, vous devez disposer des bons niveaux d’abonnement, y compris la prise en charge et les mises à jour du service des systèmes d’exploitation de la grande instance HANA. Red Hat recommande l’abonnement Red Hat Enterprise Linux pour [Solutions SAP] (https://access.redhat.com/solutions/3082481. 

Pour consulter la matrice de prise en charge des différentes versions SAP HANA avec les différentes versions Linux, reportez-vous à [Remarque SAP n° 2235581](https://launchpad.support.sap.com/#/notes/2235581).

Pour la matrice de compatibilité des versions de système d’exploitation et de microprogramme/pilote HLI, consultez [Mise à niveau du système d’exploitation pour HLI](os-upgrade-hana-large-instance.md).


**Base de données**

- Licences et composants d’installation logiciels pour SAP HANA (édition Enterprise ou Platform).

**Applications**

- Licences et composants d’installation logiciels pour toutes les applications SAP se connectant à SAP HANA et associées aux contrats de support SAP.
- Licences et composants d’installation logiciels pour toutes les applications autres que SAP utilisées dans le cadre des environnements SAP HANA sur Azure (grandes instances) et associées aux contrats de support.

**Compétences**

- Expérience et connaissances relatives à Azure IaaS et ses composants.
- Expérience et connaissances du mode de déploiement d’une charge de travail SAP dans Azure.
- Personnel certifié pour l’installation de SAP HANA.
- Compétences en architecture SAP pour concevoir la haute disponibilité et la récupération d’urgence autour de SAP HANA.

**SAP**

- Vous devez être un client SAP et disposer d’un contrat de support auprès de SAP.
- En particulier pour les implémentations de références SKU de grande instance HANA de classe Type II, consultez SAP pour connaître les versions de SAP HANA et les configurations éventuelles sur du matériel monté en puissance de grande taille.


## <a name="storage"></a>Stockage

La disposition de stockage pour SAP HANA sur Azure (grandes instances) est configurée par SAP HANA sur le modèle de déploiement classique via les recommandations de SAP. Les instructions sont documentées dans le livre blanc [Exigences de stockage SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

La grande instance HANA de classe Type I est livrée avec quatre fois le volume de mémoire et le volume de stockage. Pour les unités de grande instance HANA de classe Type II, le stockage n’est pas quatre fois plus élevé. Le volume fourni dans les unités est prévu pour le stockage des sauvegardes de fichiers journaux HANA. Pour plus d’informations, consultez [Installer et configurer SAP HANA (grandes instances) sur Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consultez le tableau suivant pour connaître l’allocation de stockage. La table indique la capacité approximative des différents volumes fournis avec les diverses unités de grande instance HANA.

| Référence SKU de grande instance HANA | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1 280 Go | 512 Go | 768 Go | 512 Go |
| S72m | 3 328 Go | 768 Go |1 280 Go | 768 Go |
| S192 | 4 608 Go | 1 024 Go | 1 536 Go | 1 024 Go |
| S192m | 11 520 Go | 1 536 Go | 1 792 Go | 1 536 Go |
| S384 | 11 520 Go | 1 536 Go | 1 792 Go | 1 536 Go |
| S384m | 12 000 Go | 2 050 Go | 2 050 Go | 2 040 Go |
| S384xm | 16 000 Go | 2 050 Go | 2 050 Go | 2 040 Go |
| S576m | 20 000 Go | 3 100 Go | 2 050 Go | 3 100 Go |
| S768m | 28 000 Go | 3 100 Go | 2 050 Go | 3 100 Go |
| S960m | 36 000 Go | 4 100 Go | 2 050 Go | 4 100 Go |


Les volumes réels déployés peuvent varier légèrement en fonction du déploiement et de l’outil utilisés pour afficher les tailles de volume.

Si vous subdivisez une référence SKU de grande instance HANA, voici quelques exemples de division possible pour les pièces :

| Partition de la mémoire en Go | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 Go | 160 Go | 304 Go | 160 Go |
| 512 | 768 Go | 384 Go | 512 Go | 384 Go |
| 768 | 1 280 Go | 512 Go | 768 Go | 512 Go |
| 1 024 | 1 792 Go | 640 Go | 1 024 Go | 640 Go |
| 1 536 | 3 328 Go | 768 Go | 1 280 Go | 768 Go |


Ces tailles sont des volumes approximatifs qui peuvent varier légèrement en fonction du déploiement et des outils utilisés pour examiner les volumes. Il existe également d’autres tailles de partition, comme 2,5 To. Ces tailles de stockage sont calculées avec une formule semblable à celle utilisée pour les partitions précédentes. Le terme de « partitions » n’indique pas que les ressources du système d’exploitation, de la mémoire ou du processeur sont partitionnées de quelque manière que ce soit. Il indique simplement les partitions de stockage pour les différentes instances HANA que vous voudrez peut-être déployer sur une seule unité de grande instance HANA. 

Vous aurez peut-être besoin de plus de stockage. Vous pouvez ajouter du stockage par l’achat de stockage supplémentaire en unités de 1 To. Ce stockage supplémentaire peut être ajouté en tant que volume supplémentaire. Il peut également être utilisé pour étendre un ou plusieurs volumes existants. Il n’est pas possible de réduire la taille des volumes tels qu’ils ont été déployés à l’origine et documentés principalement dans le ou les tables ci-dessus. Il est également impossible de modifier les noms des volumes ou les noms des montages. Les volumes de stockage, comme décrit précédemment, sont joints aux unités de grande instance HANA en tant que volumes NFS4.

Vous pouvez utiliser des captures instantanées de stockage à des fins de récupération d’urgence, de restauration et de sauvegarde. Pour plus de détails, consultez [Haute disponibilité et récupération d’urgence SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Chiffrement des données au repos
Le stockage utilisé pour la grande instance HANA permet un chiffrement transparent des données lorsqu’elles sont stockées sur les disques. Lorsqu’une unité de grande instance HANA est déployée, vous pouvez activer ce type de chiffrement. Vous pouvez également modifier les volumes chiffrés après le déploiement. Le passage d’un volume non chiffré à un volume chiffré est transparent et ne requiert aucun temps d’arrêt. 

Avec les références SKU de classe Type I, le volume sur lequel le numéro d’unité logique de démarrage est stocké est chiffré. Dans le cas de références SKU de grande instance HANA de classe Type II, vous devez chiffrer le numéro d’unité logique de démarrage avec les méthodes du système d’exploitation. Pour plus d’informations, contactez l’équipe de gestion des services Microsoft.


## <a name="networking"></a>Mise en réseau

L’architecture des services réseau Azure est un composant clé de la réussite du déploiement des applications SAP sur la grande instance HANA. En règle générale, les déploiements SAP HANA sur Azure (grandes instances) ont un plus grand paysage SAP avec plusieurs solutions SAP distinctes et tailles de bases de données variées, une consommation des ressources d’UC et une utilisation de la mémoire différentes. Il est probable que tous les systèmes SAP ne soient pas basés sur SAP HANA. Votre paysage SAP est probablement un hybride qui utilise :

- Des systèmes SAP déployés en local. En raison de leur taille, ces systèmes ne peuvent pas actuellement être hébergés dans Azure. Par exemple, un système ERP SAP qui s’exécute sur SQL Server (en tant que base de données) et qui nécessite plus de ressources UC ou de mémoire que les machines virtuelles peuvent offrir.
- Des systèmes SAP basés sur SAP HANA déployés en local.
- Des systèmes SAP déployés dans des machines virtuelles. Ces systèmes peuvent être des instances de développement, de test, de bac à sable ou de production pour toutes les applications basées sur SAP NetWeaver qui peuvent être déployées correctement dans Azure (sur des machines virtuelles), en fonction de la demande de mémoire et de consommation de ressources. Ces systèmes peuvent également reposer sur des bases de données telles que SQL Server. Pour plus d’informations, consultez [Remarque SAP n° 1928533 - Applications SAP sur Azure : produits et types de machines virtuelles Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533/E). Et ces systèmes peuvent également reposer sur des bases de données telles que SAP HANA. Pour plus d’informations, consultez [Plateformes IaaS certifiées SAP HANA](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
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

 

### <a name="additional-virtual-network-information"></a>Informations supplémentaires relatives au réseau virtuel

Pour vous connecter à un réseau virtuel sur ExpressRoute, une passerelle Azure doit être créée. Pour en savoir plus, consultez [À propos des passerelles de réseau virtuel pour ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Une passerelle Azure peut être utilisée avec ExpressRoute sur une infrastructure en dehors de Azure ou sur un tampon de grande instance Azure. Une passerelle Azure peut également être utilisée pour se connecter entre des réseaux virtuels. Pour plus d’informations, consultez [Configurer une connexion réseau au réseau pour le Gestionnaire des ressources à l’aide de PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vous pouvez connecter la passerelle Azure à un maximum de quatre connexions ExpressRoute différentes, tant que celles-ci proviennent de routeurs de périphérie d’entreprise Microsoft distincts. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Le débit fourni par une passerelle Azure est différent pour les deux cas d’usage. Pour plus d’informations, consultez [À propos de la passerelle VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Le débit maximal pouvant être atteint avec une passerelle de réseau virtuel est de 10 Gbits/s, à l’aide d’une connexion ExpressRoute. La copie de fichiers entre une machine virtuelle résidant dans un réseau virtuel et un système local (en tant que flux de copie unique) n’atteint pas le débit total des différentes références SKU des passerelles. Pour tirer parti de la bande passante complète de la passerelle de réseau virtuel, utilisez plusieurs flux de données. Ou bien, vous devez copier des fichiers différents dans des flux parallèles d’un seul fichier.


### <a name="networking-architecture-for-hana-large-instance"></a>Architecture de mise en réseau pour la grande instance HANA
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

La latence du réseau rencontrée entre les machines virtuelles et les unités de grande instance HANA peut être supérieure à la latence standard aller-retour sur un réseau de machine virtuelle à machine virtuelle. En fonction de la région Azure, les valeurs mesurées peuvent dépasser une latence aller-retour de 0,7 ms, classée en dessous de la moyenne dans [Remarque SAP n° 1100926 - FAQ : performances du réseau](https://launchpad.support.sap.com/#/notes/1100926/E). Néanmoins, les clients déploient des applications SAP de production basées sur SAP HANA avec succès sur la grande instance SAP HANA. Les clients ayant effectué ces déploiements ont signalé d’importantes améliorations en exécutant leurs applications SAP sur SAP HANA à l’aide d’unités de grande instance HANA. Vérifiez avoir testé minutieusement vos processus métier dans la Grande instance HANA Azure.
 
Afin d’offrir une latence réseau déterministe entre les machines virtuelles Azure et une grande instance HANA, le choix de la référence SKU de passerelle de réseau virtuel est essentiel. Contrairement aux modèles de trafic entre machines locales et machines virtuelles, le modèle de trafic entre les machines virtuelles et la grande instance HANA peut développer des pics courts mais élevés de requêtes et de volumes de données à transmettre. Pour bien gérer ces pics, nous vous recommandons fortement d’utiliser la référence SKU de passerelle UltraPerformance. Pour les références SKU de grande instance HANA de classe Type II, l’utilisation de la référence SKU de la passerelle UltraPerformance en tant que passerelle de réseau virtuel est obligatoire.

> [!IMPORTANT] 
> Étant donné le trafic réseau global entre les couches Base de données et Application SAP, seules les références SKU de passerelle HighPerformance et UltraPerformance pour les réseaux virtuels sont prises en charge pour la connexion à SAP HANA sur Azure (grandes instances). Pour les références SKU de grande instance HANA de Type II, seule la référence SKU de la passerelle UltraPerformance est prise en charge en tant que passerelle de réseau virtuel.



### <a name="single-sap-system"></a>Système SAP unique

L’infrastructure locale présentée précédemment est connectée via ExpressRoute dans Azure. Le circuit ExpressRoute se connecte à un routeur de périphérie d’entreprise. Pour plus d’informations, consultez [Vue d’ensemble technique d’ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Une fois l’itinéraire établi, il se connecte au serveur principal Azure et toutes les régions Azure sont accessibles.

> [!NOTE] 
> Pour exécuter des paysages SAP dans Azure, connectez le routeur de périphérie d’entreprise le plus proche de la région Azure dans le paysage SAP. Les tampons de grande instance Azure sont connectés via les périphériques du routeur de périphérie d’entreprise dédiés pour réduire la latence réseau entre les machines virtuelles dans Azure IaaS et les tampons de grande instance.

La passerelle du réseau virtuel pour les machines virtuelles qui hébergent des instances de l’application SAP est connectée au circuit ExpressRoute. Le même réseau virtuel est connecté à un routeur de périphérie d’entreprise distinct dédié à la connexion aux tampons de grande Instance.

Ce système est un exemple simple d’un système SAP unique. La couche Application SAP est hébergée dans Azure. La base de données SAP HANA s’exécute sur SAP HANA sur Azure (grandes instances). Nous supposons que le fait que le débit de la bande passante de la passerelle de réseau virtuel soit de 2 Gbits/s ou de 10 Gbit/s ne représente pas un goulot d’étranglement.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Plusieurs systèmes SAP ou grands systèmes SAP

Si plusieurs systèmes SAP ou si de grands systèmes SAP sont déployés pour se connecter à SAP HANA sur Azure (grandes instances), le débit de la passerelle de réseau virtuel peut devenir un goulot d’étranglement. Dans ce cas, divisez les couches Application en plusieurs réseaux virtuels. Vous pouvez également créer un réseau virtuel spécial qui se connecte à la grande instance HANA dans les cas suivants :

- Exécution de sauvegardes directement à partir des instances HANA dans la grande instance HANA vers une machine virtuelle dans Azure hébergeant des partages NFS.
- Copie de sauvegardes volumineuses ou d’autres fichiers des unités de grandes instances HANA vers l’espace disque géré dans Azure.

Utilisez un réseau virtuel distinct pour héberger des machines virtuelles qui gèrent le stockage. Ce procédé permet d’éviter les effets d’un fichier volumineux ou de transfert de données à partir de la grande instance HANA vers Azure sur la passerelle de réseau virtuel qui sert les machines virtuelles qui exécutent la couche Application SAP. 

Pour une architecture réseau plus évolutive :

- Utilisez plusieurs réseaux virtuels pour une couche Application SAP unique, plus grande.
- Déployez un réseau virtuel distinct pour chaque système SAP déployé, plutôt que de combiner ces systèmes SAP en sous-réseaux séparés dans le même réseau virtuel.

 Une architecture réseau plus évolutive pour SAP HANA sur Azure (grandes instances) :

![Déployer la couche Application SAP sur plusieurs réseaux virtuels](./media/hana-overview-architecture/image4-networking-architecture.png)

La figure montre la couche Application SAP ou des composants, déployés sur plusieurs réseaux virtuels. Cette configuration a introduit la surcharge de latence inévitable qui s’est produite pendant la communication entre les applications hébergées dans ces réseaux virtuels. Par défaut, le trafic réseau entre les machines virtuelles situées dans différents réseaux virtuels passe par les routeurs de périphérie d’entreprise dans cette configuration. Depuis septembre 2016, ce routage peut être optimisé. 

La méthode d’optimisation et de réduction de la latence de communication entre deux réseaux virtuels consiste à homologuer des réseaux virtuels dans la même région. Cette méthode fonctionne même si ces réseaux virtuels sont dans des abonnements différents. Grâce à l’homologation des réseaux virtuels, la communication entre des machines virtuelles de deux réseaux virtuels différents peut utiliser le réseau Azure principal pour communiquer directement entre elles. La latence s’affiche comme si les machines virtuelles étaient dans le même réseau virtuel. Le trafic d’adressage des plages d’adresses IP connectées via la passerelle du réseau virtuel Azure est acheminé via la passerelle de réseau virtuel individuelle du réseau virtuel. 

Pour plus d’informations sur l’homologation du réseau virtuel, consultez [Homologation de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routage dans Azure

Trois points importants sont à prendre en compte en matière de routage pour SAP HANA sur Azure (grandes instances) :

* L’accès à SAP HANA sur Azure (grandes instances) se fait uniquement via des machines virtuelles dans la connexion ExpressRoute dédiée et non directement via le système local. L’accès direct du réseau local aux unités de grande instance HANA, tel que fourni par Microsoft, n’est pas possible immédiatement. Les restrictions de routage transitives sont dues à l’architecture réseau Azure actuelle utilisée pour la grande instance SAP HANA. Certains clients d’administration et toutes les applications nécessitant un accès direct, tel que le gestionnaire de solutions SAP s’exécutant en local, ne peuvent pas se connecter à la base de données SAP HANA.

* Si vous avez des unités de grande instance HANA déployées dans deux régions Azure différentes à des fins de récupération d’urgence, les mêmes restrictions de routage temporaires s’appliquent. En d’autres termes, les adresses IP d’une unité de grande instance HANA dans une région (par exemple, États-Unis de l’Ouest) ne seront pas routées vers une unité de grande instance HANA déployée dans une autre région (par exemple, États-unis de l’Est). Cette restriction est indépendante de l’utilisation de l’homologation de réseau Azure entre les différentes régions ou des interconnexions de circuits ExpressRoute qui connectent les unités de grande instance HANA aux réseaux virtuels. Pour obtenir une représentation graphique, consultez la figure de la section « Utiliser des unités de grande instance HANA dans plusieurs régions. » Cette restriction, qui provient de l’architecture déployée, empêche l’utilisation immédiate de la réplication du système HANA en tant que fonctionnalité de récupération d’urgence.

* Les unités SAP HANA sur Azure (grandes instances) ont une adresse IP assignée à partir de la plage d’adresses de pools IP du serveur que vous avez envoyée. Pour plus d’informations, consultez [Infrastructure et connectivité SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette adresse IP est accessible via les abonnements Azure et ExpressRoute qui connectent des réseaux virtuels à HANA sur Azure (grandes instances). L’adresse IP assignée hors que plage d’adresses de pools IP du serveur est directement assignée à l’unité matérielle. Elle n’est *plus* assignée via la traduction d'adresses réseau, comme c’était le cas dans les premiers déploiements de cette solution. 

> [!NOTE] 
> Pour surmonter la restriction de routage temporaire, comme expliqué dans les premiers éléments de la liste ci-dessus, utilisez des composants supplémentaires pour le routage. Les composants qui peuvent être utilisés pour surmonter la restriction peuvent être :

> * Un proxy inverse pour router les données, dans un sens et dans l’autre. Par exemple, F5 BIG-IP, NGINX avec Traffic Manager déployé dans Azure en tant que solution de routage de trafic/pare-feu virtuelle.
> * Utilisation des [règles IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) sur une machine virtuelle Linux pour activer le routage entre des emplacements locaux et des unités de grande instance HANA ou entre des unités de grande instance HANA dans différentes régions.

> N’oubliez pas que l’implémentation et la prise en charge des solutions personnalisées qui impliquent des appliances réseau ou IPTables tierces n’est pas fournie par Microsoft. La prise en charge doit être réalisée par le fournisseur du composant utilisé ou de l’intégrateur. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Connectivité internet de la grande instance HANA
La grande instance HANA ne bénéficie *pas* d’une connectivité directe à Internet. Par exemple, cette limitation peut restreindre votre capacité à enregistrer l’image du système d’exploitation directement avec le fournisseur du système d’exploitation. Vous devrez peut-être travailler avec votre serveur d’outil de gestion des abonnements SUSE Linux Enterprise Server local ou d’un gestionnaire des abonnements Red Hat Enterprise Linux.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Chiffrement des données entre des machines virtuelles et la grande instance HANA
Les données transférées entre la grande instance HANA et des machines virtuelles ne sont pas chiffrées. En revanche, vous pouvez activer le chiffrement du trafic pour l’échange simple entre le SGBD HANA et les applications basées sur JDBC/ODBC. Pour plus d’informations, consultez [cette documentation par SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utiliser des unités de grande instance HANA dans plusieurs régions

Vous pouvez avoir d’autres raisons de déployer SAP HANA sur Azure (grandes instances) dans plusieurs régions Azure, en plus de la récupération d’urgence. Vous voulez peut-être accéder à la grande instance HANA à partir de chaque machine virtuelle déployée dans les différents réseaux virtuels dans les régions. Les adresses IP assignées aux différentes unités de grande instance HANA ne sont pas propagées au-delà des réseaux virtuels qui sont directement connectés via leur passerelle vers les instances. Par conséquent, une légère modification est introduite dans la conception du réseau virtuel. Une passerelle de réseau virtuel peut gérer quatre circuits ExpressRoute différents hors des routeurs de périphérie d’entreprise différents. Chaque réseau virtuel connecté à l’un des tampons de grande Instance peut être connecté au tampon de grande instance dans une autre région Azure.

![Réseau virtuel connecté aux tampons de grande instance Azure dans différentes régions Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figure illustre comment les différents réseaux virtuels dans les deux régions sont connectés à deux circuits ExpressRoute distincts utilisés pour se connecter à SAP HANA sur Azure (grandes instances) dans les deux régions Azure. Les connexions nouvellement introduites sont représentées par les lignes rouges rectangulaires. Avec ces connexions, en dehors des réseaux virtuels Azure, les machines virtuelles s’exécutant dans l’un de ces réseaux virtuels peuvent accéder à chacune des différentes unités de grande instance HANA déployées dans les deux régions. Comme le montre la figure, il est supposé que vous disposiez de deux connexions ExpressRoute en local vers les deux régions Azure. Cette configuration est recommandée à des fins de récupération d’urgence.

> [!IMPORTANT] 
> Si plusieurs circuits ExpressRoute sont utilisés, les paramètres de préfixation AS PATH et Local Preference BGP doivent être utilisés pour garantir le routage correct du trafic.


