---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 2b01b4f11ac7777075848287626e021b89254acb
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108758287"
---
Connectez-vous à votre machine virtuelle Windows en utilisant le protocole Remote Desktop Protocol (RDP) via l’adresse IP que vous avez transmise lors de la création de la machine virtuelle.

1. Sur votre client, ouvrez RDP. 
1. Accédez à **Démarrer** et entrez **mstsc**.
1. Dans le volet **Connexion Bureau à distance**, entrez l’adresse IP de la machine virtuelle et les informations d’identification d’accès que vous avez utilisées dans le fichier des paramètres du modèle de machine virtuelle. Sélectionnez **Connecter**.

   ![Capture d’écran du volet Connexion Bureau à distance pour la connexion via RDP à votre machine virtuelle Windows.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Vous devrez peut-être approuver la connexion à un ordinateur non approuvé. 

Vous êtes maintenant connecté à votre machine virtuelle qui s’exécute sur l’appliance. 
