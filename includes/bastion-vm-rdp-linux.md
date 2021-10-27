---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: c52c00dd8fa834775a01162e2506f1821b0dcdae
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130173297"
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

   Notez que l’utilisation des touches de raccourci lorsque vous êtes connecté à une machine virtuelle peut ne pas donner lieu au même comportement que les touches de raccourci sur un ordinateur local. Par exemple, sur un ordinateur client Windows connecté à une machine virtuelle Windows, « CTRL+ALT+FIN » est le raccourci clavier pour « CTRL+ALT+SUPPR ». Sur un ordinateur client Mac connecté à une machine virtuelle Windows, le raccourci clavier est « Fn+CTRL+ALT+Retour arrière ».
