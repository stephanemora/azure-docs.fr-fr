---
title: Configurations de stockage de machine virtuelle SAP HANA Azure | Microsoft Docs
description: Recommandations de stockage pour la machine virtuelle qui ont de SAP HANA déployées dans les.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735510"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurations de stockage de machine virtuelle SAP HANA Azure

Azure fournit différents types de stockage qui conviennent pour les machines virtuelles Azure qui exécutent SAP HANA. Les types de stockage Azure pouvant être pris en compte pour la liste de déploiements de SAP HANA comme : 

- Lecteurs de disque SSD standards (SSD)
- Disques SSD Premium (Solid State Drive)
- SSD ultra en version préliminaire publique et ne sont pas encore pris en charge avec les applications SAP de production

Pour en savoir plus sur ces types de disque, consultez l’article [sélectionner un type de disque](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure offre deux méthodes de déploiement avec les disques durs virtuels sur le stockage Azure Standard et Premium. Si le scénario global le permet, tirez parti des déploiements [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 

Pour obtenir une liste des types de stockage et les contrats SLA associés pour les débits d’IOPS et de stockage, passez en revue la [documentation Azure sur les disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).

**Recommandation : Utiliser le stockage Premium Azure avec l’accélérateur des écritures Azure et utiliser des disques gérés Azure pour le déploiement**

Dans le monde en local, vous deviez rarement en charge sur les sous-systèmes d’e/s et de ses fonctionnalités. En effet, le fournisseur d’appliance devait s’assurer que les exigences de stockage minimales étaient remplies pour SAP HANA. Lorsque vous créez l’infrastructure Azure vous-même, vous devez être conscient de certaines de ces exigences. Certaines des caractéristiques de débit minimal qui sont demandées sont ce qui entraîne la nécessité de :

- Activer en lecture/écriture sur **/hana/log** d’un 250 Mo/s avec des tailles d’e/s de 1 Mo
- Activer l’activité de lecture à un débit de 400 Mo/s au minimum pour **/hana/data** pour des tailles d’E/S de 16 Mo et 64 Mo
- Activer l’activité d’écriture à un débit de 250 Mo/s au minimum pour **/hana/data** avec des tailles d’E/S de 16 Mo et 64 Mo

Étant donné qu’une latence de stockage faible est critique pour les systèmes SGBD, même pour ceux comme SAP HANA, conservez les données en mémoire. Le point critique dans le stockage concerne généralement les écritures du journal de transaction sur les systèmes SGBD. D’autres opérations peuvent également être critiques, par exemple, l’écriture des points de sauvegarde ou le chargement des données en mémoire après une récupération sur incident. Par conséquent, il est **obligatoire** pour tirer parti des disques Azure Premium pour **/hana/data** et **/hana/log** volumes. Pour respecter les exigences de débit minimal requises par SAP pour les volumes **/hana/log** et **/hana/data**, créez un volume RAID 0 avec MDADM ou LVM sur plusieurs disques de stockage Azure Premium et utilisez les volumes RAID comme volumes **/hana/data** et **/hana/log**. 

**Recommandation : Comme stripe tailles pour le disque RAID 0, la recommandation consiste à utiliser :**

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

**Recommandation : À la suite de ces modèles d’e/s observées par SAP HANA, la mise en cache pour les différents volumes à l’aide de stockage Premium Azure doit être définie comme :**

- **/hana/data** : aucune mise en cache
- **/hana/log** : aucune mise en cache ; exception pour la série M (voir plus loin dans ce document)
- **/hana/shared** : mise en cache en lecture


Gardez également à l’esprit le débit d’E/S de machine virtuelle global lors du dimensionnement ou du choix d’une machine virtuelle. Le débit de stockage de machine virtuelle global est décrit dans l’article [Tailles de machine virtuelle à mémoire optimisée](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Mode Planificateur d’E-S Linux
Linux dispose de plusieurs modes de planification d’E-S. Recommandation commune via des fournisseurs de Linux et SAP consiste à reconfigurer le mode de planificateur d’e/s pour les volumes de disque à partir de la **cfq** mode pour le **noop** mode. Pour plus de détails à ce sujet, voir le document [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solution de stockage de production avec accélérateur des écritures Azure pour machines virtuelles Azure de la série M
L’Accélérateur des écritures Azure est une fonctionnalité qui est fournie uniquement pour les machines virtuelles de la série M d’Azure. Comme son nom l’indique, cette fonctionnalité vise à améliorer la latence d’E/S des opérations d’écriture sur le stockage Azure Premium. Pour SAP HANA, l’Accélérateur des écritures doit être utilisé exclusivement sur le volume **/hana/log**. Par conséquent, le **/hana/data** et **/hana/log** sont des volumes distincts avec l’accélérateur des écritures Azure prenant en charge la **/hana/log** volume uniquement. 

> [!IMPORTANT]
> La certification SAP HANA des machines virtuelles Azure de la série M est valable exclusivement avec l’Accélérateur des écritures Azure sur le volume **/hana/log**. Par conséquent, dans les scénarios de production, les déploiements SAP HANA sur des machines virtuelles Azure de la série M doivent être configurés avec l’Accélérateur des écritures Azure sur le volume **/hana/log**.  

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Recommandation : Les configurations recommandées pour les scénarios de production ressembler à :**

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1 000 Mo/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1 000 Mo/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2 000 Mo/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2 000 Mo/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Types de machines virtuelles M416xx_v2 ne sont pas encore accessibles par Microsoft pour le grand public. Vérifiez si le débit de stockage pour les différents volumes suggérés répond à la charge de travail que vous souhaitez exécuter. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Dimensionnement d’un volume avec plusieurs disques durs virtuels que répertoriées augmente les e/s et un débit d’e/s dans les limites du type de machine virtuelle Azure.

L’Accélérateur des écritures Azure fonctionne uniquement en association avec des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Cela signifie que les disques de stockage Azure Premium constituant le volume **/hana/log** doivent être déployés en tant que disques managés.

L’Accélérateur des écritures Azure prend en charge un nombre limité de disques durs virtuels de stockage Azure Premium par machine virtuelle. Les limites actuelles sont :

- 16 disques durs virtuels pour une machine virtuelle M128xx et M416xx
- 8 disques durs virtuels pour une machine virtuelle M64xx et M208xx
- 4 disques durs virtuels pour une machine virtuelle M32xx

Vous trouverez des instructions plus détaillées sur l’activation de l’Accélérateur des écritures Azure dans l’article [Accélérateur des écritures](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Vous y trouverez aussi les détails et les restrictions relatifs à l’utilisation de cet accélérateur.

**Recommandation : Vous devez utiliser l’accélérateur des écritures pour les disques formant le volume/Hana/log**


## <a name="cost-conscious-azure-storage-configuration"></a>Configuration du stockage Azure avec prise en compte des coûts
Le tableau suivant illustre une configuration de types de machines virtuelles que les clients utilisent également pour héberger SAP HANA sur machines virtuelles Azure. Il peut y avoir certains types de machines virtuelles ne peuvent pas répondre à tous les critères de stockage minimale pour SAP HANA ou ne sont pas officiellement pris en charge avec SAP HANA par SAP. Toutefois, ces machines virtuelles n’ont pas montré, jusqu’à présent, de problèmes de fonctionnement dans les scénarios hors production. Le **/hana/data** et **/hana/log** sont combinées à un seul volume. Par conséquent, l’utilisation de l’accélérateur des écritures Azure peut devenir une limitation en termes d’e/s.

> [!NOTE]
> Pour les scénarios SAP pris en charge, vérifiez si un certain type de machine virtuelle est prise en charge pour SAP HANA par SAP dans le [documentation SAP pour IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Diffère d’un anciennes recommandations pour une solution consciente de coût, consiste à déplacer à partir de [des disques HDD Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) à l’exécution d’une meilleure et plus fiable [disques SSD Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | /hana/data et /hana/log<br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 Gio | 768 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 Go | 384 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 Gio | 768 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 Gio | 1 200 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 Gio | 2 000 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 Gio | 500 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 Gio | 500 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 Go | 1 000 Mo/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1 000 Mo/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1 000 Mo/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2 000 Mo/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2 000 Mo/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Types de machines virtuelles M416xx_v2 ne sont pas encore accessibles par Microsoft pour le grand public. Les disques recommandés pour les types de machines virtuelles les plus petits avec 3 x P20 dépassent la taille des volumes en ce qui concerne les recommandations d’espace selon le [livre blanc de stockage TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Toutefois, le choix affiché dans le tableau a été effectué pour fournir un débit de disque suffisant pour SAP HANA. La taille indiquée pour le volume **/hana/backup** a été fixée pour permettre la conservation de sauvegardes représentant deux fois le volume de mémoire, mais vous pouvez ajuster ce paramètre selon vos besoins.   
Vérifiez si le débit de stockage pour les différents volumes suggérés répond à la charge de travail que vous souhaitez exécuter. Si la charge de travail nécessite de plus grands volumes pour **/hana/data** et **/hana/log**, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Dimensionnement d’un volume avec plusieurs disques durs virtuels que répertoriées augmente les e/s et un débit d’e/s dans les limites du type de machine virtuelle Azure. 

> [!NOTE]
> Les configurations ci-dessus ne bénéficient PAS du [contrat SLA de machine virtuelle Azure à instance unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), car elles utilisent à la fois le stockage Azure Premium et le stockage Azure Standard. Toutefois, ce choix a été fait dans le but d’optimiser les coûts. Vous devez choisir Stockage Premium pour tous les disques ci-dessus qui sont répertoriés comme Azure Standard Storage (Sxx) pour que la configuration de la machine virtuelle soit compatible avec le contrat de niveau de service de machine virtuelle unique Azure.


> [!NOTE]
> Les recommandations relatives à la configuration du disque qui sont indiquées ciblent une configuration minimale pour le protocole SAP équivalente à celles de leurs fournisseurs d’infrastructure. En pratique, dans les scénarios de déploiement et de charge de travail des clients, il se peut que ces recommandations ne fournissent pas suffisamment de fonctionnalités. Dans certains cas, il se peut que le client demande un rechargement plus rapide des données après un redémarrage HANA ou que les configurations de sauvegarde demandent une bande passante plus élevée pour le stockage. Dans d’autres cas, pour **/hana/log**, 5 000 IOPS étaient insuffisants pour la charge de travail spécifique. Par conséquent, prenez ces recommandations en tant que point de départ et adaptez-les en fonction des exigences de la charge de travail.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Configuration du stockage SSD Ultra Azure pour SAP HANA
Microsoft est en train d’introduire un nouveau type de stockage Azure appelé [SSD Ultra Azure](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). La principale différence entre le stockage Azure proposés jusqu'à présent et Ultra SSD est que les fonctionnalités de disque ne sont soumises à la taille de disque plus. En tant que client, vous pouvez définir ces fonctionnalités pour Ultra SSD :

- Taille d’un disque allant de 4 Go à 65 536 Go
- Plage d’e/s à partir de 100 e/s à 160 Ko IOPS (maximum dépend également des types de machines virtuelles)
- Débit de stockage à partir de 300 Mo/s à 2 000 Mo/s

Pour plus d’informations, consultez l’article de la [annonce Ultra SSD – la nouvelle génération de la technologie de disques de Azure (version préliminaire)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD vous donne la possibilité de définir un seul disque qui répond à votre taille, une e/s et une plage de débit de disque. Au lieu d’utiliser des gestionnaires de volumes logiques tels que LVM ou MDADM sur le stockage Azure Premium pour construire des volumes qui respectent les exigences de débit e/s et de stockage. Vous pouvez exécuter un mélange de configuration entre UltraSSD et le stockage Premium. Par conséquent, vous pouvez limiter l’utilisation d’UltraSSD aux critiques/Hana/Data de performances et les volumes/Hana/log et couvrent les autres volumes avec le stockage Azure Premium

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Débit | volume de données/hana / | / hana/débit de données d’e/s | / hana/data e/s | volume de journal/hana / | / hana/débit d’e/s de journal | / hana/log e/s |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 Gio | 500 Mo/s | 250 Go | 500 Mbits/s | 7500 | 256 Go | 500 Mbits/s  | 2000 |
| M32ls | 256 Gio | 500 Mo/s | 300 Go | 500 Mbits/s | 7500 | 256 Go | 500 Mbits/s  | 2000 |
| M64ls | 512 Go | 1 000 Mo/s | 600 Go | 500 Mbits/s | 7500 | 512 Go | 500 Mbits/s  | 2000 |
| M64s | 1 000 Gio | 1 000 Mo/s |  1200 Go | 500 Mbits/s | 7500 | 512 Go | 500 Mbits/s  | 2000 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 2 100 GO | 500 Mbits/s | 7500 | 512 Go | 500 Mbits/s  | 2000 |
| M128s | 2 000 Gio | 2 000 Mo/s |2 400 GO | Mbits/s de 1200 |9000 | 512 Go | 800 Mbits/s  | 2000 | 
| M128ms | 3 800 Gio | 2 000 Mo/s | GO 4800 | Mbits/s de 1200 |9000 | 512 Go | 800 Mbits/s  | 2000 | 
| M208s_v2 | 2850 GiB | 1 000 Mo/s | 3500 GO | 1000 Mbits/s | 9000 | 512 Go | 500 Mbits/s  | 2000 | 
| M208ms_v2 | 5700 GiB | 1 000 Mo/s | GO 7200 | 1000 Mbits/s | 9000 | 512 Go | 500 Mbits/s  | 2000 | 
| M416s_v2 | 5700 GiB | 2 000 Mo/s | GO 7200 | Nombre de Bps 1500 M | 9000 | 512 Go | 800 Mbits/s  | 2000 | 
| M416ms_v2 | 11400 GiB | 2 000 Mo/s | 14400 GO | Mbits/s de 1500 | 9000 | 512 Go | 800 Mbits/s  | 2000 |   

Types de machines virtuelles M416xx_v2 ne sont pas encore accessibles par Microsoft pour le grand public. Les valeurs répertoriées sont destinées à être un point de départ et doivent être évalués par rapport à la réelle de la demande. L’avantage de SSD Ultra Azure est que les valeurs d’IOPS et de débit peuvent être adaptés sans avoir à arrêter la machine virtuelle ou arrêt de la charge de travail appliqués au système.   

> [!NOTE]
> Jusqu’ici, captures instantanées de stockage avec le stockage UltraSSD n’est pas disponible. Cela empêche l’utilisation de captures instantanées de machine virtuelle avec les Services de sauvegarde Azure

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez les pages suivantes :

- [Guide de haute disponibilité de SAP HANA pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).