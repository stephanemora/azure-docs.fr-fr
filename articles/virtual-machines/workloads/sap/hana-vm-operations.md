---
title: Configurations et opérations de l’infrastructure SAP HANA sur Azure | Microsoft Docs
description: Guide des opérations pour les systèmes SAP HANA qui sont déployés sur des machines virtuelles Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: db2d7fbe395a6d7e332d79183a331b45f7767f51
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434056"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurations et opérations de l’infrastructure SAP HANA sur Azure
Ce document fournit des instructions pour la configuration des infrastructures Azure et le fonctionnement des systèmes SAP HANA qui sont déployés sur des machines virtuelles Azure natives. Le document inclut également des informations de configuration pour le scale-out de SAP HANA sur la référence SKU de machine virtuelle M128s. Ce document n’a pas pour but de remplacer la documentation SAP standard, qui propose le contenu suivant :

- [SAP Administration Guide (Guide d’administration de SAP)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP Installation Guide (Guide d’installation de SAP)](https://service.sap.com/instguides)
- [Notes SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide, vous devez disposer des connaissances de base quant aux différents composants Azure suivants :

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Mise en réseau et réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Stockage Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Pour en savoir plus sur SAP NetWeaver et d’autres composants SAP sur Azure, consultez la section [SAP sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) de la [documentation Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considérations de base relatives à la configuration
Les sections suivantes décrivent les considérations de base relatives à la configuration pour le déploiement de systèmes SAP HANA sur des machines virtuelles Azure.

### <a name="connect-into-azure-virtual-machines"></a>Se connecter à des machines virtuelles Azure
Comme décrit dans le [guide de planification des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), il existe deux méthodes de base pour se connecter à des machines virtuelles Azure :

- Connexion par le biais de points de terminaison publics et Internet sur une machine virtuelle Jump ou la machine virtuelle qui exécute SAP HANA.
- Connexion par le biais d’un [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou d’Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Pour les scénarios de production, vous devez avoir une connectivité de site à site par le biais d’un VPN ou d’ExpressRoute. Ce type de connexion est également nécessaire pour les scénarios hors production qui alimentent des scénarios de production où des logiciels SAP sont utilisés. L’image suivante représente un exemple de connectivité intersite :

![Connectivité intersite](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Choisir des types de machines virtuelles Azure
Les types de machines virtuelles Azure qui peuvent être utilisés pour les scénarios de production sont répertoriés dans la [documentation SAP pour IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pour les scénarios hors production, une plus large gamme de types de machines virtuelles Azure natives est disponible.

>[!NOTE]
> Pour les scénarios hors production, utilisez les types de machines virtuelles qui sont répertoriés dans la [note SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Pour les scénarios de production, utilisez les machines virtuelles Azure certifiées SAP HANA qui sont répertoriées dans la [liste des plateformes IaaS certifiées](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) publiée par SAP.

Déployez les machines virtuelles sur Azure en utilisant :

- le portail Azure ;
- les applets de commande Azure PowerShell ;
- l’interface de ligne de commande Azure.

Vous pouvez également déployer une plateforme SAP HANA installée et complète sur les services de machine virtuelle Azure par le biais de la [plateforme cloud SAP](https://cal.sap.com/). Le processus d’installation est décrit dans [Déploiement de SAP S/4HANA ou BW/4HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) ou avec le processus d’automatisation publié [ici](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Choisir le type de stockage Azure
Azure fournit deux types de stockage adaptés aux machines virtuelles Azure exécutant SAP HANA :

- [Stockage Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Stockage Premium Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure offre deux méthodes de déploiement avec les disques durs virtuels sur le stockage Azure Standard et Premium. Si le scénario global le permet, tirez parti des déploiements [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Pour obtenir une liste des types de stockage et les contrats SLA associés pour les débits d’IOPS et de stockage, passez en revue la [documentation Azure sur les disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Configuration du stockage des machines virtuelles Azure

Tant que vous achetiez des appliances SAP HANA en local, vous n’aviez pas besoin de vous occuper des sous-systèmes d’E/S et de leurs fonctionnalités, car le fournisseur d’appliance devait s’assurer que les exigences de stockage minimales étaient remplies pour SAP HANA. Cependant, lorsque vous générez l’infrastructure Azure vous-même, vous devez connaître certaines de ces exigences et également comprendre les exigences de configuration suggérées dans les sections suivantes. Cela vaut également si vous configurez des machines virtuelles pour y exécuter SAP HANA. Certaines des exigences requises le sont pour :

- Activer le volume de lecture/écriture sur **/hana/log** à un débit de 250 Mo/s au minimum pour des tailles d’E/S de 1 Mo
- Activer l’activité de lecture à un débit de 400 Mo/s au minimum pour **/hana/data** pour des tailles d’E/S de 16 Mo et 64 Mo
- Activer l’activité d’écriture à un débit de 250 Mo/s au minimum pour **/hana/data** avec des tailles d’E/S de 16 Mo et 64 Mo

Étant donné qu’une latence de stockage faible est critique pour les systèmes SGBD, même pour ceux comme SAP HANA, conservez les données en mémoire. Le point critique dans le stockage concerne généralement les écritures du journal de transaction sur les systèmes SGBD. D’autres opérations peuvent également être critiques, par exemple, l’écriture des points de sauvegarde ou le chargement des données en mémoire après une récupération sur incident. C’est pourquoi vous devez obligatoirement utiliser des disques Azure Premium pour les volumes **/hana/data** et **/hana/log**. Pour respecter les exigences de débit minimal requises par SAP pour les volumes **/hana/log** et **/hana/data**, créez un volume RAID 0 avec MDADM ou LVM sur plusieurs disques de stockage Azure Premium et utilisez les volumes RAID comme volumes **/hana/data** et **/hana/log**. Utilisez les tailles de bande recommandées suivantes pour le disque RAID 0 :

- 64 Ko ou 128 Ko pour  **/hana/data**
- 32 Ko pour **/hana/log**

> [!NOTE]
> Vous n’avez pas besoin de configurer un niveau de redondance avec des volumes RAID, car les stockages Azure Premium et Standard conservent trois images d’un disque dur virtuel. L’utilisation d’un volume RAID a pour but principal de configurer des volumes qui fournissent un débit d’E/S suffisant.

L’augmentation du nombre de disques durs virtuels Azure sous un volume RAID a pour effet d’augmenter les débits d’IOPS et de stockage. Ainsi, si vous placez un volume RAID 0 sur trois disques P30 de stockage Azure Premium, vous obtenez en principe trois fois plus de débit d’IOPS et trois fois plus de débit de stockage qu’avec un seul disque P30 de stockage Azure Premium.

Les suggestions de mise en cache indiquées plus bas supposent que SAP HANA présente les caractéristiques d’E/S suivantes :

- Il n’y a pratiquement aucune charge de travail de lecture sur les fichiers de données HANA. Les exceptions sont les E/S volumineuses après le redémarrage de l’instance HANA ou quand les données sont chargées dans HANA. Les sauvegardes de base de données HANA peuvent constituer un autre cas d’E/S en lecture plus volumineuses sur des fichiers de données. Ainsi, la mise en cache en lecture n’est pas pertinente car, dans la plupart des cas, tous les volumes de fichiers de données doivent être lus complètement.
- Les points de sauvegarde HANA et la récupération sur incident HANA donnent lieu à des écritures en rafales dans les fichiers de données. L’écriture de point de sauvegarde est asynchrone et n’accapare pas de transactions utilisateur. L’écriture de données au cours d’une récupération sur incident est critique pour les performances et la réactivité du système. Toutefois, une récupération sur incident doit intervenir dans des situations plutôt exceptionnelles.
- Il n’y a pratiquement pas de lectures à partir des fichiers de restauration par progression HANA. Les exceptions sont les grandes E/S pendant les sauvegardes de fichier journal, une récupération sur incident ou le redémarrage d’une instance HANA.  
- La charge principale par rapport au fichier journal de restauration par progression SAP HANA est constituée d’écritures. Selon la nature de la charge de travail, la tailles des E/S peut aller de 4 Ko à 1 Mo, voire plus. La latence des écritures par rapport au journal de restauration par progression SAP HANA est critique pour les performances.
- Toutes les écritures doivent être rendues persistantes sur le disque de manière fiable.

Pour observer ces modèles d’E/S définis par SAP HANA, vous devez paramétrer la mise en cache pour les différents volumes à l’aide de Stockage Premium Azure comme suit :

- **/hana/data** : aucune mise en cache
- **/hana/log** : aucune mise en cache ; exception pour la série M (voir plus loin dans ce document)
- **/hana/shared** : mise en cache en lecture


Gardez également à l’esprit le débit d’E/S de machine virtuelle global lors du dimensionnement ou du choix d’une machine virtuelle. Le débit de stockage de machine virtuelle global est décrit dans l’article [Tailles de machine virtuelle à mémoire optimisée](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Configuration du stockage Azure avec prise en compte des coûts
Le tableau suivant illustre une configuration de types de machines virtuelles que les clients utilisent couramment pour héberger SAP HANA sur des machines virtuelles Azure. Il est possible que certains types de machines virtuelles ne remplissent pas tous les critères minimum pour SAP HANA. Toutefois, ces machines virtuelles n’ont pas montré, jusqu’à présent, de problèmes de fonctionnement dans les scénarios hors production. 

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data et /hana/log<br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 Go | 768 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 Go | 384 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 Gio | 768 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 Gio | 1 200 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 Gio | 2 000 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Les disques recommandés pour les types de machines virtuelles les plus petits avec 3 x P20 dépassent la taille des volumes en ce qui concerne les recommandations d’espace selon le [livre blanc de stockage TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Toutefois, le choix affiché dans le tableau a été effectué pour fournir un débit de disque suffisant pour SAP HANA. La taille indiquée pour le volume **/hana/backup** a été fixée pour permettre la conservation de sauvegardes représentant deux fois le volume de mémoire, mais vous pouvez ajuster ce paramètre selon vos besoins.   
Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à effectuer. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume en ajoutant plus de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure. 

> [!NOTE]
> Les configurations ci-dessus ne bénéficient PAS du [contrat SLA de machine virtuelle Azure à instance unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), car elles utilisent à la fois le stockage Azure Premium et le stockage Azure Standard. Toutefois, ce choix a été fait dans le but d’optimiser les coûts.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Configuration du stockage Azure pour bénéficier du contrat SLA de machine virtuelle unique
Pour bénéficier du [contrat SLA de machine virtuelle Azure à instance unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), vous devez utiliser exclusivement des disques durs virtuels de stockage Azure Premium.

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data et /hana/log<br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 Go | 768 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 Go | 384 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 Gio | 768 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 Gio | 1 200 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 Gio | 2 000 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Les disques recommandés pour les types de machines virtuelles les plus petits avec 3 x P20 dépassent la taille des volumes en ce qui concerne les recommandations d’espace selon le [livre blanc de stockage TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Toutefois, le choix affiché dans le tableau a été effectué pour fournir un débit de disque suffisant pour SAP HANA. La taille indiquée pour le volume **/hana/backup** a été fixée pour permettre la conservation de sauvegardes représentant deux fois le volume de mémoire, mais vous pouvez ajuster ce paramètre selon vos besoins.  
Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à effectuer. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume en ajoutant plus de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure. 




#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solution de stockage avec l’Accélérateur des écritures Azure pour les machines virtuelles Azure de la série M
L’Accélérateur des écritures Azure est une fonctionnalité qui est fournie uniquement pour les machines virtuelles de la série M. Comme son nom l’indique, cette fonctionnalité vise à améliorer la latence d’E/S des opérations d’écriture sur le stockage Azure Premium. Pour SAP HANA, l’Accélérateur des écritures doit être utilisé exclusivement sur le volume **/hana/log**. Les configurations présentées plus haut doivent donc être modifiées en conséquence. Le principal changement concerne la répartition entre les volumes **/hana/data** et **/hana/log** pour utiliser l’Accélérateur des écritures Azure uniquement sur le volume **/hana/log**. 

> [!IMPORTANT]
> La certification SAP HANA des machines virtuelles Azure de la série M est valable exclusivement avec l’Accélérateur des écritures Azure sur le volume **/hana/log**. Par conséquent, dans les scénarios de production, les déploiements SAP HANA sur des machines virtuelles Azure de la série M doivent être configurés avec l’Accélérateur des écritures Azure sur le volume **/hana/log**.  

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Les configurations recommandées sont les suivantes :

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à effectuer. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume en ajoutant plus de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure.

L’Accélérateur des écritures Azure fonctionne uniquement en association avec [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Cela signifie que les disques de stockage Azure Premium constituant le volume **/hana/log** doivent être déployés en tant que disques managés.

L’Accélérateur des écritures Azure prend en charge un nombre limité de disques durs virtuels de stockage Azure Premium par machine virtuelle. Les limites actuelles sont :

- 16 disques durs virtuels pour une machine virtuelle M128xx
- 8 disques durs virtuels pour une machine virtuelle M64xx
- 4 disques durs virtuels pour une machine virtuelle M32xx

Vous trouverez des instructions plus détaillées sur l’activation de l’Accélérateur des écritures Azure dans l’article [Accélérateur des écritures](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Vous y trouverez aussi les détails et les restrictions relatifs à l’utilisation de cet accélérateur.

> [!NOTE]
> Les recommandations relatives à la configuration du disque qui sont indiquées ciblent une configuration minimale pour le protocole SAP équivalente à celles de leurs fournisseurs d’infrastructure. En pratique, dans les scénarios de déploiement et de charge de travail des clients, il se peut que ces recommandations ne fournissent pas suffisamment de fonctionnalités. Dans certains cas, il se peut que le client demande un rechargement plus rapide des données après un redémarrage HANA ou que les configurations de sauvegarde demandent une bande passante plus élevée pour le stockage. Dans d’autres cas, pour **/hana/log**, 5 000 IOPS étaient insuffisants pour la charge de travail spécifique. Par conséquent, prenez ces recommandations en tant que point de départ et adaptez-les en fonction des exigences de la charge de travail.
>  

### <a name="set-up-azure-virtual-networks"></a>Configurer les réseaux virtuels Azure
Quand vous disposez d’une connectivité de site à site dans Azure via un VPN ou ExpressRoute, vous avez au minimum un réseau virtuel Azure connecté par le biais d’une passerelle virtuelle au circuit ExpressRoute ou VPN. Dans des déploiements simples, la passerelle virtuelle peut être déployée dans un sous-réseau du réseau virtuel (VNet) Azure qui héberge également les instances SAP HANA. Pour installer SAP HANA, vous devez créer deux sous-réseaux supplémentaires dans le réseau virtuel Azure. Un sous-réseau héberge les machines virtuelles pour exécuter les instances de SAP HANA. L’autre sous-réseau exécute des machines virtuelles Jumpbox ou de gestion pour héberger SAP HANA Studio, d’autres logiciels de gestion ou votre logiciel d’application.

Quand vous installez les machines virtuelles pour exécuter SAP HANA, celles-ci nécessitent :

- deux cartes réseau virtuelles installées, une pour se connecter au sous-réseau de gestion et une autre qui sert à se connecter à l’instance de SAP HANA dans la machine virtuelle Azure à partir du réseau local ou d’autres réseaux ;
- des adresses IP statiques privées déployées pour les deux cartes réseau virtuelles.

> [!NOTE]
> Vous devez attribuer des adresses IP statiques aux cartes réseau virtuelles individuelles à l’aide des outils Azure. Vous ne devez pas attribuer d’adresses IP statiques au sein du système d’exploitation invité à une carte réseau virtuelle. Certains services Azure, comme le service de Sauvegarde Azure, s’appuient sur le fait que la carte réseau virtuelle principale est définie sur la DHCP et non sur des adresses IP statiques. Consultez également le document [Dépannage de la sauvegarde de machine virtuelle Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Si vous avez besoin d’attribuer plusieurs adresses IP statiques à une machine virtuelle, vous devez attribuer plusieurs cartes réseau virtuelles à une machine virtuelle.
>
>

Toutefois, pour les déploiements viables sur le long terme, vous devez créer une architecture réseau de centre de données virtuel dans Azure. Cette architecture recommande la séparation de la passerelle de réseau virtuel Azure qui établit une connexion en local dans un réseau virtuel Azure distinct. Celui-ci doit héberger tout le trafic sortant vers les destinations en local ou Internet. Cette approche vous permet de déployer un logiciel d’audit et de journalisation du trafic qui entre par ce hub de réseau virtuel distinct dans le centre de données virtuel dans Azure. Ainsi, un seul réseau virtuel héberge tous les logiciels et les configurations liés au trafic entrant et sortant vers votre déploiement Azure.

Les articles [Centre de données virtuel Azure : une perspective réseau](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) et [Le centre de données virtuel Azure et le plan de contrôle entreprise](https://docs.microsoft.com/azure/architecture/vdc/) donnent plus d’informations sur l’approche du centre de données virtuel et la conception liée du réseau virtuel Azure.


>[!NOTE]
>Le trafic qui transite entre un réseau virtuel hub et un réseau virtuel spoke à l’aide du [peering de réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) fait l’objet de [coûts](https://azure.microsoft.com/pricing/details/virtual-network/) supplémentaires. En fonction de ces coûts, vous devrez peut-être envisager des compromis entre l’exécution d’une conception de réseau hub and spoke stricte et la mise en place de plusieurs [passerelles Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que vous vous connectez aux « spokes » afin de contourner le peering de réseau virtuel. Toutefois, les passerelles Azure ExpressRoute entraînent des [coûts](https://azure.microsoft.com/pricing/details/vpn-gateway/) supplémentaires également. Les logiciels tiers que vous utilisez pour la journalisation, l’audit et la surveillance du trafic réseau peuvent aussi engendrer des coûts supplémentaires. Selon les coûts induits par l’échange de données via le peering de réseau virtuel d’un côté et les coûts engendrés par les passerelles Azure ExpressRoute et les licences logicielles supplémentaires, vous pouvez opter pour la micro-segmentation au sein d’un réseau virtuel en utilisant les sous-réseaux en tant qu’unité d’isolation à la place des réseaux virtuels.


Pour obtenir une vue d’ensemble des différentes méthodes d’attribution des adresses IP, consultez [Types d’adresses IP et méthodes d’allocation dans Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pour les machines virtuelles qui exécutent SAP HANA, vous devez utiliser les adresses IP statiques attribuées. En effet, certains attributs de configuration pour HANA référencent des adresses IP.

Les [groupes de sécurité réseau Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) permettent de diriger le trafic qui est acheminé vers l’instance de SAP HANA ou la machine virtuelle Jumpbox. Les groupes de sécurité réseau ainsi que les [groupes de sécurité d’application](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) sont associés au sous-réseau SAP HANA et au sous-réseau de gestion.

L’image suivante représente une vue d’ensemble d’un schéma de déploiement approximatif pour SAP HANA selon une architecture de réseau virtuel hub and spoke :

![Schéma de déploiement approximatif pour SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Pour déployer SAP HANA dans Azure sans une connexion de site à site, vous voulez protéger l’instance SAP HANA de l’internet public et le masquer derrière un proxy. Dans ce scénario de base, le déploiement s’appuie sur les services DNS intégrés à Azure pour résoudre les noms d’hôte. Dans un déploiement plus complexe où des adresses IP publiques sont utilisées, les services DNS intégrés à Azure sont particulièrement importants. Utilisez des groupes de sécurité réseau et [appliances virtuelles réseau Azure](https://azure.microsoft.com/solutions/network-appliances/) pour contrôler et surveiller le routage à partir d’internet dans votre architecture de réseau virtuel Azure dans Azure. L’image suivante représente un schéma approximatif pour le déploiement de SAP HANA sans connexion de site à site dans une architecture de réseau virtuel  hub and spoke :
  
![Schéma de déploiement approximatif pour SAP HANA sans une connexion de site à site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Vous trouverez une autre description sur l’utilisation des appliances virtuelles réseau Azure pour contrôler et surveiller l’accès à partir d’Internet sans l’architecture de réseau virtuel hub et spoke dans l’article [Déployer des appliances virtuelles réseau hautement disponibles](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configuration de l’infrastructure Azure pour le scale-out de SAP HANA
Microsoft dispose d’une référence SKU de machine virtuelle de série M certifiée pour une configuration de scale-out de SAP HANA. Le type de machine virtuelle M128s a obtenu une certification pour un scale-out allant jusqu'à 16 nœuds. Pour les changements dans les certifications de scale-out pour SAP HANA sur des machines virtuelles Azure, consultez la [liste des plateformes IaaS certifiées](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Les versions de système d’exploitation minimales pour le déploiement de configurations de scale-out dans les machines virtuelles Azure sont :

- SUSE Linux 12 SP3
- Red Hat Linux 7.4

Sur les 16 nœuds de certification de scale-out

- Le nœud principal est au nombre de 1
- Les nœuds de travail sont au nombre de 15 maximum

>[!NOTE]
>Dans les déploiements de scale-out de machine virtuelle Azure, il est impossible d’utiliser un nœud de secours
>

Il est impossible de configurer un nœud de secours pour deux raisons :

- À ce stade, Azure ne dispose d’aucun service NFS natif. Par conséquent les partages NFS doivent être configurés à l’aide d’une fonctionnalité de fournisseur tiers.
- Aucune de ces configurations de NFS tierces n’est en mesure de répondre aux critères de latence de stockage pour SAP HANA lorsque leurs solutions sont déployées sur Azure.

Par conséquent, les volumes **/hana/data** et **/hana/log** ne peuvent pas être partagés. L’impossibilité de partager ces volumes de nœuds uniques empêche l’utilisation d’un nœud de secours SAP HANA dans une configuration scale-out.

Par conséquent, la conception de base pour un nœud unique dans une configuration scale-out va ressembler à ceci :

![Scale-out de base pour un nœud unique](media/hana-vm-operations/scale-out-basics.PNG)

La configuration de base d’un nœud de machine virtuelle pour le scale-out de SAP HANA ressemble à ceci :

- Pour **/hana/shared**, vous allez créer un cluster NFS hautement disponible basé sur SUSE Linux 12 SP3. Ce cluster doit héberger le ou les partage(s) NFS **/hana/shared** de votre configuration scale-out et SAP NetWeaver ou les Services centraux BW/4HANA. La documentation pour générer une telle configuration est disponible dans l’article [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Tous les autres volumes de disque NE sont **PAS** partagés entre les différents nœuds et NE sont **PAS** basés sur NFS. Les configurations d’installation et les étapes pour les installations de scale-out HANA avec **/hana/data** et **/hana/log** non partagés sont fournies plus bas dans ce document.

>[!NOTE]
>Le cluster NFS à haute disponibilité tel qu’illustré jusqu'à présent dans les graphiques est pris en charge avec SUSE Linux uniquement. Une solution NFS hautement disponible basée sur Red Hat sera disponible ultérieurement.

Le dimensionnement des volumes pour les nœuds est identique au scale-up, sauf pour **/hana/shared**. Pour la référence SKU de machine virtuelle M128s, voici les tailles et les types suggérés :

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data | /hana/log | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à effectuer. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume en ajoutant plus de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure. Applique également l’Accélérateur des écritures Azure aux disques qui forment le volume **/hana/log**.
 
Dans le document [SAP HANA TDI Storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Exigences de stockage SAP HANA TDI) se trouve une formule qui définit la taille du volume **/hana/shared** pour le scale-out comme la taille de mémoire d’un nœud de travail unique par 4 nœuds de travail.

En supposant que vous prenez la machine virtuelle Azure M128s certifiée scale-out SAP HANA avec environ 2 To de mémoire, les recommandations de SAP peuvent être résumées ainsi :

- Un nœud principal et jusqu’à quatre nœuds Worker, le volume **/hana/shared** doit avoir une taille de 2 To. 
- Un nœud principal et jusqu'à cinq à huit nœuds de travail, le volume **/hana/shared** doit être de 4 To. 
- Un nœud principal et de neuf à 12 nœuds de travail, le volume **/hana/shared** doit être de 6 To. 
- Un nœud principal et de 12 à 15 nœuds de travail, le volume **/hana/shared** doit être de 8 To.

L’autre conception importante illustrée dans le graphique de la configuration de nœud unique pour un scale-out de machine virtuelle SAP HANA est le réseau virtuel, plus précisément la configuration du sous-réseau. SAP recommande fortement de séparer le trafic client/application des communications entre les nœuds HANA. Comme indiqué dans les graphiques, cet objectif est réalisé en attachant deux cartes réseau virtuelles différentes à la machine virtuelle. Chacune des cartes réseau virtuelles est dans un sous-réseau différent et dispose d’un adresse IP distincte. Vous contrôlez le trafic avec les règles de routage à l’aide de groupes de sécurité réseau ou d’itinéraires définis par l’utilisateur.

En particulier dans Azure, il n’existe aucun moyen ni de méthode pour appliquer la qualité de service et les quotas sur des cartes réseau virtuelles spécifiques. Par conséquent, la séparation de la communication client/application et intra-nœud n’ouvre pas d’opportunité pour prioriser un flux plutôt que l’autre. Au lieu de cela, la séparation reste une mesure de sécurité pour la protection des communications intra-nœud dans les configurations de scale-out.  

>[!IMPORTANT]
>SAP recommande fortement de séparer le trafic réseau vers le côté client/application et le trafic intra-nœud, comme décrit dans ce document. Il est par conséquent fortement recommandé de mettre une architecture en place, comme indiqué dans les derniers graphiques.
>

Du point de vue du réseau, l’architecture réseau minimale requise serait la suivante :

![Scale-out de base pour un nœud unique](media/hana-vm-operations/scale-out-networking-overview.PNG)

Les limites prises en charge jusqu'à présent sont de 15 nœuds de travail en plus du nœud principal.

Du point de vue du stockage, l’architecture de stockage ressemblerait à ceci :


![Scale-out de base pour un nœud unique](media/hana-vm-operations/scale-out-storage-overview.PNG)

Le volume **/hana/shared** se trouve sur la configuration de partage NFS hautement disponible, tandis que tous les autres disques sont montés « en local » sur les machines virtuelles individuelles. 

### <a name="highly-available-nfs-share"></a>Partage NFS haute disponibilité
À l’heure actuelle, le cluster NFS hautement disponible fonctionne sous SUSE Linux uniquement. Le document [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) décrit la procédure de configuration. Si vous ne partagez pas le cluster NFS avec d’autres configurations HANA en dehors du réseau virtuel Azure qui exécute les instances SAP HANA, installez-le dans le même réseau virtuel. Installez-le dans son propre sous-réseau et assurez-vous que tout le trafic arbitraire ne peut accéder au sous-réseau. Au lieu de cela, vous souhaiterez limiter le trafic vers ce sous-réseau aux adresses IP de la machine virtuelle qui exécute le trafic vers le volume **/hana/shared**.

Concernant la carte réseau virtuelle d’une machine virtuelle de scale-out HANA devant acheminer le trafic **/hana/shared**, voici les recommandations :

- Étant donné que le trafic vers **/hana/shared** est modéré, routez-le via la carte réseau virtuelle assignée au réseau client dans la configuration minimale
- Enfin, pour le trafic vers **/hana/shared**, déployez un troisième sous-réseau dans le réseau virtuel où vous déployez la configuration de scale-out SAP HANA et assignez une troisième carte réseau virtuelle hébergée dans ce sous-réseau. Utilisez la troisième carte réseau virtuelle et l’adresse IP associée pour le trafic vers le partage NFS. Vous pouvez ensuite appliquer des accès et des règles de routage spécifiques.

>[!IMPORTANT]
>Le trafic réseau entre les machines virtuelles qui déploient SAP HANA en scale-out et un NFS à haute disponibilité ne peut en aucun cas être routé via une [NVA](https://azure.microsoft.com/solutions/network-appliances/) ou des appliances virtuelles similaires, tandis que les groupes de sécurité réseau Azure ne contiennent pas ces appareils. Vérifiez vos règles de routage pour vous assurer que les appliances virtuelles réseau ou appliances virtuelles similaires sont contournées lors de l’accès au partage NFS à haute disponibilité depuis les machines virtuelles SAP HANA en cours d’exécution.
> 

Si vous souhaitez partager le cluster NFS à haute disponibilité entre les configurations de SAP HANA, déplacez toutes ces configurations HANA dans le même réseau virtuel. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Installation du scale-out SAP HANA dans Azure
Lors de l’installation d’une configuration de scale-out SAP, vous devez procéder :

- Au déploiement ou à l’adaptation de nouvelles infrastructures de réseau virtuel Azure
- Au déploiement de nouvelles machines virtuelles en utilisant les volumes Stockage Premium gérés par Azure
- Au déploiement d’un nouveau cluster NFS à haute disponibilité, ou à l’adaptation d’un cluster existant
- À l’adaptation d’un routage réseau pour vous assurer que, par exemple, les communications intra-nœud entre les machines virtuelles ne sont pas acheminées via un [NVA](https://azure.microsoft.com/solutions/network-appliances/). Il en est de même pour le trafic entre les machines virtuelles et le cluster NFS à haute disponibilité.
- À l’installation du nœud principal SAP HANA.
- À l’adaptation des paramètres de configuration du nœud principal SAP HANA
- Continuez avec l’installation des nœuds de travail SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Installation de SAP HANA dans une configuration scale-out
Votre infrastructure de machine virtuelle Azure est déployée, et toutes les autres tâches de préparation sont terminées. Vous devez installer les configurations de scale-out SAP HANA en suivant ces étapes :

- Installer le nœud principal SAP HANA selon la documentation de SAP
- **Après l’installation, vous devez modifier le fichier global.ini et y ajouter le paramètre « basepath_shared = no »**. Ce paramètre permet à SAP HANA d’exécuter le scale-out sans les volumes « shared » **/hana/data** et **/hana/log** entre les nœuds. Pour plus de détails à ce sujet, voir le document [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Après avoir modifié le paramètre pour global.ini, redémarrer l’instance SAP HANA
- Ajoutez des nœuds de travail supplémentaires. Voir aussi <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Spécifiez le réseau interne pour la communication inter-nœuds de SAP HANA lors de l’installation ou ultérieurement, avec par exemple l’outil hdblcm local. Pour plus de détails à ce sujet, voir également le document [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Après cette routine d’installation, la configuration de scale-out que vous avez installée utilisera des disques non partagés pour exécuter **/hana/data** et **/hana/log**, tandis que le volume **/hana/shared** sera placé sur le partage NFS à haute disponibilité.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 pour les machines virtuelles Azure

En plus des certifications SAP HANA sur les machines virtuelles Azure de série M, SAP HANA Dynamic Tiering 2.0 est également pris en charge sur Microsoft Azure (consultez les liens donnés plus loin vers la documentation de SAP HANA Dynamic Tiering). S’il n’existe aucune différence dans l’installation du produit ou de son fonctionnement, par exemple via SAP HANA Cockpit à l’intérieur d’une machine virtuelle Azure, quelques éléments importants sont obligatoires pour le support officiel sur Azure. Ces points clés sont décrits ci-dessous. Tout au long de l’article, l’abréviation « DT 2.0 » sera utilisée au lieu du nom complet « Dynamic Tiering 2.0 ».

SAP HANA Dynamic Tiering 2.0 n’est pas pris en charge par SAP BW ou S4HANA. Les cas d’utilisation principaux sont actuellement des applications HANA natives.


### <a name="overview"></a>Vue d’ensemble

L’image ci-dessous donne une vue d’ensemble de la prise en charge de DT 2.0 sur Microsoft Azure. Il existe un ensemble de conditions obligatoires, qui doivent être suivies pour être conforme à la certification officielle :

- DT 2.0 doit être installé sur une machine virtuelle Azure dédiée. Il peut ne pas s’exécuter sur la même machine virtuelle que celle où SAP HANA s’exécute
- SAP HANA et les machines virtuelles DT 2.0 doivent être déployées dans le même réseau virtuel Azure
- Les machines virtuelles SAP HANA et DT 2.0 doivent être déployées avec la mise en réseau accélérée Azure activée
- Le type de stockage pour les machines virtuelles DT 2.0 doit être Stockage Premium Azure
- Plusieurs disques Azure doivent être attachés à la machine virtuelle DT 2.0
- Il est nécessaire de créer un volume Raid / agrégé par bandes (via lvm ou mdadm) en utilisant l’agrégation entre les disques Azure

La section suivante contient plus de détails à ce sujet.

![Vue d’ensemble de l’architecture de SAP HANA DT 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Machine virtuelle Azure dédiée pour SAP HANA DT 2.0

Sur Azure IaaS, DT 2.0 est pris en charge seulement sur une machine virtuelle dédiée. Il est interdit d’exécuter DT 2.0 sur la même machine virtuelle Azure que celle où l’instance HANA s’exécute. Initialement, deux types de machine virtuelle peuvent être utilisés pour exécuter SAP HANA DT 2.0 :

M64-32ms, E32sv3 

Consultez la description des types de machine virtuelle [ici](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Étant donné le principe fondamental de DT 2.0, qui est de décharger les données « chaudes » afin de réduire les coûts, il est judicieux d’utiliser des tailles de machine virtuelle correspondantes. Il n’existe cependant pas de règle stricte concernant les combinaisons possibles. Cela dépend de la charge de travail spécifique du client.

Voici les configurations recommandées :

| Type de machine virtuelle SAP HANA | Type de machine virtuelle DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Toutes les combinaisons de machines virtuelles de la série M certifiées pour SAP HANA avec les machines virtuelles DT 2.0 prises en charge (M64-32ms, E32sv3) sont possibles.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Mise en réseau Azure et SAP HANA DT 2.0

L’installation de DT 2.0 sur une machine virtuelle dédiée nécessite un débit minimal de 10 Gbits entre la machine virtuelle DT 2.0 et la machine virtuelle SAP HANA. Par conséquent, il est obligatoire de placer toutes les machines virtuelles dans le même réseau virtuel Azure et d’activer la mise en réseau accélérée Azure.

Vous pouvez consulter des informations supplémentaires sur la mise en réseau accélérée Azure [ici](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Stockage des machines virtuelles pour SAP HANA DT 2.0

Conformément au guide des bonnes pratiques pour DT 2.0, le débit d’E/S des disques doit être au minimum de 50 Mo/s par cœur physique. Si vous consultez les spécifications pour les deux types de machine virtuelle Azure qui sont pris en charge pour DT 2.0, vous voyez la limite maximale du débit d’E/S pour la machine virtuelle :

- E32sv3    :   768 Mo/s (sans mise en cache), ce qui signifie un ratio de 48 Mo/s par cœur physique
- M64-32ms  :  1 000 Mo/s (sans mise en cache), ce qui signifie un ratio de 62,5 Mo/s par cœur physique

Il est obligatoire d’attacher plusieurs disques Azure à la machine virtuelle DT 2.0 et de créer un RAID logiciel (agrégation) sur le niveau du système d’exploitation pour atteindre la limite maximale de débit des disques par machine virtuelle. Un seul disque Azure ne peut pas fournir le débit nécessaire pour atteindre la limite maximale de la machine virtuelle à cet égard. Un stockage Premium Azure est obligatoire pour exécuter DT 2.0. 

- Vous pouvez trouver plus d’informations sur les types de disque Azure disponibles [ici](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)
- Vous pouvez trouver des informations sur la création d’un RAID logiciel via mdadm [ici](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Vous pouvez trouver des informations sur la configuration de LVM pour créer un volume agrégé par bandes de façon à obtenir un débit maximal [ici](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

Selon les exigences de taille, il existe différentes options pour atteindre le débit maximal d’une machine virtuelle. Voici les configurations des disques de volume de données possibles permettant à chaque type de machine virtuelle DT 2.0 d’atteindre la limite supérieure de débit. La machine virtuelle E32sv3 doit être considérée comme un niveau d’entrée pour les charges de travail plus petites. Dans le cas où elle ne s’avère pas suffisamment rapide, il peut être nécessaire de redimensionner la machine virtuelle en M64-32ms.
Comme la machine virtuelle M64-32ms a beaucoup de mémoire, la charge d’E/S peut ne pas atteindre la limite, en particulier pour les charges de travail qui font des lectures de façon intensive. Ainsi, il est possible que moins de disques dans l’ensemble agrégé par bandes soient suffisants, en fonction de la charge de travail spécifique du client. Cependant, par prudence, les configurations de disques ci-dessous ont été choisies pour garantir le débit maximal :


| Référence de la machine virtuelle | Configuration de disque 1 | Configuration de disque 2 | Configuration de disque 3 | Configuration de disque 4 | Configuration de disque 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 To | 4 x P40 -> 8 To | 5 x P30 -> 5 To | 7 x P20 -> 3,5 To | 8 x P15 -> 2 To | 
| E32sv3 | 3 x P50 -> 12 To | 3 x P40 -> 6 To | 4 x P30 -> 4 To | 5 x P20 -> 2,5 To | 6 x P15 -> 1,5 To | 


En particulier au cas où la charge de travail implique des lectures intensives, vous pouvez accélérer les performances des E/S en activant le cache d’hôte Azure « en lecture seule », comme c’est recommandé pour les volumes de données des logiciels de base de données. Pour le journal des transactions, le cache disque d’hôte Azure doit par contre être défini sur « aucun ». 

Concernant la taille du volume des journaux, un point de départ recommandé est une heuristique de 15 % de la taille des données. Vous pouvez créer le volume des journaux en utilisant des types de disque Azure différents, en fonction des exigences quant au débit et aux coûts. Pour le volume des journaux, un débit élevé est également préférable et, dans le cas de M64-32ms, il est fortement recommandé d’activer l’accélérateur d’écriture (ce qui est obligatoire pour SAP HANA). Ceci permet une latence optimale des écritures sur disque pour le journal des transactions (disponible seulement pour la série M). Vous devez néanmoins prendre en compte certains éléments, comme le nombre maximal de disques par type de machine virtuelle. Vous pouvez trouver plus d’informations sur l’accélérateur d’écriture [ici](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


Voici quelques exemples de dimensionnement du volume des journaux :

| taille et type de disque du volume de données | volume des journaux et type de disque - Configuration 1 | volume des journaux et type de disque - Configuration 2 |
| --- | --- | --- |
| 4 x P50 -> 16 To | 5 x P20 -> 2,5 To | 3 x P30 -> 3 To |
| 6 x P15 -> 1,5 To | 4 x P6 -> 256 Go | 1 x P15 -> 256 Go |


Comme pour la montée en puissance parallèle de SAP HANA, le répertoire /Hana/shared doit être partagé entre la machine virtuelle SAP HANA et la machine virtuelle DT 2.0. La même architecture que pour la montée en puissance parallèle de SAP HANA avec des machines virtuelles dédiées qui agissent comme un serveur NFS à haute disponibilité est recommandée. Pour fournir un volume de sauvegarde partagé, vous pouvez utiliser la même conception. Le fait que la haute disponibilité soit nécessaire ou qu’il soit suffisant d’utiliser une machine virtuelle dédiée avec une capacité de stockage suffisante pour agir comme serveur de sauvegarde dépend du client.



### <a name="links-to-dt-20-documentation"></a>Liens vers la documentation de DT 2.0 

- [SAP HANA Dynamic Tiering installation and update guide (Guide d’installation et de mise à jour de SAP HANA Dynamic Tiering)](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA Dynamic Tiering tutorials and resources (Tutoriels et ressources pour SAP HANA Dynamic Tiering)](https://www.sap.com/developer/topics/hana-dynamic-tiering.html)
- [SAP HANA Dynamic Tiering PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamic tiering enhancements](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Opérations pour le déploiement de SAP HANA sur des machines virtuelles Azure
Les sections suivantes décrivent certaines des opérations liées au déploiement de systèmes SAP HANA sur des machines virtuelles Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Opérations de sauvegarde et restauration sur des machines virtuelles Azure
Les documents suivants décrivent comment sauvegarder et restaurer votre déploiement de SAP HANA :

- [Vue d’ensemble de la sauvegarde SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Sauvegarde SAP HANA au niveau des fichiers](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Démarrer et redémarrer des machines virtuelles contenant SAP HANA
L’une des caractéristiques importantes du cloud public Azure est que vous êtes facturé uniquement pour les minutes de calcul que vous dépensez. Par exemple, quand vous arrêtez une machine virtuelle exécutant SAP HANA, seuls les coûts de stockage pendant cette durée vous sont facturés. Une autre fonctionnalité est disponible quand vous spécifiez des adresses IP statiques pour vos machines virtuelles dans votre déploiement initial. Quand vous redémarrez une machine virtuelle avec SAP HANA, la machine virtuelle redémarre avec ses adresses IP antérieures. 


### <a name="use-saprouter-for-sap-remote-support"></a>Utiliser SAProuter pour la prise en charge à distance de SAP
Si vous avez une connexion de site à site entre vos emplacements locaux et Azure, et que vous exécutez des composants SAP, il est très probable que vous exécutez déjà SAProuter. Dans ce cas, effectuez les opérations suivantes pour la prise en charge à distance :

- Mettre à jour l’adresse IP privée et statique de la machine virtuelle qui héberge SAP HANA dans la configuration de SAProuter.
- Configurer le groupe de sécurité réseau du sous-réseau qui héberge la machine virtuelle HANA pour autoriser le trafic via le port TCP/IP 3299.

Si vous vous connectez à Azure par le biais d’Internet et que vous n’avez pas installé de routeur SAP pour la machine virtuelle avec SAP HANA, vous devez installer le composant. Installez SAProuter sur une machine virtuelle distincte dans le sous-réseau de gestion. L’image suivante représente un schéma approximatif pour le déploiement de SAP HANA sans une connexion de site à site et avec SAProuter :

![Schéma de déploiement approximatif pour SAP HANA sans une connexion de site à site et avec SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Veillez à installer SAProuter sur une machine virtuelle distincte, et non sur votre machine virtuelle Jumpbox. La machine virtuelle distincte doit avoir une adresse IP statique. Pour connecter votre SAProuter au SAProuter hébergé par SAP, contactez SAP pour obtenir une adresse IP. (Le SAProuter qui est hébergé par SAP est l’équivalent de l’instance de SAProuter que vous installez sur la machine virtuelle.) Utilisez l’adresse IP de SAP pour configurer votre instance de SAProuter. Dans les paramètres de configuration, le seul port nécessaire est le port TCP 3299.

Pour plus d’informations sur la façon de configurer et de gérer des connexions de prise en charge à distance par le biais de SAProuter, consultez la [documentation SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Haute disponibilité avec SAP HANA sur les machines virtuelles Azure natives
Si vous exécutez SUSE Linux Enterprise Server pour des applications SAP 12 SP1 ou version ultérieure, vous pouvez établir un cluster Pacemaker avec des appareils STONITH. Vous pouvez utiliser les appareils afin de définir une configuration SAP HANA qui utilise la réplication synchrone avec la réplication de système HANA et le basculement automatique. Pour plus d’informations sur la procédure de configuration, consultez le [Guide de la haute disponibilité de SAP HANA sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
