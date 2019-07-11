---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b044912fd8f52f3f4fdbe1b3a74b64f9b565ddf0
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673337"
---
1. Dans le [portail](https://portal.azure.com), accédez au réseau virtuel Resource Manager pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la section **Paramètres** de la page de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer la page **Sous-réseaux**.
3. Sur la page **Sous-réseaux**, cliquez sur **+Sous-réseau de passerelle** pour ouvrir la page **Ajouter un sous-réseau**. 

   ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Ajouter un sous-réseau de passerelle")
4. Le **Nom** de votre sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Ajustez les valeurs de **plage d’adresses** renseignées automatiquement pour qu’elles correspondent à la configuration requise. Ne configurez des points de terminaison de table de routage ou de service.

   ![Ajout du sous-réseau](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Ajout du sous-réseau")
5. Cliquez sur **OK** en bas de la page pour créer le sous-réseau.
