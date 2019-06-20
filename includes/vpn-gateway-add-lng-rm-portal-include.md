---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176935"
---
1. Dans le portail, à partir de **Toutes les ressources**, cliquez sur **+ Ajouter**.
2. Dans la zone de recherche de la page **Tout**, tapez **passerelle de réseau local**, puis cliquez pour obtenir une liste de ressources. Cliquez sur **Passerelle de réseau local** pour ouvrir la page correspondante, puis cliquez sur **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.

   ![créer une passerelle de réseau local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Dans la page **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local.

   - **Nom :** Spécifiez un nom pour votre objet de passerelle de réseau local. Dans la mesure du possible, utilisez un nom intuitif, tel que **RéseauVirtuelClassicLocal** ou **RéseauVirtuel1TestLocal**. Cette approche vous aidera à identifier plus facilement la passerelle de réseau local dans le portail.
   - **Adresse IP :** Spécifiez une **adresse IP** publique valide pour la passerelle de réseau virtuel ou le périphérique VPN auquel vous souhaitez vous connecter.

     * **Si ce réseau local représente un emplacement local :** Spécifiez l’adresse IP publique du périphérique VPN auquel vous souhaitez vous connecter. Il ne peut pas se trouver derrière NAT et doit être accessible par Azure.
     * **Si ce réseau local représente un autre réseau virtuel :** Spécifiez l’adresse IP publique affectée à la passerelle de réseau virtuel pour ce réseau virtuel.
     * **Si vous n’avez pas encore l’adresse IP :** Vous pouvez créer une adresse IP avec espace réservé valide, puis revenir modifier ce paramètre avant de vous connecter.
   - **Espace d’adressage** fait référence aux plages d’adresses du réseau qui représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne chevauchent pas les plages d’autres réseaux auxquels vous vous connectez.
   - **Configurer les paramètres BGP :** À utiliser uniquement durant la configuration de BGP. pour la configuration du protocole BGP.
   - **Abonnement :** Vérifiez que l’abonnement approprié s’affiche.
   - **Groupe de ressources :** Sélectionnez le groupe de ressources à utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
   - **Emplacement :** Sélectionnez l’emplacement dans lequel cet objet va être créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.

4. Cliquez sur **Créer** pour créer la passerelle de réseau local.
