---
title: Accès et authentification sans connexion - Azure Logic Apps | Microsoft Docs
description: Créez une identité managée afin que votre application logique puisse s’authentifier et accéder aux ressources dans d’autres locataires Azure AD (Azure Active Directory) sans vos informations d’identification
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973964"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Accéder aux ressources et s’authentifier en tant qu’identités managées dans Azure Logic Apps

Pour accéder aux ressources dans d’autres locataires Azure AD (Azure Active Directory) et authentifier votre identité sans vous connecter, vous pouvez créer une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) que votre application logique utilise au lieu de vos informations d’identification. Azure gère cette identité pour vous et vous aide à sécuriser vos informations d’identification, car vous n’êtes pas obligé de fournir ni de faire pivoter des secrets. Cet article montre comment créer et utiliser une identité managée pour votre application logique. Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../app-service/app-service-managed-service-identity.md).

> [!NOTE]
> Le nom « identités managées pour les ressources Azure » est le nouveau nom du service anciennement nommé MSI (Managed Service Identity).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure ou, si vous n’en avez pas, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Application logique dans laquelle vous souhaitez utiliser l’identité managée. Si vous n’avez pas une application logique, consultez [Créer votre premier flux de travail d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Créer une identité managée

Vous pouvez créer ou activer une identité managée pour votre application logique via le portail Azure, les modèles Azure Resource Manager ou Azure PowerShell. 

### <a name="azure-portal"></a>Portail Azure

Pour créer une identité managée pour votre application logique via le portail Azure, activez le paramètre **Inscription auprès d’Azure Active Directory** dans les paramètres de flux de travail de votre application logique.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Procédez comme suit : 

   1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**. 

   1. Sous **Managed Service Identity** > 
   **Inscription auprès d’Azure Active Directory**, choisissez **Activé**.

   1. Quand vous avez terminé, sélectionnez **Enregistrer** dans la barre d’outils.

      ![Activer le paramètre d’identité managée](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure affiche maintenant ces propriétés et valeurs pour l’identité managée de votre application logique :

      ![GUID de l’ID du principal et l’ID du locataire](./media/create-managed-service-identity/principal-tenant-id.png)

      | Propriété | Valeur | Description | 
      |----------|-------|-------------| 
      | **ID du principal** | <*GUID-ID-principal*> | Identificateur global unique (GUID) qui représente l’application logique dans un locataire Azure AD | 
      | **Tenant ID** | <*GUID-ID-locataire-Azure-AD*> | Identificateur global unique (GUID) qui représente le locataire Azure AD où votre application logique est maintenant un membre. À l’intérieur du locataire Azure AD, le principal du service a le même nom que l’instance d’application logique. | 
      ||| 

### <a name="deployment-template"></a>Modèle de déploiement

Pour automatiser la création et le déploiement des ressources Azure telles que les applications logiques, vous pouvez configurer des modèles Azure Resource Manager. Pour plus d’informations, consultez [Créer et déployer des applications logiques à l’aide des modèles Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). 

Pour créer une identité managée pour votre application logique via un modèle, ajoutez l’élément **identity** et la propriété **type** à la définition de flux de travail d’application logique dans votre modèle de déploiement. Ces paramètres indiquent qu’Azure crée et gère cette identité pour votre application logique :

```json
"identity": {
    "type": "SystemAssigned"
}
```

Par exemple, votre application logique peut ressembler à cette version :

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| Propriété | Valeur | Description | 
|----------|-------|-------------|
| **principalId** | <*GUID-ID-principal*> | Identificateur global unique (GUID) qui représente l’application logique dans le locataire Azure AD | 
| **tenantId** | <*GUID-ID-locataire-Azure-AD*> | Identificateur global unique (GUID) qui représente le locataire Azure AD où l’application logique est maintenant un membre. À l’intérieur du locataire Azure AD, le principal du service a le même nom que l’instance d’application logique. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Accéder aux ressources avec une identité managée

Une fois que vous avez créé une identité managée pour votre application logique, vous pouvez [accorder à cette identité l’accès à d’autres ressources](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Vous pouvez ensuite utiliser cette identité managée pour l’authentification, comme n’importe quel autre [principal du service](../active-directory/develop/app-objects-and-service-principals.md). 

Par exemple, supposons que vous avez déjà configuré une application logique avec une identité managée qui a accès à une autre ressource. Vous pouvez maintenant ajouter une action HTTP afin que votre application logique puisse envoyer une demande HTTP à cette ressource ou l’appeler. 

1. Dans votre application logique, ajoutez l’action **HTTP**. 

1. Fournissez les informations nécessaires pour cette action, telles que la **méthode** de demande et l’emplacement **URI** pour la ressource que vous souhaitez appeler.

1. Dans l’action HTTP, choisissez **Afficher les options avancées**. 

1. Dans la liste **Authentification**, sélectionnez **Managed Service Identity**, qui affiche ensuite la propriété **Audience** que vous devez définir :

   ![Sélectionnez Managed Service Identity.](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Continuez à créer l’application logique comme vous le souhaitez.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Supprimer l’identité managée

Pour désactiver une identité managée sur votre application logique, vous pouvez suivre la procédure similaire à la création de l’identité via le portail Azure, les modèles de déploiement Azure Resource Manager ou Azure PowerShell. 

Quand vous supprimez votre application logique, Azure supprime automatiquement d’Azure AD l’identité affectée par le système de votre application logique.

### <a name="azure-portal"></a>Portail Azure

1. Dans le Concepteur d’application logique, ouvrez votre application logique.

1. Procédez comme suit : 

   1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**. 
   
   1. Sous **Managed Service Identity**, choisissez **Désactivé** pour la propriété **Inscription auprès d’Azure Active Directory**.

   1. Quand vous avez terminé, sélectionnez **Enregistrer** dans la barre d’outils.

      ![Désactiver le paramètre d’identité managée](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modèle de déploiement

Si vous avez créé l’identité managée de l’application logique avec un modèle de déploiement Azure Resource Manager, affectez à la propriété `"type"` de l’élément `"identity"` la valeur `"None"`. Cette action supprime également l’ID du principal d’Azure AD. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).
