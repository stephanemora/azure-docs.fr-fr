---
title: Utiliser des identités managées Azure pour créer des environnements dans DevTest Labs | Microsoft Docs
description: Découvrez comment utiliser des identités managées dans Azure pour déployer des environnements dans un labo dans Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4a8afd74014cb940be17d9a84168e8bfe7daff67
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854681"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Utiliser des identités managées Azure pour déployer des environnements dans un labo 

En tant que propriétaire de labo, vous pouvez utiliser une identité managée pour déployer des environnements dans un labo. Cette fonctionnalité est utile dans les scénarios où l’environnement contient des ressources Azure (par exemple, des coffres de clés, des galeries d’images partagées et des réseaux externes au groupe de ressources de l’environnement) ou qu’il a des références à de telles ressources. Elle permet de créer des environnements de bac à sable (sandbox) qui ne sont pas limités au groupe de ressources de cet environnement. 

Par défaut, lorsque vous créez un environnement, le lab crée une identité affectée par le système pour accéder aux ressources et services Azure au nom d’un utilisateur du lab lors du déploiement du modèle Azure Resource Manager (modèle ARM). En savoir plus sur [les raisons pour lesquelles un lab crée une identité affectée par le système](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity). Pour les labs nouveaux et existants, une identité affectée par le système est créée par défaut lors de la première création d’un environnement lab.  

Notez que, en tant que propriétaire du lab, vous pouvez choisir d’accorder les autorisations d’identité affectées par le système au lab pour accéder aux ressources Azure en dehors du lab, ou vous pouvez utiliser votre propre identité affectée par l’utilisateur pour le scénario. L’identité affectée par le système du lab n’est valide que pendant la durée de vie du lab. L’identification affectée par le système est supprimée lorsque vous supprimez le lab. Lorsque vous avez des environnements dans plusieurs labs qui doivent utiliser une identité, envisagez d’utiliser une identité affectée par l’utilisateur.  

> [!NOTE]
> Actuellement, une seule identité attribuée par l’utilisateur est prise en charge par labo. 

## <a name="prerequisites"></a>Conditions préalables requises

- [Créez, répertoriez, supprimez ou attribuez un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Vérifiez que votre identité managée a été créée dans la même région et le même abonnement que votre labo. L’identité managée ne doit pas nécessairement se trouver dans le même groupe de ressources.

## <a name="use-azure-portal"></a>Utiliser le portail Azure

Dans cette section, vous, en tant que propriétaire de labo, utilisez le portail Azure pour ajouter une identité managée par l’utilisateur au labo. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **DevTest Labs**.
1. Dans la liste des laboratoires, sélectionnez le laboratoire souhaité.
1. Sélectionnez **Configuration et stratégies** -> **Identité (Préversion)** . 
1. Pour ajouter une identité affectée par l’utilisateur, sélectionnez l’onglet **Affectée par l’utilisateur**.
1. Appuyez sur **Ajouter**.
1. Dans la liste déroulante, sélectionnez un utilisateur existant que vous avez créé et/ou auquel vous avez accès.
 
    ![Ajouter une identité managée par l’utilisateur](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Appuyez sur **Enregistrer** en haut de la page.

    Une fois cette identité enregistrée, le labo l’utilise lors du déploiement de tous les environnements de labo. Vous pouvez également accéder à la ressource d’identité dans Azure en sélectionnant l’identité dans la liste. 

Le propriétaire du labo n’a pas besoin d’effectuer une action particulière lors du déploiement d’un environnement tant que l’identité ajoutée au labo dispose d’autorisations sur les ressources externes auxquelles l’environnement doit accéder. 

Pour changer l’identité managée par l’utilisateur attribuée au labo, supprimez d’abord l’identité attachée au labo, puis ajoutez-lui en une autre. Pour supprimer une identité attachée au labo, sélectionnez les points de suspension (**...**), puis cliquez sur **Supprimer**. 

## <a name="use-api"></a>Utiliser une API

1. Après avoir créé une identité, notez l’ID de ressource de cette identité. Il doit ressembler à l’exemple suivant : 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.

1. Exécutez une méthode HTTPS sur la ressource lab pour ajouter une identité affectée par l’utilisateur ou activer une identité affectée par le système pour le lab.

   > [!NOTE]
   > Que vous ayez créé une identité affectée par l’utilisateur ou non, le lab crée automatiquement une identité affectée par le système la première fois qu’un environnement lab est créé. Toutefois, si une identité affectée par l’utilisateur est déjà configurée pour le lab, le service lab DevTest continue à utiliser cette identité pour déployer les environnements lab. 
 
    ```json
    
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{labname}

    {
        "location": "{location}",
        "properties": {
          **lab properties**
         } 
        "identity":{
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}":{}
            }
        } 
    }
    
    ```
 
Une fois que l’identité attribuée par l’utilisateur a été ajoutée au labo, le service Azure DevTest Labs l’utilise lors du déploiement d’environnements Azure Resource Manager. Par exemple, si vous avez besoin que votre modèle Resource Manager accède à une image de galerie d’images partagées externe, vérifiez que l’identité que vous avez ajoutée au labo dispose des autorisations minimales exigées pour la ressource de galerie d’images partagées. 
