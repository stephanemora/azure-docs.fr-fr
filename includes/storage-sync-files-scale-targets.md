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
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84466842"
---
| Ressource | Cible | Limite inconditionnelle |
|----------|--------------|------------|
| Services de synchronisation de stockage par région | 100 services de synchronisation de stockage | Oui |
| Groupes de synchronisation par service de synchronisation de stockage | 200 groupes de synchronisation | Oui |
| Serveurs inscrits par le service de synchronisation de stockage | 99 serveurs | Oui |
| Points de terminaison cloud par groupe de synchronisation | 1 point de terminaison cloud | Oui |
| Points de terminaison de serveur par groupe de synchronisation | 50 points de terminaison de serveur | Non |
| Points de terminaison de serveur par serveur | 30 points de terminaison de serveur | Oui |
| Objets du système de fichiers (répertoires et fichiers) par groupe de synchronisation | 100 millions d’objets | Non |
| Nombre maximal d’objets de système de fichiers (répertoires et fichiers) dans un répertoire | 5 millions d’objets | Oui |
| Taille maximale du descripteur de sécurité d’objet (répertoires et fichiers) | 64 Kio | Oui |
| Taille du fichier | 100 Gio | Non |
| Taille minimale d’un fichier à hiérarchiser | V9 : en fonction de la taille de cluster du système de fichiers (le double de cette taille). Par exemple, si la taille du cluster de système de fichiers est de 4 Ko, la taille de fichier minimale sera de 8 Ko.<br> V8 et versions antérieures : 64 Kio  | Oui |

> [!Note]  
> Un point de terminaison Azure File Sync peut augmenter la taille d’un partage de fichiers Azure. Si la limite de taille du partage de fichiers Azure est atteinte, la synchronisation ne fonctionne pas.
