---
title: Configuration d’Azure File Sync
description: Configurez Azure File Sync. Bloc de texte commun, partagé entre plusieurs documents sur la migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209328"
---
Cette étape lie l’ensemble des ressources et dossiers que vous avez configurés sur votre instance Windows Server au cours des étapes précédentes.

* Connectez-vous au [portail Azure](https://portal.azure.com).
* Localisez votre ressource de service de synchronisation de stockage.
* Créez un *groupe de synchronisation* au sein de la ressource de service de synchronisation de stockage pour chaque partage de fichiers Azure. Dans la terminologie Azure File Sync, le partage de fichiers Azure devient un *point de terminaison cloud* dans la topologie de synchronisation que vous décrivez lors de la création d’un groupe de synchronisation. Donnez au groupe de synchronisation que vous créez un nom familier de sorte de reconnaître l'ensemble de fichiers synchronisés ici. Veillez à référencer le partage de fichiers Azure avec un nom correspondant.
* Une fois le groupe de synchronisation créé, la ligne correspondante s'affiche dans la liste des groupes de synchronisation. Cliquez sur le nom (un lien) pour afficher le contenu du groupe de synchronisation. Vous verrez votre partage de fichiers Azure sous « points de terminaison cloud ».
* Localisez le bouton de commande pour *+ Ajouter un point de terminaison de serveur*. Le dossier situé sur le serveur local que vous avez approvisionné devient le chemin d’accès pour ce point de terminaison *serveur*.
