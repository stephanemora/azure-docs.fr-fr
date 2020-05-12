---
title: Emplacements de données pour Windows Virtual Desktop - Azure
description: Brève vue d’ensemble des emplacements de stockage des données et métadonnées de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 853f5766c4fd6344eecfd7be1d7911163133a8a5
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611532"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Emplacements de données pour Windows Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service et nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop est disponible pour tous les emplacements géographiques. Les administrateurs peuvent choisir l’emplacement de stockage des données utilisateur au moment de la création des machines virtuelles du pool d’hôtes et des services associés, par exemple les serveurs de fichiers. Découvrez plus en détail les zones géographiques Azure sur la [carte des centres de données Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft ne contrôle ni ne limite les régions où vos utilisateurs et vous pouvez accéder aux données utilisateur et aux données spécifiques de vos utilisateurs et applications.

>[!IMPORTANT]
>Windows Virtual Desktop stocke des informations de métadonnées globales, telles que des noms de locataires, des noms de pools d’hôtes, des noms de groupes d’applications et des noms d’utilisateurs principaux dans un centre de données. Chaque fois qu’un client crée un objet service, il doit entrer un emplacement pour celui-ci. L’emplacement entré détermine où les métadonnées de l’objet seront stockées. Le client choisit une région Azure et les métadonnées sont stockées dans la zone géographique associée. Pour obtenir la liste de toutes les régions Azure et des zones géographiques associées, consultez [Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Pour le moment, nous ne prenons en charge le stockage des métadonnées que dans la zone géographique Azure États-Unis. Les métadonnées stockées sont chiffrées au repos, et des miroirs géoredondants sont à l’intérieur de la zone géographique. L’ensemble des données client, par exemple les paramètres d’application et les données utilisateur, réside à l’emplacement choisi par le client et n’est pas géré par le service. D’autres zones géographiques deviendront disponibles à mesure que le service croîtra.

Les métadonnées de service sont répliquées dans la zone géographique Azure à des fins de récupération d’urgence.