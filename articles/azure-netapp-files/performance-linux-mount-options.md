---
title: Bonnes pratiques pour les options de montage NFS Linux pour Azure NetApp Files | Microsoft Docs
description: Décrit les options de montage et les bonnes pratiques relatives à leur utilisation avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: b-juche
ms.openlocfilehash: f62c22c5615b1494ad8c1ebb966db9f1c25a8df7
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111441915"
---
# <a name="linux-nfs-mount-options-best-practices-for-azure-netapp-files"></a>Bonnes pratiques pour les options de montage NFS Linux pour Azure NetApp Files

Cet article vous aide à comprendre les options de montage et les bonnes pratiques d’utilisation avec Azure NetApp Files.

## `Nconnect` 

L’utilisation de l’option de montage `nconnect` vous permet de spécifier le nombre de connexions (flux réseau) qui doivent être établies entre le client NFS et le point de terminaison NFS, dans une limite de 16 connexions. Normalement, un client NFS utilise une seule connexion entre lui-même et le point de terminaison.  En augmentant le nombre de flux réseau, vous augmentez considérablement la limite maximale des E/S et celle du débit. D’après nos tests, `nconnect=8` permet d’obtenir les meilleures performances.  

Lorsque vous préparerez un environnement SAS GRID multinœud pour la mise en production, vous remarquerez peut-être une diminution répétable de 30 % du temps d’exécution faisant passer celui-ci de 8 heures à 5 heures et demi : 

| Option de montage | Durée d’exécution des tâches |
|-|-|
| Aucune `nconnect` | 8 heures |
| `nconnect=8`  | 5 heures et demi | 

Les deux ensembles de tests ont utilisé la même machine virtuelle E32-8_v4 et RHEL 8.3, avec la valeur de lecture par anticipation définie sur 15 Mio.

Lorsque vous utilisez `nconnect`, gardez les règles suivantes à l’esprit :

* `nconnect` est pris en charge par Azure NetApp Files sur toutes les principales distributions Linux, mais uniquement sur les versions les plus récentes :

    | Version Linux | NFSv3 (version minimale) | NFSv 4.1 (version minimale) |
    |-|-|-|
    | Redhat Enterprise Linux | RHEL8.3 | RHEL8.3 |
    | SUSE | SLES12SP4 ou SLES15SP1 | SLES15SP2 |
    | Ubuntu | Ubuntu18.04 |          |

    > [!NOTE]
    > SLES15SP2 est la version SUSE minimale dans laquelle `nconnect` est pris en charge par Azure NetApp Files pour NFSv4.1.  Toutes les autres versions spécifiées sont les premières versions à avoir inclus la fonctionnalité `nconnect`.

* Tous les montages effectués à partir d’un même point de terminaison hériteront du paramètre `nconnect` de la première exportation montée, comme indiqué dans les scénarios suivants : 

    Scénario 1 : `nconnect` est utilisé par le premier montage. Par conséquent, tous les montages situés sur le même point de terminaison utilisent `nconnect=8`.

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.10.10.10:/volume2 /mnt/volume2`
    * `mount 10.10.10.10:/volume3 /mnt/volume3`

    Scénario 2 : `nconnect` n’est pas utilisé par le premier montage. Par conséquent, aucun montage situé sur le même point de terminaison n’utilise `nconnect`, même si `nconnect` est spécifié.

    * `mount 10.10.10.10:/volume1 /mnt/volume1`
    * `mount 10.10.10.10:/volume2 /mnt/volume2 -o nconnect=8`
    * `mount 10.10.10.10:/volume3 /mnt/volume3 -o nconnect=8`

    Scénario 3 : Les paramètres `nconnect` ne sont pas propagés sur des points de terminaison de stockage distincts.  `nconnect` est utilisé par le montage provenant de `10.10.10.10`, mais pas par le montage provenant de `10.12.12.12`.

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.12.12.12:/volume2 /mnt/volume2`

* `nconnect` peut être utilisé pour augmenter la concurrence de stockage à partir d’un client donné. 

Pour plus d’informations, consultez [Bonnes pratiques concernant la concurrence Linux pour Azure NetApp Files](performance-linux-concurrency-session-slots.md).

## <a name="rsize-and-wsize"></a>`Rsize` et `Wsize`
 
Les indicateurs `rsize` et `wsize` définissent la taille de transfert maximale d’une opération NFS.  Si `rsize` ou `wsize` ne sont pas spécifiés sur le montage, le client et le serveur négocient la plus grande taille prise en charge par les deux.   À l’heure actuelle, Azure NetApp Files et les distributions Linux modernes prennent en charge des tailles de lecture et d’écriture pouvant aller jusqu’à 1 048 576 octets (1 Mio).   Toutefois, pour optimiser l’ensemble du débit et de la latence, Azure NetApp Files recommande de définir `rsize` et `wsize` sur une valeur inférieure ou égale à 262 144 octets (256 Ko). Vous pouvez constater que la latence augmente et que le débit baisse lorsque vous utilisez `rsize` et `wsize` avec une valeur supérieure à 256 Kio. 

Par exemple, la rubrique [Déployer un système Scale-out SAP HANA avec le nœud de secours sur des machines virtuelles Azure à l’aide d’Azure NetApp Files sur SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md#mount-the-azure-netapp-files-volumes) explique la valeur maximale de 256 Kio pour `rsize` et `wsize` de la façon suivante :

```
sudo vi /etc/fstab
# Add the following entries
10.23.1.5:/HN1-data-mnt00001 /hana/data/HN1/mnt00001  nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-data-mnt00002 /hana/data/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-log-mnt00001 /hana/log/HN1/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
```
 
Par exemple, SAS Viya recommande une taille de lecture et d’écriture de 256 Kio, et [SAS GRID](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/606973/highlight/true#M17740) limite `r/wsize` à 64 Kio, tout en augmentant les performances de lecture grâce à l’augmentation de la lecture par anticipation pour les montages NFS.  <!-- For more information on readahead, see the article “NFS Readahead”. --> 

Les remarques suivantes s’appliquent à l’utilisation de `rsize` et de `wsize` :

* La taille des opérations d’E/S aléatoires sont souvent inférieures à celles des options de montage `rsize` et `wsize`. En conséquence, elles ne seront pas limitées.
* Lorsque vous utilisez le cache du système de fichiers, les E/S séquentielles ont la taille prévue par les options de montage `rsize` et `wsize`, sauf si la taille du fichier est inférieure à `rsize` et à `wsize`.
* Les opérations qui contournent le cache du système de fichiers, même si elles sont toujours limitées par les options de montage `rsize` et `wsize`, n’auront pas nécessairement la taille maximale spécifiée par `rsize` ou `wsize`.  Ceci est important lorsque vous utilisez des générateurs de charges de travail qui ont l’option `directio`.

*Avec Azure NetApp Files, pour optimiser l’ensemble du débit et de la latence, il est recommandé de définir `rsize` et `wsize` sur une valeur inférieure ou égale à 262 144 octets.*

## <a name="close-to-open-consistency-and-cache-attribute-timers"></a>Cohérence de bout en bout et minuteurs d’attribut de cache   

NFS utilise un modèle de cohérence approximative. La cohérence est approximative, car l’application n’a pas à accéder au stockage partagé pour récupérer les données à chaque fois qu’elle en a besoin, ce qui aurait un impact considérable sur les performances de l’application.  Il existe deux mécanismes qui gèrent ce processus : les minuteurs d’attribut de cache et la cohérence de bout en bout.

*Si le client est entièrement propriétaire des données, c’est-à-dire que celles-ci ne sont pas partagées entre plusieurs nœuds ou systèmes, la cohérence est garantie.* Dans ce cas, vous pouvez réduire les opérations d’accès au stockage `getattr` pour accélérer l’application en désactivant la cohérence de bout en bout (`cto`) (avec `nocto` comme option de montage) et en rallongeant les délais d’expiration pour la gestion du cache d’attributs (`actimeo=600` comme option de montage remplace la valeur par défaut du minuteur (`acregmin=3,acregmax=30,acdirmin=30,acdirmax=60`) par 10m). Dans certains tests, `nocto` réduit les appels d’accès `getattr` d’environ 65 à 70 %, et l’ajustement de `actimeo` réduit les appels de 20 à 25 % supplémentaires.

### <a name="how-attribute-cache-timers-work"></a>Fonctionnement des minuteurs du cache d’attributs  

Les attributs `acregmin`, `acregmax`, `acdirmin` et `acdirmax` contrôlent la cohérence du cache. Les deux premiers attributs contrôlent la durée d’approbation des attributs des fichiers. Les deux derniers attributs contrôlent la durée d’approbation des attributs du fichier d’annuaire (taille du répertoire, propriété du répertoire, autorisations du répertoire).  Les attributs `min` et `max` définissent la durée minimale et la durée maximale pendant lesquelles les attributs d’un répertoire, les attributs d’un fichier ou le contenu du cache d’un fichier sont considérés comme approuvés. Entre `min` et `max`, un algorithme est utilisé pour définir la durée pendant laquelle une entrée mise en cache est approuvée.

Prenons par exemple les valeurs par défaut de `acregmin` et de `acregmax` qui sont de 3 et 30 secondes, respectivement.  Par exemple, les attributs sont évalués plusieurs fois pour les fichiers d’un répertoire.  Au bout de 3 secondes, le service NFS est interrogé à des fins d’actualisation.  Si les attributs sont considérés comme valides, le client double la durée d’approbation : 6 secondes, 12 secondes, 24 secondes, puis 30 secondes (30 étant la valeur maximale).  À partir de là, et jusqu’à ce que les attributs mis en cache soient considérés comme obsolètes (ce qui correspondra au début d’un nouveau cycle), l’approbation est définie sur 30 secondes (valeur spécifiée par `acregmax`).

Il existe d’autres cas dans lesquels cet ensemble d’options de montage peut être bénéfique, même si les clients ne sont pas entièrement propriétaires. C’est le cas, par exemple, quand les clients utilisent des données en lecture seule et quand la mise à jour des données est gérée par un autre biais.  Pour les applications qui utilisent des grilles de clients comme EDA, l’hébergement web et l’affichage de films, et qui ont des jeux de données relativement statiques (outils ou bibliothèques EDA, contenu web, données de texture), en général, le jeu de données est en grande partie mis en cache sur les clients. Il y a très peu de lectures et aucune écriture. Un grand nombre d’appels `getattr`/access reviendront au stockage.  Ces jeux de données sont généralement mis à jour par le biais d’un autre client qui monte les systèmes de fichiers et envoie (push) régulièrement des mises à jour de contenu.

Dans ces cas-là, il existe un retard de sélection du nouveau contenu, ce qui fait que l’application fonctionne avec des données potentiellement obsolètes.  `nocto` et `actimeo` peuvent alors être utilisés pour contrôler la période durant laquelle les données obsolètes peuvent être gérées.  Par exemple, dans les outils et les bibliothèques EDA, `actimeo=600` fonctionne bien, car en général, ces données ne sont pas mises à jour fréquemment.  Pour les petits hébergements web où les clients ont besoin de voir les mises à jour de leurs données pendant qu’ils modifient leurs sites, `actimeo=10` peut être acceptable. Pour les sites web à grande échelle où le contenu est envoyé (push) vers plusieurs systèmes de fichiers, `actimeo=60` peut être acceptable.

Dans ces cas-là, l’utilisation de ces options de montage réduit considérablement la charge de travail dans le stockage. Par exemple, une expérience EDA récente a réduit le nombre d’E/S par seconde du volume d’outil, permettant de passer de >150 K à ~6 K. Les applications peuvent s’exécuter beaucoup plus rapidement, car elles peuvent faire confiance aux données en mémoire. Le temps d’accès à la mémoire s’exprime en nanosecondes, par rapport aux centaines de microsecondes nécessaires pour `getattr`/access sur un réseau rapide.

### <a name="close-to-open-consistency"></a>Cohérence de bout en bout 

La cohérence de bout en bout (option de montage `cto`) garantit que, quel que soit l’état du cache, lors de l’ouverture, les données les plus récentes d’un fichier seront toujours présentées à l’application.  

* Lorsqu’un répertoire est analysé (`ls`, `ls -l` par exemple), un certain ensemble d’appels PRC sont émis.  
    Le serveur NFS partage sa vue du système de fichiers. Tant que `cto` est utilisé par tous les clients NFS accédant à une exportation NFS donnée, tous les clients verront la même liste de fichiers et de répertoires.  L’actualisation des attributs des fichiers dans le répertoire est contrôlée par les [minuteurs du cache d’attributs](#how-attribute-cache-timers-work).  En d’autres termes, tant que `cto` est utilisé, les fichiers s’affichent pour les clients distants dès que le fichier est créé et qu’il arrive dans le stockage.
* Lorsqu’un fichier est ouvert, son contenu est garanti à jour du point de vue du serveur NFS.  
    S’il existe une condition de concurrence dans laquelle le contenu n’a pas terminé le vidage de la machine 1 lorsqu’un fichier est ouvert sur la machine 2, la machine 2 recevra uniquement les données qui étaient présentes sur le serveur au moment de l’ouverture. Dans ce cas, la machine 2 ne récupère pas plus de données à partir du fichier tant que la valeur du minuteur `acreg` n’est pas atteinte. Une fois cette valeur atteinte, la machine 2 vérifie à nouveau la cohérence de cache à partir du serveur.  Vous pouvez observer ce scénario à l’aide d’un `-f` de fin à partir de la machine 2 pendant que le fichier est encore en cours d’écriture à partir de la machine 1.

### <a name="no-close-to-open-consistency"></a>Aucune cohérence de bout en bout  

Lorsqu’aucune cohérence de bout en bout (`nocto`) n’est utilisée, le client approuve l’actualisation de sa vue actuelle du fichier et du répertoire jusqu’à ce que la valeur des minuteurs d’attribut de cache soient atteintes.  

* Lorsqu’un répertoire est analysé (`ls`, `ls -l` par exemple), un certain ensemble d’appels PRC sont émis.  
    Le client n’émettra qu’un seul appel au serveur pour obtenir la liste actuelle des fichiers lorsque la valeur du minuteur du cache `acdir` sera atteinte.  Dans ce cas, les fichiers et les répertoires créés récemment ne s’afficheront pas, et ceux supprimés récemment continueront de s’afficher.  

* Lorsqu’un fichier est ouvert, tant qu’il se trouve toujours dans le cache, son contenu mis en cache (s’il y en a) est retourné sans validation de la cohérence avec le serveur NFS.

## <a name="next-steps"></a>Étapes suivantes  

* [Bonnes pratiques concernant la concurrence Linux pour Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md) 
