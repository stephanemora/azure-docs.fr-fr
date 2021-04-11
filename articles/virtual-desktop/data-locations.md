---
title: Emplacements de données pour Windows Virtual Desktop - Azure
description: Brève vue d’ensemble des emplacements de stockage des données et métadonnées de Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a4c63cc686b08d179e20e6f3e3a7aa1efa69a5f8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447078"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Emplacements de données et de métadonnées pour Windows Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à Windows Virtual Desktop avec des objets Windows Virtual Desktop Azure Resource Manager. Si vous utilisez la version Windows Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/data-locations-2019.md).

Windows Virtual Desktop est disponible pour tous les emplacements géographiques. Les administrateurs peuvent choisir l’emplacement de stockage des données utilisateur au moment de la création des machines virtuelles du pool d’hôtes et des services associés, par exemple les serveurs de fichiers. Découvrez plus en détail les zones géographiques Azure sur la [carte des centres de données Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft ne contrôle ni ne limite les régions où vos utilisateurs et vous pouvez accéder aux données utilisateur et aux données spécifiques de vos utilisateurs et applications.

>[!IMPORTANT]
>Windows Virtual Desktop stocke des informations de métadonnées globales, telles que des noms de locataires, des noms de pools d’hôtes, des noms de groupes d’applications et des noms d’utilisateurs principaux dans un centre de données. Chaque fois qu’un client crée un objet service, il doit entrer un emplacement pour celui-ci. L’emplacement entré détermine où les métadonnées de l’objet seront stockées. Le client choisit une région Azure et les métadonnées sont stockées dans la zone géographique associée. Pour obtenir la liste de toutes les régions Azure et des zones géographiques associées, consultez [Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Nous prenons actuellement en charge le stockage des métadonnées dans les zones géographiques suivantes :

- États-Unis (US) (En disponibilité générale)
- Europe (EU) (Préversion publique) 

>[!NOTE]
> Lorsque vous sélectionnez une région pour y créer des objets du service Windows Virtual Desktop, vous voyez des régions affichées sous les zones géographiques US et EU. Pour être sûr de savoir quelle région convient le mieux à votre déploiement, consultez [notre carte d’infrastructure mondiale Azure](https://azure.microsoft.com/global-infrastructure/geographies/#geographies).

Les métadonnées stockées sont chiffrées au repos, et des miroirs géoredondants sont à l’intérieur de la zone géographique. L’ensemble des données client, par exemple les paramètres d’application et les données utilisateur, réside à l’emplacement choisi par le client et n’est pas géré par le service. D’autres zones géographiques deviendront disponibles à mesure que le service croîtra.

Les métadonnées de service sont répliquées dans la zone géographique Azure à des fins de récupération d’urgence.