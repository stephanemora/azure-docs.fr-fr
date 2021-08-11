---
title: Fichier Include
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: aacd6a780562476ea338b4c08795e14b6ba39760
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579164"
---
1. Localisez l’instance Virtual WAN que vous avez créée. Dans la page Virtual WAN, sous la section **Connectivité**, sélectionnez **Hubs**.
1. Dans la page **Hubs**, cliquez sur **+Nouveau hub** pour ouvrir la page **Créer un hub virtuel**.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="Capture d’écran montrant le volet Créer un hub virtuel avec l’onglet Informations de base sélectionné." border="false":::
1. Dans la page **Créer un hub virtuel**, sous l’onglet **Fonctions de base**, renseignez les champs suivants :

   * **Région** (précédemment appelée Emplacement)
   * **Nom**
   * **Espace d’adressage privé du hub** : pour créer un hub, l’espace d’adressage minimal est /24. Si vous utilisez un espace d’adressage compris entre /25 et /32, vous obtiendrez une erreur lors de la création. Vous n’avez pas besoin de planifier explicitement l’espace d’adressage du sous-réseau pour les services du hub virtuel. Étant donné que le service Azure Virtual WAN est un service managé, il crée les sous-réseaux appropriés dans le hub virtuel pour les divers services ou passerelles (par exemple, les passerelles VPN, les passerelles ExpressRoute, les passerelles VPN utilisateur/point à site, le pare-feu, le routage, etc.).
