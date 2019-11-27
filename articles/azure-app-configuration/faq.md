---
title: Azure App Configuration – Questions fréquentes (FAQ) | Microsoft Docs
description: Questions fréquentes (FAQ) sur App Configuration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904088"
---
# <a name="azure-app-configuration-faq"></a>Azure App Configuration – Questions fréquentes (FAQ)

Cet article traite les questions fréquentes sur Azure App Configuration.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>En quoi App Configuration est-il différent d’Azure Key Vault ?

App Configuration a été conçu pour des cas d’usage bien distincts : il aide les développeurs à gérer les paramètres d’application et à contrôler la disponibilité des fonctionnalités. Il vise à simplifier de nombreuses tâches impliquant l’utilisation de données de configuration complexes.

App Configuration prend en charge :

- Les espace de noms hiérarchiques
- L’étiquetage
- Le requêtes étendues
- La récupération par lots
- Les opérations de gestion spécialisées
- Une interface utilisateur de gestion des fonctionnalités

App Configuration et Key Fault sont complémentaires. D’ailleurs, il est conseillé d’utiliser les deux côte à côte dans la plupart des déploiements d’applications.

## <a name="should-i-store-secrets-in-app-configuration"></a>Dois-je stocker les secrets dans App Configuration ?

Même si App Configuration offre une sécurité renforcée, Key Vault reste le meilleur emplacement de stockage pour les secrets d’application. Key Vault propose un chiffrement au niveau du matériel, des stratégies d’accès granulaires ainsi que des opérations de gestion, comme la rotation des certificats.

Vous pouvez créer des valeurs App Configuration qui font référence à des secrets stockés dans Key Vault. Pour plus d’informations, consultez [Utiliser des références Key Vault dans une application ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Mes données sont-elles chiffrées par App Configuration ?

Oui. App Configuration chiffre toutes les valeurs de clés qu’il renferme, de même que les communications réseau. Les noms de clés servent d’index pour récupérer les données de configuration et ne sont pas chiffrés.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>En quoi App Configuration est-il différent des paramètres Azure App Service ?

Azure App Service vous permet de définir des paramètres d’application pour chaque instance App Service. Ces paramètres sont transmis en tant que variables d’environnement au code de l’application. Vous pouvez associer un paramètre à un emplacement de déploiement spécifique, si vous le souhaitez. Pour plus d’informations, consultez [Configuration des paramètres de l’application](/azure/app-service/configure-common#configure-app-settings).

En revanche, Azure App Configuration vous permet de définir des paramètres qui peuvent être partagés entre plusieurs applications, y compris les applications qui s’exécutent dans App Service. Ces paramètres sont accessibles dans le code de l’application via les fournisseurs de configuration pour .NET et Java, par le biais du Kit de développement logiciel (SDK) Azure, ou directement via les API REST.

Vous pouvez également importer et exporter des paramètres entre App Service et App Configuration. Cela vous permet de configurer rapidement un nouveau magasin App Configuration en fonction des paramètres App Service existants, ou de partager facilement la configuration avec une application existante qui s’appuie sur des paramètres d’App Service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existe-t-il des limitations de taille sur les clés et les valeurs stockées dans App Configuration ?

Il existe une limite de 10 Ko pour un seul élément de clé-valeur.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Comment dois-je stocker les configurations pour plusieurs environnements (test, intermédiaire, production, etc) ?

Actuellement, vous pouvez contrôler qui a accès à App Configuration par magasin. Utilisez un magasin distinct pour chaque environnement qui nécessite des autorisations différentes. Il s’agit de l’approche qui offre la meilleure isolation sur le plan de la sécurité.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quels sont les modes d’utilisation recommandés d’App Configuration ?

Prenez connaissance des [bonnes pratiques](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quel est le coût d’App Configuration ?

L’utilisation de ce service est gratuite pour la préversion publique.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Comment puis-je recevoir des annonces sur les nouvelles versions et d’autres informations relatives à App Configuration ?

Abonnez-vous à notre [référentiel d’annonces GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Comment puis-je signaler un problème ou faire une suggestion ?

Vous pouvez nous contacter directement sur [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Étapes suivantes

* [À propos d’Azure App Configuration](./overview.md)
