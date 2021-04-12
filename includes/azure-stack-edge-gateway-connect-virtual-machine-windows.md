---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730656"
---
Connectez-vous à votre machine virtuelle Windows en utilisant le protocole Remote Desktop Protocol (RDP) via l’adresse IP que vous avez transmise lors de la création de la machine virtuelle.

1. Sur votre client, ouvrez RDP. 
1. Accédez à **Démarrer** et saisissez **mstsc**.
1. Dans le volet **Connexion Bureau à distance**, entrez l’adresse IP de la machine virtuelle et les informations d’identification d’accès que vous avez utilisées dans le fichier des paramètres du modèle de machine virtuelle, puis sélectionnez **Se connecter**.

   ![Capture d’écran du volet Connexion Bureau à distance pour la connexion via RDP à votre machine virtuelle Windows.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Vous devrez peut-être approuver la connexion à un ordinateur non approuvé. 

Vous êtes maintenant connecté à votre machine virtuelle qui s’exécute sur l’appliance. 
