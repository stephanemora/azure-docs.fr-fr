---
title: Emplacements de données pour Windows Virtual Desktop - Azure
description: Brève vue d’ensemble des emplacements de stockage des données et métadonnées de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
ms.openlocfilehash: 5a634f3449d88e2c1885f4a32ae2662c93811c63
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349944"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Emplacements de données pour Windows Virtual Desktop

Windows Virtual Desktop est disponible pour tous les emplacements géographiques. Initialement, les métadonnées de service peuvent uniquement être stockées dans la zone géographique États-Unis. Les administrateurs peuvent choisir l’emplacement de stockage des données utilisateur au moment de la création des machines virtuelles du pool d’hôtes et des services associés, par exemple les serveurs de fichiers. Découvrez plus en détail les zones géographiques Azure sur la [carte des centres de données Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft ne contrôle ni ne limite les régions où vous ou vos utilisateurs pouvez accéder aux données utilisateur et aux données spécifiques aux applications.

>[!IMPORTANT]
>Windows Virtual Desktop stocke les informations de métadonnées globales, telles que les noms de locataires, les noms de pools d’hôtes, les noms de groupes d’applications et les noms d’utilisateurs principaux dans un centre de données situé aux États-Unis. Les métadonnées stockées sont chiffrées au repos, et des miroirs géoredondants sont conservés aux États-Unis. L’ensemble des données client, par exemple les paramètres d’application et les données utilisateur, réside à l’emplacement choisi par le client et n’est pas géré par le service.

Les métadonnées de service sont répliquées aux États-Unis à des fins de reprise d’activité après sinistre.