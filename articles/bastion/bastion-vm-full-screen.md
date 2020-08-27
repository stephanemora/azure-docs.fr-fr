---
title: 'Azure Bastion : Afficher une session de machine virtuelle en plein écran'
description: Découvrez comment activer et désactiver l’affichage en plein écran de la machine virtuelle dans votre navigateur pour une connexion RDP ou SSH dans Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: fa795d7b066733aba54f8612bbabecf264a11c70
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270289"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Passez en plein écran pour une session d’une machine virtuelle : Azure Bastion

Cet article vous permet de passer l’affichage de la machine virtuelle en plein écran dans votre navigateur. Avant d’utiliser une machine virtuelle, assurez-vous d’avoir suivi les étapes de [Créer un hôte Bastion](bastion-create-host-portal.md). Connectez-vous ensuite à la machine virtuelle que vous souhaitez utiliser à l’aide de [RDP](bastion-connect-vm-rdp.md) ou de [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Démarrer l’outil presse-papiers

Au cours de la session à distance, lancez la palette d’outils d’accès au Presse-papiers Bastion en sélectionnant les deux flèches situées au milieu à gauche de la session.

![outils](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Sélectionner le plein écran

Sélectionnez le bouton **Plein écran** pour passer la session en plein écran. Lorsque vous changez, la session est réinitialisée en plein écran.

![plein écran](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
Découvrez comment [Copier et coller](bastion-vm-copy-paste.md) vers et depuis une machine virtuelle Azure.
