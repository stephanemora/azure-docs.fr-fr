---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795713"
---
| Ressource | Cible | Limite inconditionnelle |
|----------|--------------|------------|
| Services de synchronisation de stockage par région | 20 services de synchronisation de stockage | Oui |
| Groupes de synchronisation par service de synchronisation de stockage | 100 groupes de synchronisation | Oui |
| Serveurs inscrits par le service de synchronisation de stockage | 99 serveurs | Oui |
| Points de terminaison cloud par groupe de synchronisation | 1 point de terminaison cloud | Oui |
| Points de terminaison de serveur par groupe de synchronisation | 50 points de terminaison de serveur | Non |
| Points de terminaison de serveur par serveur | 30 points de terminaison de serveur | Oui |
| Objets du système de fichiers (répertoires et fichiers) par groupe de synchronisation | 100 millions d’objets | Non |
| Nombre maximal d’objets de système de fichiers (répertoires et fichiers) dans un répertoire | 5 millions d’objets | Oui |
| Taille maximale du descripteur de sécurité d’objet (répertoires et fichiers) | 64 Kio | Oui |
| Taille du fichier | 100 Gio | Non |
| Taille minimale d’un fichier à hiérarchiser | V9 : en fonction de la taille de cluster du système de fichiers (le double de cette taille). Par exemple, si la taille du cluster de système de fichiers est de 4 Ko, la taille de fichier minimale sera de 8 Ko.<br> V8 et versions antérieures : 64 Kio  | Oui |

> [!Note]  
> Un point de terminaison Azure File Sync peut augmenter la taille d’un partage de fichiers Azure. Si la limite de taille du partage de fichiers Azure est atteinte, la synchronisation ne fonctionne pas.
