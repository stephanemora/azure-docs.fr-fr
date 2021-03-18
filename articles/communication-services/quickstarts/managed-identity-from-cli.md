---
title: Créer une application d’identité managée Azure Active Directory à partir d’Azure CLI
titleSuffix: An Azure Communication Services quickstart
description: Les identités managées vous permettent d’autoriser l’accès à Azure Communication Services à partir d’applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, et d’autres ressources. Ce guide de démarrage rapide porte sur la gestion des identités avec Azure CLI.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: e708536395807fc74dc5bfd73836e050832cca39
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493337"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Autoriser une identité managée à accéder aux ressources de communication de votre environnement de développement

La bibliothèque de client Azure Identity fournit une prise en charge de l’authentification par jeton Azure Active Directory (Azure AD) pour le SDK Azure. Les versions les plus récentes des bibliothèques de client Azure Communication Services pour .NET, Java, Python et JavaScript s’intègrent à la bibliothèque Azure Identity afin de fournir un moyen simple et sécurisé d’acquérir un jeton OAuth 2.0 pour l’autorisation des demandes Azure Communication Services.

L’un des avantages de la bibliothèque de client Azure Identity est qu’elle vous permet d’utiliser le même code pour vous authentifier auprès de plusieurs services, que votre application soit exécutée dans l’environnement de développement ou dans Azure. La bibliothèque cliente Azure Identity authentifie un principal de sécurité. Lorsque votre code s’exécute dans Azure, le principal de sécurité est une identité managée pour les ressources Azure. Dans l’environnement de développement, étant donné que l’identité managée n’existe pas, la bibliothèque de client authentifie soit l’utilisateur, soit l’application inscrite à des fins de test.

## <a name="prerequisites"></a>Prérequis

 - Azure CLI. [Guide d'installation](https://docs.microsoft.com/cli/azure/install-azure-cli)
 - Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).

## <a name="setting-up"></a>Configuration

Les identités managées doivent être activées sur les ressources Azure que vous autorisez. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliothèques clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Authentifier une application inscrite dans l’environnement de développement

Si votre environnement de développement ne prend pas en charge l’authentification unique ou la connexion via un navigateur web, vous pouvez utiliser une application inscrite pour vous authentifier dans l’environnement de développement.

### <a name="creating-an-azure-active-directory-registered-application"></a>Création d’une application inscrite Azure Active Directory

Pour créer une application inscrite à partir d’Azure CLI, vous devez être connecté au compte Azure dans lequel vous souhaitez que les opérations soient effectuées. Pour ce faire, vous pouvez utiliser la commande `az login` et entrer vos informations d’identification dans le navigateur. Une fois que vous êtes connecté à votre compte Azure à partir d’Azure CLI, vous pouvez appeler la commande `az ad sp create-for-rbac` pour créer l’application inscrite.

Les exemples suivants utilisent Azure CLI pour créer une application inscrite.

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

La commande `az ad sp create-for-rbac` retourne une liste de propriétés de principal du service au format JSON. Copiez ces valeurs pour pouvoir les utiliser afin de créer les variables d’environnement nécessaires à l’étape suivante.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> La propagation des attributions de rôles Azure peut prendre plusieurs minutes.

#### <a name="set-environment-variables"></a>Définir des variables d’environnement

La bibliothèque de client Azure Identity lit les valeurs de trois variables d’environnement au moment de l’exécution pour authentifier l’application. Le tableau suivant indique la valeur à définir pour chaque variable d’environnement.

|Variable d’environnement|Valeur
|-|-
|`AZURE_CLIENT_ID`|Valeur `appId` du JSON généré 
|`AZURE_TENANT_ID`|Valeur `tenant` du JSON généré
|`AZURE_CLIENT_SECRET`|Valeur `password` du JSON généré

> [!IMPORTANT]
> Après avoir défini les variables d’environnement, fermez et rouvrez votre fenêtre de console. Si vous utilisez Visual Studio ou un autre environnement de développement, vous devrez peut-être le redémarrer afin qu’il enregistre les nouvelles variables d’environnement.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’authentification](../concepts/authentication.md)

Vous souhaiterez peut-être également :

- [En savoir plus sur la bibliothèque Azure Identity](/dotnet/api/overview/azure/identity-readme)
