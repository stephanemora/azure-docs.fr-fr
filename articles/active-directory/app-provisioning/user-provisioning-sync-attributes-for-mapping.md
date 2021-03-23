---
title: Synchroniser les attributs avec Azure AD pour le mappage
description: Découvrez comment synchroniser les attributs de votre répertoire local Active Directory avec Azure AD. Lors de la configuration des utilisateurs pour les applications SaaS, servez-vous de la fonctionnalité d’extension de répertoire pour ajouter des attributs sources qui ne sont pas synchronisés par défaut.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.author: kenwith
ms.openlocfilehash: 0f8369c80a7a219b159f31aacb7d10a0dd009d00
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418672"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronisez un attribut à partir d’un répertoire Active Directory local vers Azure AD pour la configuration vers une application donnée

Lors de la personnalisation des mappages d’attributs pour la configuration utilisateur, vous constaterez peut-être que l’attribut à mapper n’apparaît pas dans la liste **Attribut source**. Cet article vous montre comment ajouter l’attribut manquant en le synchronisant de votre répertoire Active Directory (AD) local vers Azure Active Directory (Azure AD).

Azure AD doit contenir toutes les données requises pour créer un profil utilisateur lors de la configuration des comptes utilisateur d’Azure AD vers une application SaaS. Pour rendre les données disponibles, vous devez parfois synchroniser les attributs de votre répertoire AD local vers Azure AD. Azure AD Connect synchronise automatiquement certains attributs avec Azure AD, mais pas tous. En outre, certains attributs (tels que SAMAccountName) qui sont synchronisés par défaut peuvent ne pas être exposés par le biais de l'API Microsoft Graph. Dans ce cas, vous pouvez utiliser la fonctionnalité d’extension de répertoire Azure AD Connect pour synchroniser l’attribut avec Azure AD. De cette façon, l'attribut est visible pour l'API Microsoft Graph et le service de configuration Azure AD.

Si les données dont vous avez besoin pour l’approvisionnement se trouvent dans Active Directory mais ne sont pas disponibles pour l’approvisionnement pour les raisons décrites ci-dessus, vous pouvez utiliser Azure AD Connect ou PowerShell pour créer des attributs d’extension. 
 
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

## <a name="create-an-extension-attribute-using-powershell"></a>Créer un attribut d’extension à l’aide de PowerShell
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
