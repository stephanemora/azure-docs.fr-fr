---
title: fichier descriptif
description: inclure fichier
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 08/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4561159b143f7cbaa9908aabb48e8a0dd08578be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627156"
---
1. Dans le [Portail Azure](https://portal.azure.com), accédez à la machine virtuelle à laquelle vous souhaitez vous connecter. Sur la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="Capture d’écran de l’option Se connecter." lightbox="./media/bastion-vm-rdp/connect.png":::

1. Une fois que vous avez sélectionné Bastion dans la liste déroulante, une barre latérale affiche trois onglets : RDP, SSH et Bastion. Si le service Bastion a été approvisionné pour le réseau virtuel, l’onglet Bastion est actif par défaut. Sélectionnez **Utiliser Bastion**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Capture d’écran de la sélection de l’option Utiliser Bastion.":::

1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le nom d’utilisateur et le mot de passe de votre machine virtuelle, puis sélectionnez **Se connecter**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Capture d’écran du bouton Se connecter.":::

1. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion. 

   * Lorsque vous vous connectez, le bureau de la machine virtuelle peut être différent de celui présenté dans la capture d’écran. 
   * L’utilisation de touches de raccourci lorsque vous êtes connecté à une machine virtuelle peut ne pas s’accompagner du même comportement que les touches de raccourci sur un ordinateur local. Par exemple, lorsque vous êtes connecté à une machine virtuelle Windows à partir d’un client Windows, CTRL+ALT+FIN est le raccourci clavier pour CTRL+ALT+SUPPR sur un ordinateur local. Pour effectuer cette opération depuis un Mac alors que vous êtes connecté à une machine virtuelle Windows, le raccourci clavier est Fn+CTRL+ALT+Retour arrière.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Capture d’écran de l’option Se connecter avec le port 443.":::
