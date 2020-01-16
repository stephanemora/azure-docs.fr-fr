---
title: Azure App Configuration – Bonnes pratiques | Microsoft Docs
description: Découvrir comment utiliser au mieux Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3b43ca5b6bec64d9283a64c9bcc0a3b60c21bca4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750418"
---
# <a name="azure-app-configuration-best-practices"></a>Azure App Configuration – Bonnes pratiques

Cet article décrit les modèles courants et les bonnes pratiques à observer quand vous utilisez Azure App Configuration.

## <a name="key-groupings"></a>Regroupements de clés

App Configuration propose deux options pour organiser les clés :

* Préfixes de clés
* Étiquettes

Vous pouvez regrouper vos clés avec une de ces options ou les deux.

Les *préfixes de clés* constituent la partie initiale des clés. Vous pouvez regrouper logiquement un ensemble de clés en ajoutant le même préfixe à leur nom. Les préfixes peuvent être constitués de plusieurs composants reliés par un délimiteur, par exemple `/`, à la manière d’un chemin d’URL, pour former un espace de noms. De telles hiérarchies sont utiles quand vous stockez des clés pour un grand nombre d’applications, de services de composants et d’environnements dans un même magasin App Configuration.

Une chose importante à garder à l’esprit est que les clés sont ce à quoi votre code d’application fait référence pour récupérer les valeurs des paramètres correspondants. Les clés ne doivent pas changer, sans quoi vous devez modifier votre code chaque fois que cela se produit.

Les *étiquettes* représentent un attribut de clé. Elles servent à créer des variantes d’une clé. Par exemple, vous pouvez attribuer des étiquettes à plusieurs versions d’une clé. Une version peut être une itération, un environnement ou d’autres informations contextuelles. Votre application peut demander un ensemble de valeurs de clés totalement différent en spécifiant une autre étiquette. De ce fait, toutes les références de clés restent inchangées dans votre code.

## <a name="key-value-compositions"></a>Compositions clé-valeur

App Configuration considère toutes les clés qui y sont stockées comme des entités indépendantes. App Configuration ne tente pas de déduire une quelconque relation entre les clés ou d’hériter des valeurs de clés en fonction de leur hiérarchie. Vous avez la possibilité d’agréger plusieurs ensembles de clés, mais il convient dans ce cas d’utiliser des étiquettes avec un empilement de configuration adéquat dans votre code d’application.

Intéressons-nous à un exemple. Supposez que vous disposez d’un paramètre nommé **Asset1**, dont la valeur peut varier en fonction de l’environnement de développement. Vous créez une clé nommée « Asset1 » avec une étiquette vide et une étiquette nommée « Development ». Dans la première étiquette, vous placez la valeur par défaut de **Asset1**, et vous placez une valeur spécifique pour « Development » dans l’autre.

Dans votre code, vous commencez par récupérer les valeurs de clés sans étiquettes, puis vous récupérez une deuxième fois le même ensemble de valeurs de clés, mais cette fois avec l’étiquette « Development ». Quand vous récupérez les valeurs à la deuxième fois, les valeurs précédentes des clés sont remplacées. Le système de configuration .NET Core vous permet d’« empiler » plusieurs ensembles de données de configuration les uns sur les autres. Si une clé se trouve dans plusieurs ensembles, le dernier ensemble la contenant est utilisé. Avec un framework de programmation moderne comme .NET Core, vous bénéficiez gratuitement de cette fonctionnalité d’empilement si vous utilisez un fournisseur de configuration natif pour accéder à App Configuration. L’extrait de code suivant montre comment vous pouvez implémenter l’empilement dans une application .NET Core :

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Amorçage d’App Configuration

Pour accéder à un magasin App Configuration, vous pouvez utiliser sa chaîne de connexion, qui est disponible sur le portail Azure. Étant donné que les chaînes de connexion contiennent des informations d’identification, elles sont considérées comme des secrets. Ces secrets doivent être stockés dans Azure Key Vault, et votre code doit s’authentifier auprès de Key Vault pour les récupérer.

Une meilleure option consiste à utiliser la fonctionnalité d’identités managées dans Azure Active Directory. Avec les identités managées, vous avez seulement besoin de l’URL de point de terminaison App Configuration pour amorcer l’accès à votre magasin App Configuration. Vous pouvez incorporer l’URL dans votre code d’application (par exemple, dans le fichier *appsettings.json*). Pour plus d’informations, consultez [S’intégrer avec des identités managées Azure](howto-integrate-azure-managed-service-identity.md).

## <a name="app-or-function-access-to-app-configuration"></a>Accès des applications ou des fonctions à App Configuration

Vous pouvez fournir un accès à App Configuration pour les applications web ou les fonctions en utilisant une des méthodes suivantes :

* À partir du portail Azure, entrez la chaîne de connexion à votre magasin App Configuration dans les paramètres Application d’App Service.
* Stockez la chaîne de connexion à votre magasin App Configuration dans Key Vault et [faites-y référence dans App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Utilisez les identités managées Azure pour accéder au magasin App Configuration. Pour plus d’informations, consultez [S’intégrer avec des identités managées Azure](howto-integrate-azure-managed-service-identity.md).
* Envoyer (push) la configuration d’App Configuration vers App Service. App Configuration propose une fonction d’exportation (sur le portail Azure et dans Azure CLI) qui envoie directement les données dans App Service. Avec cette méthode, vous n’avez pas du tout besoin de modifier le code d’application.

## <a name="next-steps"></a>Étapes suivantes

* [Clés et valeurs](./concept-key-value.md)
