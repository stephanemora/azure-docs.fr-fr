---
title: Emplacements de données pour Windows Virtual Desktop (classique) - Azure
description: Brève vue d’ensemble des emplacements de stockage des données et métadonnées de Windows Virtual Desktop (classique).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7726795582ffa3d85601ec123b58ab705442ef67
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444987"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Emplacements de données pour Windows Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Windows Virtual Desktop (classique), qui ne prend pas en charge les objets Windows Virtual Desktop Azure Resource Manager. Si vous essayez de gérer des objets Windows Virtual Desktop Azure Resource Manager, consultez [cet article](../data-locations.md).

Windows Virtual Desktop est disponible pour tous les emplacements géographiques. Initialement, les métadonnées de service peuvent uniquement être stockées dans la zone géographique États-Unis. Les administrateurs peuvent choisir l’emplacement de stockage des données utilisateur au moment de la création des machines virtuelles du pool d’hôtes et des services associés, par exemple les serveurs de fichiers. Découvrez plus en détail les zones géographiques Azure sur la [carte des centres de données Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft ne contrôle ni ne limite les régions où vos utilisateurs et vous pouvez accéder aux données utilisateur et aux données spécifiques de vos utilisateurs et applications.

>[!IMPORTANT]
>Windows Virtual Desktop stocke les informations de métadonnées globales, telles que les noms de locataires, les noms de pools d’hôtes, les noms de groupes d’applications et les noms d’utilisateurs principaux dans un centre de données situé aux États-Unis. Les métadonnées stockées sont chiffrées au repos, et des miroirs géoredondants sont conservés aux États-Unis. L’ensemble des données client, par exemple les paramètres d’application et les données utilisateur, réside à l’emplacement choisi par le client et n’est pas géré par le service.

Les métadonnées de service sont répliquées aux États-Unis à des fins de reprise d’activité après sinistre.