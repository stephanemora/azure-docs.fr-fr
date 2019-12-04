---
title: Tutoriel `:` Utiliser une identité managée de machine virtuelle Linux pour accéder à l’API Graph Azure AD
description: Ce didacticiel vous guide tout au long du processus consistant à utiliser une identité managée de machine virtuelle Linux affectée par le système pour accéder à l’API Azure AD Graph.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68d560e7d326cc2ddc47ed9f689dc8e31f8ab9ff
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183643"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Didacticiel : Utiliser une identité managée de machine virtuelle Linux affectée par le système pour accéder à l’API Azure AD Graph

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité managée de machine virtuelle Linux affectée par le système pour accéder à l’API Azure AD Graph afin de récupérer ses appartenances aux groupes. Les identités managées pour ressources Azure sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code.  

Dans le cadre de ce didacticiel, vous allez effectuer une requête sur l’appartenance de l’identité de votre machine virtuelle à des groupes Azure AD. Les informations de groupe sont souvent utilisées pour les décisions d’autorisation. En coulisses, l’identité managée de votre machine virtuelle est représentée par un **principal de service** dans Azure AD. 

> [!div class="checklist"]
> * Se connecter à Azure AD
> * Ajouter l’identité de votre machine virtuelle à un groupe dans Azure AD 
> * Accorder à l’identité de la machine virtuelle l’accès à Azure AD Graph 
> * Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour appeler Azure AD Graph

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Installez la dernière version de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

- Pour que vous puissiez accorder à l’identité de la machine virtuelle l’accès à Azure AD Graph, le rôle **Administrateur général** doit être assigné à votre compte dans Azure AD.

## <a name="connect-to-azure-ad"></a>Se connecter à Azure AD

Vous devez vous connecter à Azure AD pour assigner la machine virtuelle à un groupe et pour accorder à la machine virtuelle l’autorisation de récupérer ses appartenances aux groupes.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Ajouter l’identité de votre machine virtuelle à un groupe dans Azure AD

Lorsque vous avez activé l’identité managée affectée par le système sur la machine virtuelle Linux, un principal de service a été créé dans Azure AD.  Vous devez ajouter la machine virtuelle à un groupe. Consultez l’article suivant pour obtenir des instructions sur l’ajout d’une machine virtuelle à un groupe dans Azure AD :

- [Add group members](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add) (Ajouter des membres à un groupe)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Accorder à votre machine virtuelle l’accès à l’API Azure AD Graph

À l’aide d’identités managées pour ressources Azure, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure AD. L’API Microsoft Azure AD Graph prend en charge l’authentification Azure AD. Dans cette étape, vous allez accorder au principal de service de l’identité de votre machine virtuelle l’accès à Azure AD Graph, de sorte qu’il puisse effectuer une requête d’appartenance à des groupes. Les principaux de service doivent bénéficier de **permissions d’application** pour pouvoir accéder à Microsoft ou Azure AD Graph. Le type de permission d’application que vous devez accorder dépend de l’entité à laquelle vous souhaitez accéder dans MS ou Azure AD Graph.

Dans le cadre de ce didacticiel, vous allez accorder à l’identité de votre machine virtuelle l’autorisation d’effectuer une requête sur les appartenances à des groupes à l’aide de la permission d’application `Directory.Read.All`. Pour que vous puissiez accorder cette autorisation, le rôle Administrateur général doit être assigné à votre compte d’utilisateur dans Azure AD. La méthode la plus courante pour accorder une permission d’application consiste à accéder à l’inscription de votre application dans le Portail Azure et à ajouter l’autorisation. Toutefois, les identités managées pour les ressources Azure n’inscrivent pas les objets d’application dans Azure AD. Elles inscrivent uniquement les principaux de service. Pour inscrire la permission d’application, vous allez utiliser l’outil de ligne de commande Azure AD PowerShell. 

Azure AD Graph :
- AppID du principal de service (utilisé quand vous accordez la permission d’application) : 00000002-0000-0000-c000-000000000000
- ID de ressource (utilisé lors d’une demande de jeton d’accès à partir des identités managées pour les ressources Azure) : https://graph.windows.net
- Référence de l’étendue d’autorisation : [Référence des autorisations pour Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Accorder des permissions d’application à l’aide de CURL

1. Récupérez un jeton pour effectuer des requêtes CURL :

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Vous devez récupérer et notez le `objectId` de votre machine virtuelle. Il est utilisé dans les étapes suivantes pour accorder à la machine virtuelle les autorisations requises pour lire son appartenance aux groupes. Remplacez `<ACCESS TOKEN>` par le jeton d’accès que vous avez récupéré à l’étape précédente.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. À l’aide de l’appID Azure AD Graph, 00000002-0000-0000-c000-000000000000, récupérez et notez le `objectId` pour `odata.type: Microsoft.DirectoryServices.ServicePrincipal` et le `id` pour l’autorisation de rôle d’application `Directory.Read.All`.  Remplacez `<ACCESS TOKEN>` par le jeton d’accès que vous avez récupéré précédemment.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Réponse :

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. À présent, accordez au principal de service de la machine virtuelle l’accès en lecture aux objets d’annuaire Azure AD par le biais de l’API Azure AD Graph.  La valeur `id` correspond à la valeur de l’autorisation de rôle d’application `Directory.Read.All` et la valeur `resourceId` est le `objectId` pour le principal de service `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (les valeurs que vous avez notées à l’étape précédente).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle pour appeler Azure AD Graph 

Pour effectuer cette procédure, vous avez besoin d'un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail, accédez à votre machine virtuelle Linux et dans **Vue d’ensemble**, cliquez sur **Connexion**.  
2. **Connectez-vous** à la machine virtuelle à l’aide du client SSH de votre choix. 
3. Dans la fenêtre de terminal, avec CURL, envoyez une requête aux identités managées locales pour le point de terminaison des ressources Azure en vue d’obtenir un jeton d’accès pour Azure AD Graph.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   La réponse inclut le jeton d’accès dont vous avez besoin pour accéder à Azure AD Graph.

   Réponse :

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. À l’aide de l’ID d’objet du principal de service de votre machine virtuelle (la valeur que vous avez récupérée lors des étapes précédentes), vous pouvez interroger l’API Azure AD Graph pour récupérer ses appartenances aux groupes. Remplacez `<OBJECT-ID>` par l’ID d’objet du principal de service de votre machine virtuelle, et remplacez `<ACCESS-TOKEN>` par le jeton d’accès obtenu précédemment :

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Réponse :

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser une identité managée de machine virtuelle Linux affectée par le système pour accéder à Azure AD Graph.  Pour en savoir plus sur Azure AD Graph, consultez :

>[!div class="nextstepaction"]
>[API Graph Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
