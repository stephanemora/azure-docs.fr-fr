---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780140"
---
Le sous-réseau de passerelle contient les adresses IP réservées utilisées par les services de passerelle de réseau virtuel. Créez un sous-réseau de passerelle.

1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la page de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer la page **VNet1 - Sous-réseaux**.
3. Cliquez sur **+ Sous-réseau de passerelle** en haut de la page pour ouvrir la page **Ajouter un sous-réseau**.

   ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Ajouter un sous-réseau de passerelle")
4. Le **nom** de votre sous-réseau est automatiquement renseigné avec la valeur requise « GatewaySubnet ». Ajustez la **plage d'adresses (bloc CIDR)** remplie automatiquement pour qu’elle corresponde à la valeur suivante :

   **Plage d’adresses (bloc CIDR)** : 10.1.255.0/27

   ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Ajouter un sous-réseau de passerelle")
5. Laissez la valeur par défaut sur **Aucun** ou **0 sélectionnée** pour les autres paramètres. Cliquez ensuite sur **OK** pour créer le sous réseau de passerelle.