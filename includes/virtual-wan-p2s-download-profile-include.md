---
title: Fichier include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628965"
---
1. Dans la page de votre WAN virtuel, sélectionnez **Configurations de VPN utilisateur**.
1. Dans la page **Configurations de VPN utilisateur**, sélectionnez une configuration, puis sélectionnez **Télécharger le profil VPN utilisateur du WAN virtuel**. Quand vous téléchargez la configuration de niveau WAN, vous obtenez un profil VPN utilisateur basé sur Traffic Manager intégré. Pour plus d’informations sur les profils globaux ou un profil basé sur un hub, consultez [Profils hub](../articles/virtual-wan/global-hub-profile.md). Les scénarios de basculement sont simplifiés avec le profil global.

   
   Si un hub n’est pas disponible pour une raison ou une autre, la gestion du trafic intégré fourni par le service garantit la connectivité (via un hub différent) aux ressources Azure pour les utilisateurs point à site. Vous pouvez toujours télécharger une configuration VPN propre à un hub en accédant au hub. Sous **VPN utilisateur (point à site)** , téléchargez le profil **VPN utilisateur** du hub virtuel.
1. Dans la page **Télécharger le profil VPN utilisateur du WAN virtuel**, sélectionnez le **type d’authentification**, puis sélectionnez **Générer et télécharger un profil**. Le package de profil sera généré et un fichier zip contenant les paramètres de configuration sera téléchargé.
