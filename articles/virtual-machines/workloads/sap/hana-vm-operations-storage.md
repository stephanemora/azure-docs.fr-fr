---
title: Configurations du stockage des machines virtuelles SAP HANA Azure | Microsoft Docs
description: Recommandations de stockage pour les machines virtuelles avec SAP HANA déployé.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4e8b544ea3daeb23b22f3864beb21ba9d3f342f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255615"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurations du stockage des machines virtuelles SAP HANA Azure

Azure fournit des types de stockage différents, adaptés aux machines virtuelles Azure exécutant SAP HANA. **Types de stockage Azure certifiés SAP HANA** pouvant être pris en compte pour la liste des déploiements SAP HANA, tels que : 

- SSD Premium Azure  
- [Disque Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Pour en savoir plus sur ces types de disques, consultez l’article [Sélectionner un type de disque](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure offre deux méthodes de déploiement avec les disques durs virtuels sur le stockage Azure Standard et Premium. Si le scénario global le permet, tirez parti des déploiements [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 

Pour obtenir une liste des types de stockage et les contrats SLA associés pour les débits d’IOPS et de stockage, passez en revue la [documentation Azure sur les disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Indépendamment du type de stockage Azure choisi, le système de fichiers utilisé sur ce stockage doit être pris en charge par SAP pour le système d’exploitation et le SGBD spécifiques. La [Note de support SAP n°405827](https://launchpad.support.sap.com/#/notes/405827) répertorie les systèmes de fichiers pris en charge pour les différents systèmes d’exploitation et bases de données, sont SAP HANA. Cela s’applique à tous les volumes SAP HANA qui pourraient accéder à des fins de lecture et d’écriture pour n’importe quelle tâche. En particulier, en utilisant NFS sur Azure pour SAP HANA, des restrictions supplémentaires des versions NFS s’appliquent, comme indiqué plus loin dans cet article 


Les conditions certifiée SAP HANA minimales pour les différents types de stockages sont les suivantes : 

- SSD Premium Azure : /hana/log doit être mis en cache avec l’[Accélérateur d’écriture Azure](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Le volume /hana/data peut être placé sur un SSD Premium sans Accélérateur d’écriture Azure ou sur un disque Ultra
- Disque Ultra Azure au moins pour le volume /hana/log. Le volume /hana/data peut être placé sur SSD Premium sans Accélérateur d’écriture Azure ou sur disque Ultra pour accélérer les temps de redémarrage
- Volumes **NFS v4.1** par-dessus Azure NetApp Files pour /hana/log **et** /hana/data. Le volume de/hana/shared peut utiliser le protocole NFS v3 ou NFS v4.1. Le protocole NFS v4.1 est obligatoire pour les volumes /hana/data et /hana/log.

Certains types de stockage peuvent être combinés. Par exemple, il est possible de placer /hana/data sur un stockage Premium et /hana/log sur un disque de stockage Ultra afin d’obtenir la faible latence requise. Toutefois, si vous utilisez un volume NFS v4.1 basé sur ANF pour /hana/data, vous devez utiliser un autre volume NFS v4.1 basé sur ANF pour /hana/log. L’utilisation de NFS en plus d’ANF pour l’un des volumes (comme /hana/data) et du stockage Premium Azure ou d’un disque Ultra pour l’autre volume (comme /hana/log) n’est **pas prise en charge**.

Localement, vous aviez rarement besoin de vous occuper des sous-systèmes d’E/S et de leurs fonctionnalités. En effet, le fournisseur d’appliance devait s’assurer que les exigences de stockage minimales étaient remplies pour SAP HANA. Comme vous générez l’infrastructure Azure vous-même, vous devez connaître certaines de ces conditions requises par SAP. Certaines des caractéristiques de débit minimal requises par SAP le sont pour :

- Activer la lecture/l’écriture sur **/hana/log** à un débit de 250 Mo/s pour des tailles d’E/S de 1 Mo
- Activer l’activité de lecture à un débit de 400 Mo/s au minimum pour **/hana/data** pour des tailles d’E/S de 16 Mo et 64 Mo
- Activer l’activité d’écriture à un débit de 250 Mo/s au minimum pour **/hana/data** avec des tailles d’E/S de 16 Mo et 64 Mo

Étant donné qu’une latence de stockage faible est critique pour les systèmes SGBD, même pour ceux comme SAP HANA, conservez les données en mémoire. Le point critique dans le stockage concerne généralement les écritures du journal de transaction sur les systèmes SGBD. D’autres opérations peuvent également être critiques, par exemple, l’écriture des points de sauvegarde ou le chargement des données en mémoire après une récupération sur incident. C’est pourquoi vous devez **obligatoirement** utiliser des disques Azure Premium pour les volumes **/hana/data** et **/hana/log**. Pour respecter les exigences de débit minimal requises par SAP pour les volumes **/hana/log** et **/hana/data**, créez un volume RAID 0 avec MDADM ou LVM sur plusieurs disques de stockage Azure Premium et utilisez les volumes RAID comme volumes **/hana/data** et **/hana/log**. 

**Recommandation : utilisez les tailles de bande recommandées suivantes pour le disque RAID 0 :**

- 256 Ko pour **/hana/data**
- 32 Ko pour **/hana/log**

> [!IMPORTANT]
> La taille de bande pour /hana/data a été modifiée par rapport aux recommandations antérieures demandant 64 Ko ou 128 Ko à 256 Ko, en fonction des expériences client avec les versions plus récentes de Linux. La taille de 256 Ko offre des performances légèrement meilleures

> [!NOTE]
> Vous n’avez pas besoin de configurer un niveau de redondance avec des volumes RAID, car les stockages Azure Premium et Standard conservent trois images d’un disque dur virtuel. L’utilisation d’un volume RAID a pour but principal de configurer des volumes qui fournissent un débit d’E/S suffisant.

L’augmentation du nombre de disques durs virtuels Azure sous un volume RAID a pour effet d’augmenter les débits d’IOPS et de stockage. Ainsi, si vous placez un volume RAID 0 sur trois disques P30 de stockage Azure Premium, vous obtenez en principe trois fois plus de débit d’IOPS et trois fois plus de débit de stockage qu’avec un seul disque P30 de stockage Azure Premium.

Gardez également à l’esprit le débit d’E/S de machine virtuelle global lors du dimensionnement ou du choix d’une machine virtuelle. Le débit de stockage de machine virtuelle global est décrit dans l’article [Tailles de machine virtuelle à mémoire optimisée](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Mode Planificateur d’E-S Linux
Linux dispose de plusieurs modes de planification d’E-S. Les fournisseurs Linux et SAP recommandent couramment de remplacer le mode planificateur d’E/S **mq-deadline** ou **kyber** par **noop** (absence de plusieurs files d’attente) ou **none** (plusieurs files d’attente) pour les volumes de disque. Pour plus de détails à ce sujet, consultez la [Note SAP 1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solutions avec Stockage Premium et Accélérateur d’écriture Azure pour les machines virtuelles Azure de la série M
L’Accélérateur d’écriture Azure est une fonctionnalité qui est fournie uniquement pour les machines virtuelles Azure de la série M. Comme son nom l’indique, cette fonctionnalité vise à améliorer la latence d’E/S des écritures dans le stockage Azure Premium. Pour SAP HANA, l’Accélérateur des écritures doit être utilisé exclusivement sur le volume **/hana/log**. Par conséquent, **/hana/data** et **/hana/log** sont des volumes distincts avec l’Accélérateur d’écriture Azure prenant en charge le volume **/hana/log** uniquement. 

> [!IMPORTANT]
> En cas d’utilisation du Stockage Premium Azure, l’utilisation de l’[Accélérateur d’écriture](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) Azure ou du volume /hana/log est obligatoire. L’Accélérateur d’écriture est disponible pour le Stockage Premium et les machines virtuelles de série M et Mv2 uniquement.

Les suggestions de mise en cache indiquées plus bas supposent que SAP HANA présente les caractéristiques d’E/S suivantes :

- Il n’y a pratiquement aucune charge de travail de lecture sur les fichiers de données HANA. Les exceptions sont les E/S volumineuses après le redémarrage de l’instance HANA ou quand les données sont chargées dans HANA. Les sauvegardes de base de données HANA peuvent constituer un autre cas d’E/S en lecture plus volumineuses sur des fichiers de données. Ainsi, la mise en cache en lecture n’est pas pertinente car, dans la plupart des cas, tous les volumes de fichiers de données doivent être lus complètement.
- Les points de sauvegarde HANA et la récupération sur incident HANA donnent lieu à des écritures en rafales dans les fichiers de données. L’écriture de point de sauvegarde est asynchrone et n’accapare pas de transactions utilisateur. L’écriture de données au cours d’une récupération sur incident est critique pour les performances et la réactivité du système. Toutefois, une récupération sur incident doit intervenir dans des situations plutôt exceptionnelles.
- Il n’y a pratiquement pas de lectures à partir des fichiers de restauration par progression HANA. Les exceptions sont les grandes E/S pendant les sauvegardes de fichier journal, une récupération sur incident ou le redémarrage d’une instance HANA.  
- La charge principale par rapport au fichier journal de restauration par progression SAP HANA est constituée d’écritures. Selon la nature de la charge de travail, la taille des E/S peut aller de 4 Ko à 1 Mo, voire plus. La latence des écritures par rapport au journal de restauration par progression SAP HANA est critique pour les performances.
- Toutes les écritures doivent être rendues persistantes sur le disque de manière fiable.

**Recommandation : pour observer ces modèles d’E/S définis par SAP HANA, vous devez paramétrer la mise en cache pour les différents volumes à l’aide du Stockage Premium Azure comme suit :**

- **/hana/data** : aucune mise en cache
- **/hana/log** : aucune mise en cache, à l’exception des séries M et Mv2, où l’Accélérateur d’écriture doit être activé sans mise en cache de lecture. 
- **/hana/shared** : mise en cache en lecture

### <a name="production-recommended-storage-solution"></a>Solution de stockage recommandée pour la production

> [!IMPORTANT]
> La certification SAP HANA des machines virtuelles Azure de la série M est valable exclusivement avec l’Accélérateur des écritures Azure sur le volume **/hana/log**. Par conséquent, dans les scénarios de production, les déploiements SAP HANA sur des machines virtuelles Azure de la série M doivent être configurés avec l’Accélérateur des écritures Azure sur le volume **/hana/log**.  

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Les recommandations vont souvent au-delà des conditions minimales requise par SAP, comme indiqué plus haut dans cet article. Les recommandations listées sont un compromis entre les recommandations de taille de SAP et le débit de stockage maximal fourni par les différents types de machines virtuelles.

**Recommandation : Les configurations recommandées pour les scénarios de production ressemblent à ce qui suit :**

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2 850 Gio | 1 000 Mo/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5 700 Gio | 1 000 Mo/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5 700 Gio | 2 000 Mo/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11 400 Gio | 2 000 Mo/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à exécuter. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume avec davantage de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure.

L’Accélérateur des écritures Azure fonctionne uniquement en association avec des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Cela signifie que les disques de stockage Azure Premium constituant le volume **/hana/log** doivent être déployés en tant que disques managés.

L’Accélérateur des écritures Azure prend en charge un nombre limité de disques durs virtuels de stockage Azure Premium par machine virtuelle. Les limites actuelles sont :

- 16 disques durs virtuels pour une machine virtuelle M128xx et M416xx
- 8 disques durs virtuels pour une machine virtuelle M64xx et M208xx
- 4 disques durs virtuels pour une machine virtuelle M32xx

Vous trouverez des instructions plus détaillées sur l’activation de l’Accélérateur des écritures Azure dans l’article [Accélérateur des écritures](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Vous y trouverez aussi les détails et les restrictions relatifs à l’utilisation de cet accélérateur.

**Recommandation : vous devez utiliser l’Accélérateur d’écriture pour les disques formant le volume /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configuration du stockage Azure avec prise en compte des coûts
Le tableau suivant illustre une configuration des types de machines virtuelles que les clients utilisent également pour héberger SAP HANA sur des machines virtuelles Azure. Il est possible que certains types de machines virtuelles ne remplissent pas tous les critères minimum pour SAP HANA ou ne soient pas officiellement pris en charge avec SAP HANA par SAP. Toutefois, ces machines virtuelles n’ont pas montré, jusqu’à présent, de problèmes de fonctionnement dans les scénarios hors production. **/hana/data** et **/hana/log** sont combinés en un seul volume. Par conséquent, l’utilisation de l’Accélérateur d’écriture Azure peut devenir une limitation en termes d’IOPS.

> [!NOTE]
> Pour les scénarios SAP pris en charge, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Une modification des anciennes recommandations pour une solution plus économique serait de remplacer les [disque HDD Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) par des [disque SSD Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

Les recommandations vont souvent au-delà des conditions minimales requise par SAP, comme indiqué plus haut dans cet article. Les recommandations listées sont un compromis entre les recommandations de taille de SAP et le débit de stockage maximal fourni par les différents types de machines virtuelles.

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data et /hana/log<br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 Gio | 768 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 Go | 384 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 Gio | 768 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 Gio | 1 200 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 Gio | 2 000 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3,800 Gio | 2 000 Mo/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2 850 Gio | 1 000 Mo/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5 700 Gio | 1 000 Mo/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5 700 Gio | 2 000 Mo/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11 400 Gio | 2 000 Mo/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Les disques recommandés pour les types de machines virtuelles les plus petits avec 3 x P20 dépassent la taille des volumes en ce qui concerne les recommandations d’espace selon le [livre blanc de stockage TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Toutefois, le choix affiché dans le tableau a été effectué pour fournir un débit de disque suffisant pour SAP HANA. La taille indiquée pour le volume **/hana/backup** a été fixée pour permettre la conservation de sauvegardes représentant deux fois le volume de mémoire, mais vous pouvez ajuster ce paramètre selon vos besoins.   
Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à exécuter. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume avec davantage de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure. 

> [!NOTE]
> Les configurations ci-dessus ne bénéficient PAS du [contrat SLA de machine virtuelle Azure à instance unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), car elles utilisent à la fois le stockage Azure Premium et le stockage Azure Standard. Toutefois, ce choix a été fait dans le but d’optimiser les coûts. Vous devez choisir Stockage Premium pour tous les disques ci-dessus qui sont répertoriés comme Azure Standard Storage (Sxx) pour que la configuration de la machine virtuelle soit compatible avec le contrat de niveau de service de machine virtuelle unique Azure.


> [!NOTE]
> Les recommandations relatives à la configuration du disque qui sont indiquées ciblent une configuration minimale pour le protocole SAP équivalente à celles de leurs fournisseurs d’infrastructure. En pratique, dans les scénarios de déploiement et de charge de travail des clients, il se peut que ces recommandations ne fournissent pas suffisamment de fonctionnalités. Dans certains cas, il se peut que le client demande un rechargement plus rapide des données après un redémarrage HANA ou que les configurations de sauvegarde demandent une bande passante plus élevée pour le stockage. Dans d’autres cas, pour **/hana/log**, 5 000 IOPS étaient insuffisants pour la charge de travail spécifique. Par conséquent, prenez ces recommandations en tant que point de départ et adaptez-les en fonction des exigences de la charge de travail.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuration du stockage sur disque Ultra Azure pour SAP HANA
Microsoft est en train de déployer un nouveau type de stockage Azure appelé [disque Ultra Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). La grande différence entre le stockage Azure proposé jusqu’à présent et le disque Ultra est que les capacités du disque ne sont plus limitées à la taille du disque. En tant que client, vous pouvez définir ces capacités pour le disque Ultra :

- Taille d’un disque allant de 4 à 65 536 Gio
- IOPS allant de 100 à 160K (la valeur maximum dépend également des types de machine virtuelle)
- Débit de stockage de 300 à 2 000 Mo/s

Avec le disque Ultra, vous avez la possibilité de définir un seul disque qui répond à vos exigences en matière de plage de débit de disque, d’IOPS et de taille. Au lieu d’utiliser des gestionnaires de volumes logiques tels que LVM ou MDADM en plus du Stockage Azure Premium pour construire des volumes qui respectent les exigences de débit de stockage et d’IOPS, vous pouvez choisir une configuration mixte entre le disque Ultra et le stockage Premium. Vous pouvez ainsi limiter l’utilisation du disque Ultra aux volumes /hana/data et /hana/log ayant des performances critiques et utiliser le stockage Premium Azure pour les autres volumes

Les autres avantages du disque Ultra peuvent être la meilleure latence de lecture par rapport au Stockage Premium. La latence de lecture plus courte peut avoir des avantages quand vous souhaitez réduire le temps de démarrage HANA et la charge ultérieure des données en mémoire. Les avantages du stockage sur disque Ultra peuvent également être observés quand HANA écrit des points d’enregistrement. Les disques de Stockage Premium pour /hana/data n’étant généralement pas mis en cache par l’Accélérateur d’écriture, la latence d’écriture vers /hana/data sur le Stockage Premium est plus élevée que sur disque Ultra. Vous pouvez vous attendre à ce que l’écriture de point d’enregistrement sur disque Ultra offre de meilleures performances.

> [!IMPORTANT]
> Le disque Ultra n’est pas encore disponible dans toutes les régions Azure, ni pris en charge par tous les types de machines virtuelles listés ci-dessous. Pour plus d’informations sur la disponibilité du disque Ultra et sa prise en charge par les différentes familles de machines virtuelles, consultez l’article [Quels sont les types de disque disponibles dans Azure ?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solution de stockage recommandée pour la production avec une pure configuration de disque Ultra
Dans cette configuration, vous conservez les volumes hana/data et/hana/log séparément. Les valeurs suggérées sont dérivées des indicateurs de performance clés que SAP doit certifier pour les configurations SAP HANA et de stockage, comme recommandé dans le [livre blanc sur le stockage SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Les recommandations vont souvent au-delà des conditions minimales requise par SAP, comme indiqué plus haut dans cet article. Les recommandations listées sont un compromis entre les recommandations de taille de SAP et le débit de stockage maximal fourni par les différents types de machines virtuelles.

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | Volume /hana/data | Débit d’e/s /hana/data | IOPS /hana/data | Volume /hana/log | Débit d’e/s /hana/log | IOPS /hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 Gio | 1 200 Mo/s | 600 Go | 700 Mo/s | 7 500 | 512 Go | 500 Mo/s  | 2 000 |
| M32ts | 192 Gio | 500 Mo/s | 250 Go | 400 Mbits/s | 7 500 | 256 Go | 250 Mbits/s  | 2 000 |
| M32ls | 256 Gio | 500 Mo/s | 300 Go | 400 Mbits/s | 7 500 | 256 Go | 250 Mbits/s  | 2 000 |
| M64ls | 512 Go | 1 000 Mo/s | 600 Go | 600 Mbits/s | 7 500 | 512 Go | 400 Mbits/s  | 2 500 |
| M64s | 1 000 Gio | 1 000 Mo/s |  1 200 GO | 600 Mbits/s | 7 500 | 512 Go | 400 Mbits/s  | 2 500 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 2 100 Gio | 600 Mbits/s | 7 500 | 512 Go | 400 Mbits/s  | 2 500 |
| M128s | 2 000 Gio | 2 000 Mo/s |2 400 Go | 1 200 Mbits/s |9 000 | 512 Go | 800 Mo/s  | 3 000 | 
| M128ms | 3,800 Gio | 2 000 Mo/s | 4 800 Go | 1 200 Mo/s |9 000 | 512 Go | 800 Mo/s  | 3 000 | 
| M208s_v2 | 2 850 Gio | 1 000 Mo/s | 3 500 Go | 1 000 Mbits/s | 9 000 | 512 Go | 400 Mbits/s  | 2 500 | 
| M208ms_v2 | 5 700 Gio | 1 000 Mo/s | 7 200 Go | 1 000 Mbits/s | 9 000 | 512 Go | 400 Mbits/s  | 2 500 | 
| M416s_v2 | 5 700 Gio | 2 000 Mo/s | 7 200 Go | 1 500 Mbits/s | 9 000 | 512 Go | 800 Mo/s  | 3 000 | 
| M416ms_v2 | 11 400 Gio | 2 000 Mo/s | 14 400 Go | 1 500 Mbits/s | 9 000 | 512 Go | 800 Mo/s  | 3 000 |   

Les valeurs répertoriées sont destinées à être un point de départ et doivent être évaluées en fonction de la demande réelle. L’avantage du disque Ultra Azure est que les valeurs d’IOPS et de débit peuvent être adaptées sans avoir à arrêter la machine virtuelle, ni la charge de travail appliquée au système.   

> [!NOTE]
> À ce jour, les captures instantanées du disque de stockage Ultra ne sont pas disponibles. Cela empêche l’utilisation de captures instantanées de machine virtuelle avec les services Sauvegarde Azure

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Solution de stockage avec prise en compte des coûts en utilisant une configuration pure de disque Ultra
Dans cette configuration, vous disposez des volumes /hana/data et/hana/log sur le même disque. Les valeurs suggérées sont dérivées des indicateurs de performance clés que SAP doit certifier pour les configurations SAP HANA et de stockage, comme recommandé dans le [livre blanc sur le stockage SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Les recommandations vont souvent au-delà des conditions minimales requise par SAP, comme indiqué plus haut dans cet article. Les recommandations listées sont un compromis entre les recommandations de taille de SAP et le débit de stockage maximal fourni par les différents types de machines virtuelles.

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | Volume pour /hana/data et/log | Débit d’E/S de /hana/data et log | IOPS de /hana/data et log |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 Gio | 1 200 Mo/s | 1 200 GO | 1 200 Mbits/s | 9 500 | 
| M32ts | 192 Gio | 500 Mo/s | 512 Go | 400 Mbits/s | 9 500 | 
| M32ls | 256 Gio | 500 Mo/s | 600 Go | 400 Mbits/s | 9 500 | 
| M64ls | 512 Go | 1 000 Mo/s | 1 100 GO | 900 Mbits/s | 10 000 | 
| M64s | 1 000 Gio | 1 000 Mo/s |  1 700 Go | 900 Mbits/s | 10 000 | 
| M64ms | 1 750 Gio | 1 000 Mo/s | 2 600 Go | 900 Mbits/s | 10 000 | 
| M128s | 2 000 Gio | 2 000 Mo/s |2 900 Go | 1 800 Mbits/s |12,000 | 
| M128ms | 3,800 Gio | 2 000 Mo/s | 5 300 GO | 1 800 Mbits/s |12,000 |  
| M208s_v2 | 2 850 Gio | 1 000 Mo/s | 4 000 Go | 900 Mbits/s | 10 000 |  
| M208ms_v2 | 5 700 Gio | 1 000 Mo/s | 7 700 Go | 900 Mbits/s | 10 000 | 
| M416s_v2 | 5 700 Gio | 2 000 Mo/s | 7 700 Go | 1 800 Mbits/s | 12,000 |  
| M416ms_v2 | 11 400 Gio | 2 000 Mo/s | 15 000 Go | 1 800 Mbits/s | 12,000 |    

Les valeurs répertoriées sont destinées à être un point de départ et doivent être évaluées en fonction de la demande réelle. L’avantage du disque Ultra Azure est que les valeurs d’IOPS et de débit peuvent être adaptées sans avoir à arrêter la machine virtuelle, ni la charge de travail appliquée au système.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumes NFS v4.1 sur Azure NetApp Files
Azure NetApp Files fournit des partages NFS natifs qui peuvent être utilisés pour les volumes /hana/shared, /hana/data et /hana/log. L’utilisation de partages NFS basés sur ANF pour les volumes /hana/data et /hana/log nécessite l’utilisation du protocole NFS v4.1. Le protocole NFS v3 n’est pas pris en charge pour l’utilisation de volumes /hana/data et /hana/log quand les partages sont basés sur ANF. 

> [!IMPORTANT]
> L’utilisation du protocole NFS v3 implémenté sur Azure NetApp Files n’est **pas** prise en charge pour /hana/data et /hana/log. L’utilisation du NFS 4.1 est obligatoire pour les volumes /hana/data et /hana/log d’un point de vue fonctionnel. En revanche, pour le volume/hana/shared, les systèmes NFS v3 ou NFS v4.1 peuvent tous eux êtres utilisés d’un point de vue fonctionnel.

### <a name="important-considerations"></a>Points importants à prendre en compte
Lorsque vous envisagez d’utiliser Azure NetApp Files pour SAP Netweaver et SAP HANA, tenez compte des importantes considérations suivantes :

- La taille de pool de capacité minimale est de 4 Tio.  
- La taille de volume minimale est de 100 Gio
- Azure NetApp Files et toutes les machines virtuelles, où les volumes Azure NetApp Files seront montés, doivent être déployés dans le même réseau virtuel Azure ou dans des [réseaux virtuels homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) de la même région.  
- Le réseau virtuel sélectionné doit avoir un sous-réseau délégué à Azure NetApp Files.
- Le débit d’un volume NetApp Azure est une fonction du quota de volume et du niveau de service, comme décrit dans [Niveau de service pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Lors du dimensionnement de volumes HANA Azure NetApp, assurez-vous que le débit obtenu répond à la configuration système requise pour HANA.  
- Azure NetApp Files propose une [stratégie d’exportation](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) : vous pouvez contrôler les clients autorisés, le type d’accès (lecture et écriture, lecture seule, etc.). 
- La fonctionnalité Azure NetApp Files ne tient pas encore compte des zones. Actuellement, la fonctionnalité Azure NetApp Files n’est pas déployée dans toutes les zones de disponibilité d’une région Azure. Méfiez-vous de l’impact potentiel sur les temps de latence dans certaines régions Azure.  
- Il est important que les machines virtuelles soient déployées à proximité du stockage Azure NetApp pour des raisons de faible latence. 
- L’ID d’utilisateur pour <b>sid</b>adm et l’ID de groupe pour `sapsys` sur les machines virtuelles doivent correspondre à la configuration dans Azure NetApp Files. 

> [!IMPORTANT]
> Pour les charges de travail SAP HANA, une latence faible est critique. Collaborez avec votre représentant Microsoft pour vous assurer que les machines virtuelles et les volumes Azure NetApp Files soient déployés à proximité.  

> [!IMPORTANT]
> En cas d’incompatibilité entre l’ID d’utilisateur pour <b>sid</b>adm et l’ID de groupe pour `sapsys` entre la machine virtuelle et la configuration Azure NetApp, les autorisations pour les fichiers sur les volumes NetApp Azure, montés sur les machines virtuelles, s’afficheront en tant que `nobody`. Veillez à spécifier l’ID d’utilisateur correct pour <b>sid</b>adm et l’ID de groupe pour `sapsys`, lors de l’[intégration d’un nouveau système](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) à Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionnement de la base de données HANA sur Azure NetApp Files

Le débit d’un volume NetApp Azure est une fonction de la taille de volume et du niveau de service, comme décrit dans [Niveau de service pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Lorsque vous concevez l’infrastructure pour SAP dans Azure, vous devez connaître certaines exigences de débit de stockage minimal de SAP, qui se traduisent par des caractéristiques de débit minimal pour :

- Activer la lecture/l’écriture sur /hana/log à un débit de 250 Mo/s pour des tailles d’E/S de 1 Mo  
- Activer l’activité de lecture à un débit de 400 Mo/s au minimum pour /hana/data pour des tailles d’E/S de 16 Mo et 64 Mo  
- Activer l’activité d’écriture à un débit de 250 Mo/s au minimum pour /hana/data avec des tailles d’E/S de 16 Mo et 64 Mo  

Les [limites de débit Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) pour un quota de volume de 1 Tio sont les suivantes :
- Niveau de stockage Premium - 64 Mio/s  
- Niveau de stockage Ultra - 128 Mio/s  

> [!IMPORTANT]
> Indépendamment de la capacité que vous déployez sur un volume NFS unique, le débit est supposé stagner dans la plage de bande passante de 1,2 à 1,4 Go/s exploitée par un consommateur dans une machine virtuelle. Cela concerne l’architecture sous-jacente de l’offre ANF et aux limites de session Linux associées relatives à NFS. Les valeurs de performances et de débit décrites dans l’article [Résultats des tests de performances pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) ont été effectuées sur un volume NFS partagé avec plusieurs machines virtuelles clientes et, par conséquent, avec plusieurs sessions. Ce scénario est différent du scénario que nous mesurons dans SAP. Nous y mesurons le débit d’une seule machine virtuelle par rapport à un volume NFS. hébergé sur ANF.

Pour respecter les exigences de débit minimal SAP pour les données et le journal, et conformément aux instructions pour `/hana/shared`, les tailles recommandées ressemblent à ceci :

| Volume | Taille<br /> Niveau de stockage Premium | Taille<br /> Niveau de stockage Ultra | Protocole NFS pris en charge |
| --- | --- | --- |
| /hana/log/ | 4 Tio | 2 Tio | v4.1 |
| /hana/data | 6,3 Tio | 3,2 Tio | v4.1 |
| /hana/shared | Max (512 Go, 1xRAM) par 4 nœuds Worker | Max (512 Go, 1xRAM) par 4 nœuds Worker | v3 ou v4.1 |


> [!NOTE]
> Les recommandations relatives aux tailles Azure NetApp Files qui sont indiquées ici ciblent une configuration minimale pour le protocole SAP équivalente à celles de leurs fournisseurs d’infrastructure. Dans les déploiements de clients et les scénarios de charge de travail réels, cela peut ne pas suffire. Prenez ces recommandations en tant que point de départ et adaptez-les en fonction des exigences de votre charge de travail spécifique.  

Par conséquent, vous pouvez envisager de déployer un débit similaire pour les volumes ANF tels qu’ils sont déjà listés pour les disques de stockage Ultra. Prenez également en compte les tailles listées pour les volumes des différentes références SKU de machine virtuelle, comme déjà indiqué dans les tables de disques Ultra.

> [!TIP]
> Vous pouvez redimensionner les volumes Azure NetApp Files de manière dynamique, sans avoir à `unmount` les volumes, à arrêter les machines virtuelles ou à arrêter SAP HANA. Cela permet de répondre aux exigences à la fois attendues et imprévues de votre application en matière de débit.

La documentation sur le déploiement d’une configuration de scale-out de SAP HANA avec un nœud de secours à l’aide de volumes NFS v4.1 hébergés dans ANF est publiée dans [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez les pages suivantes :

- [Haute disponibilité de SAP HANA pour les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
