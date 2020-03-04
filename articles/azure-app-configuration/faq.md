---
title: Azure App Configuration – Questions fréquentes (FAQ)
description: Questions fréquentes (FAQ) sur App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 60ba0a7723861d6e642a23418dda6a1daa57f14e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523490"
---
# <a name="azure-app-configuration-faq"></a>Azure App Configuration – Questions fréquentes (FAQ)

Cet article répond aux questions fréquentes sur Azure App Configuration.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>En quoi App Configuration est-il différent d’Azure Key Vault ?

App Configuration permet aux développeurs de gérer les paramètres d’application et de contrôler la disponibilité des fonctionnalités. Il vise à simplifier de nombreuses tâches impliquant l’utilisation de données de configuration complexes.

App Configuration prend en charge :

- Les espace de noms hiérarchiques
- L’étiquetage
- Le requêtes étendues
- La récupération par lots
- Les opérations de gestion spécialisées
- Une interface utilisateur de gestion des fonctionnalités

App Configuration et Key Fault sont complémentaires. D’ailleurs, il est conseillé d’utiliser les deux côte à côte dans la plupart des déploiements d’applications.

## <a name="should-i-store-secrets-in-app-configuration"></a>Dois-je stocker les secrets dans App Configuration ?

Même si App Configuration offre une sécurité renforcée, Key Vault reste le meilleur emplacement de stockage pour les secrets d’application. Key Vault propose un chiffrement au niveau du matériel, des stratégies d’accès précises, ainsi que des opérations de gestion, comme la rotation des certificats.

Vous pouvez créer des valeurs App Configuration qui font référence à des secrets stockés dans Key Vault. Pour plus d’informations, consultez [Utiliser des références Key Vault dans une application ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Mes données sont-elles chiffrées par App Configuration ?

Oui. App Configuration chiffre toutes les valeurs de clés qu’il renferme, de même que les communications réseau. Les noms de clés et les étiquettes servent d’index pour récupérer les données de configuration et ne sont pas chiffrés.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>En quoi App Configuration est-il différent des paramètres Azure App Service ?

Azure App Service vous permet de définir des paramètres d’application pour chaque instance App Service. Ces paramètres sont transmis en tant que variables d’environnement au code de l’application. Vous pouvez associer un paramètre à un emplacement de déploiement spécifique, si vous le souhaitez. Pour plus d’informations, consultez [Configuration des paramètres de l’application](/azure/app-service/configure-common#configure-app-settings).

En revanche, Azure App Configuration vous permet de définir des paramètres qui peuvent être partagés entre plusieurs applications. Cela comprend les applications qui s’exécutent dans App Service, de même que d'autres plateformes. Votre code d'application accède à ces paramètres via les fournisseurs de configuration pour .NET et Java, par le biais du kit de développement logiciel (SDK) Azure, ou directement via les API REST.

Vous pouvez également importer et exporter des paramètres entre App Service et App Configuration. Cela vous permet de configurer rapidement un nouveau magasin App Configuration en fonction des paramètres App Service existants. Vous pouvez aussi partager la configuration avec une application existante qui s’appuie sur les paramètres App Service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existe-t-il des limitations de taille sur les clés et les valeurs stockées dans App Configuration ?

Il existe une limite de 10 Ko pour un seul élément de clé-valeur.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Comment dois-je stocker les configurations pour plusieurs environnements (test, intermédiaire, production, etc) ?

Vous contrôlez qui peut accéder à App Configuration au niveau de chaque magasin. Utilisez un magasin distinct pour chaque environnement qui nécessite des autorisations différentes. Il s’agit de l’approche qui offre la meilleure isolation sur le plan de la sécurité.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quels sont les modes d’utilisation recommandés d’App Configuration ?

Prenez connaissance des [bonnes pratiques](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quel est le coût d’App Configuration ?

Il existe deux niveaux tarifaires : 

- Niveau Gratuit
- Niveau standard.

Si vous avez créé un magasin avant l’introduction du niveau Standard, il passe automatiquement au niveau Gratuit lors de la mise à la disposition générale. Vous pouvez choisir de passer au niveau Standard ou de rester au niveau Gratuit.

En revanche, vous ne pouvez pas passer un magasin du niveau Standard au niveau Gratuit. Au niveau Gratuit, vous pouvez créer un magasin, puis importer des données de configuration dans ce magasin.

## <a name="which-app-configuration-tier-should-i-use"></a>Quel niveau App Configuration dois-je utiliser ?

Les deux niveaux App Configuration offrent des fonctionnalités de base, notamment des paramètres de configuration, indicateurs de fonctionnalité, références Key Vault, opérations de gestion de base, métriques et journaux.

Voici quelques considérations relatives au choix d'un niveau.

- **Ressources par abonnement** : Une ressource se compose d’un magasin de configuration unique. Au niveau Gratuit, chaque abonnement est limité à un magasin de configuration. Au niveau Standard, les abonnements ne sont pas limités en termes de magasins de configuration.
- **Stockage par ressource** : Au niveau Gratuit, chaque magasin de configuration est limité à 10 Mo de stockage. Au niveau Standard, chaque magasin de configuration peut utiliser jusqu’à 1 Go de stockage.
- **Historique des clés** : App Configuration stocke l'historique des différentes modifications apportées aux clés. Au niveau Gratuit, cet historique est stocké pendant sept jours. Au niveau Standard, cet historique est stocké pendant 30 jours.
- **Requêtes par jour** : Au niveau Gratuit, les magasins sont limités à 1 000 requêtes par jour. Lorsqu'un magasin atteint 1 000 requêtes, il renvoie le code d’état HTTP 429 pour toutes les requêtes jusqu’à minuit UTC.

    Pour les magasins du niveau Standard, les 200 000 premières requêtes quotidiennes sont comprises dans les frais journaliers. Les requêtes supplémentaires sont facturées en tant que dépassement.

- **Contrat de niveau de service** : Le niveau Standard présente un contrat de niveau de service garantissant une disponibilité de 99,9 %. Le niveau Gratuit ne présente aucun contrat de niveau de service.
- **Fonctionnalités de sécurité** : Les deux niveaux incluent des fonctionnalités de sécurité de base, parmi lesquelles le chiffrement avec des clés managées par Microsoft, l’authentification via HMAC ou Azure Active Directory, la prise en charge de RBAC et l’identité managée. Le niveau Standard offre des fonctionnalités de sécurité plus avancées, notamment la prise en charge d'Azure Private Link et le chiffrement avec des clés managées par le client.
- **Coût** : Au niveau Standard, les magasins génèrent des frais d’utilisation quotidiens. Au-delà de l'allocation quotidienne, les requêtes génèrent aussi des frais de dépassement. Au niveau Gratuit, l'utilisation d'un magasin ne génère pas de frais.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Puis-je passer un magasin du niveau Gratuit au niveau Standard ? Puis-je passer un magasin du niveau Standard au niveau Gratuit ?

Vous pouvez à tout moment passer du niveau Gratuit au niveau Standard.

En revanche, vous ne pouvez pas passer un magasin du niveau Standard au niveau Gratuit. Au niveau Gratuit, vous pouvez créer un magasin, puis [importer des données de configuration dans ce magasin](howto-import-export-data.md).

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Comment puis-je recevoir des annonces sur les nouvelles versions et d’autres informations relatives à App Configuration ?

Abonnez-vous à notre [référentiel d’annonces GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Comment puis-je signaler un problème ou faire une suggestion ?

Vous pouvez nous contacter directement sur [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Étapes suivantes

* [À propos d’Azure App Configuration](./overview.md)
