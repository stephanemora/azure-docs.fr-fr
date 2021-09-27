---
title: Connectez-vous à vos machines virtuelles à l’aide d’un navigateur
description: Découvrez comment vous connecter à vos machines virtuelles à l’aide d’un navigateur.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: dbbaa4286aac91e362a2024f1705add8f48d566d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644996"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Connectez-vous à vos machines virtuelles à l’aide d’un navigateur 

DevTest Labs s’intègre à [Azure Bastion](../bastion/index.yml), ce qui vous permet de vous connecter à vos machines virtuelles via un navigateur. Pour plus d’informations sur l’activation de cette fonctionnalité dans DevTest Labs, consultez la page [Activer la connexion du navigateur sur des machines virtuelles du labo](enable-browser-connection-lab-virtual-machines.md).

Une fois la *connexion du navigateur*, les utilisateurs du labo peuvent accéder aux machines virtuelles à l’aide d’un navigateur.  

## <a name="create-a-lab-virtual-machine"></a>Créer une machine virtuelle du labo

Vous devez d’abord créer la machine virtuelle du labo dans un réseau virtuel sur lequel Bastion est configuré. Sélectionnez le deuxième **sous-réseau** que vous avez créé, pas AzureBastionSubnet. Vous pouvez sélectionner un réseau virtuel durant la création de la machine virtuelle en accédant à l’onglet **Paramètres avancés**.

![Créer une machine virtuelle](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Lancez la machine virtuelle dans un navigateur

Une fois la machine virtuelle créée, vous pouvez la lancer dans un navigateur en cliquant sur le bouton *Connexion du navigateur* et en entrant votre nom d’utilisateur et votre mot de passe pour la machine.  

![Lancez dans le navigateur](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Étapes suivantes

[Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs](devtest-lab-add-vm.md)
