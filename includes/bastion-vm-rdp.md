---
title: Fichier Include
description: inclure fichier
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/21/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 026e505e1ce5fe4d561289b2a98c8c0324136cbc
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108234"
---
1. Dans le [Portail Azure](https://portal.azure.com), accédez à la machine virtuelle à laquelle vous souhaitez vous connecter. Sur la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Sélectionnez Bastion":::

1. Une fois que vous avez sélectionné Bastion dans la liste déroulante, une barre latérale affiche trois onglets : RDP, SSH et Bastion. Si le service Bastion a été approvisionné pour le réseau virtuel, l’onglet Bastion est actif par défaut. Sélectionnez **Utiliser Bastion**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Sélectionnez Utiliser Bastion":::

1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le nom d’utilisateur et le mot de passe de votre machine virtuelle, puis sélectionnez **Se connecter**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Connexion":::

1. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion. 

   * Lorsque vous vous connectez, le bureau de la machine virtuelle peut être différent de celui présenté dans la capture d’écran. 
   * L’utilisation de touches de raccourci lorsque vous êtes connecté à une machine virtuelle peut ne pas s’accompagner du même comportement que les touches de raccourci sur un ordinateur local. Par exemple, lorsque vous êtes connecté à une machine virtuelle Windows à partir d’un client Windows, CTRL+ALT+FIN est le raccourci clavier pour CTRL+ALT+SUPPR sur un ordinateur local. Pour effectuer cette opération depuis un Mac alors que vous êtes connecté à une machine virtuelle Windows, le raccourci clavier est Fn+CTRL+ALT+Retour arrière.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Se connecter à l’aide du port 443":::
