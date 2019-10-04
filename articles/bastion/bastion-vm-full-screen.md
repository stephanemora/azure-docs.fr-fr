---
title: Passer l’affichage de session d’une machine virtuelle en plein écran dans Azure Bastion | Microsoft Docs
description: Dans cet article, vous apprenez à passer l’affichage en plein écran.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191252"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>Passez en plein écran pour une session d’une machine virtuelle : Azure Bastion (préversion)

Cet article vous permet de passer l’affichage de la machine virtuelle en plein écran dans votre navigateur. Avant d’utiliser une machine virtuelle, assurez-vous d’avoir suivi les étapes de [Créer un hôte Bastion](bastion-create-host-portal.md). Connectez-vous ensuite à la machine virtuelle que vous souhaitez utiliser à l’aide de [RDP](bastion-connect-vm-rdp.md) ou de [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="launch-the-clipboard-tool"></a>Démarrer l’outil presse-papiers

Au cours de la session à distance, lancez la palette d’outils d’accès au Presse-papiers Bastion en sélectionnant les deux flèches situées au milieu à gauche de la session.

![outils](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Sélectionner le plein écran

Sélectionnez le bouton **Plein écran** pour passer la session en plein écran. Lorsque vous changez, la session est réinitialisée en plein écran.

![plein écran](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
Découvrez comment [Copier et coller](bastion-vm-copy-paste.md) vers et depuis une machine virtuelle Azure.