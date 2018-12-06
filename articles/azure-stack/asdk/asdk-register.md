---
title: Inscrire le kit ASDK auprès d’Azure | Microsoft Docs
description: Explique comment inscrire Azure Stack auprès d’Azure pour activer la syndication de la Place de marché et les rapports d’utilisation.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e86ff4ebf91d0c0b691caf429d9489bf769f16af
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975190"
---
# <a name="azure-stack-registration"></a>Inscription d’Azure Stack
Vous pouvez inscrire le Kit de développement Azure Stack auprès d’Azure pour télécharger des éléments de la Place de marché à partir d’Azure et renvoyer des rapports de données commerciales à Microsoft. L’inscription est obligatoire pour prendre en charge les fonctionnalités complètes d’Azure Stack, notamment la syndication de la Place de marché. L’inscription est recommandée, car elle vous permet de tester des fonctionnalités Azure Stack importantes, telles que la syndication de la Place de marché et les rapports d’utilisation. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription. Toutefois, l’utilisation du kit de développement Azure Stack n’est pas facturée à ses utilisateurs.

Si vous n’inscrivez pas votre Kit de développement Azure Stack (ASDK), il se peut qu’un message d’avertissement **Activation requise** s’affiche pour vous inviter à l’inscrire. Il s’agit du comportement attendu.

## <a name="prerequisites"></a>Prérequis
Avant de suivre ces instructions pour inscrire le kit ASDK auprès d’Azure, veillez à installer Azure Stack PowerShell et télécharger les outils Azure Stack, comme décrit dans l’article relatif à la [configuration post-déploiement](asdk-post-deploy.md).

De plus, le mode de langage PowerShell doit avoir la valeur **FullLanguageMode** sur l’ordinateur utilisé pour inscrire le kit ASDK auprès d’Azure. Pour vérifier que le mode de langage actuel est défini sur full, ouvrez une fenêtre PowerShell avec élévation de privilèges et exécutez les commandes PowerShell suivantes :

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Vérifiez que la sortie retourne **FullLanguageMode**. Si tout autre mode de langage est retourné, vous devez exécuter l’inscription sur un autre ordinateur ou définir le mode de langage sur **FullLanguageMode** avant de continuer.

## <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure
Pour inscrire le kit ASDK auprès d’Azure, procédez aux étapes suivantes.

> [!NOTE]
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié. Pour le kit ASDK, il s’agit de l’ordinateur hôte du kit de développement.

1. Ouvrez une console PowerShell en tant qu’administrateur.  

2. Exécutez les commandes PowerShell suivantes pour inscrire votre installation ASDK auprès d’Azure. Vous devrez vous connecter à votre abonnement Azure et à l’installation ASDK locale. Si vous n’avez toujours pas d’abonnement Azure, vous pouvez créer un [compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.<br><br>Si vous exécutez le script d’inscription dans plusieurs instances Azure Stack en utilisant le même ID d’abonnement Azure, définissez un nom unique pour l’inscription au moment d’exécuter l’applet de commande **Set-AzsRegistration**. Par défaut, le paramètre **RegistrationName** a la valeur **AzureStackRegistration**. Cependant, si vous utilisez le même nom dans plusieurs instances Azure Stack, le script échoue.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. Quand le script est terminé, le message suivant doit s’afficher : **Votre environnement est à présent enregistré et activé à l’aide des paramètres fournis.**

    ![Votre environnement est maintenant inscrit](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Effectuer une inscription dans des environnements déconnectés
Si vous inscrivez Azure Stack dans un environnement déconnecté (sans connectivité Internet), vous devez obtenir un jeton d’inscription à partir de l’environnement Azure Stack, puis l’utiliser sur un ordinateur qui peut se connecter à Azure pour vous inscrire et créer une ressource d’activation pour votre environnement ASDK.
 
 > [!IMPORTANT]
 > Avant de suivre ces instructions pour inscrire Azure Stack, vérifiez que vous avez installé PowerShell pour Azure Stack et téléchargé les outils Azure Stack comme décrit dans l’article [Configuration post-déploiement](asdk-post-deploy.md) sur l’ordinateur hôte ASDK et sur l’ordinateur avec un accès Internet utilisé pour se connecter à Azure et effectuer l’inscription.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack
Sur l’ordinateur hôte ASDK, démarrez PowerShell en tant qu’administrateur et accédez au dossier **Inscription** dans le répertoire **AzureStack-Tools-master** créé quand vous avez téléchargé les outils Azure Stack. Utilisez les commandes PowerShell suivantes pour importer le module **RegisterWithAzure.psm1**, puis utilisez l’applet de commande **Get-AzsRegistrationToken** pour obtenir le jeton d’inscription :  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```
Par défaut, le jeton d’inscription est enregistré dans le fichier spécifié pour le paramètre *$FilePathForRegistrationToken*. Vous pouvez modifier à votre convenance le nom ou le chemin d’accès au fichier.

Enregistrez ce jeton d’inscription pour l’utiliser sur l’ordinateur connecté à Internet. Vous pouvez copier le fichier ou le texte à partir de $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Se connecter à Azure et s’inscrire
Sur l’ordinateur connecté à Internet, démarrez PowerShell en tant qu’administrateur et accédez au dossier **Inscription** dans le répertoire **AzureStack-Tools-master** créé quand vous avez téléchargé les outils Azure Stack. Utiliser les commandes PowerShell suivantes pour importer le module **RegisterWithAzure.psm1**, puis utilisez l’applet de commande **Register-AzsEnvironment** pour vous inscrire auprès d’Azure en fournissant le jeton d’inscription que vous venez de créer et un nom d’inscription unique :  

  ```PowerShell  
  $registrationToken = "<your registration token>"
  $RegistrationName = "<unique registration name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Vous pouvez également utiliser l’applet de commande **Get-Content** pour pointer vers un fichier contenant votre jeton d’inscription :

  ```PowerShell  
  $registrationToken = Get-Content -Path '<path>\<registration token file>'
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Enregistrez le jeton d’inscription et le nom de la ressource d’inscription pour vous y référer ultérieurement.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Récupérer une clé d’activation à partir de la ressource d’inscription Azure

Toujours sur l’ordinateur connecté à Internet, récupérez une clé d’activation à partir de la ressource d’inscription créée quand vous vous êtes inscrit auprès d’Azure.

Pour obtenir la clé d’activation, exécutez les commandes PowerShell suivantes, utilisez la même valeur de nom d’inscription unique que celle fournie lors de l’inscription auprès d’Azure à l’étape précédente :  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

La clé d’activation est enregistrée dans le fichier spécifié pour *$KeyOutputFilePath*. Vous pouvez modifier à votre convenance le nom ou le chemin d’accès au fichier.

### <a name="create-an-activation-resource-in-azure-stack"></a>Créer une ressource d’activation dans Azure Stack

Retournez dans l’environnement Azure Stack avec le fichier ou le texte obtenu de la clé d’activation créée à partir de **Get-AzsActivationKey**. Exécutez les commandes PowerShell suivantes pour créer une ressource d’activation dans Azure Stack à l’aide de cette clé d’activation :   

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Vous pouvez également utiliser l’applet de commande **Get-Content** pour pointer vers un fichier contenant votre jeton d’inscription :

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = Get-Content -Path '<path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

## <a name="verify-the-registration-was-successful"></a>Vérifier que l’inscription a abouti
Suivez ces étapes pour vérifier que l’inscription du kit ASDK auprès d’Azure a abouti.

1. Connectez-vous au [portail d’administration Azure Stack](https://adminportal.local.azurestack.external).

2. Cliquez sur **Gestion de la Place de marché** > **Ajouter à partir d’Azure**.

    ![](media/asdk-register/2.PNG)

3. Si une liste d’éléments disponibles dans Azure s’affiche, l’activation a réussi.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Déplacer une ressource d’inscription
Le déplacement d’une ressource d’inscription entre des groupes de ressources sous le même abonnement **est** pris en charge. Pour plus d’informations sur le déplacement de ressources vers un nouveau groupe de ressources, voir [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Étapes suivantes
[Ajouter un élément marketplace Azure Stack](.\.\azure-stack-marketplace.md)
