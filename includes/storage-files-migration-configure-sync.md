---
title: Configuration d’Azure File Sync
description: Configurez Azure File Sync. Bloc de texte commun, partagé entre plusieurs documents de migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143616"
---
Cette étape lie l’ensemble des ressources et dossiers que vous avez configurés sur votre instance Windows Server au cours des étapes précédentes.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Localisez votre ressource de service de synchronisation de stockage.
1. Créez un *groupe de synchronisation* au sein de la ressource de service de synchronisation de stockage pour chaque partage de fichiers Azure. Dans la terminologie Azure File Sync, le partage de fichiers Azure devient un *point de terminaison cloud* dans la topologie de synchronisation que vous décrivez lors de la création d’un groupe de synchronisation. Lorsque vous crées le groupe de synchronisation, donnez-lui un nom familier qui vous permette de reconnaître le groupe de fichiers qui se synchronise ici. Veillez à référencer le partage de fichiers Azure avec un nom correspondant.
1. Une fois le groupe de synchronisation créé, une ligne correspondant à celui-ci apparaît dans la liste des groupes de synchronisation. Cliquez sur le nom (un lien) du groupe de synchronisation pour afficher son contenu. Votre partage de fichiers Azure apparaît sous **Points de terminaison cloud**.
1. Recherchez le bouton **+ Ajouter un point de terminaison de serveur**. Le dossier situé sur le serveur local que vous avez approvisionné devient le chemin d’accès pour ce *point de terminaison de serveur*.
