---
title: Déplacer le stockage de macroordinateur à stockage Azure
description: Ressources de stockage Azure hautement évolutif peuvent aider à des organisations basées sur un macroordinateur migrer et moderniser des applications z14 IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4fea787e7fe20d60de91761811e1b69bebf3a010
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896350"
---
# <a name="move-mainframe-storage-to-azure"></a>Déplacer le stockage de macroordinateur à Azure

Pour exécuter des charges de travail mainframe sur Microsoft Azure, vous devez savoir comment les fonctionnalités du macroordinateur par rapport à Azure. Les ressources de stockage très évolutif peuvent aider les organisations à commencer à moderniser votre environnement sans abandonner les applications qu’elles dépendent.

Azure fournit des fonctionnalités de macroordinateur-type et de capacité de stockage qui est comparable au IBM z14 systèmes (le modèle plus récent à ce jour). Cet article vous indique comment obtenir des résultats comparables sur Azure.

## <a name="mainframe-storage-at-a-glance"></a>Stockage de macroordinateur en un clin de œil

Le macroordinateur IBM caractérise le stockage de deux manières. Le premier est un périphérique de stockage de l’accès direct (DASD). Le second est le stockage séquentiel. Pour la gestion du stockage, le macroordinateur fournit le sous-système de gestion de stockage (DFSMS) données facilité. Il gère les accès aux données pour les périphériques de stockage différents.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) fait référence à une unité distincte pour le stockage secondaire (hors de la mémoire) qui permet à une adresse unique à utiliser pour l’accès direct de données. À l’origine, le terme DASD appliqué à la rotation des disques, des tambours magnétiques ou des cellules de données. Toutefois, maintenant le terme peut s’applique également aux dispositifs de stockage SSD (SSD), les réseaux de stockage (SAN), connecté au réseau (NAS) de stockage et les lecteurs optiques. Dans le cadre de ce document, DASD fait référence à des disques, les réseaux SAN et des disques SSD en rotation.

Contrairement au stockage DASD, stockage séquentiel sur un macroordinateur fait référence aux appareils tels que les lecteurs de bande où données sont accessible à partir d’un point de départ, puis lues ou écrites dans une ligne.

Périphériques de stockage sont généralement connectés à l’aide d’une connexion fiber (FICON) ou sont accessibles directement sur le macroordinateur de bus d’e/s à l’aide [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), une technologie d’IBM pour les communications à haut débit entre les partitions sur un serveur avec un hyperviseur.

La plupart des systèmes mainframe, stockage distinct en deux types :

- *Stockage en ligne* (également connu en tant que stockage à chaud) est nécessaire pour les opérations quotidiennes. DASD stockage est généralement utilisé à cet effet. Toutefois, le stockage séquentiel, telles que les sauvegardes sur bande quotidiennes (logiques ou physiques), permet également à cet effet.

- *Stockage archive* (également connu sous le stockage à froid) n’est pas garanti d’être monté à un moment donné. Au lieu de cela, il est monté et accessible en fonction des besoins. Stockage archive est souvent implémenté à l’aide de sauvegardes sur bande séquentiel (logiques ou physiques) pour le stockage.

## <a name="mainframe-versus-io-latency-and-iops"></a>Macroordinateur et la latence d’e/s et les e/s

Macroordinateurs sont souvent utilisés pour les applications qui nécessitent de hautes performances d’e/s et une faible latence d’e/s. Cela à l’aide de connexions pour les périphériques d’e/s et HiperSockets FICON. HiperSockets permettent de connecter des applications et les périphériques directement au canal d’e/s d’un macroordinateur, latence en les microsecondes peut être obtenue.

## <a name="azure-storage-at-a-glance"></a>Stockage Azure en un clin de œil

Azure infrastructure-as-a-service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) options de stockage fournissent une capacité mainframe comparable.

Microsoft offre l’équivalent de plusieurs pétaoctets de stockage pour les applications hébergées dans Azure, et vous disposez de plusieurs options de stockage. Ces comprises entre le stockage SSD hautes performances et stockage d’objets blob de faible coût pour le stockage de masse et des archives. En outre, Azure fournit une option de redondance de données pour le stockage, une opération qui requiert davantage d’efforts pour la configurer dans un environnement de macroordinateurs.

Stockage Azure est disponible en tant que [disques Azure](/azure/virtual-machines/windows/managed-disks-overview), [Azure Files](/azure/storage/files/storage-files-introduction), et [objets BLOB Azure](/azure/storage/blobs/storage-blobs-overview) le tableau suivant résume également. En savoir plus sur [quand utiliser chaque](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Description</th><th>Utilisez-le quand vous désirez :</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Fournit une interface SMB, les bibliothèques clientes et un <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> interface qui autorise l’accès depuis n’importe où aux fichiers stockés.
</td>
<td><ul>
<li>Élever et déplacer une application vers le cloud lors de l’application utilise l’API de système de fichiers natif pour partager des données entre elle et d’autres applications s’exécutant dans Azure.</li>
<li>Store développement et outils de débogage qui doivent être accessibles à partir de plusieurs machines virtuelles.</li>
</ul>
</td>
</tr>
<tr><td>Objets blob Azure
</td>
<td>Fournit des bibliothèques clientes et une <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> interface qui permet à des données non structurées stockées et d’y accéder à grande échelle dans les objets BLOB de blocs. Prend également en charge <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> pour les solutions d’analytique d’entreprise Big Data.
</td>
<td><ul>
<li>Prend en charge les scénarios de diffusion en continu et à accès aléatoire dans une application.</li>
<li>Avoir accès aux données d’application à partir de n’importe quel endroit.</li>
<li>Générer un lac de données d’entreprise sur Azure et effectuer l’analytique des données volumineuses.</li>
</ul></td>
</tr>
<tr><td>Disques Azure
</td>
<td>Fournit des bibliothèques clientes et une <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> interface qui permet aux données stockées et d’y accéder à partir d’un disque dur virtuel attaché façon persistante.
</td>
<td><ul>
<li>Lift- and -shift des applications qui utilisent les API du système de fichiers natif pour lire et écrire des données sur les disques persistants.</li>
<li>Store les données qui n’est pas nécessaire pour être accessible depuis l’extérieur de la machine virtuelle à laquelle le disque est attaché.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure à chaud (en ligne) et le stockage à froid (archive)

Le type de stockage d’un système donné dépend de la configuration requise du système, y compris la taille de stockage, le débit et e/s. Pour le stockage DASD-type sur un macroordinateur, les applications sur Azure utilisent le stockage Azure Disks lecteur à la place. Pour le stockage d’archive de macroordinateur, stockage d’objets blob est utilisé sur Azure.

Les disques SSD offrent les meilleures performances de stockage sur Azure. Les options suivantes sont disponibles (au moment de la rédaction de ce document) :

| Type         | Taille           | E/S par seconde                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 Go à 64 To  | e/s de 1 200 à 160,000 |
| SSD Premium  | 32 Go à 32 To | 12 à 15 000 IOPS     |
| SSD Standard | 32 Go à 32 To | 12 à 2 000 IOPS      |

Stockage d’objets BLOB fournit le plus grand volume de stockage sur Azure. En plus de la taille de stockage, Azure propose un stockage à la fois managé et non managées. Avec stockage géré, Azure s’occupe de la gestion des comptes de stockage sous-jacents. Avec stockage non gérés, l’utilisateur assume la responsabilité de configurer des comptes de stockage Azure de la taille appropriée satisfaire les besoins de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Migration mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe réhébergement sur des Machines virtuelles Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Déplacer le calcul de macroordinateur vers Azure](mainframe-compute-Azure.md)
- [Quand utiliser des objets BLOB Azure, Azure Files ou disques Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Disque SSD disques gérés standard pour les charges de travail de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Ressources d’IBM

- [Parallel Sysplex sur IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS et la fonctionnalité de couplage faible : Notions avancées](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Création des utilisateurs requis pour l’installation d’une fonctionnalité DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [DB2icrt - Commande Créer une instance](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale Solution de base de données de cluster](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBMData Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government cloud pour applications mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft et FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Plus de ressources de migration

- [Platform Modernization Alliance : IBM Db2 sur Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Virtual Datacenter: Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [iSCSI GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
