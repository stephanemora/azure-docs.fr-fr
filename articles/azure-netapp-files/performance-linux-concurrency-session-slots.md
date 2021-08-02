---
title: Meilleures pratiques Linux en matière de concurrence pour Azure NetApp Files – Emplacements de session et entrées de table d’emplacements | Microsoft Docs
description: Décrit les meilleures pratiques relatives aux emplacements de session et aux entrées de table d’emplacements pour le protocole NFS d’Azure NetApp Files.
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
ms.date: 06/03/2021
ms.author: b-juche
ms.openlocfilehash: 3158d4fae313afcb1fef69ba7a2728df4d235175
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525333"
---
# <a name="linux-concurrency-best-practices-for-azure-netapp-files---session-slots-and-slot-table-entries"></a>Meilleures pratiques Linux en matière de concurrence pour Azure NetApp Files – Emplacements de session et entrées de table d’emplacements

Cet article vous aide à comprendre les meilleures pratiques en matière de concurrence relatives aux emplacements de session et aux entrées de table d’emplacements pour le protocole NFS d’Azure NetApp Files. 

## <a name="nfsv3"></a>NFSv3

NFSv3 n’a pas de mécanisme de négociation de la concurrence entre le client et le serveur. Le client et le serveur définissent chacun leur limite sans consulter l’autre.  Pour des performances optimales, vous devez aligner le nombre maximal d’entrées de table d’emplacements `sunrpc` côté client avec celui pris en charge sans résistance sur le serveur.  Lorsqu’un client dépasse la capacité de la pile réseau du serveur à traiter une charge de travail, le serveur répond en diminuant la taille de la fenêtre pour la connexion, ce qui n’est pas un scénario idéal pour les performances.

Par défaut, les noyaux Linux modernes définissent la taille de l’entrée de table d’emplacements `sunrpc` par connexion `sunrpc.max_tcp_slot_table_entries` comme prenant en charge 65 536 opérations en suspens, comme indiqué dans le tableau suivant. 

| Serveur NFSv3 d’Azure NetApp Files <br> Nombre maximal de contextes d’exécution par connexion | Client Linux <br> Nombre maximal par défaut d’entrées de table d’emplacements `sunrpc` par connexion |
|-|-|
| 128 | 65 536 |

Ces entrées de table d’emplacements définissent les limites de la concurrence. Des valeurs aussi élevées ne sont pas nécessaires.  Par exemple, en utilisant une théorie de mise en file d’attente connue sous le nom de *Loi de Little*, vous constaterez que le taux d’E/S est déterminé par la concurrence (autrement dit, les E/S en suspens) et la latence. En tant que tel, l’algorithme prouve que 65 536 emplacements sont des ordres de grandeur supérieurs à ce qui est nécessaire pour gérer des charges de travail même extrêmement exigeantes.

`Littles Law: (concurrency = operation rate × latency in seconds)`

Un niveau de concurrence aussi bas que 155 suffit pour réaliser 155 000 opérations NFS Oracle DB par seconde à l’aide du protocole NFS Oracle Direct, une technologie dont le concept est similaire à l’option de montage `nconnect` :

* Pour une latence de 0,5 ms, une concurrence de 55 est nécessaire pour atteindre 110 000 IOPS.
* Pour une latence de 1 ms, une concurrence de 155 est nécessaire pour atteindre 155 000 IOPS.

![Courbe de latence d’Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

Pour plus d’informations, consultez [Performances des bases de données Oracle sur des volumes uniques Azure NetApp Files](performance-oracle-single-volumes.md).

Le paramètre `sunrpc.max_tcp_slot_table_entries` est un paramètre de réglage au niveau de la connexion.  *Il est recommandé de définir cette valeur sur 128 ou moins par connexion, sans dépasser 3 000 emplacements dans tout l’environnement.*

### <a name="examples-of-slot-count-based-on-concurrency-recommendation"></a>Exemples de nombre d’emplacements basés sur la recommandation de concurrence 

Les exemples de cette section illustrent le nombre d’emplacements en fonction de la recommandation de concurrence.  

#### <a name="example-1--one-nfs-client-65536-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128-based-on-the-server-side-limit-of-128"></a>Exemple 1 : Un client NFS, 65 536 `sunrpc.max_tcp_slot_table_entries` et pas de `nconnect` pour une concurrence maximale de 128 basée sur la limite de 128 côté serveur

L’exemple 1 est basé sur une seule charge de travail client avec la valeur par défaut `sunrpc.max_tcp_slot_table_entry` de 65 536 et une seule connexion réseau, c’est-à-dire sans `nconnect`.  Dans ce cas, une concurrence de 128 est réalisable.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP`) 
        * En théorie, le client ne peut pas émettre plus de 65 536 requêtes en mode Flighting vers le serveur par connexion.
        * Le serveur n’acceptera pas plus de 128 requêtes en mode Flighting à partir de cette seule connexion.

#### <a name="example-2--one-nfs-client-128-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128"></a>Exemple 2 : Un client NFS, 128 `sunrpc.max_tcp_slot_table_entries` et pas de `nconnect` pour une concurrence maximale de 128

L’exemple 2 est basé sur une seule charge de travail client avec une valeur `sunrpc.max_tcp_slot_table_entry` de 128, mais sans l’option de montage `nconnect`.  Avec ce paramètre, une concurrence de 128 est réalisable à partir d’une seule connexion réseau.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * Le client n’émettra pas plus de 128 requêtes en mode Flighting vers le serveur par connexion.
        * Le serveur n’acceptera pas plus de 128 requêtes en mode Flighting à partir de cette seule connexion.

#### <a name="example-3--one-nfs-client-100-sunrpcmax_tcp_slot_table_entries-and-nconnect8-for-a-maximum-concurrency-of-800"></a>Exemple 3 : Un client NFS, 100 `sunrpc.max_tcp_slot_table_entries` et `nconnect=8` pour une concurrence maximale de 800

L’exemple 3 est basé sur une seule charge de travail client, mais avec une valeur `sunrpc.max_tcp_slot_table_entry` inférieure de 100.  Cette fois-ci, l’option de montage `nconnect=8` utilisée répartit la charge de travail entre huit connexions.  Avec ce paramètre, une concurrence de 800 est réalisable sur les 8 connexions.  Cette quantité correspond à la concurrence nécessaire pour atteindre 400 000 IOPS.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP), Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)… Connection 8 (10.10.10.10:2049, 10.10.10.11:7321,TCP)`
    * Connexion 1
        * Le client n’émettra pas plus de 100 requêtes en mode Flighting vers le serveur à partir de cette connexion.
        * Le serveur ne doit accepter plus de 128 requêtes en mode Flighting du client pour cette connexion.
    * Connexion 2
        * Le client n’émettra pas plus de 100 requêtes en mode Flighting vers le serveur à partir de cette connexion.
        * Le serveur ne doit accepter plus de 128 requêtes en mode Flighting du client pour cette connexion.
    * `…`
    * `…`
    * Connexion 8
        * Le client n’émettra pas plus de 100 requêtes en mode Flighting vers le serveur à partir de cette connexion.
        * Le serveur ne doit accepter plus de 128 requêtes en mode Flighting du client pour cette connexion.

#### <a name="example-4--250-nfs-clients-8-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-2000"></a>Exemple 4 : 250 clients NFS, 8 `sunrpc.max_tcp_slot_table_entries` et pas de `nconnect` pour une concurrence maximale de 2000

L’exemple 4 utilise la valeur `sunrpc.max_tcp_slot_table_entry` réduite par client de 8 pour un environnement EDA de 250 machines. Dans ce scénario, une concurrence de 2 000 est atteinte dans l’ensemble de l’environnement, une valeur plus que suffisante pour gérer 4 000 Mio/s d’une charge de travail EDA back-end.

* `NFS_Server=10.10.10.10, NFS_Client1=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * Le client n’émettra pas plus de 8 requêtes en mode Flighting vers le serveur par connexion.
        * Le serveur n’acceptera pas plus de 128 requêtes en mode Flighting à partir de cette seule connexion.
* `NFS_Server=10.10.10.10, NFS_Client2=10.10.10.12`
    * `Connection (10.10.10.10:2049, 10.10.10.12:7820,TCP) `
        * Le client n’émettra pas plus de 8 requêtes en mode Flighting vers le serveur par connexion.
        * Le serveur n’acceptera pas plus de 128 requêtes en mode Flighting à partir de cette seule connexion.
* `…`
* `…`
* `NFS_Server=10.10.10.10, NFS_Client250=10.10.11.13`
    * `Connection (10.10.10.10:2049, 10.10.11.13:4320,TCP) `
        * Le client n’émettra pas plus de 8 requêtes en mode Flighting vers le serveur par connexion.
        * Le serveur n’acceptera pas plus de 128 requêtes en mode Flighting à partir de cette seule connexion.

Lorsque vous utilisez NFSv3, *vous devez faire en sorte que le nombre d’emplacements de point de terminaison de stockage soit inférieur ou égal à 2 000*. Il est préférable de définir la valeur par connexion pour `sunrpc.max_tcp_slot_table_entries` sur moins de 128 lorsqu’une application effectue un scale-out sur plusieurs connexions réseau (`nconnect` et HPC en général, et EDA en particulier).  

### <a name="how-to-calculate-the-best-sunrpcmax_tcp_slot_table_entries"></a>Comment calculer le meilleur `sunrpc.max_tcp_slot_table_entries` 

En utilisant la *loi de Little*, vous pouvez calculer le nombre total d’entrées de table d’emplacements nécessaire. En général, tenez compte des facteurs suivants :  

* Les charges de travail de Scale-out sont souvent principalement de nature séquentielle et de grande taille.
* Les charges de travail de base de données, notamment OLTP, sont souvent de nature aléatoire. 

Le tableau suivant présente un exemple d’étude de la concurrence avec des latences arbitraires fournies :

|     Taille des E/S    |     Latence    |     E/S ou débit    |     Accès concurrentiel    |
|-|-|-|-|
|     8 Kio    |     0,5 ms    |     110 000 IOPS \| 859 Mio/s    |     55    |
|     8 Kio    |     2 ms    |     400 000 IOPS \| 3 125 Mio/s    |     800    |
|     256 Kio    |     2 ms    |     16 000 IOPS \| 4 000 Mio/s    |     32    |
|     256 Kio    |     4 ms    |     32 000 IOPS \| 8 000 Mio/s    |     128    |

### <a name="how-to-calculate-concurrency-settings-by-connection-count"></a>Comment calculer les paramètres de concurrence par nombre de connexions

Par exemple, la charge de travail est une batterie de serveurs EDA, et 200 clients dirigent tous la charge de travail vers le même point de terminaison de stockage (un point de terminaison de stockage est une adresse IP de stockage) : vous calculez le taux d’E/S requis et divisez la concurrence sur l’ensemble de la batterie.

Supposons que la charge de travail est de 4 000 Mio/s avec une taille d’opération moyenne de 256 kio et une latence moyenne de 10 ms. Pour calculer la concurrence, utilisez la formule suivante :

`(concurrency = operation rate × latency in seconds)`

Le calcul se traduit par une concurrence de 160 : 
 
`(160 = 16,000 × 0.010)`

Étant donné le besoin de 200 clients, vous pouvez sans risque définir `sunrpc.max_tcp_slot_table_entries` sur 2 par client pour atteindre les 4 000 Mio/s.  Toutefois, vous pouvez décider de prévoir une marge supplémentaire en définissant le nombre par client sur 4 ou même 8, tout en respectant le plafond recommandé de 2 000 emplacements. 

### <a name="how-to-set-sunrpcmax_tcp_slot_table_entries-on-the-client"></a>Comment définir `sunrpc.max_tcp_slot_table_entries` sur le client

1.  Ajoutez `sunrpc.max_tcp_slot_table_entries=<n>` au fichier config `/etc/sysctl.conf`.   
    Pendant le paramétrage, si une valeur inférieure à 128 est jugée optimale, remplacez 128 par le nombre approprié.
2.  Exécutez la commande suivante :   
    `$ sysctl -p`
3.  Montez (ou remontez) tous les systèmes de fichiers NFS, car le paramétrable s’applique uniquement aux montages effectués après la définition du paramétrable.

## <a name="nfsv41"></a>NFSv4.1 

Dans NFSv4.1, les sessions définissent la relation entre le client et le serveur. Que les systèmes de fichiers NFS montés se trouvent sur une ou plusieurs connexions (comme c’est le cas avec `nconnect`), les règles de la session s’appliquent. Lors de la configuration de la session, le client et le serveur négocient le nombre maximal de requêtes pour la session, en choisissant la plus faible des deux valeurs prises en charge. Azure NetApp Files prend en charge 180 requêtes en suspens, et les clients Linux en prennent en charge 64 par défaut. Le tableau suivant indique les limites de la session : 

| Serveur NFSv4.1 d’Azure NetApp Files <br>  Nombre maximal de commandes par session | Client Linux <br>  Nombre maximal de commandes par session par défaut    | Nombre maximal de commandes négocié pour la session |
|-|-|-|
|     180    |     64    |     64    |

Bien que les clients Linux utilisent par défaut 64 requêtes maximum par session, la valeur de `max_session_slots` est réglable.  Un redémarrage est nécessaire pour que les modifications prennent effet.  Utilisez la commande `systool -v -m nfs` pour connaître le maximum actuel utilisé par le client.  Pour que la commande fonctionne, au moins un montage NFSv4.1 doit être en place :

```
$ systool -v -m nfs
{
Module = "nfs"
…
  Parameters:
…
    max_session_slots   = "64"   
…
}
```

Pour régler `max_session_slots`, créez un fichier config sous `/etc/modprobe.d` comme tel.  Assurez-vous qu’aucun « guillemets » n’est présent pour la ligne dans le fichier. Sinon, l’option ne prendra pas effet.

`$ echo “options nfs max_session_slots=180” > /etc/modprobe.d/nfsclient.conf`
`$ reboot`

Azure NetApp Files limite chaque session à 180 commandes maximum. Par conséquent, considérez 180 comme la valeur maximale actuellement configurable.  Le client ne sera pas en mesure d’atteindre une concurrence supérieure à 128, sauf si la session est divisée entre plusieurs connexions, car Azure NetApp Files limite chaque connexion à 128 commandes NFS maximum.  Pour obtenir plus d’une connexion, l’option de montage `nconnect` est recommandée, et une valeur de deux ou plus est requise.

### <a name="examples-of-expected-concurrency-maximums"></a>Exemples de limites de concurrence attendues

Les exemples de cette section illustrent les valeurs maximales de concurrence attendues.  

#### <a name="example-1--64-max_session_slots-and-no-nconnect"></a>Exemple 1 : 64 `max_session_slots` et pas de `nconnect`

L’exemple 1 est basé sur le paramètre par défaut de 64 `max_session_slots` et pas de `nconnect`. Avec ce paramètre, une concurrence de 64 est réalisable, le tout à partir d’une seule connexion réseau.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * Le client n’émettra pas plus de 64 requêtes en mode Flighting vers le serveur pour la session.
        * Le serveur n’acceptera pas plus de 64 requêtes en mode Flighting du client pour la session. (64 est la valeur négociée.)

#### <a name="example-2--64-max_session_slots-and-nconnect2"></a>Exemple 2 : 64 `max_session_slots` et `nconnect=2` 

L’exemple 2 est basé sur un maximum de 64 `session_slots`, mais avec l’ajout de l’option de montage `nconnect=2`.  Une concurrence de 64 est réalisable, mais répartie entre deux connexions.  Bien que les connexions multiples n’apportent pas plus de concurrence dans ce scénario, la diminution de la profondeur de la file d’attente par connexion a un impact positif sur la latence.

Avec la valeur de `max_session_slots` toujours à 64, mais avec `nconnect=2` en plus, remarquez que le nombre maximum de requêtes est divisé entre les connexions.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * Connexion 1
        * Le client n’émettra pas plus de 32 requêtes en mode Flighting vers le serveur à partir de cette connexion.
        * Le serveur ne doit accepter plus de 32 requêtes en mode Flighting du client pour cette connexion.
    * Connexion 2
        * Le client n’émettra pas plus de 32 requêtes en mode Flighting vers le serveur à partir de cette connexion.
        * Le serveur ne doit accepter plus de 32 requêtes en mode Flighting du client pour cette connexion.

#### <a name="example-3--180-max_session_slots-and-no-nconnect"></a>Exemple 3 : 180 `max_session_slots` et pas de `nconnect`

L’exemple 3 supprime l’option de montage `nconnect` et définit la valeur de `max_session_slots` sur 180, ce qui correspond à la concurrence maximale des sessions NFSv4.1 du serveur.  Dans ce scénario, avec une seule connexion et compte tenu du maximum d’Azure NetApp Files de 128 opérations en suspens par connexion NFS, la session est limitée à 128 opérations en mode Flighting.

Bien que `max_session_slots` ait été défini sur 180, l’unique connexion réseau est limitée à 128 requêtes maximum comme suit : 

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * Le client n’émettra pas plus de 180 requêtes en mode Flighting vers le serveur pour la session.
        * Le serveur n’acceptera pas plus de 180 requêtes en mode Flighting du client pour la session.
        * *Le serveur n’acceptera pas plus de 128 requêtes en mode Flighting pour l’unique connexion.*

#### <a name="example-4--180-max_session_slots-and-nconnect2-"></a>Exemple 4 : 180 `max_session_slots` et `nconnect=2 ` 

L’exemple 4 ajoute l’option de montage `nconnect=2` et réutilise la valeur `max_session_slots` de 180. Comme la charge de travail globale est répartie sur 2 connexions, il est possible d’obtenir 180 opérations en suspens.

Avec 2 connexions en jeu, la session prend en charge la totalité de l’allocation de 180 requêtes en suspens.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * Connexion 1
        * Le client ne doit pas gérer plus de 90 requêtes en mode Flighting vers le serveur à partir de la connexion 1.
        * *Le serveur ne doit pas gérer plus de 90 requêtes en mode Flighting du client pour cette connexion au cours de la session.*
    * Connexion 2
        * Le client ne doit pas gérer plus de 90 requêtes en mode Flighting vers le serveur à partir de la connexion 1.
        * *Le serveur ne doit pas gérer plus de 90 requêtes en mode Flighting du client pour cette connexion au cours de la session.*

> [!NOTE]
> Pour une concurrence maximale, définissez `max_session_slots` sur 180, ce qui correspond à la concurrence maximale au niveau de la session actuellement prise en charge par Azure NetApp Files.

### <a name="how-to-check-for-the-maximum-requests-outstanding-for-the-session"></a>Comment vérifier le nombre maximal de requêtes en suspens pour la session 

Pour connaître les tailles de `session_slot` prises en charge par le client et le serveur, capturez la commande mount dans la trace d’un paquet.  Recherchez l’appel `CREATE_SESSION` et la réponse `CREATE_SESSION` comme indiqué dans l’exemple suivant.  L’appel provient du client et la réponse du serveur.

Utilisez la commande `tcpdump` suivante pour capturer la commande mount :

`$ tcpdump -i eth0 -s 900 -w /tmp/write.trc port 2049`

Si vous utilisez Wireshark, les paquets les plus intéressants sont les suivants :

![Capture d’écran montrant les paquets intéressants.](../media/azure-netapp-files/performance-packets-interest.png)  

Dans ces deux paquets, regardez le champ `max_reqs` dans la section centrale du fichier de trace.

* Système de gestion de fichiers en réseau
    * Operations
        * `Opcode`
            * `csa_fore_channel_attrs`
            * `max reqs`

Le paquet 12 (nombre maximal de requêtes du client) indique que le client a une valeur `max_session_slots` de 64.  Dans la section suivante, vous remarquerez que le serveur prend en charge une concurrence de 180 pour la session.  La session finit par négocier la plus petite des deux valeurs fournies.

![Capture d’écran montrant le nombre maximal d’emplacements de session pour le paquet 12.](../media/azure-netapp-files/performance-max-session-packet-12.png)  

L’exemple suivant montre le paquet 14 (nombre maximal de requêtes du serveur) :

![Capture d’écran montrant le nombre maximal d’emplacements de session pour le paquet 14.](../media/azure-netapp-files/performance-max-session-packet-14.png) 

## <a name="next-steps"></a>Étapes suivantes  

* [Meilleures pratiques Linux concernant les options de montage NFS pour Azure NetApp Files](performance-linux-mount-options.md)
* [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md) 
