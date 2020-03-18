---
title: Latence de connexion des utilisateur de Windows Virtual Desktop – Azure
description: Latence de connexion pour les utilisateurs de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128179"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Déterminer la latence de connexion pour les utilisateurs dans Windows Virtual Desktop

Windows Virtual Desktop est disponible dans le monde entier. Les administrateurs peuvent créer des machines virtuelles (VM) dans la région Azure de leur choix. La latence de connexion varie en fonction de l’emplacement des utilisateurs et des machines virtuelles. Les services de Windows Virtual Desktop se déploient en permanence vers de nouvelles zones géographiques pour améliorer la latence. 
 
L’[outil d’estimation de l’expérience Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) peut vous aider à déterminer le meilleur emplacement pour optimiser la latence de vos machines virtuelles. Nous vous recommandons d’utiliser l’outil tous les deux à trois mois pour vous assurer que l’emplacement optimal n’a pas changé lors du développement de Windows Virtual Desktop vers de nouvelles zones. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Windows Virtual Desktop utilise Microsoft Azure Traffic Manager, qui vérifie l’emplacement du serveur DNS de l’utilisateur pour trouver l’instance du service Windows Virtual Desktop la plus proche. Nous recommandons aux administrateurs d’examiner l’emplacement du serveur DNS de l’utilisateur avant de choisir l’emplacement des machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

- Pour vérifier le meilleur emplacement pour une latence optimale, voir l’[outil d’estimation de l’expérience Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Pour les plans de tarification, voir [Tarification de Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Pour commencer votre déploiement de Windows Virtual Desktop, suivez [notre didacticiel](tenant-setup-azure-active-directory.md).