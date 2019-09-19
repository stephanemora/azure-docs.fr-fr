---
title: Monter un cache Azure HPC Cache
description: Comment connecter des clients à un service Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 0ccd256ed399fff7c364883ada45d2d15c75a120
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775073"
---
# <a name="mount-the-azure-hpc-cache"></a>Monter le cache Azure HPC Cache

Une fois le cache créé, les clients NFS peuvent y accéder à l’aide d’une simple commande « mount ».

Utilisez les adresses de montage listées dans la vue d’ensemble du cache, ainsi que le chemin de l’espace de noms virtuels que vous avez défini lors de la création de la cible de stockage. 

![Capture d’écran de la page Vue d’ensemble de l’instance Azure HPC Cache, avec la liste des adresses de montage mise en surbrillance en bas à droite](media/mount-addresses.png)

> [!NOTE] 
> Les adresses de montage du cache correspondent aux interfaces réseau du sous-réseau du cache. Dans le groupe de ressources, les cartes réseau portent un nom se terminant par `-cluster-nic-` et par un nombre. Vous ne devez ni modifier ni supprimer ces interfaces, car cela rendrait le cache indisponible.

Les chemins d’espaces de noms virtuels sont affichés dans la page **Cibles de stockage**. 
![Capture d’écran du panneau Cible de stockage du cache, avec une entrée mise en surbrillance dans la colonne Chemin de la table](media/storage-target-path.png)

## <a name="mount-command-syntax"></a>Syntaxe de la commande mount

Utilisez une commande mount comme la suivante :

> sudo mount *adresse_montage_cache*:/*chemin_espace_de_noms* *chemin_local* {*options*}

Exemple : 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

Si l’exécution de cette commande réussit, le contenu de l’exportation de stockage doit être visible dans le répertoire ``hpccache`` du client. 

> [!NOTE] 
> Vos clients doivent pouvoir accéder au réseau et au sous-réseau virtuels qui hébergent votre cache. Par exemple, créez des machines virtuelles clientes dans le même réseau virtuel, ou utilisez un point de terminaison, une passerelle ou une autre solution dans le réseau virtuel pour permettre un accès en dehors du réseau. N’oubliez pas que rien d’autre ne peut être hébergé dans le sous-réseau du cache.

### <a name="mount-command-options"></a>Options de la commande mount

Pour garantir un montage robuste du client, passez les paramètres et arguments suivants dans votre commande mount : 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Paramètres recommandés pour la commande mount | |
--- | --- 
``hard`` | Les montages conditionnels sur le cluster vFXT sont associés à des échecs d’application et à des pertes de données possibles. 
``proto=netid`` | Cette option prend en charge la gestion appropriée des erreurs réseau NFS.
``mountproto=netid`` | Cette option prend en charge la gestion appropriée des erreurs réseau pour les opérations de montage.
``retry=n`` | Définissez ``retry=30`` pour éviter les échecs de montage temporaires. (Une valeur différente est recommandée dans les montages de premier plan.)

## <a name="next-steps"></a>Étapes suivantes

* Pour déplacer des données vers les cibles de stockage du cache, consultez [Remplir de données un nouveau stockage Blob Azure](hpc-cache-ingest.md).
