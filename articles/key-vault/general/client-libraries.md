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
ms.openlocfilehash: c5b07c1059e146aaf3c3c2a26038514eb614a3cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505917"
---
# <a name="client-libraries-for-azure-key-vault"></a>Bibliothèques de client pour Azure Key Vault

Les bibliothèques de client pour Azure Key Vault permettent un accès par programmation aux fonctionnalités Key Vault à partir de différents langages, notamment .NET, Python, Java et JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Bibliothèques de client par langage et objet

Chaque SDK a des bibliothèques de client distinctes pour le coffre de clés, les secrets, les clés et les certificats, comme indiqué dans le tableau ci-dessous.

| Langage | Secrets | Keys | Certificats | Coffre de clés (plan de gestion) |
|--|--|--|--|--|
| .NET | - [Référence API](/dotnet/api/azure.security.keyvault.secrets)<br>- [Package NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Démarrage rapide](../secrets/quick-create-net.md) | - [Référence API](/dotnet/api/azure.security.keyvault.keys)<br>- [Package NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Démarrage rapide](../keys/quick-create-net.md) | - [Référence API](/dotnet/api/azure.security.keyvault.certificates)<br>- [Package NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Démarrage rapide](../certificates/quick-create-net.md) | - [Référence API](/dotnet/api/microsoft.azure.management.keyvault)<br>- [Package NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [Référence API](/python/api/overview/azure/keyvault-secrets-readme)<br>- [Package Pypi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Démarrage rapide](../secrets/quick-create-python.md) |- [Référence API](/python/api/overview/azure/keyvault-keys-readme)<br>- [Package Pypi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Démarrage rapide](../keys/quick-create-python.md) | - [Référence API](/python/api/overview/azure/keyvault-certificates-readme)<br>- [Package Pypi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Démarrage rapide](../certificates/quick-create-python.md) | - [Référence API](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [Package Pypi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [Référence API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Démarrage rapide](../secrets/quick-create-java.md) |- [Référence API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Démarrage rapide](../keys/quick-create-java.md) | - [Référence API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Démarrage rapide](../certificates/quick-create-java.md) |- [Référence API](/java/api/com.microsoft.azure.management.keyvault)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [Référence API](/javascript/api/@azure/keyvault-secrets/)<br>- [Package npm](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Démarrage rapide](../secrets/quick-create-node.md) |- [Référence API](/javascript/api/@azure/keyvault-keys/)<br>- [Package npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Démarrage rapide](../keys/quick-create-node.md)| - [Référence API](/javascript/api/@azure/keyvault-certificates/)<br>- [Package npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Démarrage rapide](../certificates/quick-create-node.md) |  - [Référence API](/javascript/api/@azure/arm-keyvault/)<br>- [Package npm](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Étapes suivantes

- Consulter le [Guide du développeur Azure Key Vault](developers-guide.md)
- En savoir plus sur l’[authentification auprès d’un coffre de clés](authentication.md)
- En savoir plus sur la [sécurisation de l’accès à un coffre de clés](secure-your-key-vault.md)
