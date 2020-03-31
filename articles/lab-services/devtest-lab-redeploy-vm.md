---
title: Redéployer une machine virtuelle dans un labo dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment redéployer une machine virtuelle (déplacement depuis un nœud Azure vers un autre) dans Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561634"
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

    ![Redeploy](media/devtest-lab-redeploy-vm/redeploy.png)
7. Lisez les informations contenues dans la page, puis sélectionnez le bouton **Redéployer**. 9. Vérifiez l’état de l’opération de redéploiement dans la fenêtre **Notifications**.

    ![État du redéploiement](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment redimensionner une machine virtuelle dans Azure DevTest Labs (consultez [Redimensionner une machine virtuelle](devtest-lab-resize-vm.md)).


