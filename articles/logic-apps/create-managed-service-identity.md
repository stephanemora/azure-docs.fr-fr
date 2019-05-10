---
title: S’authentifier avec des identités managées - Azure Logic Apps | Microsoft Docs
description: Pour vous authentifier sans vous connecter, vous pouvez créer une identité managée (anciennement appelée Managed Service Identity ou MSI) afin que votre application logique puisse accéder aux ressources des autres locataires Azure Active Directory (Azure AD) sans identifiant ni clé secrète
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: 65fe89bf775a649d5654ce739d8d18e05d3048ca
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416110"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>S’authentifier et accéder aux ressources avec des identités managées dans Azure Logic Apps

Pour accéder aux ressources des autres locataires d’Azure Active Directory (Azure AD) et authentifier votre identité sans vous connecter, votre application logique peut utiliser une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) (anciennement appelée MSI ou Managed Service Identity), plutôt que des informations d’identification ou des clés secrètes. Azure gère cette identité pour vous et vous aide à sécuriser vos informations d’identification, car vous n’êtes pas obligé de fournir ni de faire pivoter des secrets. Cet article montre comment vous pouvez configurer et utiliser une identité managée attribuée par le système pour votre application logique. Pour en savoir plus sur les identités managées, consultez la section [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Votre application logique peut utiliser des identités gérées uniquement avec les connecteurs qui prennent en charge des identités gérées. Actuellement, uniquement le connecteur HTTP prend en charge des identités gérées.
>
> Vous pouvez actuellement disposer d’un maximum de 10 flux de travail d’applications logiques avec des identités assignées par le système dans chaque abonnement Azure.

## <a name="prerequisites"></a>Conditions préalables

* Abonnement Azure ou, si vous n’en avez pas, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* L’application logique dans laquelle vous souhaitez utiliser l’identité managée attribuée par le système. Si vous n’avez pas une application logique, consultez [Créer votre premier flux de travail d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Activer une identité managée

Pour les entités managées attribuées par le système, vous n’avez pas besoin de créer manuellement cette identité. Pour configurer une identité managée attribuée par le système pour votre application logique, vous pouvez procéder comme suit : 

* [Portail Azure](#azure-portal) 
* [Modèles Microsoft Azure Resource Manager](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Portail Azure

Pour activer une identité managée attribuée par le système pour votre application logique via le portail Azure, activez le paramètre **Attribuée par le système** dans les paramètres d’identité de votre application logique.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**. 

1. Sous **Attribuée par le système** > **État**, choisissez **Activé**. Puis, choisissez **Enregistrer** > **Oui**.

   ![Activer le paramètre d’identité managée](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Votre application logique dispose maintenant d’une identité managée attribuée par le système et inscrite dans Azure Active Directory :

   ![GUID de l’ID d’objet](./media/create-managed-service-identity/object-id.png)

   | Propriété | Valeur | Description  | 
   |----------|-------|-------------| 
   | **ID d’objet** | <*identity-resource-ID*> | Identificateur global unique (GUID) qui représente l’identité managée attribuée par le système pour votre application logique dans un locataire Azure AD | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

Lorsque vous souhaitez automatiser la création et le déploiement des ressources Azure telles que les applications logiques, vous pouvez utiliser des modèles [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). Pour créer une identité managée attribuée par le système pour votre application logique via un modèle, ajoutez l’élément `"identity"` et la propriété `"type"` à la définition de flux de travail d’application logique dans votre modèle de déploiement : 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Exemple :

```json
{
   "apiVersion": "2016-06-01", 
   "type": "Microsoft.logic/workflows", 
   "name": "[variables('logicappName')]", 
   "location": "[resourceGroup().location]", 
   "identity": { 
      "type": "SystemAssigned" 
   }, 
   "properties": { 
      "definition": { 
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#", 
         "actions": {}, 
         "parameters": {}, 
         "triggers": {}, 
         "contentVersion": "1.0.0.0", 
         "outputs": {} 
   }, 
   "parameters": {}, 
   "dependsOn": [] 
}
```

Quand Azure crée votre application logique, la définition de flux de travail de cette application logique inclut ces propriétés supplémentaires :

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriété | Valeur | Description  | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Identificateur global unique (GUID) qui représente l’application logique dans le locataire Azure AD et qui s’affiche parfois comme « ID d’objet » ou `objectID` | 
| **tenantId** | <*Azure-AD-tenant-ID*> | Identificateur global unique (GUID) qui représente le locataire Azure AD où l’application logique est maintenant un membre. À l’intérieur du locataire Azure AD, le principal du service a le même nom que l’instance d’application logique. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Accéder aux ressources avec une identité managée

Une fois que vous avez créé une identité managée attribuée par le système pour votre application logique, vous pouvez [accorder à cette identité l’accès à d’aux autres ressources Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Vous pouvez ensuite utiliser cette identité pour l’authentification, comme n’importe quel autre [principal du service](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> L’identité managée attribuée par le système et la ressource à laquelle vous souhaitez accorder l’accès doivent toutes les deux disposer du même abonnement Azure.

### <a name="assign-access-to-managed-identity"></a>Attribuer l’accès à l’identité managée

Pour que l’accès à une autre ressource Azure soit accordé à l’identité managée attribuée par le système de votre application logique, procédez comme suit :

1. Dans le Portail Azure, accédez à la ressource Azure pour laquelle vous souhaitez attribuer l’accès à votre identité managée. 

1. Dans le menu de la ressource, sélectionnez **contrôle d’accès (IAM)**. Dans la barre d’outils, choisissez **ajouter** > **ajouter une attribution de rôle**.

   ![Ajouter une attribution de rôle](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Sous **Ajouter une attribution de rôle**, sélectionnez le **Rôle** que vous voulez pour l’identité. 

1. Dans la propriété **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application AD Azure**, s’il n’est pas déjà sélectionné.

1. Dans la zone **Sélectionner**, entrez le nom de votre application logique en commençant par le premier caractère. Sélectionnez l’application logique lorsqu’elle s’affiche.

   ![Sélectionner une application logique avec identité managée](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Une fois ces opérations effectuées, sélectionnez **Enregistrer**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>S’authentifier avec une identité managée dans une application logique

Après avoir configuré votre application logique avec une identité managée attribuée par le système et un accès attribué à la ressource de votre choix pour cette identité, vous pouvez maintenant vous authentifier avec l’identité en question. Par exemple, vous pouvez utiliser une action HTTP afin que votre application logique puisse envoyer une requête HTTP à cette ressource ou l’appeler. 

1. Dans votre application logique, ajoutez l’action **HTTP**.

1. Fournissez les informations nécessaires pour cette action, telles que la **méthode** de demande et l’emplacement **URI** pour la ressource que vous souhaitez appeler.

   Par exemple, supposons que vous utilisez l’authentification Azure Active Directory (Azure AD) avec [l’un de ces services Azure qui prennent en charge Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   Dans la zone **URI**, entrez l’URL du point de terminaison pour ce service Azure. 
   Par conséquent, si vous utilisez Azure Resource Manager, entrez cette valeur dans la propriété **URI** :

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. Dans l’action HTTP, choisissez **Afficher les options avancées**.

1. Dans la liste **Authentification**, sélectionnez **Identité managée**. Après avoir sélectionné cette authentification, la propriété **Audience** s’affiche avec la valeur d’ID de ressource par défaut :

   ![Sélectionnez « Identité managée »](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > Dans la propriété **Audience**, la valeur d’ID de ressource doit correspondre exactement à ce qu’attend Azure AD, notamment les barres obliques de fin obligatoires. 
   > Vous pouvez trouver ces valeurs d’ID de ressource dans ce [tableau décrivant les services Azure qui prennent en charge Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Par exemple, si vous utilisez l’ID de ressource Azure Resource Manager, veillez à ce que l’URI contienne une barre oblique de fin.

1. Continuez à créer l’application logique comme vous le souhaitez.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Supprimer l’identité managée

Pour désactiver une identité managée attribuée par le système sur votre application logique, vous pouvez suivre la procédure similaire à la configuration de l’identité via le portail Azure, les modèles de déploiement Azure Resource Manager ou Azure PowerShell.

Quand vous supprimez votre application logique, Azure supprime automatiquement d’Azure AD l’identité affectée par le système de votre application logique.

### <a name="azure-portal"></a>Portail Azure

Pour supprimer une identité managée attribuée par le système pour votre application logique via le portail Azure, désactivez le paramètre **Attribuée par le système** dans les paramètres d’identité de votre application logique.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**. 

1. Sous **Attribuée par le système** > **État**, choisissez **Désactivé**. Puis, choisissez **Enregistrer** > **Oui**.

   ![Désactiver le paramètre d’identité managée](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modèle de déploiement

Si vous avez créé l’identité managée attribuée par le système de l’application logique avec un modèle de déploiement Azure Resource Manager, affectez à la propriété `"type"` de l’élément `"identity"` la valeur `"None"`. Cette action supprime également l’ID du principal d’Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).
