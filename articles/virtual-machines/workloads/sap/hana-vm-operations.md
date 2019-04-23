---
title: Configurations et opérations de l’infrastructure SAP HANA sur Azure | Microsoft Docs
description: Guide des opérations pour les systèmes SAP HANA qui sont déployés sur des machines virtuelles Azure.
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
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699328"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurations et opérations de l’infrastructure SAP HANA sur Azure
Cet article fournit des conseils sur la façon de configurer l’infrastructure Azure et de faire fonctionner des systèmes SAP HANA qui sont déployés sur des machines virtuelles Azure natives (machines virtuelles). Cet article inclut également des informations de configuration pour SAP HANA scale-out pour la référence SKU de machine virtuelle M128s. Cet article n’est pas destiné à remplacer la documentation SAP standard, qui inclut le contenu suivant :

- [SAP Administration Guide (Guide d’administration de SAP)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP Installation Guide (Guide d’installation de SAP)](https://service.sap.com/instguides)
- [Notes SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser ce guide, vous devez disposer des connaissances de base quant aux différents composants Azure suivants :

- [Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Mise en réseau et réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Stockage Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Pour en savoir plus sur SAP NetWeaver et d’autres composants SAP sur Azure, consultez la section [SAP sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) de la [documentation Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considérations de base relatives à la configuration
Les sections suivantes décrivent les considérations de base relatives à la configuration pour le déploiement de systèmes SAP HANA sur des machines virtuelles Azure.

### <a name="connect-to-azure-virtual-machines"></a>Se connecter aux machines virtuelles Azure
Comme expliqué dans la [des machines virtuelles Azure, guide de planification](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), deux méthodes de base sont utilisées pour se connecter aux machines virtuelles Azure :

- Se connecter via internet et les points de terminaison publics sur une VM JumpBox ou sur la machine virtuelle qui exécute SAP HANA.
- Connexion par le biais d’un [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou d’Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Pour les scénarios de production, vous devez avoir une connectivité de site à site par le biais d’un VPN ou d’ExpressRoute. Ce type de connexion est également nécessaire pour les scénarios de production qui alimentent des scénarios de production où les logiciels SAP sont utilisée. L’image suivante représente un exemple de connectivité intersite :

![Connectivité intersite](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Choisir des types de machines virtuelles Azure
Les types de machine virtuelle Azure à utiliser pour les scénarios de production sont répertoriés dans le [documentation SAP pour IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pour les scénarios de production, une plus grande variété de types natifs de machine virtuelle Azure est disponible.

>[!NOTE]
> Pour les scénarios de production, utilisez les types de machines virtuelles qui sont répertoriées dans le [Remarque SAP n° 1928533](https://launchpad.support.sap.com/#/notes/1928533). Pour l’utilisation de machines virtuelles Azure pour les scénarios de production, vérifiez si SAP HANA certifié des machines virtuelles dans SAP publié [certifié la liste des plateformes IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Pour déployer les machines virtuelles dans Azure, utilisez :

- le portail Azure ;
- les applets de commande Azure PowerShell ;
- l’interface de ligne de commande Azure.

Vous pouvez également déployer une plateforme SAP HANA installée et complète sur les services de machine virtuelle Azure via le [plateforme cloud SAP](https://cal.sap.com/). Pour plus d’informations sur le processus d’installation, consultez [déploiement de SAP S/4HANA ou BW/4HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Pour plus d’informations sur l’automatisation publiée, consultez [cet article GitHub](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Choisissez un type de stockage Azure
Azure fournit deux types de stockage qui conviennent pour les machines virtuelles Azure qui exécutent SAP HANA :  

- Disques durs standards (HDD)
- Disques SSD Premium (SSD)

Pour en savoir plus sur ces types de disques, consultez [sélectionner un type de disque](../../windows/disks-types.md).

Azure propose deux méthodes de déploiement pour les disques durs virtuels sur le stockage Azure standard et premium storage. Si le scénario global le permet, tirez parti des déploiements [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Pour obtenir la liste de types de stockage et de leurs contrats SLA de débit d’e/s et de stockage, consultez [documentation Azure pour les disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Configurer le stockage des machines virtuelles Azure

Vous probablement jamais se préoccuper les sous-systèmes d’e/s et leurs fonctionnalités, car le fournisseur d’appliances fait en sorte que les exigences de stockage minimal ont été satisfaites pour SAP HANA. Lorsque vous générez vous-même l’infrastructure Azure, vous devez être conscient de certaines de ces exigences. Vous devez également comprendre les exigences de configuration suggérées dans les sections suivantes. Pour les cas où vous configurez les machines virtuelles, vous souhaitez que SAP HANA pour s’exécuter sur, vous devrez peut-être :

- Activer le volume en lecture/écriture sur/Hana/log à un minimum de 250 Mo/s pour les tailles d’e/s de 1 Mo.
- Permettre les lectures de 400 Mo/s au minimum pour/Hana/Data pour les tailles d’e/s de 16 Mo et 64 Mo.
- Permettre les écritures au moins 250 Mo/s pour/Hana/Data avec des tailles d’e/s de 16 Mo et 64 Mo.

Stockage de faible latence est primordial pour les systèmes SGBD, même si le SGBD, comme SAP HANA, conserve les données en mémoire. Le point critique dans le stockage concerne généralement les écritures du journal de transaction sur les systèmes SGBD. Mais les opérations telles qu’écrire des points d’enregistrement ou le chargement de données en mémoire une fois la récupération sur incident peut également être critique. 

Utilisation de disques premium Azure pour les volumes/Hana/Data et/Hana/log est obligatoire. Pour obtenir le débit minimal de/Hana/log et/Hana/Data comme vous le souhaitez par SAP, créer un volume RAID 0 à l’aide de mdadm ou un gestionnaire de Volume logique (LVM) sur plusieurs disques de stockage Azure premium. Utilisez également le volumes RAID comme/Hana/Data et/Hana/log. Nous vous recommandons d’utiliser les tailles d’entrelacement suivantes pour le disque RAID 0 :

- 64 ou 128 Ko pour/hana/data
- 32 Ko pour/hana/log

> [!NOTE]
> Vous n’avez pas besoin de configurer un niveau de redondance à l’aide de volumes RAID, car le stockage Azure premium et standard conservent trois images d’un disque dur virtuel. L’utilisation d’un volume RAID est purement pour configurer les volumes qui fournissent un débit d’e/s suffisant.

Nombre de disques durs virtuels Azure sous un volume RAID ne peut être cumulée à partir d’une côté de débit d’e/s et de stockage. Si vous placez un volume RAID 0 sur 3 disques de stockage Azure premium P30, il vous offre trois fois les IOPS et trois fois le débit de stockage de disque P30 de stockage premium Azure unique.

Les recommandations de mise en cache suivantes supposent les caractéristiques d’e/s pour SAP HANA :

- Il est tout à fait toute charge de travail de lecture sur les fichiers de données HANA. Les exceptions sont des e/s de grande taille après le redémarrage de l’instance HANA ou lorsque les données sont chargées dans HANA. Les sauvegardes de base de données HANA peuvent constituer un autre cas d’E/S en lecture plus volumineuses sur des fichiers de données. Par conséquent, la mise en cache en lecture est inutile, car, dans la plupart des cas, tous les volumes de fichiers de données doivent être lues entièrement.
- Les points de sauvegarde HANA et la récupération sur incident HANA donnent lieu à des écritures en rafales dans les fichiers de données. Écriture de points de sauvegarde est asynchrone et ne contenir jusqu'à toutes les transactions utilisateur. L’écriture de données au cours d’une récupération sur incident est critique pour les performances et la réactivité du système. Récupération sur incident doit être des situations exceptionnelles au lieu de cela.
- Il n’y a pratiquement pas de lectures à partir des fichiers de restauration par progression HANA. Les exceptions sont des e/s volumineuses lorsque vous effectuez des sauvegardes du journal des transactions, de récupération sur incident ou de la phase de redémarrage d’une instance HANA.  
- La charge principale par rapport au fichier journal de restauration par progression SAP HANA est écrit. Selon la nature de la charge de travail, vous pouvez avoir des e/s aussi petit que 4 Ko, ou dans d’autres cas, les e/s la taille de 1 Mo ou plus. La latence des écritures par rapport au journal de restauration par progression SAP HANA est critique pour les performances.
- Toutes les écritures doivent être conservées sur le disque de manière fiable.

À la suite de ces modèles d’e/s observées par SAP HANA, définissez la mise en cache pour les différents volumes qui utilisent le stockage premium Azure pour :

- **/ hana/data**: Aucune mise en cache.
- **/ hana/log**: Aucune mise en cache, avec une exception pour les machines virtuelles de série M (voir plus loin dans cet article).
- **/ hana/shared**: Le cache de lecture.


En outre, n’oubliez pas le débit d’e/s de machine virtuelle global lors de la taille ou de décider sur une machine virtuelle. Débit de stockage de machine virtuelle global est décrit dans [tailles de machine virtuelle à mémoire optimisée](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Mode de planificateur d’e/s de Linux
Linux dispose de plusieurs modes de planification d’E-S. Une recommandation commune provenant de fournisseurs de Linux et de SAP consiste à définir le mode de planificateur d’e/s pour les volumes de disque de la **cfq** mode pour le **noop** mode. Pour plus d’informations, consultez [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Solution de stockage avec l’accélérateur des écritures Azure pour les machines virtuelles de série M
 Écrire le Qu'accélérateur est une fonctionnalité Azure qui déploie des machines virtuelles Azure de série M exclusivement. La fonctionnalité vise à améliorer la latence d’e/s des écritures dans le stockage Azure premium. Pour SAP HANA, l’Accélérateur des écritures doit être utilisé exclusivement sur le volume /hana/log. Pour cette raison, les configurations présentées jusqu’ici doivent être modifiées. Le principal changement concerne la répartition entre/Hana/Data et/Hana/log pour pouvoir utiliser l’accélérateur des écritures sur le volume/Hana/log uniquement. 

> [!IMPORTANT]
> Certification SAP HANA pour Azure virtual machines est exclusif à l’accélérateur des écritures pour le volume du journal/hana/de la série M. Par conséquent, les déploiements de SAP HANA de scénario de production sur Azure série M-machines virtuelles doivent être configurés avec l’accélérateur des écritures pour/hana/volume du journal.

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Le tableau suivant présente les configurations recommandées.

| Référence de la machine virtuelle | RAM | E/s maximal de machines virtuelles<br /> throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1 000 Go | 1 000 Mo/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 Go | 1 000 Mo/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 Go | 2 000 Mo/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 Go | 2 000 Mo/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Vérifiez si le débit de stockage pour les différents volumes suggérés répond à la charge de travail que vous souhaitez exécuter. Si la charge de travail nécessite plus grands volumes pour/Hana/Data et/Hana/log, augmentez le nombre de disques durs virtuels le stockage Azure premium. Dimensionnement d’un volume avec plusieurs disques durs virtuels que répertoriées augmente les e/s et un débit d’e/s dans les limites du type de machine virtuelle Azure.

L’Accélérateur des écritures fonctionne uniquement en association avec des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Au minimum, les disques de stockage Azure premium constituant le volume/Hana/log doivent être déployés en tant que disques gérés.

Il existe des limites pour les disques durs virtuels par machine virtuelle accélérateur des écritures pouvant prendre en charge le stockage Azure premium. Les limites actuelles sont :

- 16 disques durs virtuels pour un M128xx machine virtuelle.
- 8 disques durs virtuels pour un M64xx machine virtuelle.
- 4 disques durs virtuels pour un M32xx machine virtuelle.

Pour plus d’informations sur la façon d’activer l’accélérateur des écritures, consultez [accélérateur des écritures](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Pour plus d’informations sur et des restrictions pour l’accélérateur des écritures, consultez la documentation de même.


#### <a name="cost-conscious-azure-storage-configuration"></a>Configuration du stockage Azure abordables
Le tableau suivant illustre une configuration de types de machines virtuelles que les clients utilisent couramment pour héberger SAP HANA sur des machines virtuelles Azure. Il peut y avoir certains types de machines virtuelles qui ne répondent pas à tous les critères minimum pour SAP HANA. Il existe peut-être certains types de machines virtuelles ne sont pas officiellement pris en charge avec SAP HANA par SAP. Jusqu'à présent, ces machines virtuelles ont effectuées correctement pour les scénarios de production. 

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| Référence de la machine virtuelle | RAM | E/s maximal de machines virtuelles<br /> throughput | /hana/data et /hana/log<br /> agrégés avec LVM ou mdadm | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 Gio | 768 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 Go | 384 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 Gio | 768 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 Gio | 1 200 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 Gio | 2 000 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1 000 Go | 1 000 Mo/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1 750 Go | 1 000 Mo/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 000 Go | 2 000 Mo/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3 800 Go | 2 000 Mo/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Les disques qui sont recommandées pour les types de la machine virtuelle plus petits avec 3 x P20 P20 les volumes en ce qui concerne les recommandations d’espace sont indiquées dans le [livre blanc de stockage TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Le choix affiché dans la table a été effectué pour fournir un débit de disque suffisante pour SAP HANA. Si vous devez modifier le **/hana/backup** volume, qui a une dimension pour conserver des sauvegardes qui représentent deux fois le volume de mémoire, n’hésitez pas à effectuer des ajustements. 

Vérifiez si le débit de stockage pour les différents volumes suggérés répond à la charge de travail que vous souhaitez exécuter. Si la charge de travail nécessite plus grands volumes pour/Hana/Data et/Hana/log, augmentez le nombre de disques durs virtuels le stockage Azure premium. Dimensionnement d’un volume avec plusieurs disques durs virtuels que répertoriées augmente les e/s et un débit d’e/s dans les limites du type de machine virtuelle Azure. 

> [!NOTE]
> Les configurations précédentes ne bénéficient [contrat SLA de machine virtuelle Azure unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , car elle utilise un mélange de stockage premium Azure et stockage Azure standard. La sélection a été choisie pour optimiser les coûts. Choisissez le stockage premium pour tous les disques dans la table qui sont répertoriés en tant que stockage Azure standard (Sxx) pour que la configuration de machine virtuelle compatible avec le SLA de machine virtuelle Azure unique.


> [!NOTE]
> Les recommandations de configuration de disque ciblent de la configuration minimale requise qui SAP donne ses fournisseurs d’infrastructure. Dans les déploiements de clients réels et des scénarios de charge de travail, ces recommandations ne permettait suffisantes dans certaines situations. Par exemple, un client a souhaité un rechargement plus rapide des données après un redémarrage HANA ou configurations de sauvegarde requis de bande passante plus élevée pour le stockage. Autres cas incluent/Hana/log, où 5 000 e/s n’étaient pas suffisantes pour la charge de travail spécifique. Utilisez ces recommandations comme point de départ et les adapter en fonction des exigences de la charge de travail.
>  

### <a name="set-up-azure-virtual-networks"></a>Configurer les réseaux virtuels Azure
Lorsque vous disposez de connectivité de site à site vers Azure via VPN ou Azure ExpressRoute, vous devez disposer d’au moins un réseau virtuel Azure qui est connecté via une passerelle virtuelle au circuit ExpressRoute ou VPN. Dans des déploiements simples, la passerelle virtuelle peut être déployée dans un sous-réseau du réseau virtuel Azure qui héberge les instances SAP HANA trop. 

Pour installer SAP HANA, créez deux sous-réseaux supplémentaires au sein du réseau virtuel Azure. Un sous-réseau héberge les machines virtuelles pour exécuter les instances de SAP HANA. L’autre sous-réseau exécute le serveur de rebond ou gestion des machines virtuelles pour héberger SAP HANA Studio, autres logiciels de gestion ou le logiciel d’application.

> [!IMPORTANT]
> Configuration [Azure les appliances virtuelles réseau](https://azure.microsoft.com/solutions/network-appliances/) dans le chemin de communication entre l’application SAP et la couche SGBD d’un SAP NetWeaver-, Hybris ou système basé sur S/4HANA SAP n’est pas pris en charge. Cette restriction est pour des raisons de performances et de fonctionnalités. Le chemin d’accès de communication entre la couche application SAP et la couche SGBD doit être direct. N’inclut pas la restriction [règles (NSG) de groupe de groupe de sécurité d’application (ASG) et de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview) si ces règles ASG et groupe de sécurité réseau autorisent un chemin de communication directe. 
>
> Autres scénarios où les appliances virtuelles réseau ne sont pas pris en charge se trouvent dans : 
>
> - Voies de communication entre les machines virtuelles Azure qui représentent les Linux Pacemaker cluster nœuds et les appareils SBD, comme décrit dans [haute disponibilité pour SAP NetWeaver sur machines virtuelles Azure sur SUSE Linux Enterprise Server for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Voies de communication entre les machines virtuelles Azure et Windows Server Scale-Out File Server défini jusqu'à comme décrit dans [Cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un partage de fichiers dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Appliances virtuelles réseau dans les chemins de communication peuvent doubler la latence du réseau entre les partenaires de communication de deux. Ils peuvent également restreindre le débit dans les chemins critiques entre la couche application SAP et la couche SGBD. Dans certains scénarios de client, les appliances virtuelles réseau peuvent provoquer des clusters Pacemaker Linux échec. Il s’agit de cas où les communications entre les nœuds de cluster Linux Pacemaker communiquent sur leur appareil SBD via une appliance virtuelle réseau.  
> 

> [!IMPORTANT]
> Une autre conception ayant de *pas* pris en charge est la répartition de la couche application SAP et la couche SGBD dans différents réseaux virtuels Azure qui ne sont pas [homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre eux. Nous vous recommandons de séparer la couche application SAP et la couche SGBD en utilisant des sous-réseaux au sein d’un réseau virtuel Azure au lieu d’à l’aide de différents réseaux virtuels Azure. 
>
>Si vous décidez de ne pas suivre la recommandation et séparer à la place les deux couches dans différents réseaux virtuels, les deux réseaux virtuels doivent être [homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> N’oubliez pas que le trafic réseau entre deux [homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) réseaux virtuels Azure est soumis à des coûts de transfert. Volume de données volumineuses qui se compose de plusieurs téraoctets est échangé entre la couche application SAP et la couche SGBD. Vous pouvez accumuler les coûts substantielles si la couche application SAP et la couche SGBD sont séparés entre deux réseaux virtuels homologués Azure. 

Quand vous installez les machines virtuelles pour exécuter SAP HANA, celles-ci nécessitent :

- Deux cartes réseau virtuelles installées. Une carte réseau se connecte au sous-réseau de gestion. Une autre carte réseau se connecte à partir du réseau local ou d’autres réseaux à l’instance SAP HANA dans la machine virtuelle Azure.
- des adresses IP statiques privées déployées pour les deux cartes réseau virtuelles.

> [!NOTE]
> Affecter des adresses IP statiques via Azure signifie que les affecter aux cartes réseau virtuelles individuelles. N’affectez pas des adresses IP statiques dans le système d’exploitation invité pour une carte réseau virtuelle. Certains services Azure comme sauvegarde Azure s’appuient sur le fait qu’au moins la principale carte réseau virtuelle est définie sur la valeur DHCP et non à des adresses IP statiques. Pour plus d’informations, consultez [sauvegarde de machines virtuelles Azure de résoudre les problèmes](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pour affecter plusieurs adresses IP statiques à une machine virtuelle, attribuer plusieurs cartes réseau virtuelles à une machine virtuelle.
>
>

Pour les déploiements qui sont vivantes, créer une architecture réseau de centre de données virtuel dans Azure. Cette architecture recommande la séparation de la passerelle de réseau virtuel Azure qui se connecte en local dans un réseau virtuel Azure distinct. Ce réseau virtuel distinct héberge tout le trafic qui quitte en local ou à internet. À l’aide de cette approche, vous pouvez déployer des logiciels à auditer et consigner le trafic qui entre dans le centre de données virtuel dans Azure dans ce réseau virtuel hub distinct. De cette façon, vous avez un seul réseau virtuel qui héberge tous les logiciels et configurations relatives au trafic de recettes et sortant vers votre déploiement Azure.


Pour plus d’informations sur l’approche de centre de données virtuel et la conception de réseau virtuel Azure, consultez : 

- [Centre de données virtuel Azure : Une perspective réseau](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Centre de données virtuel Azure et le plan de contrôle entreprise](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>Le trafic qui transite entre un réseau virtuel hub et un réseau virtuel spoke à l’aide de [homologation de réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) est soumise aux supplémentaires [coûts](https://azure.microsoft.com/pricing/details/virtual-network/). En fonction de ces coûts, vous devrez peut-être faire des compromis entre l’exécution d’une conception de réseau hub-and-spoke stricte et plusieurs [passerelles Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que vous vous connectez à des rayons (spokes) afin de contourner l’homologation de réseaux virtuels. 
>
> Les passerelles ExpressRoute Azure introduisent supplémentaires [coûts](https://azure.microsoft.com/pricing/details/vpn-gateway/) trop. Vous pouvez également rencontrer des coûts supplémentaires pour un logiciel tiers que vous permet de connecter, auditer et surveiller le trafic réseau. Considérez les coûts pour l’échange de données via l’homologation de réseaux virtuels et les coûts créés par des passerelles ExpressRoute supplémentaires et les licences logicielles. Vous pouvez décider sur micro-segmentation au sein d’un réseau virtuel à l’aide de sous-réseaux comme des unités d’isolation au lieu de réseaux virtuels.


Pour une vue d’ensemble des différentes méthodes que vous pouvez utiliser pour affecter des adresses IP, consultez [types et méthodes d’allocation dans Azure d’adresses IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pour les machines virtuelles qui exécutent SAP HANA, travailler avec les adresses IP statiques qui sont affectés. La raison est que certains attributs de configuration pour HANA référencent des adresses IP.

[Groupes de sécurité réseau Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) servent à diriger le trafic est acheminé vers l’instance SAP HANA ou un serveur de rebond. Les groupes de sécurité réseau et finalement [groupes de sécurité d’application](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) sont associés au sous-réseau SAP HANA et le sous-réseau de gestion.

L’illustration suivante montre une vue d’ensemble d’un schéma de déploiement approximatif pour SAP HANA qui suit une architecture de réseau virtuel hub-and-spoke :

![Schéma de déploiement approximatif pour SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Pour déployer SAP HANA dans Azure sans une connexion site à site, protéger l’instance SAP HANA à partir de l’internet public et masquez-le derrière un proxy vers l’avant. Dans ce scénario de base, le déploiement s’appuie sur les services DNS intégrés à Azure pour résoudre les noms d’hôte. Dans un déploiement plus complexe où des adresses IP publiques sont utilisées, les services DNS intégrés à Azure sont particulièrement importants. Utiliser des groupes de sécurité réseau Azure et [Azure les appliances virtuelles réseau](https://azure.microsoft.com/solutions/network-appliances/) pour surveiller le routage à partir d’internet dans votre architecture de réseau virtuel Azure dans Azure. 

L’illustration suivante montre un schéma approximatif pour le déploiement de SAP HANA sans une connexion site à site dans une architecture de réseau virtuel hub-and-spoke :
  
![Schéma de déploiement approximatif pour SAP HANA sans une connexion de site à site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Pour une autre description de l’utilisation des appliances virtuelles réseau Azure pour contrôler et surveiller l’accès à partir d’internet sans l’architecture de réseau virtuel hub-and-spoke, consultez [déployer des appliances virtuelles réseau hautement disponible](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>Configurer l’infrastructure Azure pour SAP HANA scale-out
Microsoft dispose d’un SKU VM série M qui est certifié pour une configuration SAP HANA avec montée en puissance. Le type de machine virtuelle M128s est certifié pour une montée en puissance de jusqu'à 16 nœuds. Pour les modifications dans les certifications de montée en puissance SAP HANA sur des machines virtuelles Azure, consultez le [certifié la liste des plateformes IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Les versions de système d’exploitation minimales utilisées pour déployer des configurations de montée en puissance dans les machines virtuelles Azure sont :

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

Pour la certification de montée en puissance de 16 nœuds :

- Un nœud est le nœud principal.
- Un maximum de 15 nœuds sont des nœuds de travail.

>[!NOTE]
>Dans les déploiements de montée en puissance de machine virtuelle Azure, il n’est pas possible d’utiliser un nœud de secours.
>

Il existe deux raisons pourquoi vous ne pouvez pas configurer un nœud de secours :

- À ce stade, Azure ne dispose d’aucun service NFS natif. Par conséquent, les partages NFS doivent être configurés à l’aide de fonctionnalités tierces.
- Aucune de ces configurations de NFS tiers correspondent au critère de latence de stockage pour SAP HANA avec leurs solutions déployées sur Azure.

Par conséquent, les volumes/Hana/Data et/Hana/log ne peut pas être partagés. Ne pas partager ces volumes des nœuds uniques empêche l’utilisation d’un nœud de secours de SAP HANA dans une configuration scale-out.

L’illustration suivante montre la conception de base pour un seul nœud dans une configuration scale-out :

![Scale-out de base pour un nœud unique](media/hana-vm-operations/scale-out-basics.PNG)

La configuration de base d’un nœud de machine virtuelle pour le scale-out de SAP HANA ressemble à ceci :

- Pour/Hana/Shared, vous créez un cluster NFS hautement disponible basé sur SUSE Linux 12 SP3. Ce cluster héberge les partages NFS/Hana/Shared de votre configuration scale-out et SAP NetWeaver ou de Services centraux BW/4HANA. Pour plus d’informations sur la création d’une telle configuration, consultez [haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Tous les autres volumes de disque ne sont pas partagés entre les différents nœuds et ne sont pas basées sur NFS. Configurations d’installation et les étapes pour les installations HANA de montée en puissance avec non partagée/Hana/Data et/Hana/log sont fournies plus loin dans cet article.

>[!NOTE]
>Le cluster NFS à haute disponibilité tel qu’illustré jusqu'à présent dans les graphiques est pris en charge avec SUSE Linux uniquement. Une solution NFS hautement disponible basée sur Red Hat sera disponible ultérieurement.

Les volumes pour les nœuds de dimensionnement est le même que pour monter en puissance, à l’exception / Hana/Shared. Le tableau suivant montre les tailles suggérées et les types pour la référence SKU de machine virtuelle M128s.

| Référence de la machine virtuelle | RAM | E/s maximal de machines virtuelles<br /> throughput | /hana/data | /hana/log | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2 000 Go | 2 000 Mo/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Vérifiez si le débit de stockage pour les différents volumes suggérés répond à la charge de travail que vous souhaitez exécuter. Si la charge de travail nécessite plus grands volumes pour/Hana/Data et/Hana/log, augmentez le nombre de disques durs virtuels le stockage Azure premium. Dimensionnement d’un volume avec plusieurs disques durs virtuels que répertoriées augmente les e/s et un débit d’e/s dans les limites du type de machine virtuelle Azure. S’appliquent également accélérateur des écritures sur les disques qui constituent le volume/Hana/log.
 

Pour une formule qui définit la taille du volume/hana/shared pour la montée en puissance en tant que la taille de mémoire d’un nœud worker unique par quatre nœuds de travail, consultez [les besoins de stockage SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

En supposant que vous prenez la SAP HANA scale-out certifiés M128s machine virtuelle Azure avec environ 2 To de mémoire, les recommandations de SAP sont réparties en tant que :

- Pour un nœud principal et jusqu'à quatre nœuds de travail, la taille du volume/Hana/Shared est de 2 To.
- Pour un seul nœud maître et cinq et huit nœuds worker, la taille du volume/Hana/Shared est de 4 To.
- Pour un nœud principal et les nœuds de travail de 9 à 12, la taille du volume/Hana/Shared est 6 To.
- Pour un nœud principal et les nœuds de travail de 12 à 15, la taille du volume/Hana/Shared est de 8 To.

L’autres conception importante qui s’affiche dans le graphique de la configuration de nœud unique pour une machine virtuelle de montée en puissance SAP HANA est le réseau virtuel avec la configuration de sous-réseau. SAP recommande vivement de séparer le trafic des clients et applications provenant des communications entre les nœuds HANA. 

Pour atteindre cet objectif, joindre deux cartes réseau virtuelles différents à la machine virtuelle, comme indiqué dans le graphique. Les deux cartes réseau virtuelles est dans des sous-réseaux différents et ont deux adresses IP différentes. Vous montre ensuite comment contrôler le flux du trafic avec les règles de routage à l’aide de groupes de sécurité réseau ou itinéraires définis par l’utilisateur.

En particulier dans Azure, il n’existe aucune moyens et les méthodes pour appliquer la qualité de service et des quotas sur les cartes réseau virtuelles spécifiques. Par conséquent, la séparation de la communication intra-nœud et les clients et applications ne s’ouvre des opportunités pour la priorité d’un flux plutôt que l’autre. Au lieu de cela, la séparation reste une mesure de sécurité dans les communications intra-nœud des configurations de montée en puissance de protection.  

>[!IMPORTANT]
>SAP recommande fortement de séparer le trafic réseau vers le client et l’application côté et intra-nœud trafic comme décrit dans cet article. Nous vous recommandons de placer une architecture en place comme indiqué dans le graphique précédent.
>

L’illustration suivante montre l’architecture réseau minimum requis à partir d’un point de vue mise en réseau :

![Scale-out de base pour un nœud unique](media/hana-vm-operations/scale-out-networking-overview.PNG)

Les limites de prise en charge jusqu'à présent sont 15 nœuds de travail en plus un nœud principal.

L’illustration suivante montre l’architecture de stockage à partir d’un point de vue du stockage :


![Scale-out de base pour un nœud unique](media/hana-vm-operations/scale-out-storage-overview.PNG)

Le volume/Hana/Shared se trouve sur la configuration de partage NFS hautement disponible. Tous les autres disques sont montés localement pour les machines virtuelles individuelles. 

### <a name="highly-available-nfs-share"></a>Partage NFS haute disponibilité
Jusqu’ici, le cluster NFS à haute disponibilité collabore avec SUSE Linux uniquement. Pour plus d’informations d’installation, consultez [haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Si vous ne partagez pas le cluster NFS avec toutes les autres configurations HANA en dehors du réseau virtuel Azure qui exécute les instances SAP HANA, installez-le dans le même réseau virtuel. Installer dans son propre sous-réseau et vous assurer que non tout le trafic arbitraire peut accéder au sous-réseau. Au lieu de cela, limiter le trafic vers ce sous-réseau pour les adresses IP de la machine virtuelle qui s’exécutent le trafic vers les volumes/Hana/Shared.

Les recommandations relatives à la carte réseau virtuelle d’une machine virtuelle montée en puissance HANA qui achemine le trafic / Hana/Shared, sont :

- Étant donné que le trafic vers/Hana/Shared est modéré, routez-le via la carte réseau virtuelle qui est affectée au réseau client dans la configuration minimale.
- Finalement, pour le trafic/hana/shared, déployez un troisième sous-réseau dans le réseau virtuel dans lequel vous déployez la configuration de scale-out SAP HANA. Affecter une troisième carte réseau virtuelle qui est hébergée dans ce sous-réseau. Utilisez la troisième carte réseau virtuelle et l’adresse IP associée pour le trafic sur le partage NFS. Vous pouvez ensuite appliquer des accès et des règles de routage spécifiques.

>[!IMPORTANT]
>Le trafic réseau entre les machines virtuelles qui ont de SAP HANA de manière montée en puissance déployée et NFS hautement disponible en aucun cas peut-être être routé via un [appliance virtuelle réseau](https://azure.microsoft.com/solutions/network-appliances/) ou des appliances virtuelles similaires. Groupes de sécurité réseau Azure ne sont aucune de ces appareils. Vérifiez vos règles de routage pour vous assurer que les appliances virtuelles réseau ou des appliances virtuelles similaires sont contournés lorsqu’ils accèdent à NFS hautement disponible partagent à partir de machines virtuelles qui exécutent SAP HANA.
> 

Si vous souhaitez partager le cluster NFS à haute disponibilité entre les configurations de SAP HANA, déplacez toutes ces configurations HANA dans le même réseau virtuel. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Installer une mise à l’échelle-out SAP HANA dans Azure
Pour installer une configuration de SAP de montée en puissance, suivez ces étapes générales :

- Déployer le nouveau ou adapter la nouvelle infrastructure de réseau virtuel Azure.
- Déployer les nouvelles machines virtuelles à l’aide de volumes de stockage premium gérés par Azure.
- Déployer une nouvelle ou adapter un cluster NFS à haute disponibilité existant.
- Adapter le routage réseau pour vous assurer que, par exemple, les communications intra-nœud entre les machines virtuelles n’est pas acheminée via un [appliance virtuelle réseau](https://azure.microsoft.com/solutions/network-appliances/). Cela vaut également pour le trafic entre les machines virtuelles et le cluster NFS hautement disponible.
- À l’installation du nœud principal SAP HANA.
- Adapter les paramètres de configuration du nœud principal SAP HANA.
- Poursuivre l’installation des nœuds de travail de SAP HANA.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Installation de SAP HANA dans une configuration scale-out
Votre infrastructure de machine virtuelle Azure est déployé, et toutes les autres tâches de préparation sont terminées. Maintenant, pour installer les configurations de montée en puissance SAP HANA, procédez comme suit :

- Installer le nœud principal SAP HANA en fonction de la documentation de SAP.
- *Après l’installation, modifiez le fichier global.ini et ajoutez le paramètre « basepath_shared = no » pour le global.ini*. Ce paramètre permet de SAP HANA à exécuter dans la montée en puissance sans partagée/Hana/Data et/Hana/log volumes entre les nœuds. Pour plus d’informations, consultez [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Après avoir modifié le paramètre global.ini, redémarrez l’instance SAP HANA.
- Ajoutez des nœuds de travail supplémentaires. Pour plus d’informations, consultez [guide d’administration de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Spécifiez le réseau interne de la communication entre nœuds SAP HANA lors de l’installation ou par la suite en utilisant, par exemple, l’outil hdblcm local. Pour plus d’informations, consultez [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Après cette routine d’installation, la configuration de montée en puissance que vous avez installé utilise des disques non partagés pour exécuter/Hana/Data et/Hana/log. Le volume/hana/shared est placé sur la haute disponibilité de partage NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA 2.0 hiérarchisation dynamique des machines virtuelles Azure

Outre les certifications SAP HANA sur machines virtuelles Azure de série M, SAP HANA 2.0 hiérarchisation dynamique (2.0 DT) est également pris en charge sur Microsoft Azure. Pour obtenir des liens vers la documentation de hiérarchisation dynamique de SAP HANA, consultez la section « Lie à DT documentation 2.0 » plus loin dans cet article. Il n’existe aucune différence dans l’installation du produit ou de son fonctionnement, par exemple, via le Cockpit SAP HANA à l’intérieur d’une machine virtuelle Azure, mais quelques éléments importants sont obligatoires pour la prise en charge officielle sur Azure. Ces points clés sont décrites dans les sections suivantes. 

SAP HANA DT 2.0 n’est pas pris en charge par SAP BW ou S4HANA. Les principaux cas d’usage sont maintenant les applications natives HANA.


### <a name="overview"></a>Présentation

L’illustration suivante donne une vue d’ensemble de la prise en charge du type de données 2.0 sur Microsoft Azure. Suivez ces exigences obligatoires pour se conformer à la certification officielle :

- DT 2.0 doit être installé sur une machine virtuelle Azure dédiée. Il peut ne pas fonctionner sur la même machine virtuelle où SAP HANA s’exécute.
- SAP HANA et les machines virtuelles de DT 2.0 doivent être déployés dans le même réseau virtuel Azure.
- La SAP HANA et les machines virtuelles de DT 2.0 doivent être déployés avec Azure en réseau accélérée est activée.
- Le type de stockage pour les machines virtuelles 2.0 de type de données doit être le stockage Azure premium.
- Plusieurs disques Azure doivent être attachés à la machine virtuelle 2.0 de DT.
- Création d’un logiciel RAID ou volume agrégé par bandes, soit par le biais de LVM ou mdadm, est requis par l’entrelacement sur les disques Azure.

Les sections suivantes fournissent des explications supplémentaires.

![Présentation de l’architecture SAP HANA 2.0 DT](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Machine virtuelle Azure dédiée pour SAP HANA DT 2.0

Sur Azure IaaS, DT 2.0 est pris en charge uniquement sur une machine virtuelle dédiée. DT 2.0 n’est pas autorisée à s’exécuter sur la machine virtuelle Azure même où l’instance HANA est en cours d’exécution. Initialement, deux types de machine virtuelle peuvent être utilisés pour exécuter SAP HANA 2.0 DT :

- M64-32ms 
- E32sv3 

Pour obtenir des descriptions de type de machine virtuelle, consultez [tailles de machine virtuelle à mémoire optimisée](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Étant donné le principe fondamental de la version 2.0 du type de données, qui est sur le déchargement des données à chaud pour réduire les coûts, il est judicieux d’utiliser des tailles de machine virtuelle correspondantes. Il n’existe aucune règle stricte pour les combinaisons possibles. Cela dépend de la charge de travail spécifique du client.

Le tableau suivant présente les configurations recommandées.

| Type de machine virtuelle SAP HANA | Type de machine virtuelle DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Toutes les combinaisons de SAP HANA certifié des machines virtuelles de la série M avec prise en charge du type de données 2.0 des machines virtuelles, telles que M64-32ms et E32sv3, sont possibles.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Mise en réseau Azure et SAP HANA DT 2.0

Débit du réseau entre la machine virtuelle 2.0 de type de données et de la machine virtuelle SAP HANA avec un minimum de 10 Go est requis pour installer DT 2.0 sur une machine virtuelle dédiée. Par conséquent, il est obligatoire pour placer toutes les machines virtuelles dans le même réseau virtuel Azure et activer la mise en réseau accélérée Azure.

Pour plus d’informations sur la mise en réseau accélérée Azure, consultez [créer une machine virtuelle Linux avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>Stockage des machines virtuelles pour SAP HANA 2.0 DT

En fonction du type de données 2.0 des méthodes recommandées, le débit d’e/s de disque minimale est 50 Mo/s par cœur physique. Examinez les spécifications pour les deux types de machine virtuelle Azure, qui sont pris en charge pour DT 2.0, la limite de débit d’e/s pour la machine virtuelle de disque maximal est :

- **E32sv3**:   768 Mo/s, mise hors cache, ce qui signifie qu’un ratio de 48 Mo/s par cœur physique
- **M64-32ms**:  1 000 Mo/s, mise hors cache, ce qui signifie qu’un ratio de 62,5 Mo/s par cœur physique

Attacher plusieurs disques Azure à la machine virtuelle 2.0 de type de données et la création d’un RAID logiciel à l’aide d’agrégation par bandes sur le niveau de système d’exploitation pour atteindre la limite maximale de débit de disque sont nécessaire. Un seul disque Azure ne peut pas fournir le débit pour atteindre la limite maximale de la machine virtuelle. Le stockage Azure premium est obligatoire pour exécuter DT 2.0. 

- Pour plus d’informations sur les types de disque Azure disponibles, consultez [sélectionner un type de disque pour les machines virtuelles Windows Azure IaaS](../../windows/disks-types.md).
- Pour plus d’informations sur la création d’un RAID logiciel par le biais de mdadm, consultez [configurer un RAID logiciel sur Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Pour plus d’informations sur la façon de configurer LVM pour créer un volume agrégé par bandes pour un débit maximal, consultez [configurer LVM sur une VM Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

Selon les exigences de taille, il existe différentes options pour atteindre le débit maximal d’une machine virtuelle. Voici les configurations des disques de volume de données possibles permettant à chaque type de machine virtuelle DT 2.0 d’atteindre la limite supérieure de débit. La machine virtuelle E32sv3 est considéré comme un niveau d’entrée pour les charges de travail plus petits. Si elle n’est pas assez rapide, il peut être nécessaire de redimensionner la machine virtuelle à M64-32ms.

Étant donné que la machine virtuelle M64-32ms a une grande quantité de mémoire, la charge d’e/s ne pourra peut-être pas atteindre la limite, en particulier pour les charges de travail gourmandes en lecture. Moins de disques dans le jeu de bandes peuvent être suffisants en fonction de la charge de travail spécifique au client. Pour être prudent, les configurations de disque suivantes ont été choisies pour garantir le débit maximal.


| Référence de la machine virtuelle | Configuration de disque 1 | Configuration de disque 2 | Configuration de disque 3 | Configuration de disque 4 | Configuration de disque 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 To | 4 x P40 -> 8 To | 5 x P30 -> 5 To | 7 x P20 -> 3,5 To | 8 x P15 -> 2 To | 
| E32sv3 | 3 x P50 -> 12 To | 3 x P40 -> 6 To | 4 x P30 -> 4 To | 5 x P20 -> 2,5 To | 6 x P15 -> 1,5 To | 


En particulier si la charge de travail gourmandes en lecture, activant le paramètre de cache hôte Azure « read-only » comme recommandé pour les volumes de données du logiciel de base de données peut améliorer les performances d’e/s. Pour le journal des transactions, le cache de disque hôte Azure doit être « none ». 

Le point de départ que nous recommandons pour la taille du volume de journal est une méthode heuristique de 15 % de la taille des données. Pour créer le volume du journal, utilisez les types de disque Azure différent selon les exigences de débit et de coût. Pour le volume du journal, un débit d’e/s élevé est requis. 

Si vous utilisez la machine virtuelle tapez M64-32ms, nous vous recommandons d’activer [accélérateur des écritures](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Accélérateur d’écriture est une fonctionnalité Azure qui fournit la latence d’écriture sur disque optimal pour le journal des transactions. Il est disponible uniquement pour la série M. Il existe certains éléments à prendre en compte, telles que le nombre maximal de disques par type de machine virtuelle. Pour plus d’informations sur l’accélérateur des écritures, consultez [accélérateur des écritures activer](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


Le tableau suivant présente quelques exemples pour vous aider à dimensionner le volume du journal.

| type de taille et le disque de volume de données | journaux de configuration de type de volume et le disque 1 | journaux de configuration de type de volume et disque 2 |
| --- | --- | --- |
| 4 x P50 -> 16 To | 5 x P20 -> 2,5 To | 3 x P30 -> 3 To |
| 6 x P15 -> 1,5 To | 4 x P6 -> 256 Go | 1 x P15 -> 256 Go |


Comme pour SAP HANA scale-out, le répertoire/Hana/Shared doit être partagé entre la machine virtuelle SAP HANA et la machine virtuelle 2.0 de DT. Nous vous recommandons d’utiliser la même architecture que pour SAP HANA scale-out à l’aide de machines virtuelles dédiées, qui agissent comme un serveur NFS hautement disponible. Pour fournir un volume partagé de sauvegarde, utilisez la conception identique. Mais c’est à vous de décider si une haute disponibilité est nécessaire ou si elle est suffisante pour utiliser une machine virtuelle dédiée avec une capacité de stockage suffisante pour agir comme un serveur de sauvegarde.



### <a name="links-to-dt-20-documentation"></a>Liens vers la documentation de DT 2.0 

- [SAP HANA dynamique hiérarchisation guide d’installation et mise à jour](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Ressources et des didacticiels de hiérarchisation dynamiques de SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dynamique hiérarchisation preuve de concept](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamic tiering enhancements](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Opérations pour le déploiement de SAP HANA sur des machines virtuelles Azure
Les sections suivantes décrivent certaines des opérations liées au déploiement de systèmes SAP HANA sur des machines virtuelles Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Opérations de sauvegarde et restauration sur des machines virtuelles Azure
Les documents suivants décrivent comment sauvegarder et restaurer votre déploiement de SAP HANA :

- [Vue d’ensemble de la sauvegarde SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Sauvegarde SAP HANA au niveau des fichiers](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Démarrer et redémarrer des machines virtuelles contenant SAP HANA
L’une des caractéristiques importantes du cloud public Azure est que vous êtes facturé uniquement pour les minutes de calcul que vous dépensez. Par exemple, lorsque vous arrêtez une machine virtuelle exécutant SAP HANA, vous êtes facturé uniquement pour les coûts de stockage pendant cette période. Une autre fonctionnalité est disponible quand vous spécifiez des adresses IP statiques pour vos machines virtuelles dans votre déploiement initial. Quand vous redémarrez une machine virtuelle avec SAP HANA, la machine virtuelle redémarre avec ses adresses IP antérieures. 


### <a name="use-saprouter-for-sap-remote-support"></a>Utiliser SAProuter pour la prise en charge à distance de SAP
Si vous avez une connexion de site à site entre vos emplacements locaux et le Azure et que vous exécutez des composants SAP, vous êtes probablement déjà en cours d’exécution SAProuter. Dans ce cas, suivez ces étapes pour la prise en charge à distance :

- Mettre à jour l’adresse IP privée et statique de la machine virtuelle qui héberge SAP HANA dans la configuration de SAProuter.
- Configurer le groupe de sécurité réseau du sous-réseau qui héberge la machine virtuelle HANA pour autoriser le trafic via le port TCP/IP 3299.

Si vous vous connectez à Azure via internet et que vous n’avez pas un routeur SAP pour la machine virtuelle avec SAP HANA, installez le composant. Installer SAProuter sur une machine virtuelle distincte dans le sous-réseau de gestion. 

L’image suivante représente un schéma approximatif pour le déploiement de SAP HANA sans une connexion de site à site et avec SAProuter :

![Schéma de déploiement approximatif pour SAP HANA sans une connexion de site à site et avec SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Veillez à installer SAProuter sur une machine virtuelle distincte et non dans votre VM JumpBox. La machine virtuelle distincte doit avoir une adresse IP statique. Pour connecter votre SAProuter au SAProuter hébergé par SAP, contactez SAP pour une adresse IP. Le SAProuter qui est hébergé par SAP est l’équivalent de l’instance de SAProuter que vous installez sur votre machine virtuelle. Utilisez l’adresse IP de SAP pour configurer votre instance de SAProuter. Dans les paramètres de configuration, le seul port nécessaire est le port TCP 3299.

Pour plus d’informations sur la façon de configurer et maintenir des connexions de support à distance via SAProuter, consultez [documentation SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Haute disponibilité avec SAP HANA sur machines virtuelles Azure natives
Si vous exécutez SUSE Linux Enterprise Server for SAP Applications 12 SP1 ou version ultérieure, vous pouvez établir un cluster Pacemaker avec des appareils STONITH. Les appareils permet de définir une configuration SAP HANA qui utilise la réplication synchrone avec la réplication de système HANA et basculement automatique. Pour plus d’informations sur la procédure d’installation, consultez [guide de haute disponibilité de SAP HANA pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
