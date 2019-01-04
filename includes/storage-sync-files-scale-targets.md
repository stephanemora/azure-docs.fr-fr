---
title: Fichier Include
description: Fichier Include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 00124562d3bc21f9139ae54f022d7893a4eaf11c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53638859"
---
| Ressource | Cible | Limite inconditionnelle |
|----------|--------------|------------|
| Services de synchronisation de stockage par abonnement | 15 services de synchronisation de stockage par région | Non  |
| Groupes de synchronisation par service de synchronisation de stockage | 100 groupes de synchronisation | Oui |
| Serveurs inscrits par le service de synchronisation de stockage | 99 serveurs | Oui |
| Points de terminaison cloud par groupe de synchronisation | 1 point de terminaison cloud | Oui |
| Points de terminaison de serveur par groupe de synchronisation | 50 points de terminaison de serveur | Non  |
| Points de terminaison de serveur par serveur | 30 points de terminaison de serveur | Oui |
| Taille de point de terminaison | 4 Tio | Non  |
| Objets du système de fichiers (répertoires et fichiers) par groupe de synchronisation | 25 millions d’objets | Non  |
| Nombre maximal d’objets de système de fichiers (répertoires et fichiers) dans un répertoire | 200 000 objets | Oui |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 255 caractères | Oui |
| Taille maximale du descripteur de sécurité d’objet (répertoires et fichiers) | 4 Kio | Oui |
| Taille du fichier | 100 Gio | Non  |
| Taille minimale d’un fichier à hiérarchiser | 64 Kio | Oui |
| Sessions de synchronisation simultanées | Agent V4 : La limitation varie selon les ressources système disponibles. <BR> Agent V3 : 2 sessions de synchronisation actives par processeur ou 8 sessions de synchronisation actives maximum par serveur | Oui
