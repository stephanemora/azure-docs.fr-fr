---
title: Valider l’identité Azure pour Azure Stack | Microsoft Docs
description: Utilisez l’outil Azure Stack Readiness Checker pour valider l’identité Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937559"
---
# <a name="validate-azure-identity"></a>Valider l’identité Azure 
Utilisez l’outil Azure Stack Readiness Checker (AzsReadinessChecker) pour vérifier que votre annuaire Azure Active Directory (Azure AD) peut être utilisé avec Azure Stack. Validez votre solution d’identité Azure avant de commencer un déploiement Azure Stack.  

L’outil Readiness Checker valide ce qui suit :
 - Azure AD comme fournisseur d’identité pour Azure Stack.
 - Le compte Azure AD que vous prévoyez d’utiliser peut se connecter en tant qu’administrateur général de votre annuaire Azure Active Directory. 

La validation garantit que votre environnement permet à Azure Stack de stocker des informations sur les utilisateurs, applications, groupes et principaux de service d’Azure Stack dans votre annuaire Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker
Téléchargez la dernière version de l’outil Azure Stack Readiness Checker (AzsReadinessChecker) sur le site [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prérequis

Vérifiez les prérequis suivants.

**Ordinateur sur lequel l’outil est exécuté :**
 - Windows 10 ou Windows Server 2016, avec connectivité à Internet.
 - PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez la commande PowerShell suivante et examinez les versions *Major* et *Minor* :  

   > `$PSVersionTable.PSVersion`
 - Configurez [PowerShell pour Azure Stack](azure-stack-powershell-install.md). 
 - Dernière version de l’outil [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Environnement Azure Active Directory :** 
 - Identifiez le compte Azure AD à utiliser pour Azure Stack et vérifiez qu’il correspond à un administrateur général Azure Active Directory.
 - Identifiez le nom du locataire Azure AD. Le nom du locataire doit être le nom de domaine *principal* de votre annuaire Azure Active Directory. Par exemple, *contoso.onmicrosoft.com*. 
 - Identifiez l’environnement Azure (AzureEnvironment) que vous allez utiliser : *AzureCloud*, *AzureGermanCloud* ou *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Valider l’identité Azure 
1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell d’administration, puis exécutez la commande suivante pour installer AzsReadinessChecker :  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. À l’invite PowerShell, exécutez ce qui suit pour affecter à *$serviceAdminCredential* l’administrateur de service de votre locataire Azure AD.  Remplacez *serviceadmin@contoso.onmicrosoft.com* par votre compte et votre locataire. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. À l’invite PowerShell, exécutez ce qui suit pour démarrer la validation de votre annuaire Azure AD. 
   - Affectez à AzureEnvironment la valeur *AzureCloud*, *AzureGermanCloud* ou *AzureChinaCloud*.  
   - Remplacez *contoso.onmicrosoft.com* par le nom de votre locataire Azure Active Directory. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est **OK** pour l’ouverture de session et les conditions d’installation. Une validation réussie génère une image semblable à la suivante : 
 
![run-validation](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>Rapport et fichier journal
Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

Ces fichiers peuvent vous aider à partager l’état de validation avant de déployer Azure Stack ou à enquêter sur les problèmes de validation.  Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation. 

Par défaut, les deux fichiers sont écrits à l’emplacement *C:\Users\<nom_utilisateur>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Utilisez le paramètre **-OutputPath** ***&lt;chemin&gt;*** à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.   
 - Utilisez le paramètre **-CleanReport** à la fin de la commande d’exécution pour effacer les informations du fichier *AzsReadinessCheckerReport.json*  concernant les exécutions précédentes de l’outil. 

Pour plus d’informations, consultez [Rapport de validation Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation
En cas d’échec de vérification de la validation, des détails sont fournis dans la fenêtre PowerShell. L’outil journalise également des informations dans le fichier AzsReadinessChecker.log.

Les exemples suivants donnent des conseils sur la façon de résoudre les échecs de validation courants.

### <a name="expired-or-temporary-password"></a>Mot de passe temporaire ou ayant expiré 
 
![expired password](./media/azure-stack-validate-identity/expired-password.png)
**Cause** : Le compte ne peut pas se connecter, car le mot de passe est temporaire ou a expiré.     

**Résolution** : Dans PowerShell, exécutez ce qui suit, puis suivez les invites pour réinitialiser le mot de passe.  
> `Login-AzureRMAccount`

Vous pouvez également vous connecter à https://portal.azure.com pour forcer le compte et l’utilisateur à changer de mot de passe.
### <a name="unknown-user-type"></a>Type d’utilisateur inconnu 
 
![unknown user](./media/azure-stack-validate-identity/unknown-user.png)
**Cause** : Le compte ne peut pas se connecter à l’annuaire Azure Active Directory spécifié (AADDirectoryTenantName). Dans cet exemple, *AzureChinaCloud* est spécifié comme *AzureEnvironment*.

**Résolution** : Vérifiez que le compte est valide pour l’environnement Azure spécifié. Dans PowerShell, exécutez ce qui suit pour vérifier que le compte est valide pour un environnement spécifique : Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Le compte n’est pas un administrateur 
 
![pas un administrateur](./media/azure-stack-validate-identity/not-admin.png)

**Cause** : Bien que le compte puisse ouvrir une session, il ne correspond pas à un administrateur d’Azure Active Directory (AADDirectoryTenantName).  

**Résolution** : Connectez-vous à https://portal.azure.com avec le compte, accédez à **Azure Active Directory** > **Utilisateurs** > *Sélectionner l’utilisateur* > **Rôle d’annuaire**, puis vérifiez que l’utilisateur est un **Administrateur général**.  Si le compte correspond à un utilisateur, accédez à **Azure Active Directory** > **Noms de domaine personnalisés**, puis vérifiez que le nom que vous avez fourni pour *AADDirectoryTenantName* est marqué comme nom de domaine principal pour cet annuaire.  Dans cet exemple, il s’agit de *contoso.onmicrosoft.com*. 

Azure Stack exige que le nom de domaine soit le nom de domaine principal.

## <a name="next-steps"></a>Étapes suivantes
[Valider l’inscription auprès d’Azure](azure-stack-validate-registration.md)  
[Afficher le rapport de préparation](azure-stack-validation-report.md)  
[Considérations générales relatives à l’intégration de Microsoft Azure Stack](azure-stack-datacenter-integration.md)  

