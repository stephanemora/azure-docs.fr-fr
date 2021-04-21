---
title: Stratégies de hiérarchisation Cloud Azure File Sync | Microsoft Docs
description: Détails sur la façon dont les stratégies de date et d’espace libre du volume fonctionnent ensemble pour différents scénarios.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796177"
---
# <a name="cloud-tiering-policies"></a>Stratégies de hiérarchisation Cloud

La hiérarchisation Cloud a deux stratégies qui déterminent les fichiers hiérarchisés dans le Cloud : la **stratégie d’espace libre du volume** et la **stratégie de date**.

La **stratégie d’espace libre du volume** garantit qu’un pourcentage spécifié du volume local sur lequel se trouve le point de terminaison de serveur est toujours libre. 

Les fichiers des niveaux de **stratégie de date** ont été consultés il y a au moins x jours. La stratégie d’espace disponible sur le volume est toujours prioritaire, et, lorsqu’il n’y a pas suffisamment d’espace libre sur le volume pour conserver les fichiers autant de jours que défini par la stratégie de date, Azure File Sync va substituer la stratégie de date et poursuivra la hiérarchisation des fichiers les plus froids jusqu’à ce que le pourcentage d’espace libre sur le volume requis soit atteint.

## <a name="how-both-policies-work-together"></a>Comment les deux stratégies fonctionnent-elles ensemble ?

Nous allons utiliser un exemple pour illustrer le fonctionnement de ces stratégies. Supposons que vous avez configuré Azure File Sync sur un volume local de 500 Go et que la hiérarchisation Cloud n’a jamais été activée. Il s’agit des fichiers de votre partage de fichiers :

|Nom de fichier |Dernier accès  |Taille du fichier  |Stockage dans |
|----------|------------------|-----------|----------|
|Fichier 1    | il y a 2 jours  | 10 Go | Serveur et partage de fichiers Azure
|Fichier 2    | Il y a 10 jours | 30 Go | Serveur et partage de fichiers Azure
|Fichier 3    | il y a 1 an | 200 Go | Serveur et partage de fichiers Azure
|Fichier 4    | 1 an, il y a 2 jours | 130 Go | Serveur et partage de fichiers Azure
|Fichier 5    | 2 ans, il y a 1 jour | 140 Go | Serveur et partage de fichiers Azure

**Modification 1 :** vous avez activé la hiérarchisation Cloud, défini une stratégie d’espace libre de volume de 20 % et conservé la stratégie de date désactivée. Avec cette configuration, la hiérarchisation Cloud garantit que 20 % (dans ce cas 100 Go) d’espace sont conservés gratuitement et disponibles sur l’ordinateur local. Par conséquent, la capacité totale du cache local est de 400 Go. Ces 400 Go stockent les fichiers les plus récents et les plus fréquemment consultés sur le volume local.

Avec cette configuration, seuls les fichiers 1 à 4 sont stockés dans le cache local, et le fichier 5 est hiérarchisé. Seuls 370 Go sur les 400 Go peuvent être utilisés. Le fichier 5 a une taille de 140 Go et la limite de 400 Go serait dépassée s’il était mis en cache localement. 

**Modification 2 :** supposons qu’un utilisateur accède au fichier 5. Cela fait du fichier 5 le fichier le plus récemment consulté dans le partage. Par conséquent, le fichier 5 est stocké dans le cache local et s’ajuste à la limite de 400 Go, le fichier 4 est hiérarchisé. Le tableau suivant indique l’emplacement de stockage des fichiers, avec ces mises à jour :

|Nom de fichier |Dernier accès  |Taille du fichier  |Stockage dans |
|----------|------------------|-----------|----------|
|Fichier 5    | Il y a 2 heures | 140 Go | Serveur et partage de fichiers Azure
|Fichier 1    | il y a 2 jours  | 10 Go | Serveur et partage de fichiers Azure
|Fichier 2    | Il y a 10 jours | 30 Go | Serveur et partage de fichiers Azure
|Fichier 3    | il y a 1 an | 200 Go | Serveur et partage de fichiers Azure
|Fichier 4    | 1 an, il y a 2 jours | 130 Go | Partage de fichiers Azure, hiérarchisés localement

**Modification 3 :** supposons que vous avez mis à jour les stratégies afin que la stratégie de hiérarchisation basée sur la date soit de 60 jours et que la stratégie d’espace libre du volume soit de 70 %. Désormais, 150 Go maximum peuvent être stockés dans le cache local. Bien que vous ayez consulté le Fichier 2 depuis moins de 60 jours, la stratégie d’espace libre du volume remplace la stratégie de date et le Fichier 2 est hiérarchisé pour conserver l’espace libre de 70 %.

**Modification 4 :** si vous avez remplacé la stratégie d’espace libre du volume par 20 %, puis utilisé `Invoke-StorageSyncFileRecall` pour rappeler tous les fichiers qui sont sur le lecteur local tout en adhérant aux stratégies de hiérarchisation Cloud, le tableau ressemble à ceci :

|Nom de fichier |Dernier accès  |Taille du fichier  |Stockage dans |
|----------|------------------|-----------|----------|
|Fichier 5    | il y a 1 heure  | 140 Go | Serveur et partage de fichiers Azure
|Fichier 1    | il y a 2 jours  | 10 Go | Serveur et partage de fichiers Azure
|Fichier 2    | Il y a 10 jours | 30 Go | Serveur et partage de fichiers Azure
|Fichier 3    | il y a 1 an | 200 Go | Partage de fichiers Azure, hiérarchisés localement
|Fichier 4    | 1 an, il y a 2 jours | 130 Go | Partage de fichiers Azure, hiérarchisés localement

Dans ce cas, les fichiers 1, 2 et 5 sont mis en cache localement et les fichiers 3 et 4 sont hiérarchisés. Étant donné que la stratégie de date est de 60 jours, les fichiers 3 et 4 sont hiérarchisés, même si la stratégie d’espace libre du volume autorise jusqu’à 400 Go localement.

> [!NOTE] 
> Les fichiers ne sont pas automatiquement rappelés quand les clients modifient la stratégie d’espace libre du volume sur une valeur inférieure (par exemple, de 20 % à 10 %) ou remplacent la valeur de la stratégie de date par une valeur plus élevée (par exemple, de 20 jours à 50 jours).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Plusieurs points de terminaison de serveur sur un volume local

La hiérarchisation Cloud peut être activée pour plusieurs points de terminaison de serveur sur un unique volume local. Pour cette configuration, vous devez définir l’espace libre du volume sur la même quantité pour tous les points de terminaison de serveur sur le même volume. Si vous définissez différentes stratégies d’espace libre de volume pour plusieurs points de terminaison de serveur sur le même volume, le pourcentage d’espace libre de volume le plus élevé est prioritaire. C’est ce que l’on appelle la **stratégie effective de l’espace libre du volume**. Par exemple, si vous avez trois points de terminaison de serveur sur le même volume local, un avec une valeur de 15 %, un autre défini à 20 % et un troisième défini sur 30%, ils commencent à hiérarchiser les fichiers les plus froids lorsqu’ils disposent de moins de 30% d’espace libre.

## <a name="next-steps"></a>Étapes suivantes

* [Superviser la hiérarchisation Cloud](file-sync-monitor-cloud-tiering.md)
