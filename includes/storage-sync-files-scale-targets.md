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
ms.openlocfilehash: c82717be3791a1bc397bbbdb369c55cd2ba04439
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213030"
---
| Ressource | Cible | Limite inconditionnelle |
|----------|--------------|------------|
| Services de synchronisation de stockage par région | 15 services de synchronisation de stockage | OUI |
| Groupes de synchronisation par service de synchronisation de stockage | 100 groupes de synchronisation | OUI |
| Serveurs inscrits par le service de synchronisation de stockage | 99 serveurs | OUI |
| Points de terminaison cloud par groupe de synchronisation | 1 point de terminaison cloud | OUI |
| Points de terminaison de serveur par groupe de synchronisation | 50 points de terminaison de serveur | Non  |
| Points de terminaison de serveur par serveur | 30 points de terminaison de serveur | OUI |
| Taille de point de terminaison | 4 Tio | Non  |
| Objets du système de fichiers (répertoires et fichiers) par groupe de synchronisation | 25 millions d’objets | Non  |
| Nombre maximal d’objets de système de fichiers (répertoires et fichiers) dans un répertoire | 1 000 000 objets | OUI |
| Taille maximale du descripteur de sécurité d’objet (répertoires et fichiers) | 4 Kio | OUI |
| Taille du fichier | 100 Gio | Non  |
| Taille minimale d’un fichier à hiérarchiser | 64 Kio | OUI |
| Sessions de synchronisation simultanées | Agent v4 et versions ultérieures : La limitation varie selon les ressources système disponibles. <BR> Agent V3 : 2 sessions de synchronisation actives par processeur ou 8 sessions de synchronisation actives maximum par serveur | OUI
