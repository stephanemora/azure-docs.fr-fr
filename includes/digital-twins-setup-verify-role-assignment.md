---
author: baanders
description: Fichier include pour vérifier l’attribution de rôle dans la configuration d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 044342122c2bd1d2b59a7fc3abb4ed6ca7bbc05f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110473828"
---
Une façon de vérifier que vous avez correctement configuré l’attribution de rôle consiste à afficher les attributions de rôle pour l’instance Azure Digital Twins dans le [portail Azure](https://portal.azure.com). Accédez à votre instance Azure Digital Twins dans le portail Azure (vous pouvez la rechercher dans la page des [instances Azure Digital Twins](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) ou rechercher son nom dans la barre de recherche du portail).

Ensuite, affichez tous les rôles qui lui sont attribués sous *Contrôle d’accès (IAM) > Attributions de rôle*. L’utilisateur doit s’afficher dans la liste avec le rôle *Propriétaire des données Azure Digital Twins*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Capture d’écran des attributions de rôles pour une instance Azure Digital Twins dans le portail Azure":::