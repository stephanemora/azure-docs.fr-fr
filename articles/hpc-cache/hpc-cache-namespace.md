---
title: Créer un cache Azure HPC Cache
description: Comment créer une instance de cache Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036840"
---
# <a name="configure-aggregated-namespace"></a>Configurer un espace de noms agrégé
<!-- change link in GUI -->

Azure HPC Cache permet aux clients d’accéder à un large éventail de systèmes de stockage par le biais d’un espace de noms virtuels qui masque les détails du système de stockage back-end.

Lorsque vous ajoutez une cible de stockage, vous définissez le chemin de fichier côté client. Les ordinateurs clients montent ce chemin de fichier. Vous pouvez modifier la cible de stockage qui est associée à ce chemin. Par exemple, vous pouvez remplacer un système de stockage matériel par du stockage cloud sans avoir à réécrire les procédures côté client.

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

| Chemin de fichier NFS back-end ou conteneur d’objets blob | Chemin d’espace de noms virtuels |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

Étant donné que les chemins sources NFS sont des sous-répertoires d’une même exportation, vous devez définir plusieurs chemins d’espaces de noms à partir de la même cible de stockage. 

| Nom d’hôte de la cible de stockage  | Chemin de l’exportation NFS      | Chemin du sous-répertoire | Chemin de l’espace de noms    |
|--------------------------|----------------------|-------------------|-------------------|
| *Adresse IP ou nom d’hôte* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Adresse IP ou nom d’hôte* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Une application cliente peut monter le cache et accéder facilement aux chemins d’espaces de noms agrégés /source, /templates/sku798 et /templates/sku980.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez décidé comment configurer votre système de fichiers virtuel, [créez des cibles de stockage](hpc-cache-add-storage.md) pour mapper votre stockage back-end à vos chemins de fichiers virtuels côté client.
