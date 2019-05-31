---
title: Configuration des applications meilleures pratiques pour Azure | Microsoft Docs
description: Découvrez comment mieux utiliser la Configuration de l’application Azure
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
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393592"
---
# <a name="azure-app-configuration-best-practices"></a>Configuration des applications meilleures pratiques pour Azure

Cet article décrit les meilleures pratiques et tendances courantes lorsque vous utilisez la Configuration de l’application Azure.

## <a name="key-groupings"></a>Regroupements de clé

Configuration de l’application fournit deux options pour organiser des clés :

* Préfixes de clé
* Étiquettes

Vous pouvez utiliser une ou les deux options pour regrouper vos clés.

*Préfixes de clé* sont les parties de début de clés. Vous pouvez regrouper logiquement un ensemble de clés en utilisant le même préfixe dans leurs noms. Préfixes peuvent contenir plusieurs composants connectés par un délimiteur, tel que `/`, similaire à un chemin d’URL, pour former un espace de noms. Ces hiérarchies sont utiles lorsque vous stockez des clés pour nombreuses applications, les services de composants et les environnements dans un magasin de Configuration de l’application.

Une chose importante à garder à l’esprit est que les clés sont référence par le code de votre application pour récupérer les valeurs des paramètres correspondants. Les clés ne doivent pas modifier, sans quoi vous devrez modifier votre code chaque fois que se produit.

*Étiquettes* sont un attribut sur les clés. Elles sont utilisées pour créer des variantes d’une clé. Par exemple, vous pouvez affecter des étiquettes à plusieurs versions d’une clé. Une version peut être une itération, un environnement ou autres informations contextuelles. Votre application peut demander un jeu totalement différent des valeurs de clé en spécifiant une autre étiquette. Par conséquent, toutes les références de clés restent inchangées dans votre code.

## <a name="key-value-compositions"></a>Compositions de clé-valeur

Configuration de l’application traite toutes les clés sont stockées avec lui en tant qu’entités indépendantes. Configuration de l’application ne tente pas de déduire une relation entre les clés ou pour l’héritage des valeurs de clé en fonction de leur hiérarchie. Vous pouvez agréger plusieurs jeux de clés, toutefois, à l’aide d’étiquettes couplées avec une configuration appropriée d’empilement dans votre code d’application.

Intéressons-nous à un exemple. Supposez que vous avez un paramètre nommé **Asset1**, dont la valeur peut varier selon l’environnement de développement. Vous créez une clé nommée « Asset1 » avec une étiquette vide et une étiquette nommée « Développement ». Dans la première étiquette, vous placez la valeur par défaut **Asset1**, et vous définissez une valeur spécifique pour « Développement » dans ce dernier.

Dans votre code, vous commencez par récupérer les valeurs de clé sans étiquettes, et ensuite vous récupérez le même ensemble de valeurs de clé, une deuxième fois avec l’étiquette « Développement ». Lorsque vous récupérez les valeurs de la deuxième fois, les valeurs précédentes des clés sont remplacées. Le système de configuration .NET Core vous permet de « empiler » plusieurs jeux de données de configuration sur chacun des autres. Si une clé existe dans plusieurs jeux, le dernier jeu qui le contient est utilisé. Avec une infrastructure de programmation moderne, tels que .NET Core, vous bénéficiez cette fonctionnalité empilement gratuitement si vous utilisez un fournisseur de configuration native pour accéder à la Configuration de l’application. L’extrait de code suivant montre comment vous pouvez implémenter l’empilement dans une application .NET Core :

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Amorçage de Configuration d’application

Pour accéder à un magasin de Configuration de l’application, vous pouvez utiliser sa chaîne de connexion, qui est disponible dans le portail Azure. Étant donné que les chaînes de connexion contiennent des informations d’identification, ils sont considérés comme des secrets. Ces secrets doivent être stockées dans Azure Key Vault, et votre code doit s’authentifier sur Key Vault et les récupérer.

Une meilleure option consiste à utiliser la fonctionnalité d’identités gérées dans Azure Active Directory. Avec des identités gérées, vous devez uniquement l’URL de point de terminaison de Configuration de l’application pour l’accès d’amorçage à votre magasin de Configuration de l’application. Vous pouvez incorporer l’URL dans votre code d’application (par exemple, dans le *appsettings.json* fichier). Consultez [intégrer avec Azure managed identités](howto-integrate-azure-managed-service-identity.md) pour plus d’informations.

## <a name="app-or-function-access-to-app-configuration"></a>Accès d’application ou une fonction à la Configuration de l’application

Vous pouvez fournir des accès à la Configuration de l’application pour les applications web ou des fonctions en utilisant l’une des méthodes suivantes :

* Via le portail Azure, entrez la chaîne de connexion à votre magasin de Configuration de l’application dans les paramètres d’Application d’App Service.
* Store de la chaîne de connexion à votre magasin de Configuration de l’application dans Key Vault et [référencez-le à partir d’App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Utilisez Azure managed identités pour accéder au magasin de Configuration de l’application. Pour plus d’informations, consultez [intégrer avec Azure managed identités](howto-integrate-azure-managed-service-identity.md).
* Envoyer la configuration à partir de la Configuration de l’application vers App Service. Configuration de l’application fournit une fonction d’exportation (dans le portail Azure et d’Azure CLI) qui envoie des données directement dans App Service. Avec cette méthode, vous n’avez pas besoin de modifier le code d’application du tout.

## <a name="next-steps"></a>Étapes suivantes

* [Clés et valeurs](./concept-key-value.md)
