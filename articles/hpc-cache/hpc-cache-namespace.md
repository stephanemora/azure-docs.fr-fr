---
title: Créer un cache Azure HPC Cache
description: Comment créer une instance de cache Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582179"
---
# <a name="plan-the-aggregated-namespace"></a>Planifier l’espace de noms agrégé

Azure HPC Cache permet aux clients d’accéder à un large éventail de systèmes de stockage par le biais d’un espace de noms virtuels qui masque les détails du système de stockage back-end.

Lorsque vous ajoutez une cible de stockage, vous définissez le chemin de fichier côté client. Les ordinateurs clients montent ce chemin de fichier et peuvent effectuer des requêtes de lecture de fichier dans le cache au lieu de monter directement le système de stockage.

Azure HPC Cache gérant ce système de fichiers virtuel, vous pouvez modifier la cible de stockage sans modifier le chemin d’accès côté client. Par exemple, vous pouvez remplacer un système de stockage matériel par du stockage cloud sans avoir à réécrire les procédures côté client.

## <a name="aggregated-namespace-example"></a>Exemple d’espace de noms agrégé

Planifiez votre espace de noms agrégé pour que les ordinateurs clients puissent facilement accéder aux informations dont ils ont besoin, et pour que les administrateurs et les ingénieurs du workflow puissent facilement distinguer les chemins.

Par exemple, imaginez un système dans lequel une instance Azure HPC Cache est utilisée pour traiter les données stockées dans un objet blob Azure. L’analyse nécessite des fichiers modèles stockés dans un centre de données local.

Les données des modèle sont stockées dans un centre de données et les informations nécessaires à ce travail sont stockées dans les sous-répertoires suivants :

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Le système de stockage du centre de données expose les exportations suivantes :

    /
    /goldline
    /goldline/templates

Les données à analyser ont été copiées dans un conteneur de stockage Blob Azure nommé « sourcecollection » à l’aide de l’[utilitaire CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Pour permettre un accès facile via le cache, vous pouvez créer des cibles de stockage avec les chemins d’espace de noms virtuels suivants :

| Système de stockage back-end <br/> (Chemin de fichier NFS ou conteneur d’objets blob) | Chemin d’espace de noms virtuels |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

Une cible de stockage NFS peut avoir plusieurs chemins d’accès d’espace de noms virtuels, à condition que chacun d’eux fasse référence à un chemin d’exportation unique.

Les chemins sources NFS étant des sous-répertoires d’une même exportation, vous devez définir plusieurs chemins d’espaces de noms à partir de la même cible de stockage.

| Nom d’hôte de la cible de stockage  | Chemin de l’exportation NFS      | Chemin du sous-répertoire | Chemin de l’espace de noms    |
|--------------------------|----------------------|-------------------|-------------------|
| *Adresse IP ou nom d’hôte* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Adresse IP ou nom d’hôte* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Une application cliente peut monter le cache et accéder facilement aux chemins d’espaces de noms agrégés ``/source``, ``/templates/sku798`` et ``/templates/sku980``.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez décidé comment configurer votre système de fichiers virtuel, [créez des cibles de stockage](hpc-cache-add-storage.md) pour mapper votre stockage back-end à vos chemins de fichiers virtuels côté client.
