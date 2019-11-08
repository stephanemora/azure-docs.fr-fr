---
title: Monter un cache Azure HPC Cache
description: Comment connecter des clients à un service Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582212"
---
# <a name="mount-the-azure-hpc-cache"></a>Monter le cache Azure HPC Cache

Une fois le cache créé, les clients NFS peuvent y accéder à l’aide d’une simple commande « mount ».

La commande Mount est composée de deux éléments :

* L’une des adresses de montage du cache (figurant sur la page de vue d’ensemble du cache)
* Le chemin d’accès à l’espace de noms virtuel que vous définissez lors de la création de la cible de stockage

![Capture d’écran de la page Vue d’ensemble de l’instance Azure HPC Cache, avec la liste des adresses de montage mise en surbrillance en bas à droite](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Les adresses de montage du cache correspondent aux interfaces réseau du sous-réseau du cache. Dans un groupe de ressources, ces cartes réseau sont répertoriées avec des noms se terminant par `-cluster-nic-` et un nombre. Vous ne devez ni modifier ni supprimer ces interfaces, car cela rendrait le cache indisponible.

Les chemins d’espaces de noms virtuels sont affichés dans la page **Cibles de stockage**. Cliquez sur un nom de cible de stockage pour afficher ses détails, dont les chemins d’accès de l’espace de noms agrégé qui y sont associés.

![Capture d’écran du panneau Cible de stockage du cache, avec une entrée mise en surbrillance dans la colonne Chemin de la table](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Syntaxe de la commande mount

Utilisez une commande mount comme la suivante :

> sudo mount *adresse_montage_cache*:/*chemin_espace_de_noms* *chemin_local* {*options*}

Exemple :

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Si l’exécution de cette commande réussit, le contenu de l’exportation de stockage doit être visible dans le répertoire ``hpccache`` du client.

> [!NOTE] 
> Vos clients doivent pouvoir accéder au réseau et au sous-réseau virtuels qui hébergent votre cache. Par exemple, créez des machines virtuelles clientes dans le même réseau virtuel, ou utilisez un point de terminaison, une passerelle ou une autre solution dans le réseau virtuel pour permettre un accès en dehors du réseau. N’oubliez pas que rien d’autre ne peut être hébergé dans le sous-réseau du cache.

### <a name="mount-command-options"></a>Options de la commande mount

Pour garantir un montage robuste du client, passez les paramètres et arguments suivants dans votre commande mount : 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Paramètres recommandés pour la commande mount | |
--- | --- 
``hard`` | Les montages conditionnels sur un cache Azure HPC Cache sont associés à des échecs d’application et à une perte possible de données. 
``proto=netid`` | Cette option prend en charge la gestion appropriée des erreurs réseau NFS.
``mountproto=netid`` | Cette option prend en charge la gestion appropriée des erreurs réseau pour les opérations de montage.
``retry=n`` | Définissez ``retry=30`` pour éviter les échecs de montage temporaires. (Une valeur différente est recommandée dans les montages de premier plan.)

## <a name="next-steps"></a>Étapes suivantes

* Pour déplacer des données vers les cibles de stockage du cache, consultez [Remplir de données un nouveau stockage Blob Azure](hpc-cache-ingest.md).
