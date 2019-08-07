---
title: Authentification Azure Active Directory (Azure AD)
titleSuffix: Azure Cognitive Services
description: Référence correspondant au kit de développement logiciel (SDK) du lecteur immersif
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: de6a29e1c4c102aa7d4038185c1635544ba9dfe2
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688791"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Utiliser l’authentification Azure Active Directory (Azure AD) avec le service Lecteur immersif

Dans les sections suivantes, vous allez utiliser l’environnement Azure Cloud Shell ou Azure CLI pour créer une ressource de Lecteur immersif avec un sous-domaine personnalisé, puis configurer Azure AD dans votre locataire Azure. Une fois la configuration initiale terminée, vous allez appeler Azure AD pour obtenir un jeton d’accès, de la même façon que vous le faites lors de l’utilisation du SDK Lecteur immersif. Si vous êtes bloqué, des liens sont fournis dans chaque section avec toutes les options disponibles pour chacune des commandes Azure CLI.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Créer une ressource de Lecteur immersif avec un sous-domaine personnalisé

1. Commencez par ouvrir [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ensuite, [sélectionnez un abonnement](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description) :

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Ensuite, [créez une ressource de Lecteur immersif](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) avec un sous-domaine personnalisé.

   >[!NOTE]
   > Le nom de sous-domaine est utilisé dans le SDK Lecteur immersif lors du lancement du lecteur avec la fonction launchAsync.

   -SkuName peut être F0 (niveau Gratuit) ou S0 (niveau Standard, également gratuit pendant la préversion publique). Le niveau S0 a une limite de débit d’appels supérieure et aucun quota mensuel sur le nombre d’appels.

   -Location peut avoir une des valeurs suivantes : `eastus`, `westus`, `australiaeast`, `centralindia`, `japaneast`, `northeurope`, `westeurope`

   -CustomSubdomainName doit être globalement unique et ne peut pas inclure de caractères spéciaux, comme : « . », « ! », « , ».


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   Si l’opération réussit, le **point de terminaison** de la ressource doit afficher le nom du sous-domaine unique pour votre ressource.

   Ici, nous capturons l’objet de ressource nouvellement créé dans une variable **$Resource**, car il sera utilisé ultérieurement lors de l’octroi de l’accès à cette ressource.


   >[!NOTE]
   > Si vous créez une ressource dans le portail Azure, la ressource « Nom » est utilisée en tant que sous-domaine personnalisé. Vous pouvez vérifier le nom du sous-domaine dans le portail en accédant à la page Vue d’ensemble des ressources et en recherchant le sous-domaine dans le point de terminaison indiqué ici, par exemple `https://[SUBDOMAIN].cognitiveservices.azure.com/`. Vous pouvez également consulter cette page plus tard quand vous devez obtenir le sous-domaine pour l’intégration au SDK.

   Si la ressource a été créée dans le portail, vous pouvez également [obtenir une ressource existante](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) maintenant.

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Attribuer un rôle à un principal de service

Maintenant que vous disposez d’un sous-domaine personnalisé associé à votre ressource, vous devez affecter un rôle à un principal du service.

1. Pour commencer, [créons une application Azure AD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > Le mot de passe, également appelé « secret du client », sera utilisé lors de l’obtention des jetons d’authentification.

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Ici, nous capturons l’objet d’application Azure AD récemment créé dans une variable **$aadApp** pour l’utiliser à l’étape suivante.

2. Ensuite, vous devez [créer un principal du service](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) pour l’application Azure AD.

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Ici, nous capturons l’objet du principal du service nouvellement créé dans une variable **$principal** pour l’utiliser à l’étape suivante.


3. La dernière étape consiste à [affecter le rôle « Utilisateur Cognitive Services »](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) au principal du service (délimité à la ressource). En affectant un rôle, vous accordez au principal du service l’accès à cette ressource. Vous pouvez accorder au même principal du service l’accès à plusieurs ressources de votre abonnement.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Cette étape doit être effectuée pour chaque ressource de Lecteur immersif que vous créez. Par exemple, si vous créez plusieurs ressources pour différentes régions mondiales, vous devez effectuer cette étape pour chacune de ces ressources régionales pour que l’authentification Azure AD fonctionne pour toutes celles-ci. Si vous créez une ressource à un moment ultérieur, vous devrez également effectuer cette étape d’affectation de rôle pour cette nouvelle ressource.


## <a name="obtain-an-azure-ad-token"></a>Obtenir un jeton Azure AD

Dans cet exemple, votre mot de passe est utilisé pour authentifier le principal du service afin d’obtenir un jeton Azure AD.

1. Obtenez votre **TenantId** :
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Obtenez un jeton :
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > Le SDK Lecteur immersif utilise la propriété AccessToken du jeton, par exemple $token.AccessToken. Pour plus d’informations, consultez les [informations de référence](reference.md) du SDK et des [exemples de code](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples).

Le principal du service peut également être authentifié avec un certificat. Outre le principal du service, les utilisateurs principaux sont également pris en charge en ayant des autorisations déléguées via une autre application Azure AD. Dans ce cas, au lieu de mots de passe ou de certificats, les utilisateurs sont invités à fournir une authentification à deux facteurs lors de l’acquisition des jetons.

## <a name="next-steps"></a>Étapes suivantes

* Consulter le [didacticiel](./tutorial.md) pour voir que vous pouvez faire d’autre avec le SDK Lecteur immersif
* Explorer le [SDK Lecteur Immersive](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](./reference.md)