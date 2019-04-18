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
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699251"
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

Ce guide fait partie de la documentation sur la façon d’implémenter et déployer des logiciels SAP sur Microsoft Azure. Avant de lire ce guide, lisez le [guide de planification et implémentation][planning-guide]. Ce document décrit les aspects de déploiement générique des systèmes SGBD portant sur SAP sur Microsoft Azure virtual machines virtuelles à l’aide de l’infrastructure Azure comme une fonctionnalités du service (IaaS).

Ce document vient compléter la documentation d’installation de SAP et les Notes SAP, qui représentent les ressources pour les déploiements de logiciels SAP et les installations sur plateformes données.

Ce document aborde l’exécution des systèmes SGBD de type SAP sur les machines virtuelles Azure. Il comporte peu de références à des systèmes SGBD spécifiques. Au lieu de cela, les systèmes SGBD spécifiques sont décrits dans ce document, après ce document.

## <a name="definitions"></a>Définitions
Dans le document, ces termes sont utilisés :

* **IaaS**: Infrastructure en tant que service.
* **PaaS**: Plateforme en tant que service.
* **SaaS**: Logiciel en tant que service.
* **Composant SAP**: Une application SAP individuelle tels que ERP Central Component (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Composants SAP peuvent reposer sur des technologies ABAP ou Java traditionnelles ou sur une application non basée sur NetWeaver telle que Business Objects.
* **Environnement SAP**: Un ou plusieurs composants SAP regroupement logiquement pour exécuter une fonction d’entreprise telles que le développement, assurance qualité, formation, récupération d’urgence ou de production.
* **Paysage SAP**: ce terme fait référence à l’ensemble des ressources SAP dans le paysage informatique d’un client. Le paysage SAP comprend tous les environnements de production et de non-production.
* **Système SAP**: La combinaison d’une couche SGBD et une couche d’application de, par exemple, un système de développement SAP ERP, SAP Business Warehouse tester le système ou un système de production SAP CRM. Dans les déploiements Azure, séparation de ces deux couches entre en local et Azure n’est pas pris en charge. Par conséquent, un système SAP est déployé sur site ou ses déployé dans Azure. Vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pourrez déployer le développement SAP CRM et systèmes de test dans Azure, mais déployer le SAP CRM production système en local.
* **Entre différents locaux**: Décrit un scénario où les machines virtuelles sont déployées à un abonnement Azure a site à site, multisite ou centres de connectivité Azure ExpressRoute entre les données sur site et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. 

    La connexion a pour but d’étendre les domaines locaux, les instances locales d’Active Directory et le serveur DNS local à Azure. Le paysage local est étendu aux ressources Azure de l’abonnement. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. Les utilisateurs de domaine du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles, tels que les services de SGBD. La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure est possible. Ce scénario est le scénario le plus courant en cours d’utilisation pour déployer des ressources SAP sur Azure. Pour plus d’informations, consultez [Planification et conception de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Déploiements intersites de systèmes SAP dans lesquels les machines virtuelles qui exécutent des systèmes SAP font partie d’un domaine local et sont prises en charge pour les systèmes SAP de production. Les configurations intersites sont prises en charge pour le déploiement d’éléments ou de l’intégralité des paysages SAP dans Azure. Même lorsque l’intégralité du paysage SAP dans Azure nécessite ces machines virtuelles doit appartenir à un domaine local et Active Directory/LDAP. 
>
> Dans les versions précédentes de la documentation, des scénarios hybrides citées. Le terme *hybride* tient au fait qu’il existe une connectivité intersite entre en local et Azure. Dans ce cas, hybride signifie également que les machines virtuelles dans Azure font partie d’Active Directory en local.
>
>

Certaines documentations Microsoft décrivent les scénarios intersites un peu différemment, en particulier pour les configurations à haute disponibilité SGBD. Dans les documents portant sur SAP, le scénario intersite se résume à site à site ou privée [ExpressRoute](https://azure.microsoft.com/services/expressroute/) connectivité et un paysage SAP qui est distribué entre en local et Azure.

## <a name="resources"></a>Ressources
D’autres articles disponibles sur la charge de travail SAP sur Azure. Démarrer avec [charge de travail SAP sur Azure : Prise en main](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) , puis choisissez votre zone d’intérêt.

Les Notes SAP suivantes sont liées à SAP sur Azure en ce qui concerne la zone couverte dans ce document.

| Numéro de la note | Intitulé |
| --- | --- |
| [1928533] |Applications SAP sur Azure : Produits et types de machines virtuelles pris en charge |
| [2015553] |SAP sur Microsoft Azure : Prérequis pour la prise en charge |
| [1999351] |Résolution des problèmes de la surveillance Azure améliorée pour SAP |
| [2178632] |Métriques de surveillance clés pour SAP sur Microsoft Azure |
| [1409604] |Virtualisation sur Windows : Surveillance améliorée |
| [2191498] |SAP sur Linux avec Azure : Surveillance améliorée |
| [2039619] |Applications SAP sur Microsoft Azure avec la base de données Oracle : Produits et versions pris en charge |
| [2233094] |DB6 : Applications SAP sur Azure à l’aide d’IBM DB2 pour Linux, UNIX et Windows : Informations supplémentaires |
| [2243692] |Linux sur machine virtuelle Microsoft Azure (IaaS) : problèmes de licence SAP |
| [1984787] |SUSE LINUX Enterprise Server 12 Notes d'installation |
| [2002167] |Red Hat Enterprise Linux 7.x : Installation et mise à niveau |
| [2069760] |Installation et mise à niveau de SAP pour Oracle Linux 7.x |
| [1597355] |Recommandations relatives à l’espace d’échange pour Linux |
| [2171857] |Oracle Database 12C : Prise en charge du système de fichiers sur Linux |
| [1114181] |Oracle Database 11g : Prise en charge du système de fichiers sur Linux |


Pour plus d’informations sur toutes les Notes SAP pour Linux, consultez le [SAP Communauté wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Vous avez besoin d’une connaissance de l’architecture de Microsoft Azure et comment les machines virtuelles Microsoft Azure sont déployées et exploitées. Pour plus d’informations, consultez [documentation Azure](https://docs.microsoft.com/azure/).

En général, l’installation de Windows, Linux et SGBD et la configuration sont essentiellement identique à une machine virtuelle ou un ordinateur nu que vous installez en local. Il existe certaines décisions de mise en œuvre management architecture et des systèmes qui sont différentes lorsque vous utilisez Azure IaaS. Ce document explique les spécifiques architecturales et les différences de gestion de système pour préparer le lorsque vous utilisez Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Structure de stockage d’une machine virtuelle pour les déploiements de SGBDR
Pour suivre ce chapitre, lire et comprendre les informations présentées dans [ce chapitre] [ deployment-guide-3] de la [Guide de déploiement][deployment-guide]. Vous devez comprendre et connaître les différentes séries de machines virtuelles et les différences entre le stockage standard et premium avant de lire ce chapitre. 

Pour en savoir plus sur le stockage Azure pour les machines virtuelles Azure, consultez :

- [Présentation des disques gérés pour les machines virtuelles Windows Azure](../../windows/managed-disks-overview.md).
- [Présentation des disques gérés pour machines virtuelles Azure Linux](../../linux/managed-disks-overview.md).

Dans une configuration de base, nous recommandons généralement une structure de déploiement dans lequel le système d’exploitation, SGBD et les fichiers binaires SAP éventuels sont séparés des fichiers de base de données. Nous recommandons que les systèmes SAP qui s’exécutent dans des machines virtuelles le disque dur virtuel de base, ou le disque, installé avec le système d’exploitation, les exécutables du système de gestion de base de données et les exécutables SAP. 

Les fichiers de données et journaux du SGBD sont stockés dans le stockage standard ou premium. Elles sont stockées dans des disques distincts et attachés en tant que disques logiques à la machine virtuelle image du système d’exploitation Azure d’origine. Pour les déploiements de Linux, différentes recommandations sont documentées, en particulier pour SAP HANA.

Lorsque vous planifiez votre disposition du disque, de trouver le meilleur compromis entre ces éléments :

* Le nombre de fichiers de données.
* Le nombre de disques qui contiennent les fichiers
* Les quotas d’E/S par seconde pour un même disque
* Le débit de données par disque
* Le nombre de disques supplémentaires possibles par taille de machine virtuelle
* Le débit de stockage global qu’une machine virtuelle peut offrir.
* Le temps de latence entre les différents types de stockage Azure.
* Contrats SLA de machine virtuelle.

Azure applique un quota d’E/S par seconde pour chaque disque de données. Ces quotas sont différents pour les disques hébergés sur le stockage standard et premium storage. La latence d’E/S est également différente entre les deux types de stockage. Premium storage offre une meilleure latence d’e/s. 

Chacun des différents types de machine virtuelle a un nombre limité de disques de données que vous pouvez attacher. Une autre restriction est que seuls certains types de machine virtuelle peuvent utiliser le stockage premium. En règle générale, vous décidez d’utiliser un certain type de machine virtuelle selon les besoins en mémoire et du processeur. Vous pouvez également envisager l’e/s, la latence et les exigences de débit de disque qui elles-mêmes dépendent du nombre de disques ou le type de disques de stockage premium. Le nombre d’e/s et le débit être atteint par chaque disque peut déterminer la taille du disque, en particulier avec le stockage premium.

> [!NOTE]
> Pour les déploiements de SGBD, nous vous recommandons d’utiliser le stockage premium pour toutes les données, journal des transactions, ou les fichiers de restauration par progression. Peu importe si vous souhaitez déployer des systèmes de production ou de production.

> [!NOTE]
> Pour bénéficier d’Azure unique de le [unique contrat SLA de machine virtuelle](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), tous les disques associés doivent être le type de stockage premium, qui inclut le disque dur virtuel de base.

> [!NOTE]
> Hébergement des fichiers de base de données principale, tels que les fichiers journaux et de données, des bases de données SAP sur du matériel de stockage qui se trouve dans les centres de données de tiers colocalisé adjacents aux centres de données Azure n’est pas pris en charge. Pour les charges de travail SAP, seul stockage qui est représenté en tant que service Azure natif est pris en charge pour les fichiers journaux de données et des transactions de bases de données SAP.

L’emplacement des fichiers de la base de données et les fichiers journaux et de restauration par progression et le type de stockage Azure que vous utilisez est définie par les exigences d’e/s, la latence et débit. Pour avoir suffisamment d’IOPS, vous pouvez être forcée à utiliser plusieurs disques ou utiliser un plus grand disque de stockage premium. Si vous utilisez plusieurs disques, créer un agrégat par bandes de logiciels sur les disques qui contiennent les fichiers de données ou le journal et de rétablissement des fichiers. Dans ce cas, les IOPS et le débit de disque SLA du stockage premium sous-jacent disques ou les e/s maximal réalisable de disques de stockage standard sont cumulatifs pour l’agrégat qui en résulte.

Comme indiqué précédemment, si vos besoins d’e/s dépasse ce qu’un seul disque dur virtuel peut fournir, équilibrer le nombre d’e/s qui sont nécessaires pour les fichiers de base de données sur un nombre de disques durs virtuels. Pour répartir la charge d’e/s sur disques, le plus simple consiste à créer un agrégat par bandes logiciel sur les différents disques. Puis, placez un nombre de fichiers de données du SGBD SAP sur les LUN issus de la bande de logiciels. Le nombre de disques de la répartition est piloté par les demandes e/s, les exigences en termes de débit de disque et volume demandes.


- - -
> ![Windows][Logo_Windows] Windows
>
> Nous vous recommandons d’utiliser les espaces de stockage Windows pour créer des agrégats par bandes sur plusieurs disques durs virtuels Azure. Utilisez au moins Windows Server 2012 R2 ou Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Seuls MDADM et Gestionnaire de Volume logique (LVM) sont pris en charge pour créer un RAID logiciel sur Linux. Pour plus d'informations, consultez les pages suivantes :
>
> - [Configuration logicielle de RAID sur Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) à l’aide de MDADM
> - [Configurer LVM sur une machine virtuelle Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) à l’aide de LVM
>
>

- - -

> [!NOTE]
> Étant donné que Azure Storage conserve trois images des disques durs virtuels, il est inutile pour configurer une redondance lorsque vous équilibrez. Vous devez uniquement configurer l’agrégation par bandes afin que les e/s sont distribuées sur les disques durs virtuels différents.
>

### <a name="managed-or-nonmanaged-disks"></a>Disques gérés ou non gérés
Un compte de stockage Azure est une construction d’administration et un objet de limitations. Limitations diffèrent entre les comptes de stockage standard et premium. Pour plus d’informations sur les fonctionnalités et limitations, consultez [les objectifs de performance et d’extensibilité Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Pour le stockage standard, n’oubliez pas qu’il existe une limite pour les IOPS par compte de stockage. Reportez-vous à la ligne qui contient **taux de demandes Total** dans l’article [les objectifs de performance et d’extensibilité Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Il existe également une limite initiale du nombre de comptes de stockage par abonnement Azure. Solde de disques durs virtuels pour le plus grand paysage SAP entre les différents comptes de stockage pour éviter d’atteindre les limites de ces comptes de stockage. Il s’agit de travail fastidieux lorsque vous parlez de quelques machines virtuelles centaines avec des disques durs virtuels de plus de mille.

Stockage standard pour les déploiements SGBD conjointement avec une charge de travail SAP n’est pas recommandé d’utiliser des références et des recommandations pour le stockage standard sont limitées à ce court [article](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Pour éviter le travail d’administration de la planification et déploiement de disques durs virtuels sur les comptes de stockage Azure différents, Microsoft a introduit [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) en 2017. Disques gérés sont disponibles pour le stockage standard et premium storage. Les principaux avantages des disques gérés par rapport aux disques non gérés sont :

- Pour les disques gérés, Azure distribue les différents disques durs virtuels entre différents comptes de stockage automatiquement au moment du déploiement. De cette façon, des limites de compte de stockage pour le volume de données, le débit d’e/s, et e/s ne sont pas atteint.
- À l’aide de disques gérés, le stockage Azure respecte les concepts de haute disponibilité Azure. Si la machine virtuelle fait partie d’un groupe à haute disponibilité Azure, le disque dur virtuel de base et un disque attaché d’une machine virtuelle sont déployées dans différents domaines d’erreur et de mise à jour.


> [!IMPORTANT]
> Étant donné les avantages d’Azure Managed Disks, nous vous recommandons d’utiliser des disques gérés Azure pour vos déploiements SGBD et les déploiements SAP en général.
>

Pour convertir du code non managé vers des disques gérés, consultez :

- [Convertir une machine virtuelle de Windows à partir de disques non gérés vers des disques gérés](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Convertir une machine virtuelle Linux à partir de disques non gérés vers des disques gérés](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Mise en cache pour les machines virtuelles et les disques de données
Lorsque vous montez des disques aux machines virtuelles, vous pouvez choisir si le trafic d’e/s entre la machine virtuelle et les disques situés dans le stockage Azure est mis en cache. Stockage standard et premium utiliser deux technologies différentes pour ce type de cache.

Les recommandations suivantes supposent ces caractéristiques d’e/s pour les SGBD standards :

- Il est principalement une charge de travail en lecture sur les fichiers de données d’une base de données. Ces lectures sont critique pour le système SGBD pour les performances.
- Écriture sur les fichiers de données se produit en rafales selon les points de contrôle ou un flux constant. Moyenne calculée sur une journée, il existe des écritures de moins que les lectures. Contrairement aux opérations de lecture à partir de fichiers de données, ces écritures sont asynchrones et ne retardez pas toutes les transactions utilisateur.
- Il n’y a pratiquement pas de lectures à partir de fichiers journaux ou de fichiers de restauration. Les exceptions sont des e/s volumineuses lorsque vous effectuez des sauvegardes de fichier journal.
- La charge principale par rapport à des fichiers de journaux de transaction ou de rétablissement est écrit. Selon la nature de la charge de travail, vous pouvez avoir e/s aussi petit que 4 Ko ou, dans les autres cas, les tailles d’e/s de 1 Mo ou plus.
- Toutes les écritures doivent être conservées sur le disque de manière fiable.

Pour le stockage standard, les types de cache possibles sont :

* Aucun
* Lire
* Lecture/écriture

Pour obtenir des performances cohérentes et déterministes, définissez la mise en cache sur le stockage standard pour tous les disques qui contiennent des fichiers de données SGBD, connectez-vous et rétablir des fichiers et l’espace de table à **NONE**. La valeur par défaut peut être conservée pour la mise en cache du disque dur virtuel de base.

Pour le stockage premium, les options de mise en cache suivantes existent :

* Aucun
* Lire
* Lecture/écriture
* Aucun + accélérateur d’écriture, qui est uniquement pour les machines virtuelles Azure série M
* Lecture + accélérateur d’écriture, qui est uniquement pour les machines virtuelles Azure série M

Pour le stockage premium, nous vous recommandons d’utiliser **lire la mise en cache pour les fichiers de données** SAP de base de données et choisissez **aucune mise en cache pour les disques des fichiers journaux**.

Pour les déploiements de la série M, nous vous recommandons d’utiliser accélérateur des écritures Azure pour votre déploiement de SGBD. Pour plus d’informations, restrictions et déploiement de l’accélérateur des écritures Azure, consultez [accélérateur des écritures activer](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Disques Azure non persistants
Les machines virtuelles Azure offrent des disques non persistants après avoir déployé une machine virtuelle. Dans le cas d’un redémarrage de la machine virtuelle, tout le contenu sur ces lecteurs seront effacé. Il s’agit une étant donné que les fichiers de données et des fichiers journaux et de restauration par progression des bases de données doivent en aucun cas être situés sur ces lecteurs non persistantes. Il peut exister des exceptions pour certaines bases de données, où ces lecteurs non persistantes peut être appropriées pour tempdb et les espaces de stockage temporaire. Évitez d’utiliser ces lecteurs pour les machines virtuelles de série, car ces lecteurs non persistantes sont limités en débit avec cette famille de machine virtuelle. 

Pour plus d’informations, consultez [comprendre le lecteur temporaire sur des machines virtuelles Windows dans Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

- - -
> ![Windows][Logo_Windows] Windows
>
> Le lecteur D dans une machine virtuelle Azure est un disque non persistante, ce qui est soutenu par des disques locaux sur le nœud de calcul Azure. Car il s’agit non persistantes, les modifications apportées au contenu sur le lecteur D sont perdues lors du redémarrage de la machine virtuelle. Modifications incluent les fichiers qui ont été stockés, des répertoires qui ont été créés et des applications qui ont été installées.
>
> ![Linux][Logo_Linux] Linux
>
> Les machines virtuelles Azure Linux montent automatiquement un lecteur à/mnt/Resource qui est un lecteur non persistante soutenu par des disques locaux sur le nœud de calcul Azure. Car il s’agit non persistantes, les modifications apportées au contenu de/mnt/resource sont perdues lors du redémarrage de la machine virtuelle. Modifications incluent les fichiers qui ont été stockés, des répertoires qui ont été créés et des applications qui ont été installées.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Résilience du Stockage Microsoft Azure
Microsoft Azure Storage stocke le VHD de base, avec le système d’exploitation et de disques attachés ou d’objets BLOB sur au moins trois nœuds de stockage distincts. Ce type de stockage est appelé stockage localement redondant (LRS). LRS est la valeur par défaut pour tous les types de stockage dans Azure.

Il existe d’autres méthodes de redondance. Pour plus d’informations, consultez l’article [Réplication de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Stockage Premium est recommandé du type de stockage pour les machines virtuelles de SGBD et les disques qui stockent des journaux et base de données et fichiers de restauration par progression. La méthode de redondance uniquement disponible pour le stockage premium est LRS. Par conséquent, vous devez configurer des méthodes de base de données pour activer la réplication de données de base de données dans une autre zone de disponibilité ou de la région Azure. Méthodes de base de données incluent SQL Server Always On, Oracle Data Guard et réplication de système HANA.


> [!NOTE]
> Pour les déploiements de SGBD, l’utilisation du stockage géo-redondant (GRS) n’est pas recommandée pour le stockage standard. GRS gravement affecte les performances et ne traite pas l’ordre d’écriture sur différents disques durs virtuels qui sont attachés à une machine virtuelle. Ne respecte ne pas l’ordre d’écriture sur différents disques durs virtuels potentiellement conduit aux bases de données incohérents du côté de la cible de réplication. Cette situation se produit si les fichiers de base de données et de journal et de restauration par progression sont réparties sur plusieurs disques durs virtuels, comme c’est généralement le cas, sur le côté de la machine virtuelle source.



## <a name="vm-node-resiliency"></a>Résilience des nœuds de machine virtuelle
Azure offre plusieurs différents contrats SLA pour les machines virtuelles. Pour plus d’informations, consultez la version la plus récente de [contrat SLA pour les Machines virtuelles](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Étant donné que la couche SGBD est généralement essentielle à la disposition dans un système SAP, vous devez comprendre les groupes à haute disponibilité, les zones de disponibilité et les événements de maintenance. Pour plus d’informations sur ces concepts, consultez [gérer la disponibilité des machines virtuelles Windows Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) et [gérer la disponibilité des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

La quantité minimale recommandée pour les scénarios de SGBD de production avec une charge de travail SAP consiste à :

- Déployer deux machines virtuelles dans un groupe de disponibilité distinct définie dans la même région Azure.
- Exécutez ces deux machines virtuelles dans le même réseau virtuel Azure et ont des cartes réseau attachées hors les mêmes sous-réseaux.
- Utilisez les méthodes de base de données pour conserver un serveur de secours avec la deuxième machine virtuelle. Il peut s’agir des méthodes SQL Server Always On, Oracle Data Guard ou HANA System Replication.

Vous pouvez également déployer une troisième machine virtuelle dans une autre région Azure et utiliser les mêmes méthodes de base de données pour fournir un réplica asynchrone dans une autre région Azure.

Pour plus d’informations sur la configuration à haute disponibilité Azure, consultez [ce didacticiel](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considérations relatives au réseau Azure
Dans les déploiements SAP à grande échelle, utilisez le plan de [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Utilisez-le pour vos attributions de configuration et les autorisations et les rôles de réseau virtuel aux différentes parties de votre organisation.

Ces meilleures pratiques sont le résultat des centaines de déploiements de client :

- Les réseaux virtuels, en que l’application SAP est déployée n’ont pas accès à internet.
- La base de données des machines virtuelles s’exécuter dans le même réseau virtuel que la couche d’application.
- Les machines virtuelles au sein du réseau virtuel ont une allocation statique de l’adresse IP privée. Pour plus d’informations, consultez [types et méthodes d’allocation dans Azure d’adresses IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Restrictions de routage vers et depuis les machines virtuelles SGBD sont *pas* défini avec un pare-feu installés sur les machines virtuelles SGBD locales. Au lieu de cela, le routage du trafic est défini avec [réseau des groupes de sécurité (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Pour séparer et isoler le trafic de VM DBMS, affecter différentes cartes réseau à la machine virtuelle. Chaque carte réseau Obtient une adresse IP différente, et chaque carte réseau est assignée à un sous-réseau de réseau virtuel différent. Chaque sous-réseau possède différentes règles de groupe de sécurité réseau. L’isolement ou la séparation du trafic réseau est une mesure pour le routage. Il n’est pas utilisé pour définir des quotas pour le débit du réseau.

> [!NOTE]
> Affecter des adresses IP statiques via Azure signifie que les affecter aux cartes réseau virtuelles individuelles. N’affectez pas des adresses IP statiques dans le système d’exploitation invité pour une carte réseau virtuelle. Certains services Azure comme sauvegarde Azure s’appuient sur le fait qu’au moins la principale carte réseau virtuelle est définie sur la valeur DHCP et non à des adresses IP statiques. Pour plus d’informations, consultez [sauvegarde de machines virtuelles Azure de résoudre les problèmes](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pour affecter plusieurs adresses IP statiques à une machine virtuelle, attribuer plusieurs cartes réseau virtuelles à une machine virtuelle.
>


> [!IMPORTANT]
> Configuration [appliances virtuelles réseau](https://azure.microsoft.com/solutions/network-appliances/) dans le chemin de communication entre l’application SAP et la couche SGBD d’un SAP NetWeaver-, Hybris ou système basé sur S/4HANA SAP n’est pas pris en charge. Cette restriction est pour des raisons de performances et de fonctionnalités. Le chemin d’accès de communication entre la couche application SAP et la couche SGBD doit être direct. N’inclut pas la restriction [groupe de sécurité d’application (ASG) et les règles NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) si ces règles ASG et groupe de sécurité réseau autorisent un chemin de communication directe. 
>
> Autres scénarios où les appliances virtuelles réseau ne sont pas pris en charge se trouvent dans :
>
> * Voies de communication entre les machines virtuelles Azure qui représentent les Linux Pacemaker cluster nœuds et les appareils SBD, comme décrit dans [haute disponibilité pour SAP NetWeaver sur machines virtuelles Azure sur SUSE Linux Enterprise Server for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Voies de communication entre les machines virtuelles Azure et le serveur de fichiers de montée en puissance (SOFS) de Windows Server a été défini comme décrit dans [Cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un partage de fichiers dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Appliances virtuelles réseau dans les chemins de communication peuvent doubler la latence du réseau entre les partenaires de communication de deux. Ils peuvent également restreindre le débit dans les chemins critiques entre la couche application SAP et la couche SGBD. Dans certains scénarios de client, les appliances virtuelles réseau peuvent provoquer des clusters Pacemaker Linux échec. Il s’agit de cas où les communications entre les nœuds de cluster Linux Pacemaker communiquent sur leur appareil SBD via une appliance virtuelle réseau.
>

> [!IMPORTANT]
> Une autre conception ayant de *pas* pris en charge est la répartition de la couche application SAP et la couche SGBD dans différents réseaux virtuels Azure qui ne sont pas [homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre eux. Nous vous recommandons de séparer la couche application SAP et la couche SGBD en utilisant des sous-réseaux au sein d’un réseau virtuel Azure au lieu d’à l’aide de différents réseaux virtuels Azure. 
>
> Si vous décidez de ne pas suivre la recommandation et séparer à la place les deux couches dans différents réseaux virtuels, les deux réseaux virtuels doivent être [homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> N’oubliez pas que le trafic réseau entre deux [homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) réseaux virtuels Azure est soumis à des coûts de transfert. Volume de données volumineuses qui se compose de plusieurs téraoctets est échangé entre la couche application SAP et la couche SGBD. Vous pouvez accumuler les coûts substantielles si la couche application SAP et la couche SGBD sont séparés entre deux réseaux virtuels homologués Azure.

Utilisez deux machines virtuelles pour votre déploiement de SGBD au sein d’un groupe de disponibilité Azure de production est définie. Utilisez également de routage pour la couche application SAP et le trafic de gestion et opérations pour les deux machines virtuelles de SGBD. Consultez le graphique suivant :

![Diagramme de deux machines virtuelles dans deux sous-réseaux](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Utiliser Azure Load Balancer pour rediriger le trafic
L’utilisation d’adresses IP privées virtuels utilisé dans les fonctionnalités telles que SQL Server AlwaysOn ou réplication de système HANA nécessite la configuration d’un équilibreur de charge Azure. L’équilibreur de charge utilise des ports de sondage pour déterminer le nœud actif de SGBD et acheminer le trafic exclusivement vers ce nœud de base de données active. 

S’il existe un basculement sur le nœud de base de données, il n’est pas nécessaire pour l’application SAP reconfigurer. Au lieu de cela, les architectures d’application SAP plus courantes se reconnecter par rapport à l’adresse IP virtuelle privée. Pendant ce temps, l’équilibrage de charge réagit au basculement de nœuds en redirigeant le trafic par rapport à l’adresse IP virtuelle privée sur le second nœud.

Azure propose deux différents [références SKU d’équilibreur de charge](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): une référence SKU de base et une référence SKU standard. Sauf si vous souhaitez déployer dans les zones de disponibilité Azure, l’équilibreur de charge de base référence (SKU) s’effectue correctement.

Est le trafic entre les machines virtuelles de SGBD et de la couche d’application SAP toujours acheminée via l’équilibreur de charge tout le temps ? La réponse dépend de la façon dont vous configurez l’équilibreur de charge. 

À ce stade, le trafic entrant à la VM de DBMS est toujours routé via l’équilibreur de charge. L’itinéraire de trafic sortant à partir de la VM DBMS à la couche application de que machine virtuelle dépend de la configuration de l’équilibreur de charge. 

L’équilibreur de charge propose une option de DirectServerReturn. Si cette option est configurée, le trafic dirigé à partir de la VM DBMS vers la couche application SAP est *pas* acheminé via l’équilibreur de charge. Au lieu de cela, il va directement à la couche application. Si DirectServerReturn n’est pas configuré, le trafic de retour à la couche application SAP est acheminé via l’équilibreur de charge.

Nous vous recommandons de configurer DirectServerReturn en combinaison avec les équilibreurs de charge sont positionnées entre la couche application SAP et la couche SGBD. Cette configuration réduit la latence du réseau entre les deux couches.

Pour obtenir un exemple montrant comment définir cette configuration avec SQL Server Always On, consultez [configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Si vous utilisez des modèles GitHub JSON publiés en tant que référence pour les déploiements de votre infrastructure SAP dans Azure, étudier cela [modèle pour un système SAP à 3 niveaux](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Dans ce modèle, vous pouvez également voir les paramètres appropriés pour l’équilibrage de charge.

### <a name="azure-accelerated-networking"></a>Mise en réseau accélérée Azure
Pour réduire davantage la latence du réseau entre les machines virtuelles Azure, nous vous recommandons de choisir [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Utilisez-le lorsque vous déployez des machines virtuelles Azure pour une charge de travail SAP, en particulier pour la couche application SAP et la couche SGBD SAP.

> [!NOTE]
> Pas tous les types de machines virtuelles prennent en charge la mise en réseau accélérée. L’article précédent répertorie les types de machine virtuelle qui prennent en charge la mise en réseau accélérée.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Pour savoir comment déployer des machines virtuelles avec accélération réseau pour Windows, consultez [créer une machine virtuelle de Windows avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Pour plus d’informations sur la distribution de Linux, consultez [créer une machine virtuelle Linux avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

- - -

> [!NOTE]
> SUSE, Red Hat et Oracle Linux prennent en charge la mise en réseau accélérée avec les versions récentes. Les versions plus anciennes comme SLES 12 SP2 ou RHEL 7.2 ne prennent pas en charge la mise en réseau accélérée Azure.
>


## <a name="deployment-of-host-monitoring"></a>Déploiement de la surveillance d’hôte
Pour la production d’applications SAP dans Azure virtual machines, SAP nécessite la possibilité d’obtenir des données à partir des hôtes physiques qui exécutent les machines virtuelles Azure de surveillance d’hôte. Un niveau de correctif logiciel SAP HostAgent spécifique est nécessaire pour activer cette fonctionnalité dans SAPOSCOL et SAP HostAgent. Le niveau de correctif logiciel exact est indiqué dans la Note de SAP [1409604].

Pour plus d’informations sur le déploiement de composants qui fournissent des données d’hôte à SAPOSCOL et SAPHOSTAGENT, et la gestion du cycle de vie de ces composants, consultez le [guide de déploiement][deployment-guide].


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur un système SGBD particulier, consultez :

- [Déploiement SGBD de machines virtuelles SQL Server Azure pour charge de travail SAP](dbms_guide_sqlserver.md)
- [Déploiement SGBD de machines virtuelles Oracle Azure pour charge de travail SAP](dbms_guide_oracle.md)
- [Déploiement SGBD de machines virtuelles IBM DB2 Azure pour charge de travail SAP](dbms_guide_ibm.md)
- [Déploiement SGBD de machines virtuelles SAP ASE Azure pour charge de travail SAP](dbms_guide_sapase.md)
- [Déploiement Content Server, LiveCache et SAP maxDB sur Azure](dbms_guide_maxdb.md)
- [Guide des opérations SAP HANA sur Azure](hana-vm-operations.md)
- [Haute disponibilité de SAP HANA pour les machines virtuelles Azure](sap-hana-availability-overview.md)
- [Guide de sauvegarde pour SAP HANA sur machines virtuelles Azure](sap-hana-backup-guide.md)

