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
ms.openlocfilehash: a4101f3bfe83859eea525370b5eebcaa6e193a2d
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44169134"
---
1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la section **Paramètres** de la page de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer la page Sous-réseaux.
3. Sur la page **Sous-réseaux**, cliquez sur **+Sous-réseau de passerelle** en haut de la page pour ouvrir la page **Ajouter un sous-réseau**.

  ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Ajouter un sous-réseau de passerelle")
  
4. Le **Nom** de votre sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». La valeur GatewaySubnet est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Ajustez les valeurs de **plage d’adresses** renseignées automatiquement pour qu’elles correspondent à la configuration requise.

  ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Ajouter un sous-réseau de passerelle")
  
5. Pour créer le sous-réseau, cliquez sur **OK** en bas de la page.
