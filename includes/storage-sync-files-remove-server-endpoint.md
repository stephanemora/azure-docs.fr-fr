---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bb923177cff3104924585e3181f259e18a6f706a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571038"
---
Non, supprimer un point de terminaison de serveur et redémarrer un serveur ne reviennent pas au même ! Le fait de supprimer et de recréer le point de terminaison de serveur n’est presque jamais une solution appropriée pour résoudre les problèmes de synchronisation, de hiérarchisation cloud ou d’autres aspects d’Azure File Sync. La suppression d’un point de terminaison de serveur est une opération destructrice. Elle peut entraîner une perte de données dans le cas où des fichiers hiérarchisés existent en dehors de l’espace de noms du point de terminaison de serveur. Pour obtenir davantage d’informations, consultez [Pourquoi les fichiers hiérarchisés existent-ils en dehors de l’espace de noms du point de terminaison du serveur ?](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint). Elle peut également provoquer l’impossibilité d’accéder aux fichiers hiérarchisés qui existent dans l’espace de noms du point de terminaison de serveur. Ces problèmes ne sont pas résolus par la recréation du point de terminaison. Des fichiers hiérarchisés peuvent exister dans l’espace de noms du point de terminaison de votre serveur même si la hiérarchisation cloud n’a jamais été activée. C’est pourquoi nous vous recommandons de ne pas supprimer le point de terminaison du serveur, sauf si vous souhaitez cesser d’utiliser Azure File Sync avec ce dossier particulier ou si un ingénieur Microsoft vous a expressément demandé de le faire. Pour plus d’informations sur la suppression de points de terminaison de serveur, consultez [Supprimer un point de terminaison de serveur](../articles/storage/file-sync/file-sync-server-endpoint-delete.md).    
