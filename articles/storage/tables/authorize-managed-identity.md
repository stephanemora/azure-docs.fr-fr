---
title: Autoriser l’accès à des données de table à l’aide d’une identité managée (préversion)
titleSuffix: Azure Storage
description: Utilisez des identités managées pour les ressources Azure afin d’autoriser l’accès à des données de table à partir d’applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, etc.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d6410115f9ba12f0beb5dc1408a076753182797
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859649"
---
# <a name="authorize-access-to-table-data-with-managed-identities-for-azure-resources-preview"></a>Autoriser l’accès à des données de table à l’aide d’identités managées pour les ressources Azure (préversion)

Stockage Table Azure prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Les identités managées pour les ressources Azure autorisent l’accès à des données de table en utilisant les informations d’identification Azure AD d’applications s’exécutant dans des machines virtuelles Azure, d’applications de fonction, de groupes de machines virtuelles identiques et d’autres services. En utilisant des identités managées pour ressources Azure et Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.

Cet article montre comment autoriser l’accès à des données de table à partir d’une machine virtuelle Azure à l’aide d’identités managées pour les ressources Azure.

> [!IMPORTANT]
> Avec Azure AD, l’autorisation pour les tables est actuellement en **PRÉVERSION**. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-managed-identities-on-a-vm"></a>Activer les identités managées sur une machine virtuelle

Avant de pouvoir utiliser des identités managées pour les ressources Azure pour autoriser l’accès à des tables à partir de votre machine virtuelle, vous devez activer les identités managées pour les ressources Azure sur la machine virtuelle. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliothèques clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Pour plus d’informations sur les identités managées, consultez [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>Attribuer un rôle RBAC à une identité managée

Lorsqu’un principal de sécurité Azure AD tente d’accéder à des données dans un compte Stockage Azure, ce principal de sécurité doit avoir des autorisations pour la ressource de données. Que le principal de sécurité soit une identité managée dans Azure ou un compte d’utilisateur Azure AD exécutant du code dans l’environnement de développement, un rôle Azure qui accorde l’accès aux données dans Stockage Azure doit être attribué au principal de sécurité. Pour obtenir des informations sur l’attribution d’autorisations d’accès aux données via le contrôle d’accès en fonction du rôle Azure, consultez [Attribuer un rôle Azure pour l’accès aux données de blob](assign-azure-role-data-access.md).

> [!NOTE]
> Lorsque vous créez un compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée via Azure AD. Vous devez vous attribuer explicitement un rôle Azure pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, groupe de ressources, compte de stockage, conteneur ou table.

## <a name="use-a-managed-identity-to-create-a-table-in-net"></a>Utiliser une identité managée pour créer une table dans .NET

La bibliothèque de client Azure Identity simplifie le processus d’obtention d’un jeton d’accès OAuth 2.0 pour l’autorisation avec Azure Active Directory (Azure AD) via le [Kit de développement logiciel (SDK) Azure](https://github.com/Azure/azure-sdk). Lorsque vous utilisez la bibliothèque de client Azure Identity pour obtenir un jeton d’accès, vous pouvez utiliser le même code pour acquérir le jeton, que votre application s’exécute dans l’environnement de développement ou dans Azure. Pour plus d’informations, consultez [Utiliser la bibliothèque Azure Identity pour obtenir un jeton d’accès pour l’autorisation](../common/identity-library-acquire-token.md).

Pour obtenir un jeton que votre code peut utiliser pour autoriser les requêtes adressées à Stockage Azure, créez une instance de la classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Vous pouvez ensuite utiliser le jeton pour créer un objet client de service qui est autorisé à effectuer des opérations sur les données dans Stockage Azure. Pour plus d’informations sur l’utilisation de la classe **DefaultAzureCredential** pour autoriser une identité managée à accéder à Stockage Azure, consultez [Bibliothèque de client Azure Identity pour .NET](/dotnet/api/overview/azure/identity-readme).

L’exemple de code suivant montre comment obtenir les informations d’identification du jeton authentifié et les utiliser pour créer un objet client de service, puis comment utiliser le client de service pour créer une table :

```csharp
public static void CreateTable(string accountName, string tableName)
{
    // Construct the table endpoint from the arguments.
    string tableEndpoint = string.Format("https://{0}.table.core.windows.net/",
                                                accountName);

    // Get a token credential and create a service client object for the table.
    TableClient tableClient = new TableClient(new Uri(tableEndpoint), 
                                                tableName, 
                                                new DefaultAzureCredential());

    try
    {
        // Create the table.
        tableClient.Create();

    }
    catch (RequestFailedException e)
    {
        Console.WriteLine("Exception: {0}", e.Message);
    }
}
```

> [!NOTE]
> Pour autoriser les requêtes sur des données de table avec Azure AD, vous devez utiliser le protocole HTTPS pour ces requêtes.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer un rôle Azure pour l’accès aux données de table](assign-azure-role-data-access.md)
- [Autoriser l’accès aux tables avec Azure Active Directory (préversion)](authorize-access-azure-active-directory.md)