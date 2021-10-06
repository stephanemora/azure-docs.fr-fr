---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: ebaa86a60ae11c50658bfcfe58bbd0184bf15576
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700670"
---
1. Dans le [Portail Azure](https://portal.azure.com), accédez à la machine virtuelle à laquelle vous souhaitez vous connecter. Sur la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="Capture d’écran de l’option Se connecter." lightbox="./media/bastion-vm-rdp/connect.png":::

1. Après avoir sélectionné Bastion, sélectionnez **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](../articles/bastion/quickstart-host-portal.md).

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Capture d’écran montrant l’option Utiliser Bastion.":::

1. Dans la page **Se connecter à l’aide d’Azure Bastion**, développez la section **Paramètres de connexion**, puis sélectionnez **RDP**. Si vous envisagez d’utiliser un port d’entrée différent du port RDP standard (3389), entrez le **Port**.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/connection-settings.png" alt-text="Capture d’écran montrant le port." lightbox="./media/bastion-connect-vm-rdp-linux/connection-settings.png":::

1. Renseignez les champs **Nom d’utilisateur** et **Mot de passe**, puis sélectionnez **Se connecter** pour vous connecter à la machine virtuelle.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/username-password.png" alt-text="Capture d’écran montrant le bouton Connecter sur lequel cliquer." lightbox="./media/bastion-connect-vm-rdp-linux/username-password.png":::

1. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion. 

   L’utilisation de touches de raccourci lorsque vous êtes connecté à une machine virtuelle peut ne pas s’accompagner du même comportement que les touches de raccourci sur un ordinateur local. Par exemple, sur un ordinateur client Windows connecté à une machine virtuelle Windows, « CTRL+ALT+FIN » est le raccourci clavier pour « CTRL+ALT+SUPPR ». Sur un ordinateur client Mac connecté à une machine virtuelle Windows, le raccourci clavier est « Fn+CTRL+ALT+Retour arrière ».