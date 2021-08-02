---
title: Emplacements des données d’Azure Virtual Desktop (classique) – Azure
description: Brève vue d’ensemble des emplacements de stockage des données et des métadonnées d’Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9ddfc6cd8a18e9bdbee071d0c5fd64a0c14b2e95
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754472"
---
# <a name="data-locations-for-azure-virtual-desktop-classic"></a>Emplacements des données d’Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../data-locations.md).

Azure Virtual Desktop est actuellement disponible pour toutes les localisations géographiques. Initialement, les métadonnées de service peuvent uniquement être stockées dans la zone géographique États-Unis. Les administrateurs peuvent choisir l’emplacement de stockage des données utilisateur au moment de la création des machines virtuelles du pool d’hôtes et des services associés, par exemple les serveurs de fichiers. Découvrez plus en détail les zones géographiques Azure sur la [carte des centres de données Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft ne contrôle ni ne limite les régions où vos utilisateurs et vous pouvez accéder aux données utilisateur et aux données spécifiques de vos utilisateurs et applications.

>[!IMPORTANT]
>Azure Virtual Desktop stocke les informations de métadonnées globales (par exemple les noms de locataires, de pools d’hôtes, de groupes d’applications et d’utilisateurs principaux) dans un centre de données situé aux États-Unis. Les métadonnées stockées sont chiffrées au repos, et des miroirs géoredondants sont conservés aux États-Unis. L’ensemble des données client, par exemple les paramètres d’application et les données utilisateur, réside à l’emplacement choisi par le client et n’est pas géré par le service.

Les métadonnées de service sont répliquées aux États-Unis à des fins de reprise d’activité après sinistre.