---
title: Stocker des secrets dans un coffre de clés dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment stocker des secrets dans un coffre Azure Key Vault et les utiliser pour créer une machine virtuelle, une formule ou un environnement.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 3f16d84f66f2da6094054d161f286070fc86a73b
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720121"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Stocker des secrets dans un coffre de clés dans Azure DevTest Labs
Vous pouvez avoir besoin d’entrer un secret complexe lorsque vous utilisez Azure DevTest Labs : un mot de passe pour votre machine virtuelle Windows, une clé SSH publique pour votre machine virtuelle Linux ou un jeton d’accès personnel pour cloner votre dépôt Git via un artefact. Les secrets sont généralement longs et ont des caractères aléatoires. Par conséquent, la saisie d’un secret peut être difficile et peu pratique, surtout si vous l’utilisez plusieurs fois.

Pour résoudre ce problème tout en conservant vos secrets dans un emplacement sûr, DevTest Labs prend en charge le stockage des secrets dans un [coffre de clés Azure](../key-vault/key-vault-overview.md). Quand un utilisateur enregistre un secret pour la première fois, le service DevTest Labs crée automatiquement un coffre de clés dans le groupe de ressources qui contient le laboratoire et stocke le secret dans le coffre de clés. DevTest Labs crée un coffre de clés distinct pour chaque utilisateur. 

Veuillez noter que l’utilisateur du laboratoire devra d’abord créer une machine virtuelle de laboratoire avant de pouvoir créer un secret dans le coffre de clés. Cela est dû au fait que le service DevTest Lab doit associer l’utilisateur du laboratoire à un document utilisateur valide avant qu’il ne soit autorisé à créer et à stocker des secrets dans son coffre de clés. 


## <a name="save-a-secret-in-azure-key-vault"></a>Enregistrer un secret dans Azure Key Vault
Pour enregistrer votre secret dans Azure Key Vault, procédez comme suit :

1. Sélectionnez **Mes secrets** dans le menu de gauche.
2. Entrez un **nom** pour le secret. Vous voyez ce nom dans la liste déroulante lorsque vous créez une machine virtuelle, une formule ou un environnement. 
3. Entrez le secret en tant que **valeur**.

    ![Stockage d’un secret](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Utiliser un secret à partir d’Azure Key Vault
Lorsque vous avez besoin d’entrer un secret pour créer une machine virtuelle, une formule ou un environnement, vous pouvez entrer un secret manuellement ou sélectionner un secret enregistré dans le coffre de clés. Pour utiliser un secret stocké dans votre coffre de clés, effectuez les actions suivantes :

1. Sélectionnez **Utiliser un secret enregistré**. 
2. Sélectionnez votre secret dans la liste déroulante pour **Choisir un secret**. 

    ![Utilisation d’un secret dans la machine virtuelle](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Utiliser un secret dans un modèle Azure Resource Manager
Vous pouvez spécifier le nom de votre secret dans un modèle Azure Resource Manager qui est utilisé pour créer une machine virtuelle, comme illustré dans l’exemple suivant :

![Utilisation d’un secret dans une formule ou un environnement](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer une machine virtuelle à l’aide du secret](devtest-lab-add-vm.md) 
- [Créer une formule à l’aide du secret](devtest-lab-manage-formulas.md)
- [Créer un environnement à l’aide du secret](devtest-lab-create-environment-from-arm.md)
