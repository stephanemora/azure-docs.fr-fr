---
title: Utiliser des identités managées dans Communication Services (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Les identités managées vous permettent d’autoriser l’accès à Azure Communication Services à partir d’applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, et d’autres ressources.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: ee691d4809a68a0ba60f60a2240b76a1e53104bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171571"
---
# <a name="use-managed-identities-net"></a>Utiliser des identités managées (.NET)

Prise en main d’Azure Communication Services à l’aide d’identités managées dans .NET. Les bibliothèques de client Communication Services Administration et SMS prennent en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Ce guide de démarrage rapide vous montre comment autoriser l’accès aux bibliothèques de client Administration et SMS à partir d’un environnement Azure qui prend en charge les identités managées. Il explique également comment tester votre code dans un environnement de développement.

## <a name="prerequisites"></a>Prérequis

 - Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
 - Une chaîne de connexion et une ressource Communication Services actives. [Créez une ressource Communication Services](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Configuration

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Activer des identités managées sur une machine virtuelle ou App service

Les identités managées doivent être activées sur les ressources Azure que vous autorisez. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliothèques clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Attribuer des rôles Azure avec le portail Azure

1. Accédez au portail Azure.
1. Accédez à la ressource Azure Communication Services.
1. Accédez au menu Contrôle d'accès (IAM) -> + Ajouter -> Ajouter une attribution de rôle.
1. Sélectionnez le rôle « Contributeur » (il s’agit du seul rôle pris en charge).
1. Sélectionnez « Identité managée affectée par l'utilisateur » (ou une identité « Identité managée affectée par le système »), puis l’identité de votre choix. Enregistrez votre sélection.

![Rôle d'identité managée](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Attribuer des rôles Azure avec PowerShell

Pour attribuer des rôles et des autorisations à l’aide de PowerShell, consultez [Ajouter ou supprimer des attributions de rôles Azure à l’aide d’Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Ajouter une identité managée à votre solution Communication Services

### <a name="install-the-client-library-packages"></a>Installer les packages de bibliothèques de client

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Utiliser les packages de bibliothèque de client

Ajoutez les directives `using` suivantes à votre code pour utiliser les bibliothèques de client Azure Identity et Stockage Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

Les exemples ci-dessous utilisent [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement.

### <a name="create-an-identity-and-issue-a-token"></a>Créer une identité et émettre un jeton

L’exemple de code suivant montre comment créer un objet client de service avec des jetons Azure Active Directory, puis comment utiliser le client afin d’émettre un jeton pour un nouvel utilisateur :

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEndpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Envoyer un SMS avec des jetons Azure Active Directory

L’exemple de code suivant montre comment créer un objet client de service avec des jetons Azure Active Directory, puis comment utiliser le client pour envoyer un SMS :

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’authentification](../concepts/authentication.md)

Vous souhaiterez peut-être également :

- [En savoir plus sur le contrôle d’accès en fonction du rôle dans Azure](../../../articles/role-based-access-control/index.yml)
- [En savoir plus sur la bibliothèque d’identité Azure pour .NET](/dotnet/api/overview/azure/identity-readme)
- [Créer des jetons d’accès utilisateur](../quickstarts/access-tokens.md)
- [Envoyer un message SMS](../quickstarts/telephony-sms/send.md)
- [En savoir plus sur les SMS](../concepts/telephony-sms/concepts.md)