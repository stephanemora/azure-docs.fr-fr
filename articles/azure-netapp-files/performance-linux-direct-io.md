---
title: Bonnes pratiques d’E/S directes Linux pour Azure NetApp Files | Microsoft Docs
description: Décrit les E/S directes Linux et les bonnes pratiques à suivre pour Azure NetApp Files.
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
ms.openlocfilehash: 6497d91169c7530251a473e8e06c26ce411e0e6a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233465"
---
# <a name="linux-direct-io-best-practices-for-azure-netapp-files"></a>Bonnes pratiques d’E/S directes Linux pour Azure NetApp Files

Cet article aide à comprendre les bonnes pratiques d’E/S pour Azure NetApp Files.  

## <a name="direct-io"></a>E/S directes

 Les E/S directes sont le paramètre le plus courant utilisé dans le benchmarking des performances de stockage. Il est pris en charge par FIO et Vdbench. DISKSPD offre la prise en charge de la construction similaire des E/S mappées en mémoire. Avec les E/S directes, le cache du système de fichiers est contourné, les opérations de copie d’accès direct à la mémoire sont évitées et les tests de stockage sont effectués rapidement et simplement.  

L’utilisation du paramètre d’E/S directes facilite les tests de stockage. Aucune donnée n’est lue à partir du cache du système de fichiers sur le client. En tant que tel, le test insiste véritablement sur le protocole et le service de stockage lui-même, plutôt que sur les vitesses d’accès à la mémoire. En outre, sans les copies de mémoire DMA, les opérations de lecture et d’écriture sont efficaces du point de vue du traitement. 

Prenez la commande `dd` Linux comme exemple de charge de travail. Sans l'indicateur `odirect` facultatif, toutes les E/S générées par `dd` sont servies à partir du cache de tampon Linux. Les lectures avec les blocs déjà en mémoire ne sont pas récupérées à partir du stockage. Les lectures qui aboutissent à un échec de la mise en cache des tampons sont lues à partir du stockage à l’aide de la lecture continue NFS avec des résultats variables, en fonction de facteurs tels que les `rsize` réglables de montage et de lecture anticipée du client. Lorsque les écritures sont envoyées via le cache des tampons, elles utilisent un mécanisme d’écriture différée, qui n’est pas réglé et utilise une quantité significative de parallélisme pour envoyer les données au périphérique de stockage. Vous pouvez essayer d’exécuter deux flux indépendants d’E/S, un `dd` pour les lectures et un`dd` pour les écritures. En fait, le système d’exploitation, non réglé, favorise les écritures par rapport aux lectures et utilise plus de parallélisme.

Hormis la base de données, peu d’applications utilisent des E/S directes. Au lieu de cela, elles choisissent de tirer parti des avantages d’un cache de mémoire volumineux pour les lectures répétées et d’un cache d’écriture différée pour les écritures asynchrones. En bref, l’utilisation d’E/S directes transforme le test en un micro-benchmark *si* l’application en cours de synthèse utilise le cache du système de fichiers.  

Voici quelques bases de données qui prennent en charge les E/S directes : 

* Oracle 
* SAP HANA
* MySQL (moteur de stockage InnoDB)
* RocksDB
* PostgreSQL
* Teradata

## <a name="best-practices"></a>Meilleures pratiques 

Le test avec `directio` est un excellent moyen de comprendre les limites du service de stockage et du client. Pour mieux comprendre comment l’application elle-même se comporte (si l’application n’utilise pas `directio`), vous devez également exécuter des tests via le cache du système de fichiers.

## <a name="next-steps"></a>Étapes suivantes  

* [Meilleures pratiques en matière de cache de système de fichiers Linux pour Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Meilleures pratiques Linux concernant les options de montage NFS pour Azure NetApp Files](performance-linux-mount-options.md)
* [Bonnes pratiques concernant la concurrence Linux pour Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Bonnes pratiques pour la lecture anticipée NFS Linux](performance-linux-nfs-read-ahead.md)
* [Bonnes pratiques pour les références SKU de machine virtuelle Azure](performance-virtual-machine-sku.md) 
* [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md) 
