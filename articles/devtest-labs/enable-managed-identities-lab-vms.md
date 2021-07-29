---
title: Activer les identités managées sur des machines virtuelles Lab dans Azure DevTest Labs
description: Cet article montre comment un propriétaire de labo peut activer les identités managées affectées par l’utilisateur sur des machines virtuelles Lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0d2c7b944d37160df241e6ca4407c730593f1b62
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854033"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Activer les identités managées affectées par l’utilisateur sur des machines virtuelles Lab dans Azure DevTest Labs
En tant que propriétaire de labo, vous pouvez activer les identités managées affectées par l’utilisateur sur vos machines virtuelles Lab dans Azure DevTest Labs.

Les identités managées permettent de s’authentifier auprès de tous les services qui prennent en charge l’authentification Azure Active Directory (AD), notamment Key Vault, sans passer d’informations d’identification dans le code. Pour plus d’informations sur les identités managées, voir [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).

Grâce à cette fonctionnalité, les utilisateurs du labo peuvent partager des ressources Azure comme Azure SQL Database dans le contexte du labo. L’authentification auprès de la ressource est gérée par l’identité elle-même. Une fois configurée, chaque machine virtuelle Lab existante/créée sera activée avec cette identité. Les utilisateurs du labo peuvent accéder aux ressources une fois connectés à leurs machines.

> [!NOTE]
> Vous pouvez ajouter plusieurs identités managées affectées par l’utilisateur à activer sur vos machines virtuelles Lab.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Pour ajouter une identité managée affectée par l’utilisateur pour des machines virtuelles Lab, suivez ces étapes :

1. [Créez une identité managée affectée par l’utilisateur dans votre abonnement](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).
1. Accédez à la page **Configuration et stratégies** de votre labo.
1. Sélectionnez **Identité (préversion)** dans le menu de gauche.
1. Sélectionnez l’onglet **Machine virtuelle**.
1. Sélectionnez **Ajouter** pour sélectionner une identité existante à partir d’une liste déroulante préremplie. 

    > [!div class="mx-imgBorder"]
    > ![Bouton Ajouter une identité](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Sélectionnez une **identité gérée par l’utilisateur** existante dans la liste déroulante, puis sélectionnez **OK**. 

    > [!div class="mx-imgBorder"]
    > ![Ajouter une identité](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Utiliser une API

1.  Après avoir créé une identité, notez son ID de ressource. Il doit ressembler à l’exemple suivant : 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.
    
2. Exécutez une méthode HTTPs sur la ressource lab pour ajouter une ou plusieurs identités affectées par l’utilisateur au champ **managementIdentities** .


    ```json
    {
        "location": "southeastasia",
        "properties": {
        ...
            "managementIdentities": {
               "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}": {}
        },
        ...
        },
    ...
    }
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les identités managées, voir [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).







