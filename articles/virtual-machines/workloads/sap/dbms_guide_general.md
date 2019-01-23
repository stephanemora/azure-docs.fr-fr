---
title: Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP | Microsoft Docs
description: Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87d3a44b01dff81242f935c7737bd170fe744536
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246872"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ce guide fait partie de la documentation sur l’implémentation et le déploiement des logiciels SAP sur Microsoft Azure. Avant de lire ce guide, consultez le [Guide de planification et d’implémentation][planning-guide]. Ce document décrit les aspects génériques du déploiement de systèmes SGBD de type SAP sur des machines virtuelles Microsoft Azure à l’aide des fonctionnalités IaaS.

Ce document vient compléter la documentation sur l’installation SAP et les notes SAP, qui représentent les ressources à consulter pour installer et déployer les logiciels SAP sur des plateformes données.

Ce document aborde l’exécution des systèmes SGBD de type SAP sur les machines virtuelles Azure. Il comporte peu de références à des systèmes SGBD spécifiques. Les systèmes SGBD spécifiques sont couverts dans la suite de ce document.

## <a name="definitions-upfront"></a>Préambule : définitions
Les termes suivants sont utilisés dans le document :

* IaaS : Infrastructure as a Service.
* PaaS : Platform as a Service.
* SaaS : Software as a Service.
* Composant SAP : application SAP individuelle telle que ECC, BW, Solution Manager ou EP. Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
* Environnement SAP : un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier telle que le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
* Paysage SAP : ce terme fait référence à l’ensemble des ressources SAP dans le paysage informatique d’un client. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
* Système SAP : ensemble couche SGBD/couche Application, tel que celui d’un système de développement SAP ERP, d’un système de test SAP BW, d’un système de production SAP CRM, etc. Dans les déploiements Azure, il n’est pas possible de répartir ces deux couches entre des sites locaux et Azure. Par conséquent, un système SAP est déployé localement ou dans Azure. Vous pouvez toutefois déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local.
* Intersite : décrit un scénario dans lequel les machines virtuelles sont déployées sur un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, les instances locales d’Active Directory et le serveur DNS local à Azure. Le paysage local est étendu aux ressources Azure de l’abonnement. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services (SGBD, par exemple) sur ces machines virtuelles. La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure est possible. Ce scénario est le plus souvent utilisé pour le déploiement de ressources SAP sur Azure. Pour plus d’informations, consultez [Planification et conception de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Les déploiements intersites de systèmes SAP dans lesquels des machines virtuelles Azure exécutant des systèmes SAP font partie d’un domaine local sont pris en charge pour les systèmes SAP de production. Les configurations entre différents locaux sont prises en charge pour le déploiement d’éléments ou de l’intégralité des paysages SAP dans Azure. Ces machines virtuelles doivent faire partie du domaine et des services Active Directory/LDAP locaux même lorsque l’intégralité du paysage SAP est exécutée dans Azure. Dans les versions précédentes de la documentation, nous avons parlé des scénarios hybrides, où le terme *hybride* tient au fait qu’il existe une connectivité intersite entre les sites locaux et Azure. Dans ce cas, *hybride* signifie également que les machines virtuelles dans Azure font partie du répertoire Active Directory local.
>
>

Certaines documentations Microsoft décrivent les scénarios intersites de façon légèrement différente, en particulier pour les configurations haute disponibilité SGBD. Dans les documents portant sur SAP, le scénario de déploiement sur plusieurs sites se résume à l’établissement d’une connectivité site à site ou privée [ExpressRoute](https://azure.microsoft.com/services/expressroute/) et à la répartition du paysage SAP entre les sites locaux et Azure.

## <a name="resources"></a>Ressources
Voici les divers articles publiés sur la charge de travail SAP sur Azure. Il est recommandé de démarrer avec [Charge de travail SAP sur Azure : prise en main](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started), puis de choisir les sujets qui vous intéresse

Les notes SAP suivantes sont en lien avec SAP sur Azure quant au domaine traité dans ce document :

| Numéro de la note | Intitulé |
| --- | --- |
| [1928533] |Applications SAP sur Azure : produits et types de machines virtuelles Azure pris en charge |
| [2015553] |SAP sur Microsoft Azure : prérequis pour le support |
| [1999351] |Résolution des problèmes de surveillance Azure améliorée pour SAP |
| [2178632] |Métriques de surveillance clés pour SAP sur Microsoft Azure |
| [1409604] |Virtualisation sur Windows : supervision améliorée |
| [2191498] |SAP sur Linux avec Azure : supervision améliorée |
| [2039619] |Applications SAP sur Microsoft Azure à l’aide d’Oracle Database : produits et versions pris en charge |
| [2233094] |DB6 : exécution d’applications SAP sur Azure à l’aide d’IBM DB2 pour Linux, UNIX et Windows - Informations supplémentaires |
| [2243692] |Linux sur machine virtuelle Microsoft Azure (IaaS) : problèmes de licence SAP |
| [1984787] |SUSE LINUX Enterprise Server 12 Notes d'installation |
| [2002167] |Red Hat Enterprise Linux 7.x : installation et mise à niveau |
| [2069760] |Installation et mise à niveau SAP pour Oracle Linux 7.x |
| [1597355] |Recommandations relatives à l’espace d’échange pour Linux |
| [2171857] |Oracle Database 12C - Prise en charge du système de fichiers dans Linux |
| [1114181] |Oracle Database 11g - Prise en charge du système de fichiers dans Linux |


Consultez également le [Wiki SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) qui regroupe toutes les notes SAP pour Linux.

Vous devez avoir une connaissance pratique de l’architecture Microsoft Azure, ainsi que du déploiement et du fonctionnement des machines virtuelles Microsoft Azure Virtual Machines. Pour plus d’informations, consultez la [Documentation Azure](https://docs.microsoft.com/azure/).

Comme il est ici question de l’IaaS, l’installation et la configuration de Windows, de Linux et du SGBD sont globalement les mêmes que pour une machine virtuelle ou un ordinateur nu que vous installeriez en local. Cependant, les décisions relatives à l’implémentation de la gestion de l’architecture et des systèmes diffèrent sur certains points lorsque vous utilisez Azure IaaS. Ce document a pour objet d’expliquer les différences spécifiques de gestion de l’architecture et des systèmes auxquelles vous devez être préparé lors de l’utilisation d’Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Structure de stockage d’une machine virtuelle pour les déploiements SGBDR
Pour pouvoir suivre ce chapitre, il est nécessaire de comprendre le contenu de [ce chapitre][deployment-guide-3] du [Guide de déploiement][deployment-guide]. Vous devez savoir ce qui distingue les différentes séries de machines virtuelles et connaître les différences entre les offres de stockage Azure Standard et [Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) avant de lire ce chapitre.

En termes de stockage Azure pour les machines virtuelles Azure, vous devez prendre connaissance des articles suivants :

- [À propos du stockage des disques pour les machines virtuelles Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [À propos du stockage des disques pour les machines virtuelles Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds)

Dans une configuration de base, nous privilégions généralement une structure de déploiement dans laquelle le système d’exploitation, le SGBD et les fichiers binaires SAP éventuels sont séparés des fichiers de base de données. Il est recommandé que les systèmes SAP exécutés dans Machines Virtuelles Azure aient la même machine virtuelle (ou le même disque) de base installée avec le système d’exploitation, les exécutables du système de gestion de base de données (SGBD) et les exécutables SAP. Les fichiers de données et les journaux du SGBD sont stockés dans le stockage Azure (Standard ou Stockage Premium) dans des fichiers de disques durs virtuels distincts et attachés en tant que disques logiques à la machine virtuelle image du système d’exploitation Azure d’origine. En particulier pour les déploiements Linux, il peut y avoir différentes recommandations. Notamment en ce qui concerne SAP HANA.

Lors de la planification des disques, vous devez trouver le meilleur compromis entre les éléments suivants :

* Le nombre de fichiers de données.
* Le nombre de disques qui contiennent les fichiers
* Les quotas d’E/S par seconde pour un même disque
* Le débit de données par disque
* Le nombre de disques supplémentaires possibles par taille de machine virtuelle
* Le débit de stockage global qu’une machine virtuelle peut offrir.
* Le temps de latence entre les différents types de stockage Azure.
* Contrats SLA de machine virtuelle

Azure applique un quota d’E/S par seconde pour chaque disque de données. Ces quotas sont différents selon que les disques sont hébergés dans le stockage Azure Standard ou le Stockage Premium. La latence d’E/S est également différente entre les deux types de stockage. Avec Stockage Premium offrant une meilleure latence d’E/S. Pour chaque type de machines virtuelles, vous ne pouvez attacher qu’un nombre limité de disques. Une autre restriction concerne le fait que seuls certains types de machines virtuelles peuvent tirer parti d’Azure Premium Storage. Cela signifie que le choix d’un certain type de machines virtuelles peut non seulement être guidé par les besoins en puissance de processeur et en mémoire, mais également par les exigences relatives aux E/S par seconde, à la latence et au débit de disque, auxquelles permettent généralement de répondre l’augmentation du nombre de disques et le Stockage Premium. Avec le Stockage Premium en particulier, la taille d’un disque peut également être dictée par le nombre d’E/S et le débit qui doit être atteint par chaque disque.

> [!NOTE]
> Pour les déploiements SGBD, l’utilisation de Stockage Premium pour les données, du journal des transactions ou des fichiers de restauration est fortement recommandée. Peu importe que vous vouliez déployer des systèmes de production ou hors production.

> [!NOTE]
> Pour tirer parti du [contrat SLA de machine virtuelle unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) d’Azure, tous les disques attachés doivent être de type Stockage Premium Azure, notamment le disque dur virtuel de base.


> [!NOTE]
> Il n’est pas pris en charge pour héberger les fichiers principaux (fichiers de données et fichiers journaux) des bases de données SAP sur du matériel de stockage situé dans des centres de données colocalisés adjacents aux centres de données Azure. Pour la charge de travail SAP, seul le stockage représenté en tant que service Azure natif est pris en charge pour les fichiers journaux des données et des transactions des bases de données SAP.
> 

L’emplacement des fichiers de base de données et des fichiers journaux/de restauration, ainsi que le type de stockage Azure utilisé, doivent être dictés par les besoins en E/S par seconde, en latence et en débit. Afin de bénéficier d’un nombre suffisant d’E/S par seconde, il se peut que vous soyez obligé d’utiliser plusieurs disques ou d’utiliser un plus grand disque Stockage Premium. Si vous utilisez plusieurs disques, vous créeriez une frange de logiciels sur les disques qui contiennent les fichiers de données ou les fichiers journaux/de restauration. Dans ce cas, les contrats de niveau de service relatifs aux IOPS et au débit de disque des disques Stockage Premium sous-jacents ou le niveau maximal d’IOPS atteignable des disques Azure Standard Storage se cumulent pour la frange qui en résulte.

Comme indiqué précédemment, si vos exigences en termes d’IOPS dépassent ce qu’un seul disque dur virtuel peut fournir, vous devez équilibrer le nombre d’IOPS nécessaire pour les fichiers de base de données sur un certain nombre de disques durs virtuels. Le moyen le plus simple pour distribuer la charge IOPS sur les disques consiste à créer une frange logicielle sur les différents disques. Ensuite, placez un certain nombre de fichiers de données du SGBD SAP sur les LUN issus de la frange logicielle. le nombre de disques de la frange est piloté par les demandes IOPS, les demandes de débit de disque et les demandes de volume.


- - -
> ![ Windows][Logo_Windows]  Windows
>
> Nous recommandons l’utilisation de Windows Storage Spaces pour créer ces franges sur plusieurs disques durs virtuels Azure. Il est recommandé d’utiliser au moins Windows Server 2012 R2 ou Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Seuls MDADM et LVM (Logical Volume Manager) sont pris en charge pour créer un RAID logiciel sur Linux. Pour plus d’informations, consultez les articles suivants :
>
> - [Configuration d’un RAID logiciel sur Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) à l’aide de MDADM
> - [Configurer LVM sur une machine virtuelle Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) à l’aide de LVM
>
>

- - -

> [!NOTE]
> Dans la mesure où le Stockage Azure consiste à conserver trois images des disques durs virtuels, il est inutile de configurer une redondance lors de l’agrégation par franges. Vous devez uniquement configurer l’agrégation par franges, de sorte que les E/S sont bien distribués sur les différents disques durs virtuels.
>

### <a name="managed-or-non-managed-disks"></a>Disques managés ou non
Un compte de stockage Azure est non seulement le fait d’un administrateur, mais également l’objet de limitations. Les limitations sont différentes entre les comptes de stockage Azure Standard et Premium. Les fonctionnalités et limites sont répertoriées dans l’article [Objectifs de performance et d’extensibilité Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

Concernant le stockage Azure Standard, il est important de rappeler qu’il y a une limite d’E/S par seconde par compte de stockage (ligne contenant **Taux de demandes total** dans l’article [Objectifs de performance et d’extensibilité Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)). De plus, il y existe une limite initiale du nombre de comptes de stockage par abonnement Azure. Par conséquent, vous devez équilibrer les disques durs virtuels pour un environnement SAP plus important sur les différents comptes de stockage pour éviter d’atteindre les limites de ces comptes de stockage. Ce travail peut s’avérer fastidieux lorsqu’il s’agit de centaines de machines virtuelles à plusieurs milliers de disques durs virtuels.

Dans la mesure où il n’est pas recommandé d’utiliser le stockage Azure Standard pour les déploiements SGBD conjointement avec la charge de travail SAP, les références et les recommandations relatives au stockage Azure Standard se limitent à ce court [article](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Afin d’éviter le travail d’administration lié à la planification et au déploiement de disques durs virtuels sur différents comptes de stockage Azure, Microsoft a introduit ce que l’on appelle les [Disques managés](https://azure.microsoft.com/services/managed-disks/) en 2017. Ces derniers sont disponibles pour le stockage Azure Standard, ainsi que le stockage Premium Azure. Les principaux avantages des disques managés par rapport aux disques non managés sont les suivants :

- Pour les disques managés, Azure distribue automatiquement les différents disques durs virtuels sur différents comptes de stockage automatiquement au moment du déploiement, ce qui évite d’atteindre les limites d’un compte de stockage Azure en termes de volume de données, de débit d’E/S et d’IOPS.
- L’utilisation des disques managés permet à au stockage Azure d’honorer les concepts des groupe à haute disponibilité Azure et de déployer le disque dur virtuel de base et le disque associé d’une machine virtuelle sur différents domaines de mise à jour et d’erreur si la machine virtuelle fait partie d’un groupe à haute disponibilité Azure.


> [!IMPORTANT]
> Étant donné les avantages d’Azure Managed Disks, il est vivement recommandé d’utiliser Azure Managed Disks pour vos déploiements SGBD et SAP en général.
>

Pour convertir les disques non managés en disques managés, consultez les articles suivants :

- [Convertir les disques non gérés d’une machine virtuelle Windows en disques gérés](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Convertir les disques non gérés d’une machine virtuelle Linux en disques gérés](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Mise en cache pour les machines virtuelles et les disques de données
Lorsque vous montez des disques sur des machines virtuelles, vous pouvez choisir de mettre ou non en cache le trafic d’E/S entre la machine virtuelle et les disques situés dans le stockage Azure. Azure Standard et Premium Storage font appel à deux technologies différentes pour ce type de mise en cache.

Les recommandations de mise en cache indiquées plus bas supposent les caractéristiques d’E/S suivantes pour les SGBD standards :

- Il s’agit la plupart du temps d’une charge de travail de lecture concernant les fichiers de données d’une base de données. Ces lectures sont essentielles aux performances du système SGBD
- Les points de contrôle ou un flux constant donnent lieu à des écritures en rafales dans les fichiers de données. Néanmoins, si l’on établit une moyenne quotidienne, les écritures sont moins nombreuses que les lectures. Contrairement aux lectures à partir de fichiers de données, ces écritures sont asynchrones et ne bloquent pas les transactions utilisateur.
- Il n’y a pratiquement pas de lectures à partir de fichiers journaux ou de fichiers de restauration. Les E/S volumineuses constituent des exceptions lorsque vous effectuez des sauvegardes de fichiers journaux.
- Les écritures constituent la charge principale portant sur les fichiers journaux de transaction ou de restauration. Selon la nature de la charge de travail, la taille des E/S peut aller de 4 Ko à 1 Mo, voire plus.
- Toutes les écritures doivent être rendues persistantes sur le disque de manière fiable.

Pour le stockage Azure Standard, les types de mise en cache possibles sont les suivants :

* Aucun
* Lire
* Lecture/écriture

Pour obtenir des performances homogènes et déterministes, vous devez définir la mise en cache dans le stockage Azure Standard de tous les disques contenant **les fichiers de données SGBD, les fichiers journaux/de restauration et l’espace de table sur AUCUNE**. La valeur par défaut peut être conservée pour la mise en cache du disque dur virtuel de base.

Pour le Stockage Premium Azure, les options de mise en cache suivantes sont disponibles :

* Aucun
* Lire
* Lecture/écriture
* Aucun + Accélérateur des écritures (uniquement pour les machines virtuelles Azure de série M)
* Lecture + Accélérateur des écritures (uniquement pour les machines virtuelles Azure de série M)

Pour le stockage Premium Azure, il est recommandé d’utiliser la **mise en cache de lecture pour les fichiers de données** de la base de données SAP et de choisir l’option avec laquelle **aucune mise en cache n’est effectuée pour les disques des fichiers journaux**.

Pour les déploiements de machines virtuelles de série M, il est recommandé d’utiliser l’Accélérateur des écritures pour votre déploiement SGBD. Pour en savoir plus sur les restrictions et le déploiement de l’Accélérateur des écritures, consultez le document [Accélérateur des écritures](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-non-persistent-disks"></a>Disques Azure non persistants
Les machines virtuelles Azure proposent des disques non persistants suite au déploiement d’une machine virtuelle. Dans le cas du redémarrage d’une machine virtuelle, tout le contenu de ces lecteurs est effacé. Par conséquent, les fichiers de données et les fichiers journaux/de restauration ne doivent en aucun cas se trouver sur ces lecteurs. Il peut exister des exceptions pour certaines des bases de données, où ces lecteurs non persistants peuvent être appropriés pour les espaces de stockage tempdb et temp. Toutefois, évitez d’utiliser ces lecteurs pour les machines virtuelles de série A dans la mesure où ces lecteurs non persistants sont limités en débit avec cette famille de machines virtuelles. Pour plus d’informations, consultez l’article [Understanding the temporary drive on Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) (Comprendre le lecteur temporaire sur les machines virtuelles Microsoft Azure)

- - -
> ![ Windows][Logo_Windows]  Windows
>
> Dans une machine virtuelle Azure, le lecteur D:\ est un lecteur non persistant soutenu par des disques locaux présents sur le nœud de calcul Azure. Comme il est non persistant, cela signifie que toutes les modifications apportées au contenu sur le lecteur D:\ sont perdues lors du redémarrage de la machine virtuelle. Par « modifications », nous entendons les fichiers enregistrés, les répertoires créés, les applications installées, etc.
>
> ![Linux][Logo_Linux] Linux
>
> Les machines virtuelles Azure Linux montent automatiquement un lecteur à l’emplacement /mnt/resource. Il s’agit d’un lecteur non persistant soutenu par des disques locaux présents sur le nœud de calcul Azure. Comme il est non persistant, toutes les modifications apportées au contenu de l’emplacement /mnt/resource seront perdues si vous redémarrez la machine virtuelle. Par « modifications », nous entendons les fichiers enregistrés, les répertoires créés, les applications installées, etc.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Résilience du Stockage Microsoft Azure
Le Stockage Microsoft Azure stocke disque dur virtuel de base (avec le système d’exploitation) et les disques ou objets blob associés sur au moins trois nœuds de stockage distincts. Il s’agit du stockage localement redondant (LRS). Le LRS est la valeur par défaut pour tous les types de stockage dans Azure.

Il existe plusieurs autres méthodes de redondance, qui sont toutes décrites dans l’article [Réplication du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>À compter du Stockage Premium Azure, qui est le type de stockage recommandé pour les machines virtuelles SGBD et les disques qui stockent les fichiers de base de données et les fichiers journaux/de restauration, la seule méthode disponible est LRS. Par conséquent, vous devez configurer les méthodes de base de données, telles que SQL Server Always On, Oracle Data Guard ou HANA System Replication pour permettre la réplication des données de base de données dans une autre région Azure ou une autre zone de disponibilité Azure.


> [!NOTE]
> Pour les déploiements SGBD, l’utilisation du stockage géo-redondant proposé par le stockage standard Azure est déconseillée, car elle influe de manière négative sur les performances et ne tient pas compte de l’ordre d’écriture sur les différents disques durs virtuels attachés à une machine virtuelle. Le fait de ne pas respecter l’ordre d’écriture sur les différents disques durs virtuels risque fortement d’aboutir à une incohérence des bases de données sur la cible de réplication, si les fichiers de base de données et les fichiers journaux/de restauration sont répartis sur plusieurs disques durs virtuels (comme cela est le plus souvent le cas) sur la machine virtuelle source.



## <a name="vm-node-resiliency"></a>Résilience des nœuds de machine virtuelle
La plateforme Azure offre plusieurs contrats de niveau de service différents pour les machines virtuelles. Vous trouverez les détails exacts dans la version la plus récente du [Contrat de niveau de service pour les Machines virtuelles](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Dans la mesure où la couche SGBD est généralement un élément essentiel de la disponibilité d’un système SAP, vous devez vous familiariser avec les concepts de groupes à haute disponibilité, de zones de disponibilité et d’événements de maintenance. Les articles décrivant tous ces concepts sont [Gérer la disponibilité des machines virtuelles Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) et [Gérer la disponibilité des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)

Les recommandations minimales pour les scénarios de SGBD de production avec charge de travail SAP sont les suivantes :

- Déployer deux machines virtuelles dans un groupe à haute disponibilité distinct dans la même région Azure.
- Ces deux machines virtuelles seraient exécutées dans le même réseau virtuel Azure et auraient des cartes réseau attachées issues des mêmes sous-réseaux.
- Utilisez les méthodes de base de données pour conserver un serveur de secours avec la deuxième machine virtuelle. Il peut s’agir des méthodes SQL Server Always On, Oracle Data Guard ou HANA System Replication.

Vous pouvez également déployer une troisième machine virtuelle dans une autre région Azure et utiliser les mêmes méthodes de base de données pour fournir un réplica asynchrone dans une autre région Azure.

La façon de configurer des groupes à haute disponibilité Azure est expliquée dans ce [tutoriel](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considérations relatives au réseau Azure
Dans les déploiements SAP à grande échelle, il est recommandé d’utiliser le blueprint du [Centre de données virtuel Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) pour leur configuration de réseau virtuel et les autorisations et affectations de rôle aux différentes parties de leur organisation.

Voici plusieurs pratiques recommandées issues de centaines de déploiements clients :

- Les réseaux virtuels sur lesquels l’application SAP est déployée n’a pas accès à Internet.
- Les machines virtuelles de base de données s’exécutent dans le même réseau virtuel que celui de la couche Application.
- Les machines virtuelles du réseau virtuel ont une allocation statique de l’adresse IP privée. Référez-vous à l’article [Types d’adresses IP et méthodes d’allocation dans Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Les restrictions de routage vers et depuis les machines virtuelles SGBD ne sont **PAS** définies avec des pare-feux installés sur les machines virtuelles SGBD locales. Au lieu de cela, le routage du trafic est défini avec les [Groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)
- À des fins de séparation et d’isolement du trafic vers la machine virtuelle SGBD, vous affectez différentes cartes réseau à la machine virtuelle. Où chaque carte réseau a une adresse IP différente et chaque carte réseau est affectée à un autre sous-réseau de réseau virtuel, qui a une nouvelle fois différentes règles de groupe de sécurité réseau. N’oubliez pas que l’isolement ou la séparation du trafic réseau est simplement une mesure de routage et n’autorise pas la définition de quotas pour le débit du réseau.

> [!NOTE]
> Vous devez attribuer des adresses IP statiques aux cartes réseau virtuelles individuelles à l’aide des outils Azure. Vous ne devez pas attribuer d’adresses IP statiques au sein du système d’exploitation invité à une carte réseau virtuelle. Certains services Azure, comme le service de Sauvegarde Azure, s’appuient sur le fait que la carte réseau virtuelle principale est définie sur la DHCP et non sur des adresses IP statiques. Consultez également le document [Dépannage de la sauvegarde de machine virtuelle Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Si vous avez besoin d’attribuer plusieurs adresses IP statiques à une machine virtuelle, vous devez attribuer plusieurs cartes réseau virtuelles à une machine virtuelle.
>


> [!IMPORTANT]
> Par manque de fonctionnalités, mais surtout pour des raisons de performances, il n’est pas prévu de configurer [Azure Network Virtual Appliances](https://azure.microsoft.com/solutions/network-appliances/) dans le chemin d’accès de communication entre l’application SAP et la couche SGBD d’un système SAP NetWeaver, Hybris ou S/4HANA basé sur SAP. La communication entre la couche application SAP et la couche SGBD doit être directe. La restriction n’inclut pas les [règles Azure ASG et NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) tant que ces règles ASG et NSG permettent une communication directe. D’autres scénarios qui ne prennent pas en charge les appliances virtuelles réseau résident dans les chemins de communication entre les machines virtuelles Azure qui représentent les nœuds de cluster Linux Pacemaker et les appareils SBD comme décrit dans [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Ou bien, dans les chemins de communication entre les machines virtuelles Azure et les systèmes SOFS Windows Server configurés comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide du partage de fichiers dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Les appliances virtuelles réseau dans les chemins de communication peuvent facilement doubler la latence du réseau entre deux partenaires de communication et limiter le débit dans les chemins d’accès critiques entre la couche application SAP et la couche SGBD. Dans certains scénarios observés avec les clients, les machines virtuelles Azure peuvent provoquer des défaillances de clusters Pacemaker Linux dans les cas où les communications entre les nœuds du cluster Linux Pacemaker doivent communiquer avec leur appareil SBD via un machine virtuelle Azure.
>

> [!IMPORTANT]
> Une autre conception qui n’est **PAS** prise en charge est la séparation de la couche application SAP et de la couche SGBD sur des réseaux virtuels Azure distincts qui ne sont pas [appairés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) l’un avec l’autre. Nous vous recommandons de séparer la couche application SAP et la couche SGBD à l’aide de sous-réseaux au sein d’un réseau virtuel Azure, plutôt que d’utiliser différents réseaux virtuels Azure. Si vous décidez de ne pas suivre cette suggetsion et de séparer les deux couches sur des réseaux virtuels distincts, les deux réseaux virtuels doivent être [appairés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). N’oubliez pas que le trafic réseau entre deux réseaux virtuels Azure [appairés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) est sujet à des coûts de transfert. Le volume de données échangé entre la couche application SAP et la couche SGBD étant énorme (plusieurs téraoctets), des coûts substantiels peuvent s’accumuler si la couche application SAP et la couche SGBD sont isolées sur deux réseaux virtuels Azure appairés.

En utilisant deux machines virtuelles pour le déploiement SGBD de production au sein d’un groupe à haute disponibilité Azure et un routage distinct pour la couche Application SAP ainsi que le trafic des opérations et de gestion vers les deux machines virtuelles SGBD, le diagramme ressemblerait à ce qui suit :

![Diagramme de deux machines virtuelles dans deux sous-réseaux](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>Équilibreur de charge Azure pour la redirection du trafic
L’utilisation d’adresses IP privées virtuelles privées utilisés dans des fonctionnalités comme la réplication SQL Server Always On ou la réplication HANA System nécessite la configuration d’un équilibreur de charge Azure. L’équilibreur de charge Azure est en mesure par le biais des ports probe de déterminer le nœud SGBD actif et d’acheminer le trafic exclusivement vers ce nœud de base de données actif. En cas de basculement du nœud de base de données, il est inutile que l’application SAP procède à la reconfiguration. Au lieu de cela, les architectures d’applications SAP les plus courantes se reconnecteront par rapport à l’adresse IP virtuelle privée. En attendant l’équilibreur de charge Azure a réagi sur le basculement du nœud en redirigeant le trafic relatif à l’adresse IP virtuelle privée vers le second nœud.

Azure propose deux [références SKU d’équilibreur de charge](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) différentes. Une référence SKU de base et standard. À moins de vouloir effectuer un déploiement sur des zones de disponibilité Azure, la référence SKU d’équilibreur de charge de base convient parfaitement.

Le trafic entre les machines virtuelles SGBD et la couche Application SAP est-il toujours acheminé via l’équilibreur de charge Azure ? La réponse dépend de la façon dont vous configurez l’équilibreur de charge. À ce stade, le trafic entrant vers la machine virtuelle SGBD est toujours acheminé via l’équilibreur de charge Azure. L’itinéraire du trafic sortant à partir de la machine virtuelle SGBD vers la machine virtuelle de la couche Application dépend de la configuration de l’équilibreur de charge Azure. L’équilibreur de charge propose une option de DirectServerReturn. Si cette option est configurée, le trafic dirigé à partir de la machine virtuelle SGBD vers la couche application SAP n’est **PAS** acheminé via l’équilibreur de charge Azure. Au lieu de cela il se dirigera directement vers la couche Application. Si DirectServerReturn n’est pas configuré, le trafic de retour vers la couche Application SAP est acheminé via l’équilibreur de charge Azure

Il est recommandé de configurer DirectServerReturn en combinaison avec les équilibreurs de charge Azure positionnés entre la couche Application SAP et la couche SGBD pour réduire la latence réseau entre les deux couches

Un exemple de configuration, tel qu’une configuration, est publié dans le serveur SQL Always On dans [cet article](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Si vous choisissez d’utiliser des modèles JSON GitHub publiés en référence pour les déploiements de votre infrastructure SAP dans Azure, vous devez étudier ce [modèle pour un système SAP à 3 niveaux](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Dans ce modèle, vous pouvez également étudier les paramètres appropriés de l’équilibreur de charge Azure.

### <a name="azure-accelerated-networking"></a>Mise en réseau accélérée Azure
Afin de réduire davantage la latence du réseau entre les machines virtuelles Azure, il est vivement recommandé de choisir l’option de [Mise en réseau accélérée](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) lors du déploiement de machines virtuelles Azure pour la charge de travail SAP. En particulier, pour la couche Application et SGBD SAP.

> [!NOTE]
> Tous les types de machines virtuelles ne prennent pas en charge la mise en réseau accélérée. L’article référencé répertorie les types de machines virtuelles qui prennent en charge la mise en réseau accélérée.
>

- - -
> ![ Windows][Logo_Windows]  Windows
>
> Pour Windows, consultez l’article [Créer une machine virtuelle Windows avec la mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) pour comprendre les concepts et la façon de déployer des machines virtuelles avec mise en réseau accélérée
>
> ![Linux][Logo_Linux] Linux
>
> Pour Linux, lisez l’article [Créer une machine virtuelle Linux avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) afin d’obtenir des informations pour la distribution Linux.
>
>

- - -

> [!NOTE]
> SUSE, Red Hat et Oracle Linux prennent en charge la mise en réseau accélérée avec les versions récentes. Les versions plus anciennes comme SLES 12 SP2 ou RHEL 7.2 ne prennent pas en charge la mise en réseau accélérée Azure
>


## <a name="deployment-of-host-monitoring"></a>Déploiement de la surveillance d’hôte
Pour une utilisation en production des applications SAP dans Azure Virtual Machines, SAP doit avoir la possibilité de collecter des données de surveillance d’hôte auprès des hôtes physiques exécutant les machines virtuelles Azure. Un niveau de correctif logiciel SAP HostAgent spécifique est nécessaire pour activer cette fonctionnalité dans SAPOSCOL et SAP HostAgent. Le niveau de correctif logiciel exact est indiqué dans la Note de SAP [1409604].

Pour plus d’informations concernant le déploiement de composants qui fournissent des données d’hôte à SAPOSCOL et SAPHostAgent, et la gestion du cycle de vie de ces composants, consultez le [Guide de déploiement][deployment-guide].


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur un SGBD particulier, consultez les articles suivants :

- [Déploiement SGBD de machines virtuelles SQL Server Azure pour charge de travail SAP](dbms_guide_sqlserver.md)
- [Déploiement SGBD de machines virtuelles Oracle Azure pour charge de travail SAP](dbms_guide_oracle.md)
- [Déploiement SGBD de machines virtuelles IBM DB2 Azure pour charge de travail SAP](dbms_guide_ibm.md)
- [Déploiement SGBD de machines virtuelles SAP ASE Azure pour charge de travail SAP](dbms_guide_sapase.md)
- [Déploiement Content Server, LiveCache et SAP maxDB sur Azure](dbms_guide_maxdb.md)
- [Guide des opérations SAP HANA sur Azure](hana-vm-operations.md)
- [Haute disponibilité de SAP HANA pour les machines virtuelles Azure](sap-hana-availability-overview.md)
- [Guide de sauvegarde pour SAP HANA sur des machines virtuelles Azure](sap-hana-backup-guide.md)

