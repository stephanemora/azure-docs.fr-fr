---
title: Problèmes connus - Azure Digital Twins
description: Recevez de l’aide pour la reconnaissance et l’atténuation des problèmes connus avec Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 09/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 02a1a6d5088a722c0b919811afc0ac89c7c52550
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593569"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problèmes connus dans Azure Digital Twins

Cet article fournit des informations sur les problèmes connus associés à Azure Digital Twins.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>« Erreur 400 du client : Requête incorrecte » dans Cloud Shell

**Description du problème :** Les commandes dans Cloud Shell qui s’exécutent sur *https://shell.azure.com* peuvent échouer par intermittence avec l’erreur « Erreur 400 du client : Requête incorrecte pour l’URL : http://localhost:50342/oauth2/token » suivie par l’arborescence complète des appels de procédure.

| Cela me concerne-t-il ? | Cause | Résolution |
| --- | --- | --- |
| Dans&nbsp;Azure&nbsp;Digital&nbsp;Twins, ce problème concerne les groupes de commandes suivants :<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | C’est le résultat de ce problème connu dans Cloud Shell : [L’obtention d’un jeton à partir de Cloud Shell échoue par intermittence avec l’erreur « Erreur 400 du client : Requête incorrecte »](https://github.com/Azure/azure-cli/issues/11749).<br><br>Le problème concerne les jetons d’authentification d’instance Azure Digital Twins et l’authentification par défaut basée sur l’[identité managée](../active-directory/managed-identities-azure-resources/overview.md) de Cloud Shell. <br><br>Il n’impacte pas les commandes Azure Digital Twins des groupes de commandes `az dt` ou `az dt endpoint`, car celles-ci utilisent un autre type de jeton d’authentification (basé sur Azure Resource Manager) qui peut être utilisé sans problème avec l’authentification par identité managée de Cloud Shell. | Une façon de résoudre ce problème est de réexécuter la commande `az login` dans Cloud Shell et d’effectuer les étapes de connexion suivantes. Après cette action, votre session n’est plus soumise à une authentification par identité managée, ce qui permet d’éviter le problème de fond. Ensuite, vous pouvez réexécuter la commande.<br><br>Vous pouvez aussi ouvrir le volet Cloud Shell dans le portail Azure et effectuer votre travail Cloud Shell à partir de là.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Capture d’écran de l’icône Cloud Shell dans la barre d’icônes du portail Azure" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Une dernière solution consiste à [installer Azure CLI](/cli/azure/install-azure-cli) sur votre ordinateur afin de pouvoir exécuter les commandes Azure CLI localement. L’interface CLI locale ne rencontre pas ce problème. |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problème avec l’authentification interactive du navigateur sur Azure.Identity 1.2.0

**Description du problème :** Lorsque vous écrivez du code d’authentification dans vos applications Azure Digital Twins avec la version **1.2.0** de la bibliothèque [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true), vous risquez de rencontrer des problèmes avec la méthode [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true). Ce problème se présente comme une réponse d’erreur « Azure.Identity.AuthenticationFailedException » lors de la tentative d’authentification dans une fenêtre du navigateur. La fenêtre du navigateur peut ne pas démarrer complètement ou sembler réussir à authentifier l’utilisateur, alors que l’application cliente échoue toujours avec l’erreur.

| Cela me concerne-t-il ? | Cause | Résolution |
| --- | --- | --- |
| La&nbsp;méthode&nbsp;concernée&nbsp;est&nbsp;utilisée&nbsp;dans&nbsp;les&nbsp;articles suivants :<br><br>[Coder une application cliente](tutorial-code.md)<br><br>[Écrire le code d’authentification de l’application](how-to-authenticate-client.md)<br><br>[API et SDK Azure Digital Twins](concepts-apis-sdks.md) | Certains utilisateurs ont rencontré ce problème avec la version **1.2.0** de la bibliothèque `Azure.Identity`. | Pour résoudre ce problème, mettez à jour vos applications afin d’utiliser une [version ultérieure](https://www.nuget.org/packages/Azure.Identity) de `Azure.Identity`. Après la mise à jour de la version de la bibliothèque, le navigateur doit se charger et s’authentifier comme prévu. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problème d’authentification avec les informations d’identification Azure par défaut sur Azure.Identity 1.3.0

**Description du problème :** Lorsque vous écrivez du code d’authentification avec la version **1.3.0** de la bibliothèque [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true), vous risquez de rencontrer un problème avec la méthode [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) utilisée dans de nombreux exemples de ces documents Azure Digital Twins. Ce problème se présente sous la forme d’une réponse d’erreur « Azure.Identity.AuthenticationFailedException : Échec de l’authentification SharedTokenCacheCredential » lorsque le code tente de s’authentifier.

| Cela me concerne-t-il ? | Cause | Résolution |
| --- | --- | --- |
| `DefaultAzureCredential` est utilisée dans la plupart des exemples de documentation pour ce service qui incluent l’authentification. Si vous écrivez du code d’authentification en utilisant `DefaultAzureCredential` avec la version 1.3.0 de la bibliothèque `Azure.Identity` et que vous voyez ce message d’erreur, ce problème vous concerne. | Ce problème est probablement dû à un problème de configuration avec `Azure.Identity`. | L’une des stratégies permettant de résoudre ce problème consiste à exclure `SharedTokenCacheCredential` de vos informations d’identification, comme décrit dans ce [problème DefaultAzureCredential](https://github.com/Azure/azure-sdk/issues/1970) qui est actuellement ouvert concernant `Azure.Identity`.<br>Une autre option consiste à modifier votre application pour qu’elle utilise une version antérieure de `Azure.Identity`, comme la [version 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). L’utilisation d’une version antérieure n’impacte pas le fonctionnement d’Azure Digital Twins et constitue donc une solution possible. |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la sécurité et les autorisations sur Azure Digital Twins :
* [Sécurité pour les solutions Azure Digital Twins](concepts-security.md)