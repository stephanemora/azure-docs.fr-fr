---
title: Synchroniser les attributs avec Azure AD pour le mappage
description: Lors de la configuration des utilisateurs pour les applications SaaS, servez-vous de la fonctionnalité d’extension de répertoire pour ajouter des attributs sources qui ne sont pas synchronisés par défaut.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579431"
---
# <a name="syncing-extension-attributes-attributes"></a>Synchronisation d’attributs d’extension

Lors de la personnalisation des mappages d’attributs pour la configuration utilisateur, vous constaterez peut-être que l’attribut à mapper n’apparaît pas dans la liste **Attribut source**. Cet article explique comment ajouter l’attribut manquant en le synchronisant à partir de votre Active Directory (AD) local sur Azure Active Directory (Azure AD), ou en créant les attributs d’extension dans Azure AD pour un utilisateur cloud uniquement. 

Azure AD doit contenir toutes les données requises pour créer un profil utilisateur lors de la configuration des comptes utilisateur d’Azure AD vers une application SaaS. Pour rendre les données disponibles, vous devez parfois synchroniser les attributs de votre répertoire AD local vers Azure AD. Azure AD Connect synchronise automatiquement certains attributs avec Azure AD, mais pas tous. En outre, certains attributs (par exemple, SAMAccountName) synchronisés par défaut ne peuvent pas être exposés à l’aide de l’API Graph Azure AD. Dans ce cas, vous pouvez utiliser la fonctionnalité d’extension de répertoire Azure AD Connect pour synchroniser l’attribut avec Azure AD. De cette façon, l’attribut est visible pour l’API Graph Azure AD et le service de configuration Azure AD. Si les données dont vous avez besoin pour l’approvisionnement se trouvent dans Active Directory mais ne sont pas disponibles pour l’approvisionnement pour les raisons décrites ci-dessus, vous pouvez utiliser Azure AD Connect pour créer des attributs d’extension. 

Si la plupart des utilisateurs sont probablement des utilisateurs hybrides synchronisés à partir d’Active Directory, vous pouvez également créer des extensions sur des utilisateurs cloud uniquement sans utiliser Azure AD Connect. À l’aide de PowerShell ou de Microsoft Graph vous pouvez étendre le schéma d’un utilisateur cloud uniquement. 

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

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Créer un attribut d’extension sur un utilisateur cloud uniquement
Les clients peuvent utiliser Microsoft Graph et PowerShell pour étendre le schéma utilisateur. Ces attributs d’extension sont automatiquement découverts dans la plupart des cas, mais les clients disposant de plus de 1 000 principaux de service peuvent trouver des extensions manquantes dans la liste d’attributs source. Si un attribut que vous créez en suivant les étapes ci-dessous n’apparaît pas automatiquement dans la liste d’attributs source, vérifiez à l’aide du graphique que l’attribut d’extension a été créé, puis ajoutez-le à votre schéma [manuellement](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes). Lorsque vous effectuez les demandes de graphe ci-dessous, cliquez sur En savoir plus pour vérifier les autorisations requises pour effectuer des demandes. Vous pouvez utiliser l’[Explorateur graphique](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) pour effectuer des demandes. 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Créer un attribut d’extension sur un utilisateur cloud uniquement à l’aide de Microsoft Graph
Vous devrez utiliser une application pour étendre le schéma de vos utilisateurs. Répertoriez les applications de votre locataire pour identifier l’ID de l’application que vous souhaitez utiliser pour étendre le schéma utilisateur. [En savoir plus.](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Créez l’attribut d’extension. Remplacez la propriété **id** ci-dessous par l’**id** récupéré à l’étape précédente. Vous devrez utiliser l’attribut **« if »** , non l’attribut « appid ». [En savoir plus.](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
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

La demande précédente a créé un attribut d’extension au format « extension_appID_extensionName ». Mettez à jour un utilisateur avec l’attribut d’extension. [En savoir plus.](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Vérifiez l’utilisateur pour vous assurer que l’attribut a été correctement mis à jour. [En savoir plus.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

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

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Étapes suivantes

* [Définir qui est concerné par la configuration](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
