---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98798573"
---
Accédez au compte de stockage dont le point de terminaison public ne doit être accessible qu’à certains réseaux virtuels. Dans la table des matières du compte de stockage, sélectionnez **Réseaux**. 

En haut de la page, activez la case d’option **Réseaux sélectionnés**. Cela aura pour effet d’afficher un certain nombre de paramètres permettant de contrôler la restriction du point de terminaison public. Cliquez sur **+ Ajouter un réseau virtuel existant** pour sélectionner le réseau virtuel qui doit être autorisé à accéder au compte de stockage via le point de terminaison public. Pour cela, vous devez sélectionner un réseau virtuel et un sous-réseau de ce réseau virtuel. 

Cochez la case **Autoriser les services Microsoft approuvés à accéder à ce compte de service** pour autoriser les services Microsoft tiers approuvés, comme Azure File Sync, à accéder au compte de stockage.

[![Capture d’écran du panneau Réseaux, montrant un réseau virtuel autorisé à accéder au compte de stockage via le point de terminaison public](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)