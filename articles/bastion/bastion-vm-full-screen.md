---
title: 'Azure Bastion : Afficher une session de machine virtuelle en plein écran'
description: Découvrez comment activer et désactiver l’affichage en plein écran de la machine virtuelle dans votre navigateur pour une connexion RDP ou SSH dans Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: 82c763647911037985dc3395a2176e8c43f34114
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629575"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Passez en plein écran pour une session d’une machine virtuelle : Azure Bastion

Cet article vous permet de passer l’affichage de la machine virtuelle en plein écran dans votre navigateur. Avant d’utiliser une machine virtuelle, assurez-vous d’avoir suivi les étapes de [Créer un hôte Bastion](./tutorial-create-host-portal.md). Connectez-vous ensuite à la machine virtuelle que vous souhaitez utiliser à l’aide de [RDP](bastion-connect-vm-rdp-windows.md) ou de [SSH](bastion-connect-vm-ssh-linux.md).

## <a name="launch-the-clipboard-tool"></a>Démarrer l’outil presse-papiers

Au cours de la session à distance, lancez la palette d’outils d’accès au Presse-papiers Bastion en sélectionnant les deux flèches situées au milieu à gauche de la session.

![outils](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Sélectionner le plein écran

Sélectionnez le bouton **Plein écran** pour passer la session en plein écran. Lorsque vous changez, la session est réinitialisée en plein écran.

![plein écran](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
Découvrez comment [Copier et coller](bastion-vm-copy-paste.md) vers et depuis une machine virtuelle Azure.
