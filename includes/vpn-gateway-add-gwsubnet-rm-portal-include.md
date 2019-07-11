---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673332"
---
1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez le réseau virtuel Resource Manager pour lequel vous souhaitez créer une passerelle de réseau virtuel.

2. Dans la section **Paramètres** de la page de votre réseau virtuel, sélectionnez **Sous-réseaux** pour développer la page **Sous-réseaux**.

3. Dans la page **Sous-réseaux**, sélectionnez **Sous-réseau de passerelle** pour ouvrir la page **Ajouter un sous-réseau**.

   ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Ajouter un sous-réseau de passerelle")

4. Le **nom** de votre sous-réseau est automatiquement renseigné avec la valeur *GatewaySubnet*. Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Changez les valeurs renseignées automatiquement dans la **plage d’adresses** pour qu’elles correspondent à vos exigences de configuration, puis sélectionnez **OK** pour créer le sous-réseau.

   ![Ajout du sous-réseau](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Ajout du sous-réseau")
