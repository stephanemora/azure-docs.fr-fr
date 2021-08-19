---
title: Exemples d’API pour la modification de vos flux d’utilisateurs Azure AD B2C | Microsoft Docs
description: Exemples de code pour la modification de flux d’utilisateurs avec des connecteurs d’API
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/16/2021
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d27bac61a7f39b50d692e579edb29da945234a27
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114479572"
---
# <a name="api-connector-rest-api-samples"></a>Exemples d’API REST de connecteur d’API

Les tableaux suivants fournissent des liens vers des exemples de code pour tirer parti des API web dans vos flux utilisateur avec des [connecteurs d’API](api-connectors-overview.md). Ces exemples sont principalement conçus pour être utilisés avec les flux d’utilisateurs intégrés.

## <a name="azure-function-quickstarts"></a>Démarrages rapides Azure Function
| Exemple                                                                                                                          | Description                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Cet exemple de fonction Azure .NET Core montre comment limiter les connexions à des domaines de messagerie spécifiques et valider les informations fournies par l’utilisateur. |
| [Node.JS](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Cet exemple de fonction Azure Node.js montre comment limiter les connexions à des domaines de messagerie spécifiques et valider les informations fournies par l’utilisateur.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Cet exemple de fonction Azure Python montre comment limiter les connexions à des domaines de messagerie spécifiques et valider les informations fournies par l’utilisateur.    |


## <a name="automated-fraud-protection-services--captcha"></a>Services automatisés de protection contre les fraudes et CAPTCHA
| Exemple                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Protection contre les fraudes et mauvaises utilisations Arkose Labs](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | Cet exemple montre comment protéger les inscriptions utilisateur à l’aide du service de protection contre les fraudes et mauvaises utilisations Arkose Labs. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | Cet exemple montre comment protéger les inscriptions utilisateur à l’aide d’un test reCAPTCHA pour empêcher les mauvaises utilisations automatisées. |


## <a name="identity-verification"></a>Vérification d’identité

| Exemple                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Cet exemple montre comment vérifier une identité d’utilisateur dans le cadre de vos flux d’inscription avec le service IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Cet exemple montre comment vérifier une identité d’utilisateur dans le cadre de vos flux d’inscription avec le service Experian. |


## <a name="other"></a>Autres

| Exemple                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Code d’invitation](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | Cet exemple montre comment limiter l’inscription à des publics spécifiques à l’aide de codes d’invitation.|
| [Connecteurs d’API – Exemples de la communauté](https://github.com/azure-ad-b2c/api-connector-samples) | Ce dépôt contient des exemples gérés par la communauté qui portent sur des scénarios activés par des connecteurs d’API.|
