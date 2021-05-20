---
title: Fichier include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: f4baa89757d4ae93af8c1067cf6ef4617ce95e5a
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109645286"
---
Cette étape lie l’ensemble des ressources et dossiers que vous avez configurés sur votre instance Windows Server au cours des étapes précédentes.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Localisez votre ressource de service de synchronisation de stockage.
1. Créez un *groupe de synchronisation* au sein de la ressource de service de synchronisation de stockage pour chaque partage de fichiers Azure. Dans la terminologie Azure File Sync, le partage de fichiers Azure devient un *point de terminaison cloud* dans la topologie de synchronisation que vous décrivez lors de la création d’un groupe de synchronisation. Lorsque vous créez le groupe de synchronisation, donnez-lui un nom familier qui vous permette de reconnaître le groupe de fichiers qui se synchronise ici. Veillez à référencer le partage de fichiers Azure avec un nom correspondant.
1. Une fois que vous avez créé le groupe de synchronisation, une ligne apparaît pour lui dans la liste des groupes de synchronisation. Cliquez sur le nom (un lien) du groupe de synchronisation pour afficher son contenu. Votre partage de fichiers Azure apparaît sous **Points de terminaison cloud**.
1. Recherchez le bouton **Ajouter un point de terminaison de serveur**. Le dossier situé sur le serveur local que vous avez approvisionné devient le chemin d’accès pour ce *point de terminaison de serveur*.
