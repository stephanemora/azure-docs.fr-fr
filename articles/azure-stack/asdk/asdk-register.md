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
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 08a300d0e2d1565428f282a2073d91b5dd08c060
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016997"
---
# <a name="azure-stack-registration"></a>Inscription d’Azure Stack
Vous pouvez inscrire le Kit de développement Azure Stack auprès d’Azure pour télécharger des éléments de la Place de marché à partir d’Azure et renvoyer des rapports de données commerciales à Microsoft. L’inscription est obligatoire pour prendre en charge les fonctionnalités complètes d’Azure Stack, notamment la syndication de la Place de marché. L’inscription est recommandée, car elle vous permet de tester des fonctionnalités Azure Stack importantes, telles que la syndication de la Place de marché et les rapports d’utilisation. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription. Toutefois, l’utilisation du kit de développement Azure Stack n’est pas facturée à ses utilisateurs.

Si vous n’inscrivez pas votre Kit de développement Azure Stack (ASDK), il se peut qu’un message d’avertissement **Activation requise** s’affiche pour vous inviter à l’inscrire. Il s’agit du comportement attendu.

## <a name="prerequisites"></a>Prérequis
Avant de suivre ces instructions pour inscrire le kit ASDK auprès d’Azure, veillez à installer Azure Stack PowerShell et télécharger les outils Azure Stack, comme décrit dans l’article relatif à la [configuration post-déploiement](asdk-post-deploy.md).

De plus, le mode de langage PowerShell doit avoir la valeur **FullLanguageMode** sur l’ordinateur utilisé pour inscrire le kit ASDK auprès d’Azure. Pour vérifier que le mode de langage actuel est défini sur full, ouvrez une fenêtre PowerShell avec élévation de privilèges et exécutez les commandes PowerShell suivantes :

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Vérifiez que la sortie retourne **FullLanguageMode**. Si tout autre mode de langage est retourné, vous devez exécuter l’inscription sur un autre ordinateur ou définir le mode de langage sur **FullLanguageMode** avant de continuer.

## <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure
Pour inscrire le kit ASDK auprès d’Azure, procédez aux étapes suivantes.

> [!NOTE]
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié. Pour le kit ASDK, il s’agit de l’ordinateur hôte du kit de développement.

1. Ouvrez une console PowerShell en tant qu’administrateur.  

2. Exécutez les commandes PowerShell suivantes pour inscrire votre installation ASDK auprès d’Azure. Vous devrez vous connecter à votre abonnement Azure et à l’installation ASDK locale. Si vous n’avez toujours pas d’abonnement Azure, vous pouvez créer un [compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. Quand le script est terminé, le message suivant doit s’afficher : **Votre environnement est à présent enregistré et activé à l’aide des paramètres fournis.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Vérifier que l’inscription a abouti
Suivez ces étapes pour vérifier que l’inscription du kit ASDK auprès d’Azure a abouti.

1. Connectez-vous au [portail d’administration Azure Stack](https://adminportal.local.azurestack.external).

2. Cliquez sur **Gestion de la Place de marché** > **Ajouter à partir d’Azure**.

    ![](media/asdk-register/2.PNG)

3. Si une liste d’éléments disponibles dans Azure s’affiche, l’activation a réussi.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>Étapes suivantes
[Ajouter un élément marketplace Azure Stack](.\.\azure-stack-marketplace.md)
