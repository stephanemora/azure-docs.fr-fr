---
author: cherylmc
ms.author: cherylmc
ms.date: 06/29/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: f0585ca5bc2a76cb7f969f8a599ee98152b872cf
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108232"
---
Quand vous vous connectez à une machine virtuelle à l’aide d’Azure Bastion, vous n’avez pas besoin d’une adresse IP publique pour votre machine virtuelle. Si vous n’utilisez pas l’adresse IP publique pour autre chose, vous pouvez la dissocier de votre machine virtuelle. Pour dissocier une adresse IP publique de votre machine virtuelle, procédez aux étapes suivantes :

1. Accédez à votre machine virtuelle et sélectionnez **Mise en réseau**. Sélectionnez l’**adresse IP publique de carte réseau** pour ouvrir la page de l’adresse IP publique.

   :::image type="content" source="./media/bastion-remove-ip/networking.png" alt-text="Capture d’écran de la page de mise en réseau":::

1. Dans la page **Adresse IP publique** de la machine virtuelle, sélectionnez **Dissocier**.

   :::image type="content" source="./media/bastion-remove-ip/disassociate.png" alt-text="Capture d’écran de l’adresse IP publique de la machine virtuelle":::

1. Sélectionnez **Oui** pour dissocier l’adresse IP de l’interface réseau.

   :::image type="content" source="./media/bastion-remove-ip/disassociate-yes.png" alt-text="Capture d’écran de Dissocier l’adresse IP publique":::

1. Après avoir dissocié l’adresse IP, vous pouvez supprimer la ressource d’adresse IP publique. Pour supprimer la ressource d’adresse IP publique, accédez au groupe de ressources et recherchez la ressource d’adresse IP à supprimer. Ensuite, sélectionnez **Supprimer** pour supprimer la ressource.

   :::image type="content" source="./media/bastion-remove-ip/delete-resource.png" alt-text="Capture d’écran de suppression de la ressource d’adresse IP publique":::