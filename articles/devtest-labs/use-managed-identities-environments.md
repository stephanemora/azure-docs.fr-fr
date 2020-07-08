---
title: Utiliser des identités managées Azure pour créer des environnements dans DevTest Labs | Microsoft Docs
description: Découvrez comment utiliser des identités managées dans Azure pour déployer des environnements dans un labo dans Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4d4df9cab17289eba21caf9d7c88eb37626b3349
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478873"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Utiliser des identités managées Azure pour déployer des environnements dans un labo 
En tant que propriétaire de labo, vous pouvez utiliser une identité managée pour déployer des environnements dans un labo. Cette fonctionnalité est utile dans les scénarios où l’environnement contient des ressources Azure (par exemple, des coffres de clés, des galeries d’images partagées et des réseaux externes au groupe de ressources de l’environnement) ou qu’il a des références à de telles ressources. Elle permet de créer des environnements de bac à sable (sandbox) qui ne sont pas limités au groupe de ressources de cet environnement.

> [!NOTE]
> Actuellement, une seule identité attribuée par l’utilisateur est prise en charge par labo. 

## <a name="prerequisites"></a>Conditions préalables requises
- [Créer, lister, supprimer ou affecter un rôle à une identité managée attribuée par l’utilisateur à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Dans cette section, vous, en tant que propriétaire de labo, utilisez le portail Azure pour ajouter une identité managée par l’utilisateur au labo. 

1. Dans la page du labo, sélectionnez **Configuration et stratégies**. 
1. Sélectionnez **Identité** dans la section **Paramètres**.
1. Pour ajouter une identité attribuée par l’utilisateur, sélectionnez **Ajouter** dans la barre d’outils. 
1. Sélectionnez une **identité** dans une liste déroulante préremplie.
1. Sélectionnez **OK**.

    ![Ajouter une identité managée par l’utilisateur](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. L’identité managée par l’utilisateur ajoutée s’affiche dans la liste. 

    ![Identité managée par l’utilisateur dans la liste](./media/use-managed-identities-environments/identity-in-list.png)

Une fois cette identité enregistrée, le labo l’utilise lors du déploiement de tous les environnements de labo. Vous pouvez également accéder à la ressource d’identité dans Azure en sélectionnant l’identité dans la liste. 

Le propriétaire du labo n’a pas besoin d’effectuer une action particulière lors du déploiement d’un environnement tant que l’identité ajoutée au labo dispose d’autorisations sur les ressources externes auxquelles l’environnement doit accéder. 

Pour changer l’identité managée par l’utilisateur attribuée au labo, supprimez d’abord l’identité attachée au labo, puis ajoutez-lui en une autre. Pour supprimer une identité attachée au labo, sélectionnez les points de suspension ( **...** ), puis cliquez sur **Supprimer**. 

![Identité managée par l’utilisateur dans la liste](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Utiliser une API

1. Après avoir créé une identité, notez l’ID de ressource de cette identité. Il doit ressembler à l’exemple suivant : 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Exécutez une méthode HTTPS PUT pour ajouter une nouvelle ressource `ServiceRunner` au labo similaire à l’exemple suivant. La ressource d’exécuteur de service est une ressource proxy permettant de gérer et de contrôler les identités managées dans DevTest Labs. Le nom de l’exécuteur de service peut être n’importe quel nom valide, mais nous vous recommandons d’utiliser le nom de la ressource d’identité managée. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Voici un exemple : 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Une fois que l’identité attribuée par l’utilisateur a été ajoutée au labo, le service Azure DevTest Labs l’utilise lors du déploiement d’environnements Azure Resource Manager. Par exemple, si vous avez besoin que votre modèle Resource Manager accède à une image de galerie d’images partagées externe, vérifiez que l’identité que vous avez ajoutée au labo dispose des autorisations minimales exigées pour la ressource de galerie d’images partagées. 
