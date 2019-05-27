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
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150273"
---
1. Dans le [Portail Azure](http://portal.azure.com), sélectionnez le réseau virtuel Resource Manager pour lequel vous souhaitez créer une passerelle de réseau virtuel.

2. Dans la section **Paramètres** de la page de votre réseau virtuel, sélectionnez **Sous-réseaux** pour développer la page **Sous-réseaux**.

3. Dans la page **Sous-réseaux**, sélectionnez **Sous-réseau de passerelle** pour ouvrir la page **Ajouter un sous-réseau**.

   ![Ajouter un sous-réseau de passerelle](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Ajouter un sous-réseau de passerelle")

4. Le **nom** de votre sous-réseau est automatiquement renseigné avec la valeur *GatewaySubnet*. Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Changez les valeurs renseignées automatiquement dans la **plage d’adresses** pour qu’elles correspondent à vos exigences de configuration, puis sélectionnez **OK** pour créer le sous-réseau.

   ![Ajout du sous-réseau](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Ajout du sous-réseau")