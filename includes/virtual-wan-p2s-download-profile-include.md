---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812757"
---
1. Dans la page de votre WAN virtuel, sélectionnez **Configurations de VPN utilisateur**.
1. En haut de la page, sélectionnez **Télécharger une configuration de VPN utilisateur**. Quand vous téléchargez la configuration de niveau WAN, vous obtenez un profil VPN utilisateur basé sur Traffic Manager intégré. Pour plus d’informations sur les profils globaux ou un profil basé sur un hub, consultez [Profils hub](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile). Les scénarios de basculement sont simplifiés avec le profil global.

   Si un hub n’est pas disponible pour une raison ou une autre, la gestion du trafic intégré fourni par le service garantit la connectivité (via un hub différent) aux ressources Azure pour les utilisateurs point à site. Vous pouvez toujours télécharger une configuration VPN propre à un hub en accédant au hub. Sous **VPN utilisateur (point à site)** , téléchargez le profil **VPN utilisateur** du hub virtuel.
1. Une fois le fichier créé, vous pouvez le télécharger en sélectionnant le lien correspondant.
1. Utilisez le fichier de profil pour configurer les clients VPN.
