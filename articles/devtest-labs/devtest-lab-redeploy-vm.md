---
title: Redéployer une machine virtuelle dans un laboratoire
description: Découvrez comment redéployer une machine virtuelle (déplacement depuis un nœud Azure vers un autre) dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 375b70d3058a8fb0e23e65f4b96b4601aad12723
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591574"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Redéployer une machine virtuelle dans un labo dans Azure DevTest Labs
Si vous ne pouvez pas vous connecter à une machine virtuelle dans un labo via une connexion Bureau à distance, redéployez la machine virtuelle et réessayez de vous y connecter. Quand vous redéployez une machine virtuelle, DevTest Labs la déplace depuis le nœud sur lequel elle est en cours d’exécution vers un nouveau nœud au sein de l’infrastructure Azure. Il démarre ensuite la machine virtuelle tout en conservant la totalité des options de configuration et des ressources associées. Ainsi, vous n’avez pas besoin de consacrer du temps à la résolution de problèmes liés à la connexion Bureau à distance ou à l’accès d’une application à des machines virtuelles Windows dans le labo. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Étapes du redéploiement d’une machine virtuelle dans un labo 
Pour redéployer une machine virtuelle dans un labo dans Azure DevTest Labs, effectuez les étapes suivantes : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Dans la liste des labos, sélectionnez celui dans lequel se trouve la machine virtuelle que vous souhaitez redéployer.  
4. Dans le panneau de gauche, sélectionnez **Mes machines virtuelles**. 
5. Dans la liste des machines virtuelles, sélectionnez une machine virtuelle.
6. Dans la page Machine virtuelle associée à votre machine virtuelle, sélectionnez **Redéployer** sous **OPÉRATIONS** dans le menu de gauche.

    ![La capture d’écran montre la page de la machine virtuelle avec l’option Redéployer sélectionnée.](media/devtest-lab-redeploy-vm/redeploy.png)
7. Lisez les informations contenues dans la page, puis sélectionnez le bouton **Redéployer**. 9. Vérifiez l’état de l’opération de redéploiement dans la fenêtre **Notifications**.

    ![État du redéploiement](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment redimensionner une machine virtuelle dans Azure DevTest Labs (consultez [Redimensionner une machine virtuelle](devtest-lab-resize-vm.md)).
