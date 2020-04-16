---
title: Gérer les services Office 365 à l’aide d’Azure Automation
description: Indique comment utiliser Azure Automation pour gérer les services d’abonnement Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80576768"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Gérer les services Office 365 à l’aide d’Azure Automation

Vous pouvez utiliser Azure Automation pour la gestion des services d’abonnement Office 365, pour des produits comme Microsoft Word et Microsoft Outlook. Les interactions avec Office 365 sont activées par [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Consultez [Utiliser Azure AD dans Azure Automation pour s’authentifier dans Azure](automation-use-azure-ad.md).

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prérequis

Pour gérer les services d’abonnement Office 365 dans Azure Automation, vous avez besoin des éléments suivants.

* Un abonnement Azure. Consultez [Guide de décision concernant les abonnements](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Un objet Automation dans Azure pour conserver les informations d’identification de compte d’utilisateur et les runbooks. Consultez [Présentation d’Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Consultez [Utiliser Azure AD dans Azure Automation pour s’authentifier dans Azure](automation-use-azure-ad.md).
* Un client Office 365 avec un compte. Consultez [Configuration de votre client Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Installation des modules MSOnline et MSOnlineExt

L’utilisation d’Office 365 dans Azure Automation nécessite Microsoft Azure Active Directory pour Windows PowerShell (module `MSOnline`). Vous aurez aussi besoin du module [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), qui simplifie la gestion d’Azure AD dans les environnements monolocataires ou multilocataires. Installez les modules comme indiqué dans [Utiliser Azure AD dans Azure Automation pour s’authentifier dans Azure](automation-use-azure-ad.md).

>[!NOTE]
>Pour utiliser MSOnline PowerShell, vous devez être membre d’Azure AD. Les utilisateurs invités ne peuvent pas utiliser le module.

## <a name="creating-an-azure-automation-account"></a>Création d’un compte Azure Automation

Pour effectuer les étapes décrites dans cet article, vous avez besoin d’un compte dans Azure Automation. Consultez [Créer un compte Azure Automation](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Ajout de MSOnline et MSOnlineExt en tant que ressources

Ajoutez maintenant les modules MSOnline et MSOnlineExt installés pour activer la fonctionnalité Office 365. Consultez [Gérer les modules dans Azure Automation](shared-resources/modules.md).

1. Dans le portail Azure, sélectionnez **Comptes Automation**.
2. Choisissez votre compte Automation.
3. Sélectionnez **Galerie de modules** sous **Ressources partagées**.
4. Recherchez MSOnline.
5. Sélectionnez le module PowerShell `MSOnline`, puis cliquez sur **Importer** pour importer le module en tant que ressource.
6. Répétez les étapes 4 et 5 pour localiser et importer le module `MSOnlineExt`. 

## <a name="creating-a-credential-asset-optional"></a>Création d’une ressource d’informations d’identification (facultatif)

Vous pouvez éventuellement créer une ressource d’informations d’identification pour l’utilisateur administratif Office 365 qui dispose des autorisations nécessaires pour exécuter votre script. Il peut être toutefois judicieux de ne pas exposer de noms et de mots de passe utilisateur à l’intérieur des scripts PowerShell. Pour obtenir des instructions, consultez [Création d’une ressource d’informations d’identification](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Création d’un compte de service Office 365

Pour exécuter les services d’abonnement Office 365, vous avez besoin d’un compte de service Office 365 assorti d’autorisations vous permettant de faire ce que vous voulez. Vous pouvez utiliser un compte d’administrateur général, un compte par service ou faire en sorte qu’une seule fonction ou un seul script s’exécute. Dans tous les cas, le compte de service exige un mot de passe complexe et sécurisé. Consultez [Configurer Office 365 pour les entreprises](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Connexion au service en ligne Azure AD

>[!NOTE]
>Pour utiliser les applets de commande du module MSOnline, vous devez les exécuter à partir de Windows PowerShell. PowerShell Core ne prend pas en charge ces applets de commande.

Vous pouvez utiliser le module MSOnline pour vous connecter à Azure AD à partir de l’abonnement Office 365. La connexion utilise le nom et le mot de passe d’un utilisateur Office 365 ou l’authentification multifacteur (MFA). Vous pouvez vous connecter à partir du portail Azure ou d’une invite de commandes Windows PowerShell (sans élévation de privilèges).

Un exemple PowerShell est présenté ci-dessous. L’applet de commande [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) demande les informations d’identification et les stocke dans la variable `Msolcred`. L’applet de commande [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) utilise alors les informations d’identification pour vous connecter au service en ligne Azure Directory. Si vous voulez vous connecter à un environnement Azure spécifique, utilisez le paramètre `AzureEnvironment`.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Si vous n’obtenez pas d’erreur, c’est que la connexion a abouti. Un moyen rapide de le vérifier consiste à exécuter une applet de commande Office 365, par exemple `Get-MsolUser`, puis de voir les résultats. Si vous obtenez une erreur, sachez que le problème est souvent lié à l’utilisation d’un mot de passe incorrect.

>[!NOTE]
>Vous pouvez aussi utiliser le module AzureRM ou le module Az pour vous connecter à Azure AD à partir de l’abonnement Office 365. La principale applet de commande de connexion est [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Cette applet de commande prend en charge le paramètre `AzureEnvironmentName` pour les environnements Office 365 spécifiques.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Création d’un runbook PowerShell à partir d’un script existant

Vous accédez à la fonctionnalité Office 365 à partir d’un script PowerShell. Voici un exemple de script pour des informations d’identifications nommées `Office-Credentials` et un nom d’utilisateur `admin@TenantOne.com`. Les informations d’identification Office 365 sont importées à l’aide de `Get-AutomationPSCredential`.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Exécution du script dans un runbook

Vous pouvez utiliser votre script dans un runbook Azure Automation. Pour cet exemple, nous allons utiliser le type de runbook PowerShell.

1. Créez un nouveau runbook PowerShell. Consultez [Créer un runbook Azure Automation](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. À partir de votre compte Automation, sélectionnez **Runbooks** sous **Automatisation des processus**.
3. Sélectionnez le nouveau runbook et cliquez sur **Modifier**.
4. Copiez votre script et collez-le dans l’éditeur de texte pour le runbook.
5. Sélectionnez **Ressources**, développez **Informations d’identification**, puis vérifiez que les informations d’identification Office 365 sont présentes.
6. Cliquez sur **Enregistrer**.
7. Sélectionnez **Volet de test**, puis cliquez sur **Démarrer** pour commencer à tester votre runbook. Consultez [Gérer les runbooks dans Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Une fois le test terminé, quittez le volet de test.

## <a name="publishing-and-scheduling-the-runbook"></a>Publication et planification du runbook

Pour publier et planifier votre runbook, consultez [Gérer les runbooks dans Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Étapes suivantes

* Vous trouverez des informations sur les ressources d’informations d’identification Automation dans [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md).
* Consultez [Gérer les modules dans Azure Automation](shared-resources/modules.md) pour savoir comment utiliser les modules Automation.
* Pour obtenir une vue d’ensemble de la gestion des runbooks, consultez [Gérer les runbooks dans Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Pour en savoir plus sur les méthodes qui peuvent être utilisées pour démarrer un runbook dans Azure Automation, consultez [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md).
* Pour plus d’informations sur PowerShell, notamment des informations de référence sur le langage et des modules de formation, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).