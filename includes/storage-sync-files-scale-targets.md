---
title: Fichier Include
description: Fichier Include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114493"
---
| Ressource | Cible | Limite inconditionnelle |
|----------|--------------|------------|
| Services de synchronisation de stockage par région | 20 Services de synchronisation de stockage | Oui |
| Groupes de synchronisation par service de synchronisation de stockage | 100 groupes de synchronisation | Oui |
| Serveurs inscrits par le service de synchronisation de stockage | 99 serveurs | Oui |
| Points de terminaison cloud par groupe de synchronisation | 1 point de terminaison cloud | Oui |
| Points de terminaison de serveur par groupe de synchronisation | 50 points de terminaison de serveur | Non |
| Points de terminaison de serveur par serveur | 30 points de terminaison de serveur | Oui |
| Objets du système de fichiers (répertoires et fichiers) par groupe de synchronisation | 25 millions d’objets | Non |
| Nombre maximal d’objets de système de fichiers (répertoires et fichiers) dans un répertoire | 1 million d’objets | Oui |
| Taille maximale du descripteur de sécurité d’objet (répertoires et fichiers) | 64 Kio | Oui |
| Taille de fichier | 100 Go | Non |
| Taille minimale d’un fichier à hiérarchiser | 64 Kio | Oui |
| Sessions de synchronisation simultanées | Agent v4 et versions ultérieures : La limite varie en fonction des ressources système disponibles. <BR> Agent V3 : Deux sessions de synchronisation active par processeur ou un maximum de huit sessions de synchronisation active par serveur. | Oui

> [!Note]  
> Un point de terminaison Azure File Sync peut augmenter la taille d’un partage de fichiers Azure. Si la limite de taille de partage de fichiers Azure est atteinte, la synchronisation ne sera pas en mesure de fonctionner.