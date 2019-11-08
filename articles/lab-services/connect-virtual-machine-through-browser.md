---
title: Se connecter à des machines virtuelles à l’aide d’un navigateur – Azure | Microsoft Docs
description: Découvrez comment vous connecter à vos machines virtuelles à l’aide d’un navigateur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 1bb5d979ff7c9fe6e2afec4c7ebd81649b4488e3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581231"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Connectez-vous à vos machines virtuelles à l’aide d’un navigateur 

DevTest Labs s’intègre à [Azure Bastion](https://docs.microsoft.com/azure/bastion/), ce qui vous permet de vous connecter à vos machines virtuelles à l’aide d’un navigateur. Pour plus d’informations sur l’activation de cette fonctionnalité dans DevTest Labs, consultez la page [Activer la connexion du navigateur sur des machines virtuelles du labo](enable-browser-connection-lab-virtual-machines.md).

Une fois la *connexion du navigateur*, les utilisateurs du labo peuvent accéder aux machines virtuelles à l’aide d’un navigateur.  


## <a name="create-a-lab-virtual-machine"></a>Créer une machine virtuelle du labo

Vous devez d’abord créer la machine virtuelle du labo dans un réseau virtuel sur lequel Bastion est configuré. Vous pouvez sélectionner un réseau virtuel lors de la création de la machine virtuelle en accédant à l’onglet **Paramètres avancés**.

![Créer une machine virtuelle](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Lancez la machine virtuelle dans un navigateur

Une fois la machine virtuelle créée, vous pouvez la lancer dans un navigateur en cliquant sur le bouton *Connexion du navigateur* et en entrant votre nom d’utilisateur et votre mot de passe pour la machine.  

![Lancez dans le navigateur](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Étapes suivantes

[Add a VM to a lab in Azure DevTest Labs](devtest-lab-add-vm.md) (Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs)
