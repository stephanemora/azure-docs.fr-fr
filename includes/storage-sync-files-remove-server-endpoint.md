---
title: Fichier Include
description: Fichier Include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114568"
---
Non, supprimer un point de terminaison de serveur et redémarrer un serveur ne reviennent pas au même ! Le fait de supprimer et de recréer le point de terminaison de serveur n’est presque jamais une solution appropriée pour résoudre les problèmes de synchronisation, de hiérarchisation cloud ou d’autres aspects d’Azure File Sync. La suppression d’un point de terminaison de serveur est une opération destructive et peut entraîner une perte de données si des fichiers hiérarchisés existent en dehors de l’espace de noms du point de terminaison du serveur (consultez [Pourquoi les fichiers hiérarchisés existent-ils en dehors de l’espace de noms du point de terminaison du serveur ?](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) pour plus d’informations) ou dans des fichiers inaccessibles pour des fichiers hiérarchisés existant dans l’espace de noms du point de terminaison du serveur. Ces problèmes ne sont pas résolus par la recréation du point de terminaison. Des fichiers hiérarchisés peuvent exister dans l’espace de noms du point de terminaison de votre serveur même si la hiérarchisation cloud n’a jamais été activée. Par conséquent, nous vous recommandons de ne pas supprimer le point de terminaison du serveur sauf si vous souhaitez cesser d’utiliser Azure File Sync avec ce dossier particulier ou si un ingénieur Microsoft vous a expressément demandé de le faire. Pour plus d’informations sur la suppression de points de terminaison de serveur, consultez [Supprimer un point de terminaison de serveur](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    