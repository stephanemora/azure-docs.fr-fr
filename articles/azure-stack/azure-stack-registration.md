---
title: Inscription Azure pour systèmes intégrés Azure Stack | Microsoft Docs
description: Décrit le processus d’inscription Azure pour les déploiements à plusieurs nœuds d’Azure Stack connectés à Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: f34c4697439685ce6ea0ce3f2c7e954ee81b5079
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077188"
---
# <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure
L’inscription de [Azure Stack](azure-stack-poc.md) auprès d’Azure permet de télécharger des éléments de la Place de marché à partir d’Azure et de configurer la génération de rapports de données commerciales envoyés à Microsoft. Après avoir enregistré Azure Stack, l’utilisation est signalée au commerce Azure et vous pouvez le voir dans l’abonnement utilisé pour l’inscription. 

> [!IMPORTANT]
> L’inscription est requise pour prendre en charge les fonctionnalités complètes d’Azure Stack, y compris la syndication de la place de marché. En outre, vous enfreindrez les conditions du contrat de licence d’Azure Stack si vous ne vous inscrivez pas lorsque vous utilisez le modèle de facturation à l’utilisation. Pour en savoir plus sur les modèles de licence d’Azure Stack, consultez la [page Comment acheter](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Prérequis

Avant d’inscrire Azure Stack auprès d’Azure, vous devez disposer des éléments suivants :

- L’ID d’abonnement d’un abonnement Azure. Pour obtenir l’ID, connectez-vous à Azure, cliquez sur **Plus de services** > **Abonnements**, cliquez sur l’abonnement que vous voulez utiliser. Sous **Éléments principaux** vous trouverez alors l’ID d’abonnement. 

  > [!NOTE]
  > Les abonnements au cloud du gouvernement d’Allemagne et des États-Unis ne sont pas pris en charge pour le moment.

- Le nom d’utilisateur et le mot de passe d’un compte qui est un propriétaire de l’abonnement (les comptes MSA/2FA sont pris en charge).
- Le fournisseur de ressources Azure Stack inscrit (pour plus d’informations, consultez la section Inscrire le fournisseur de ressources Azure Stack ci-dessous).

Si vous n’avez pas d’abonnement Azure répondant à ces exigences, vous pouvez [créer un compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.

### <a name="bkmk_powershell"></a>Installer PowerShell pour Azure Stack
Vous devez utiliser la dernière version de PowerShell pour Azure Stack pour vous inscrire auprès d’Azure.

S’il ne l’est pas déjà, [installez PowerShell pour Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Télécharger les outils Azure Stack
Le référentiel GitHub d’outils Azure Stack contient des modules PowerShell qui prennent en charge les fonctionnalités Azure Stack, notamment des fonctionnalités d’inscription. Pendant le processus d’inscription, vous devez importer et utiliser le module PowerShell RegisterWithAzure.psm1, qui se trouve dans le référentiel d’outils Azure Stack, pour inscrire votre instance d’Azure Stack auprès d’Azure. 

Pour garantir que vous utilisez la version la plus récente, vous devez supprimer toutes les versions existantes des outils Azure Stack et [télécharger la dernière version à partir de GitHub](azure-stack-powershell-download.md) avant l’inscription auprès de Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Inscrire Azure Stack dans des environnements connectés
Les environnements connectés peuvent accéder à Internet et à Azure. Pour ces environnements, vous devez inscrire le fournisseur de ressources Azure Stack auprès d’Azure, puis configurer votre modèle de facturation.

> [!NOTE]
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié. 

### <a name="register-the-azure-stack-resource-provider"></a>Inscrire le fournisseur de ressources Azure Stack
Pour inscrire le fournisseur de ressources Azure Stack avec Azure, démarrez PowerShell ISE en tant qu’administrateur et utilisez les commandes PowerShell suivantes avec le paramètre **EnvironmentName** défini sur le type d’abonnement Azure approprié (voir les paramètres ci-dessous). 

1. Ajoutez le compte Azure que vous utilisez pour inscrire Azure Stack. Pour ajouter le compte, exécutez la cmdlet **Add-AzureRmAccount**. Vous êtes invité à entrer vos informations d’identification du compte administrateur global Azure et vous devrez peut-être utiliser l’authentification à 2 facteurs en fonction de la configuration de votre compte.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnement pris en charge sont **AzureCloud** ou, si vous utilisez un abonnement Azure en Chine, **AzureChinaCloud**.  |
   |  |  |

2. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Exécutez la commande suivante pour inscrire le fournisseur de ressources Azure Stack dans votre abonnement Azure :

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Inscrire Azure Stack auprès d’Azure à l’aide du modèle de facturation de paiement à l’utilisation
Utilisez ces étapes pour inscrire Azure Stack auprès d’Azure à l’aide du modèle de facturation de paiement à l’utilisation.

1. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** dans le répertoire **AzureStack-Tools-master** créé lorsque vous avez [téléchargé les outils Azure Stack](#bkmk_tools). Importez le module **RegisterWithAzure.psm1** à l’aide de PowerShell : 

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Ensuite, dans la même session PowerShell, vérifiez que vous êtes connecté au contexte Azure PowerShell approprié. Il s’agit du compte Azure qui a été utilisé pour inscrire le fournisseur de ressources Azure Stack ci-dessus. PowerShell pour : 

  ```powershell 
  Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>" 
  ``` 

3. Dans la même session PowerShell, exécutez l’applet de commande **Set-AzsRegistration**. PowerShell pour :  

  ```powershell
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
  ```

  |Paramètre|Description|
  |-----|-----|
  |PrivilegedEndpointCredential|Les informations d’identification qui sont utilisées pour [accéder au point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Le nom d’utilisateur est au format **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Une console PowerShell distante préconfigurée qui vous fournit des fonctionnalités telles que la collecte de journaux et d’autres tâches de post-déploiement. Pour en savoir plus, reportez-vous à l’article relatif à l’[utilisation du point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).|
  |BillingModel|Le modèle de facturation utilisé par votre abonnement. Les valeurs autorisées pour ce paramètre sont : Capacity, PayAsYouUse et Development.|
  |  |  |

  L’opération prend entre 10 et 15 minutes. Lorsque la commande est terminée, le message **« Votre environnement est à présent enregistré et activé à l’aide des paramètres fournis. »** s’affiche.

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Inscrire Azure Stack auprès d’Azure à l’aide du modèle de facturation de capacité
Suivez les mêmes instructions que celles utilisées pour l’inscription à l’aide du modèle de facturation de paiement à l’utilisation, mais ajoutez le numéro de contrat sous lequel la capacité a été achetée et définissez le paramètre **Modèle de facturation** sur **Capacité**. Tous les autres paramètres restent inchangés.

PowerShell pour :

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Inscrire Azure Stack dans des environnements déconnectés 
*Les informations contenues dans cette section s’appliquent à compter de la version de mise à jour 1712 d’Azure Stack (180106.1) et ne sont pas prises en charge par les versions antérieures.*

Si vous inscrivez Azure Stack dans un environnement déconnecté (sans connectivité Internet), vous devez obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack, puis l’utiliser sur un ordinateur qui peut se connecter à Azure et sur lequel [PowerShell pour Azure Stack est installé](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack

1. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** dans le répertoire **AzureStack-Tools-master** créé lorsque vous avez [téléchargé les outils Azure Stack](#bkmk_tools). Importez le module **RegisterWithAzure.psm1** :  

  ```powershell 
  Import-Module .\RegisterWithAzure.psm1 
  ``` 

2. Pour obtenir le jeton d’inscription, exécutez les commandes PowerShell suivantes :  

  ```Powershell
  $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
  $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
  ```
  
  > [!TIP]  
  > Le jeton d’inscription est enregistré dans le fichier spécifié pour *$FilePathForRegistrationToken*. Vous pouvez modifier à votre convenance le nom ou le chemin d’accès au fichier. 

3. Enregistrez ce jeton d’inscription pour l’utiliser sur la machine connectée à Azure. Vous pouvez copier le fichier ou le texte à partir de $FilePathForRegistrationToken.


### <a name="connect-to-azure-and-register"></a>Se connecter à Azure et s’inscrire
Sur l’ordinateur qui est connecté à Internet, effectuez les mêmes étapes pour importer le module RegisterWithAzure.psm1 et se connecter au bon contexte Azure Powershell. Appelez ensuite Register-AzsEnvironment et spécifiez le jeton d’inscription à enregistrer auprès d’Azure :

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
Si vous le souhaitez, vous pouvez utiliser l’applet de commande Get-Content pour pointer vers un fichier contenant votre jeton d’inscription :

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
  > [!NOTE]  
  > Enregistrez le nom de la ressource d’inscription et le jeton d’inscription pour une référence ultérieure.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Récupérer une clé d’activation auprès d’Azure Registration Resource 
Vous devez ensuite extraire une clé d’activation auprès de la ressource d’inscription créée dans Azure pendant Register-AzsEnvironment. 
 
Pour obtenir la clé d’activation, exécutez les commandes PowerShell suivantes :  

  ```Powershell 
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>" 
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt" 
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath 
  ``` 
  > [!TIP]   
  > La clé d’activation est enregistrée dans le fichier spécifié pour *$KeyOutputFilePath*. Vous pouvez modifier à votre convenance le nom ou le chemin d’accès au fichier. 

### <a name="create-an-activation-resource-in-azure-stack"></a>Créer une ressource d’activation dans Azure Stack 
Revenez à l’environnement Azure Stack avec le fichier ou le texte obtenu à partir de la clé d’activation créée depuis Get-AzsActivationKey. Vous allez ensuite créer une ressource d’activation dans Azure Stack à l’aide de cette clé d’activation. Pour créer une ressource d’activation, exécutez les commandes PowerShell suivantes :  

  ```Powershell 
  $ActivationKey = "<activation key>" 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

Si vous le souhaitez, vous pouvez utiliser l’applet de commande Get-Content pour pointer vers un fichier contenant votre jeton d’inscription : 

  ```Powershell   
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>' 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

## <a name="verify-azure-stack-registration"></a>Vérifier l’inscription Azure Stack
Utilisez ces étapes pour vérifier qu’Azure Stack a bien été inscrit auprès d’Azure.
1. Connectez-vous au [portail d’administration](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal) Azure Stack : https&#58;//adminportal.*&lt;region>.&lt;fqdn>*.
2. Cliquez sur **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir d’Azure**.

Si une liste d’éléments disponibles dans Azure (tels que WordPress) s’affiche, l’activation a réussi. Toutefois, dans les environnements déconnectés, vous ne verrez pas les éléments de la Place de marché Azure dans Azure Stack Marketplace.

> [!NOTE]
> Une fois l’inscription terminée, l’avertissement relatif à la non-inscription n’apparaît plus.

## <a name="renew-or-change-registration"></a>Renouveler ou modifier l’inscription
### <a name="renew-or-change-registration-in-connected-environments"></a>Renouveler ou de modifier l’inscription dans des environnements connectés
Vous devez mettre à jour ou renouveler votre inscription dans les cas suivants :
- Après le renouvellement de votre abonnement annuel basé sur la capacité.
- Lorsque vous changez de modèle de facturation.
- Lorsque vous mettez à l’échelle des modifications (ajout/suppression de nœuds) pour la facturation basée sur la capacité.

#### <a name="change-the-subscription-you-use"></a>Modifier l’abonnement que vous utilisez
Si vous souhaitez modifier l’abonnement que vous utilisez, vous devez d’abord exécuter l’applet de commande **Remove-AzsRegistration**, vous assurer d’être connecté au contexte Azure PowerShell correct, puis exécuter **Set-AzsRegistration** avec les paramètres modifiés :

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Modifier le modèle de facturation ou les fonctionnalités de syndication
Si vous souhaitez modifier le modèle de facturation ou les fonctionnalités de syndication pour votre installation, vous pouvez appeler la fonction d’inscription pour définir les nouvelles valeurs. Vous n’avez pas besoin de supprimer l’inscription actuelle d’abord : 

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Renouveler ou de modifier l’inscription dans des environnements déconnectés 
Vous devez mettre à jour ou renouveler votre inscription dans les cas suivants : 
- Après le renouvellement de votre abonnement annuel basé sur la capacité. 
- Lorsque vous changez de modèle de facturation. 
- Lorsque vous mettez à l’échelle des modifications (ajout/suppression de nœuds) pour la facturation basée sur la capacité. 

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Supprimer la ressource d’activation d’Azure Stack 
Vous devez d’abord supprimer la ressource d’activation d’Azure Stack, puis la ressource d’inscription dans Azure.  

Pour supprimer la ressource d’activation d’Azure Stack, exécutez les commandes PowerShell suivantes dans votre environnement Azure Stack :  

  ```Powershell 
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint 
  ``` 

Ensuite, pour supprimer la ressource d’inscription dans Azure, assurez-vous d’être sur un ordinateur connecté à Azure, connectez-vous au bon contexte Azure PowerShell et exécutez les commandes PowerShell appropriées, comme décrit ci-dessous.

Vous pouvez utiliser le jeton d’inscription utilisé pour créer la ressource :  

  ```Powershell 
  $registrationToken = "<registration token>" 
  Unregister-AzsEnvironment -RegistrationToken $registrationToken 
  ``` 
  
Vous pouvez également utiliser le nom d’inscription : 

  ```Powershell 
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>" 
  Unregister-AzsEnvironment -RegistrationName $registrationName 
  ``` 

### <a name="re-register-using-disconnected-steps"></a>Effectuer une réinscription à l’aide des étapes déconnectées 
Vous avez maintenant complètement annulé l’inscription dans un scénario déconnecté et devez répéter les étapes pour inscrire un environnement Azure Stack dans un scénario déconnecté. 

## <a name="next-steps"></a>Étapes suivantes

[Télécharger des éléments de la Place de marché à partir d’Azure](azure-stack-download-azure-marketplace-item.md)
