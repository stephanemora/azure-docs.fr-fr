---
title: Synchroniser les attributs avec Azure Active Directory pour le mappage
description: Lors de la configuration de l’attribution d’utilisateurs pour les applications Azure Active Directory et SaaS, servez-vous de la fonctionnalité d’extension de répertoire pour ajouter des attributs sources qui ne sont pas synchronisés par défaut.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: 102c0f7363b8d4f635762a33b82825e9ae71dfc6
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120790"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Synchronisation des attributs d’extension pour l’approvisionnement d’applications

Azure Active Directory (Azure AD) doit contenir toutes les données (attributs) nécessaires pour créer un profil utilisateur lors de l’attribution de comptes d’utilisateur d’Azure AD vers une [application SaaS](../saas-apps/tutorial-list.md). Lors de la personnalisation des mappages d’attributs pour l’attribution d’utilisateurs, vous constaterez peut-être que l’attribut à mapper n’apparaît pas dans la liste **Attribut source**. Cet article explique comment ajouter l’attribut manquant.

Pour les utilisateurs uniquement dans Azure AD, vous pouvez [créer des extensions de schéma à l’aide de PowerShell ou de Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

Pour les utilisateurs dans Windows Server Active Directory, vous devez synchroniser les utilisateurs sur Azure AD. Vous pouvez synchroniser les utilisateurs et les attributs en utilisant [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect synchronise automatiquement certains attributs avec Azure AD, mais pas tous. En outre, certains attributs (par exemple, SAMAccountName) synchronisés par défaut ne peuvent pas être exposés à l’aide de l’API Graph Azure AD. Dans ce cas, vous pouvez [utiliser la fonctionnalité d’extension de répertoire d’Azure AD Connect pour synchroniser l’attribut avec Azure AD](#create-an-extension-attribute-using-azure-ad-connect). De cette façon, l’attribut est visible pour l’API Graph Azure AD et le service de configuration Azure AD.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Créer un attribut d’extension sur un utilisateur cloud uniquement
Vous pouvez utiliser Microsoft Graph et PowerShell pour étendre le schéma d’utilisateur pour les utilisateurs dans Azure AD. Dans la plupart des cas, ces attributs d’extension sont automatiquement découverts.

Si vous avez plus de 1000 principaux de service, il est possible que des extensions manquent dans la liste des attributs sources. Si un attribut que vous avez créé ne s’affiche pas automatiquement, vérifiez que l’attribut a été créé et ajoutez-le manuellement à votre schéma. Pour vérifier qu’il a été créé, utilisez Microsoft Graph et [Graph Explorer](/graph/graph-explorer/graph-explorer-overview.md). Pour l’ajouter manuellement à votre schéma, consultez [Modification de la liste des attributs pris en charge](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Créer un attribut d’extension sur un utilisateur cloud uniquement à l’aide de Microsoft Graph
Vous pouvez étendre le schéma des utilisateurs Azure AD à l’aide de [Microsoft Graph](/graph/overview.md). 

Tout d’abord, répertoriez les applications de votre locataire pour récupérer l’ID de l’application sur laquelle vous travaillez. Pour en savoir plus, consultez [Lister extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Ensuite, créez l’attribut d’extension. Remplacez la propriété **ID** ci-dessous par l’**ID** récupéré à l’étape précédente. Vous devrez utiliser l’attribut **« ID »** , et non « appId ». Pour en savoir plus, consultez [Créer extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

La demande précédente a créé un attribut d’extension au format `extension_appID_extensionName`. Vous pouvez maintenant mettre à jour un utilisateur avec cet attribut d’extension. Pour en savoir plus, consultez [Mettre à jour un utilisateur](/graph/api/user-update.md?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Enfin, vérifiez l’attribut de l’utilisateur. Pour en savoir plus, consultez [Obtenir un utilisateur](/graph/api/user-get.md?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Créez un attribut d’extension sur un utilisateur cloud uniquement à l’aide de PowerShell.
Créer une extension personnalisée à l’aide de PowerShell et attribuer une valeur à un utilisateur. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Créer un attribut d’extension à l’aide d’Azure AD Connect

1. Ouvrez l’Assistant Azure AD Connect et sélectionnez Tâches, puis **Personnaliser les options de synchronisation**.

   ![Page Tâches supplémentaires de l’Assistant Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Connectez-vous en tant qu’Administrateur général Azure AD. 

3. Sur la page **Fonctionnalités facultatives**, sélectionnez **Synchronisation des attributs des extensions d’annuaire**.
 
   ![Page Fonctionnalités facultatives de l’Assistant Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Sélectionnez le ou les attributs que vous souhaitez étendre à Azure AD.
   > [!NOTE]
   > La recherche **Attributs disponibles** respecte la casse.

   ![Capture d’écran montrant la page de sélection « Extensions d’annuaire »](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Terminez l’Assistant Azure AD Connect et autorisez l’exécution d’un cycle de synchronisation complète. Lorsque le cycle est terminé, le schéma est étendu et les nouvelles valeurs sont synchronisées entre votre répertoire AD local et Azure AD.
 
6. Dans le Portail Azure, lorsque vous [modifiez des mappages d’attributs utilisateur](customize-application-attributes.md), la liste **Attribut source** contient désormais l’attribut ajouté au format `<attributename> (extension_<appID>_<attributename>)`. Sélectionnez l’attribut et mappez-le à l’application cible pour la configuration.

   ![Page de sélection des extensions d’annuaire de l’Assistant Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> À l’heure actuelle, il n’est pas possible de configurer les attributs de référence à partir du répertoire AD local, comme **managedby** ou **DN/DistinguishedName**. Vous pouvez demander cette fonctionnalité sur [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 


## <a name="next-steps"></a>Étapes suivantes

* [Définir qui est concerné par la configuration](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
