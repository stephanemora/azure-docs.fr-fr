---
title: 'Copier et coller vers et depuis une machine virtuelle : Azure Bastion | Microsoft Docs'
description: Dans cet article, découvrez comment copier et coller vers et depuis une machine virtuelle Azure à l’aide de Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191332"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Copier et coller vers une machine virtuelle : Azure Bastion (préversion)

Cet article vous aide à copier et coller du texte vers et depuis des machines virtuelles à l’aide d’Azure Bastion. Avant d’utiliser une machine virtuelle, assurez-vous d’avoir suivi les étapes de [Créer un hôte Bastion](bastion-create-host-portal.md). Connectez-vous ensuite à la machine virtuelle que vous souhaitez utiliser à l’aide de [RDP](bastion-connect-vm-rdp.md) ou de [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Pour les navigateurs qui prennent en charge l’accès à l’API de Presse-papiers avancé, vous pouvez copier et coller du texte entre votre appareil local et la session à distance comme si vous le faisiez entre des applications sur votre appareil local. Pour les autres navigateurs, vous pouvez utiliser la palette d’outils d’accès au Presse-papiers Bastion.

  ![Autoriser le Presse-papiers](./media/bastion-vm-manage/allow.png)

Seul le copier/coller de texte est pris en charge. Pour les opérations de copie et collage directs, votre navigateur peut vous demander l’accès au Presse-papiers lors de l’initialisation de la session Bastion. **Autorisez** la page web à accéder au Presse-papiers.

## <a name="to"></a>Copier vers une session à distance

Une fois connecté à la machine virtuelle à l’aide du [portail Azure](https://aka.ms/BastionHost) pour la préversion de Bastion :

1. Copiez le texte/contenu de l’appareil local vers le Presse-papiers local.
1. Au cours de la session à distance, lancez la palette d’outils d’accès au Presse-papiers Bastion en sélectionnant les deux flèches. Les flèches sont situées à gauche au centre de la session.

    ![palette d’outils](./media/bastion-vm-manage/left.png)

    ![presse-papiers](./media/bastion-vm-manage/clipboard.png)

1. En règle générale, le texte copié s’affiche automatiquement dans la palette copier/coller Bastion. Si votre texte n’y apparaît pas, collez le texte dans la zone de texte de la palette.
1. Lorsque le texte est présent dans la zone de texte, vous pouvez le coller dans la session à distance.

    ![coller](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Copier depuis une session à distance

Une fois connecté à la machine virtuelle à l’aide du [portail Azure](https://aka.ms/BastionHost) pour la préversion de Bastion :

1. Copiez le texte/contenu depuis la session à distance dans le Presse-papiers à distance (à l’aide de Ctrl-C).

    ![palette d’outils](./media/bastion-vm-manage/remote.png)

1. Au cours de la session à distance, lancez la palette d’outils d’accès au Presse-papiers Bastion en sélectionnant les deux flèches. Les flèches sont situées à gauche au centre de la session.

    ![presse-papiers](./media/bastion-vm-manage/clipboard2.png)

1. En règle générale, le texte copié s’affiche automatiquement dans la palette copier/coller Bastion. Si votre texte n’y apparaît pas, collez le texte dans la zone de texte de la palette.
1. Lorsque le texte est présent dans la zone de texte, vous pouvez le coller dans l’appareil local.

    ![coller](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes (FAQ) sur Bastion](bastion-faq.md).