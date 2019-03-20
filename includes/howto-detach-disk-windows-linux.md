---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: c97c94492417dcc87d34751908f1766393ad37ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115278"
---
Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Détacher un disque supprime le disque de la machine virtuelle, mais ne supprime pas le disque du compte de stockage Azure.

Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.  

> [!NOTE]
> Pour détacher un disque de système d’exploitation, vous devez tout d’abord supprimer la machine virtuelle.
>

## <a name="find-the-disk"></a>Recherche du disque
Si vous ne connaissez pas le nom du disque ou souhaitez le vérifier avant de le détacher, procédez comme suit.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

3. Cliquez sur **Disques** sur le bord gauche du tableau de bord de la machine virtuelle, sous **Paramètres**.

   Ce tableau de bord de machine virtuelle répertorie le nom et le type de tous les disques attachés. Par exemple, cet écran affiche une machine virtuelle avec un disque de système d’exploitation et un disque de données :

    ![Rechercher un disque de données](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Détachement du disque
1. À partir du portail Azure, cliquez sur **Machines virtuelles**, sur le nom de la machine virtuelle qui comporte le disque de données que vous souhaitez détacher.

2. Cliquez sur **Disques** sur le bord gauche du tableau de bord de la machine virtuelle, sous **Paramètres**.

3. Cliquez sur le disque que vous souhaitez détacher.

   ![Identification du disque à détacher](./media/howto-detach-disk-windows-linux/disklist.png)

4. Dans la barre de commandes, cliquez sur **Détacher**.

   ![Localiser la commande de détachement](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. Dans la fenêtre de confirmation, cliquez sur **Oui** pour détacher le disque.

   ![Confirmation du détachement du disque](./media/howto-detach-disk-windows-linux/confirmdetach.png)

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.
