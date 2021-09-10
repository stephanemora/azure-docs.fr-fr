---
title: Meilleures pratiques en matière de cache de système de fichiers Linux pour Azure NetApp Files | Microsoft Docs
description: Décrit les meilleures pratiques à suivre en matière de cache de système de fichiers pour Azure NetApp Files.
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
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: d0d097a13fa24fb62f70648a34edd27e3b5a0636
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203856"
---
# <a name="linux-filesystem-cache-best-practices-for-azure-netapp-files"></a>Meilleures pratiques en matière de cache de système de fichiers Linux pour Azure NetApp Files

Cet article aide à comprendre les meilleures pratiques en matière de cache de système de fichiers concernant Azure NetApp Files.  

## <a name="filesystem-cache-tunables"></a>Éléments réglables du cache du système de fichiers

Vous devez comprendre les facteurs suivants concernant les éléments réglables du cache du système de fichiers :  

* Le vidage d’un tampon compromis laisse les données dans un état propre utilisable pour de futures lectures jusqu’à ce que la pression de la mémoire conduise à une éviction.  
* Il existe trois déclencheurs pour une opération de vidage asynchrone :
    * Basée sur l’heure : lorsqu’une mémoire tampon atteint l’âge défini par cet élément e réglable, elle doit être marquée pour le nettoyage (autrement dit, le vidage ou l’écriture dans le stockage).
    * Pression de la mémoire : consultez [`vm.dirty_ratio | vm.dirty_bytes`](#vmdirty_ratio--vmdirty_bytes) pour plus d’informations.
    * Fermeture : lorsqu’un descripteur de fichier est fermé, toutes les mémoires tampons compromises sont vidées de façon asynchrone dans le stockage.

Ces facteurs sont contrôlés par quatre éléments réglables. Chaque élément réglable peut ajusté dynamiquement et de façon permanente au moyen de `tuned` ou `sysctl` dans le fichier `/etc/sysctl.conf`. Le paramétrage de ces variables améliore les performances des applications.  

> [!NOTE]
> Les informations présentées dans cet article ont été découvertes lors des exercices de validation SAP GRID et SAS Viya. En tant que tels, les éléments réglables sont basés sur les leçons tirées des exercices de validation. De nombreuses applications tirent également parti de l’optimisation de ces paramètres.

### `vm.dirty_ratio | vm.dirty_bytes` 

Ces deux éléments réglables définissent la quantité de RAM rendue utilisable pour les données modifiées mais pas encore écrites sur le stockage stable.  Quel que soit l’élément réglable défini, l’autre élément réglable est automatiquement mis à zéro. RedHat déconseille de mettre manuellement l’un des deux éléments réglables à zéro.  L’option `vm.dirty_ratio` (la valeur par défaut des deux) est définie par RedHat sur 20 % ou 30 % de la mémoire physique, selon le système d’exploitation, ce qui représente une part importante de l’empreinte mémoire des systèmes modernes. Envisagez de définir `vm.dirty_bytes` au lieu de `vm.dirty_ratio` pour obtenir une expérience plus cohérente quelle que soit la taille de la mémoire.  Par exemple, le travail en cours avec SAS GRID a déterminé 30 Mio comme étant un paramètre approprié pour les meilleures performances globales de charge de travail mixte. 

### `vm.dirty_background_ratio | vm.dirty_background_bytes` 

Ces éléments réglables définissent le point de départ où le mécanisme d’écriture différée de Linux commence à vider les blocs compromis vers un stockage stable. RedHat utilise par défaut 10 % de la mémoire physique, ce qui, sur un système à grande mémoire, représente une quantité importante de données à vider. Si l’on prend SAS GRID par exemple, historiquement la recommandation a été définie sur `vm.dirty_background` à 1/5 de la taille de `vm.dirty_ratio` ou `vm.dirty_bytes`. Compte tenu de l’agressivité avec laquelle le paramètre `vm.dirty_bytes` est défini pour SAS GRID, aucune valeur spécifique n’est définie ici.  

### `vm.dirty_expire_centisecs` 

Cet élément réglable définit l’âge d’un tampon compromis avant qu’il ne soit marqué pour une écriture asynchrone. Prenez la charge de travail CAS de SAS Viya par exemple. Une charge de travail éphémère à dominante d’écriture a montré que la définition de cette valeur à 300 centisecondes (3 secondes) était optimale, 3 000 centisecondes (30 secondes) étant la valeur par défaut.  

SAS Viya partage les données CAS en plusieurs petits blocs de quelques mégaoctets chacun.  Au lieu de fermer ces descripteurs de fichiers après avoir écrit des données sur chaque partition, les descripteurs restent ouverts et les mémoires tampons s’y trouvant sont mappées en mémoire par l’application.  Sans une fermeture, il n’y aura pas de vidage tant que la sollicitation de la mémoire ou les 30 secondes ne sont pas passées. L’attente de la pression de la mémoire s’est avérée sous-optimale, tout comme l’attente de l’expiration d’un long retardateur. Contrairement à SAS Grid, qui recherchait le meilleur débit global, SAS Viya a cherché à optimiser la bande passante en écriture.  

### `vm.dirty_writeback_centisecs` 

Le thread de vidage du noyau est chargé de vider de manière asynchrone les tampons compromis entre chaque mise en veille du thread de vidage.  Cet élément réglable définit la quantité passée en veille entre les vidages.  En prenant en compte la valeur de 3 secondes `vm.dirty_expire_centisecs` utilisée par SAS Viya, SAS définit cette élément réglable sur 100 centiseconde (1 seconde) au lieu de la valeur par défaut de 500 centisecondes (5 secondes) pour trouver les meilleures performances globales.

## <a name="impact-of-an-untuned-filesystem-cache"></a>Impact d’un cache de système de fichiers non réglé

Compte tenu des paramètres par défaut de la mémoire virtuelle et de la quantité de RAM dans les systèmes modernes, l’écriture différée peut ralentir d’autres opérations liées au stockage du point de vue du client spécifique qui gère cette charge de travail mixte.  Les symptômes suivants peuvent être attendus d’une machine Linux non réglée, à forte densité d’écriture et chargée en cache.  

* Les listes d’annuaires `ls` prennent suffisamment de temps pour paraître sans réponse.
* Le débit de lecture par rapport au système de fichiers diminue considérablement par rapport au débit d’écriture.
* `nfsiostat` signale les latences d’écriture **en secondes ou plus**.

Vous pouvez constater ce comportement uniquement sur *la machine Linux* qui effectue la charge de travail mixte à forte intensité d’écriture.  En outre, l’expérience est dégradée pour tous les volumes NFS montés sur un seul point de terminaison de stockage.  Si les montages proviennent de deux points de terminaison ou plus, seuls les volumes partageant un point de terminaison présentent ce comportement.

Il a été démontré que le réglage des paramètres de cache du système de fichiers, tel que décrit dans cette section, permet de résoudre ces problèmes.

## <a name="monitoring-virtual-memory"></a>Surveillance de la mémoire virtuelle

Pour comprendre ce qui se passe avec la mémoire virtuelle et l’écriture différée, considérez l’extrait de code et le résultat suivants.  *Dirty* représente la quantité de mémoire compromise dans le système, et *writeback* représente la quantité de mémoire activement écrite vers le stockage.  

`# while true; do echo "###" ;date ; egrep "^Cached:|^Dirty:|^Writeback:|file" /proc/meminfo; sleep 5; done`

La sortie suivante vient d’une expérience dans laquelle le `vm.dirty_ratio` et le ratio `vm.dirty_background` ont été définis sur 2 % et 1 % de la mémoire physique, respectivement.  Dans ce cas, le vidage a commencé à 3,8 Gio, soit 1 % du système de mémoire de 384 Gio.  L’écriture différée ressemble étroitement au débit d’écriture sur NFS. 

```
Cons
Dirty:                                    1174836 kB
Writeback:                         4 kB
###
Dirty:                                    3319540 kB
Writeback:                         4 kB
###
Dirty:                                    3902916 kB        <-- Writes to stable storage begins here
Writeback:                         72232 kB   
###
Dirty:                                    3131480 kB
Writeback:                         1298772 kB   
``` 

## <a name="next-steps"></a>Étapes suivantes  

* [Meilleures pratiques d’E/S directes Linux pour Azure NetApp Files](performance-linux-direct-io.md)
* [Meilleures pratiques Linux concernant les options de montage NFS pour Azure NetApp Files](performance-linux-mount-options.md)
* [Bonnes pratiques concernant la concurrence Linux pour Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Bonnes pratiques pour la lecture anticipée NFS Linux](performance-linux-nfs-read-ahead.md)
* [Bonnes pratiques pour les références SKU de machine virtuelle Azure](performance-virtual-machine-sku.md) 
* [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md) 
