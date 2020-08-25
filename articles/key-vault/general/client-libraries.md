---
title: Bibliothèques de client pour Azure Key Vault | Microsoft Docs
description: Bibliothèques de client pour Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263782"
---
# <a name="client-libraries-for-azure-key-vault"></a>Bibliothèques de client pour Azure Key Vault

Les bibliothèques de client pour Azure Key Vault permettent un accès par programmation aux fonctionnalités Key Vault à partir de différents langages, notamment .NET, Python, Java et JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Bibliothèques de client par langage et objet

Chaque SDK a des bibliothèques de client distinctes pour les secrets, les clés et les certificats, comme indique dans le tableau ci-dessous.

| Langage | Secrets | Keys | Certificats |
|--|--|--|--|
| .NET | - [Référence API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Package NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Démarrage rapide](../secrets/quick-create-net.md) | - [Référence API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Package NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Référence API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Package NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [Référence API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Package Pypi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Démarrage rapide](../secrets/quick-create-python.md) |- [Référence API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Package Pypi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Démarrage rapide](../keys/quick-create-python.md) | - [Référence API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Package Pypi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Démarrage rapide](../certificates/quick-create-python.md) |
| Java | - [Référence API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Démarrage rapide](../secrets/quick-create-java.md) |- [Référence API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Référence API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [Référence API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [Package npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Démarrage rapide](../secrets/quick-create-node.md) |- [Référence API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [Package npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Référence API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [Package npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Étapes suivantes

- Consulter le [Guide du développeur Azure Key Vault](developers-guide.md)
- En savoir plus sur l’[identité managée pour Azure Key Vault](managed-identity.md)