---
title: Valider l’inscription auprès d’Azure pour Azure Stack | Microsoft Docs
description: Utilisez Azure Stack Readiness Checker pour valider l’inscription auprès d’Azure.
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
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937565"
---
# <a name="validate-azure-registration"></a>Valider l’inscription auprès d’Azure 
Utilisez l’outil Azure Stack Readiness Checker (AzsReadinessChecker) pour vérifier que votre abonnement Azure est prêt à être utilisé avec Azure Stack. Validez l’inscription avant de commencer un déploiement d’Azure Stack. L’outil Readiness Checker valide ce qui suit :
- Le type d’abonnement Azure que vous utilisez est pris en charge. Les abonnements doivent être de type Fournisseur de services cloud (CSP) ou Contrat Entreprise (EA). 
- Le compte que vous utilisez pour inscrire votre abonnement auprès d’Azure peut se connecter à Azure et correspond à un propriétaire de l’abonnement. 

Pour plus d’informations sur l’inscription d’Azure Stack, consultez [Inscrire Azure Stack auprès d’Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker
Téléchargez la dernière version de l’outil Azure Stack Readiness Checker (AzsReadinessChecker) sur le site [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prérequis

Vérifiez les prérequis suivants.

**Ordinateur sur lequel l’outil est exécuté :**
 - Windows 10 ou Windows Server 2016, avec connectivité à Internet.
 - PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez la commande PowerShell suivante et examinez les versions *Major* et *Minor* :  

    >`$PSVersionTable.PSVersion` 
 - Configurez [PowerShell pour Azure Stack](azure-stack-powershell-install.md). 
 - Téléchargez la dernière version de l’outil [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).  

**Environnement Azure Active Directory :** 
 - Identifiez le nom d’utilisateur et le mot de passe d’un compte associé à un propriétaire de l’abonnement Azure que vous allez utiliser avec Azure Stack.  
 - Identifiez l’ID d’abonnement pour l’abonnement Azure à utiliser. 
 - Identifiez l’environnement Azure (AzureEnvironment) que vous allez utiliser : *AzureCloud*, *AzureGermanCloud* ou *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Valider l’inscription auprès d’Azure
1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell d’administration, puis exécutez la commande suivante pour installer AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. À l’invite PowerShell, exécutez ce qui suit pour affecter à *$registrationCredential* le compte associé au propriétaire de l’abonnement.   Remplacez *subscriptionowner@contoso.onmicrosoft.com* par votre compte et votre locataire. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. À l’invite PowerShell, exécutez ce qui suit pour affecter à *$subscriptionID* l’abonnement Azure que vous allez utiliser. Remplacez *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* par votre ID d’abonnement.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. À l’invite PowerShell, exécutez ce qui suit pour démarrer la validation de votre abonnement. 
   - Affectez à AzureEnvironment la valeur *AzureCloud*, *AzureGermanCloud* ou *AzureChinaCloud*.  
   - Indiquez l’administrateur et le nom du locataire Azure Active Directory. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est OK pour l’ouverture de session et les conditions d’inscription. Une validation réussie génère une image semblable à la suivante :  
![run-validation](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Rapport et fichier journal
Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell. 

Ces fichiers peuvent vous aider à partager l’état de validation avant de déployer Azure Stack ou à enquêter sur les problèmes de validation. Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation. 

Par défaut, les deux fichiers sont écrits à l’emplacement *C:\Users\<nom_utilisateur>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Utilisez le paramètre **-OutputPath** ***&lt;chemin&gt;*** à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.   
 - Utilisez le paramètre **-CleanReport** à la fin de la commande d’exécution pour effacer les informations du fichier *AzsReadinessCheckerReport.json*  concernant les exécutions précédentes de l’outil. Pour plus d’informations, consultez [Rapport de validation Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation
En cas d’échec de vérification de la validation, des détails sont fournis dans la fenêtre PowerShell. L’outil journalise également des informations dans le fichier AzsReadinessChecker.log.

Les exemples suivants donnent des conseils sur la façon de résoudre des échecs de validation courants.

### <a name="user-must-be-an-owner-of-the-subscription"></a>L’utilisateur doit être un propriétaire de l’abonnement.   
![subscription-owner](./media/azure-stack-validate-registration/subscription-owner.png)
**Cause** : Le compte n’est pas celui d’un administrateur de l’abonnement Azure.   

**Résolution** : Utilisez le compte d’un administrateur de l’abonnement Azure auquel l’utilisation du déploiement d’Azure Stack sera facturée.


### <a name="expired-or-temporary-password"></a>Mot de passe temporaire ou ayant expiré 
![expired-password](./media/azure-stack-validate-registration/expired-password.png)
**Cause** : Le compte ne peut pas se connecter, car le mot de passe est temporaire ou a expiré.     

**Résolution** : Dans PowerShell, exécutez ce qui suit, puis suivez les invites pour réinitialiser le mot de passe. 
  > `Login-AzureRMAccount` 

Vous pouvez également vous connecter à https://portal.azure.com pour forcer le compte et l’utilisateur à changer de mot de passe.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Les comptes Microsoft ne sont pas pris en charge pour l’inscription  
![unsupported-account](./media/azure-stack-validate-registration/unsupported-account.png)
**Cause** : Un compte Microsoft (comme Outlook.com ou Hotmail.com) a été spécifié.  Ces comptes ne sont pas pris en charge.

**Résolution** : Utilisez un compte et un abonnement d’un fournisseur de services cloud ou d’un accord entreprise. 


### <a name="unknown-user-type"></a>Type d’utilisateur inconnu  
![unknown-user](./media/azure-stack-validate-registration/unknown-user.png)
**Cause** : Le compte ne peut pas se connecter à l’environnement Azure Active Directory spécifié. Dans cet exemple, *AzureChinaCloud* est spécifié comme *AzureEnvironment*.  

**Résolution** : Vérifiez que le compte est valide pour l’environnement Azure spécifié. Dans PowerShell, exécutez ce qui suit pour vérifier que le compte est valide pour un environnement spécifique.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Étapes suivantes
[Valider l’identité Azure](azure-stack-validate-identity.md)
[Afficher le rapport de préparation](azure-stack-validation-report.md)
[Considérations générales relatives à l’intégration de Microsoft Azure Stack](azure-stack-datacenter-integration.md)

