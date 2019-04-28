---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407637"
---
1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la section **Paramètres** de la page de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer la page Sous-réseaux.
3. Sur la page **Sous-réseaux**, cliquez sur **+Sous-réseau de passerelle** en haut de la page pour ouvrir la page **Ajouter un sous-réseau**.

   ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Ajouter un sous-réseau de passerelle")
  
4. Le **Nom** de votre sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». La valeur GatewaySubnet est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Ajustez les valeurs de **plage d’adresses** renseignées automatiquement pour qu’elles correspondent à la configuration requise.

   ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Ajouter un sous-réseau de passerelle")
  
5. Pour créer le sous-réseau, cliquez sur **OK** en bas de la page.
