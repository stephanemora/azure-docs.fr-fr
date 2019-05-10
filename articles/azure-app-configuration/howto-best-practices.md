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
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413615"
---
# <a name="azure-app-configuration-best-practices"></a>Configuration des applications meilleures pratiques pour Azure

Cet article décrit les modèles et les pratiques courantes lors de l’utilisation de Configuration de l’application Azure.

## <a name="key-groupings"></a>Regroupements de clé

Configuration de l’application fournit deux options pour organiser des clés : la clé des préfixes ou des étiquettes. Vous pouvez utiliser une ou les deux.

Préfixes de clé sont les parties de début de clés. Vous pouvez regrouper logiquement un ensemble de clés en utilisant le même préfixe dans leurs noms. Préfixes peuvent contenir plusieurs composants interconnectés par un délimiteur tel que `/`, similaire à un chemin d’URL, pour former un espace de noms. Ces hiérarchies sont utiles lorsque vous stockez des clés pour nombreuses applications, les services de composants et les environnements dans une banque de Configuration de l’application. Une chose importante à garder à l’esprit est que les clés sont référence par le code de votre application pour récupérer les valeurs des paramètres correspondants. Une clé ne doit pas modifier, faute de quoi vous devrez modifier le code chaque fois que se produit.

Étiquettes est un attribut sur les clés. Ils sont utilisés pour créer des variantes d’une clé. Par exemple, vous pouvez affecter des étiquettes à plusieurs versions d’une clé. Une version peut être une itération, environnement ou autres informations contextuelles. Votre application peut demander un jeu totalement différent des valeurs de clé en spécifiant une autre étiquette simplement. Toutes les références de clés restent inchangés.

## <a name="key-value-compositions"></a>Compositions de clé-valeur

Configuration de l’application traite toutes les clés sont stockées avec lui en tant qu’entités indépendantes. Il ne tente pas de déduire une relation entre les clés ou hériter des valeurs de clé en fonction de leur hiérarchie. Vous pouvez agréger plusieurs jeux de clés, toutefois, à l’aide d’étiquettes couplée avec une configuration appropriée d’empilement dans votre code d’application.

Intéressons-nous à un exemple. Vous avez un paramètre **Asset1** dont la valeur peut varier pour l’environnement de « Développement ». Vous pouvez créer une clé nommée « Asset1 » avec une étiquette vide et une étiquette appelée « Développement ». Vous placez la valeur par défaut **Asset1** dans l’ancien et toute valeur spécifique pour « Développement » dans ce dernier. Dans votre code, vous commencez par récupérer les valeurs de clé sans une étiquette, puis ceux avec une étiquette de « Développement » pour remplacer toutes les valeurs précédentes des mêmes clés. Si vous utilisez une infrastructure de programmation moderne tels que .NET Core, vous pouvez obtenir cette fonctionnalité empilement gratuitement si vous utilisez un fournisseur de configuration native pour accéder à la Configuration de l’application. L’extrait de code suivant montre comment vous pouvez implémenter l’empilement dans une application .NET Core.

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

Pour accéder à un magasin de Configuration de l’application, vous pouvez utiliser sa chaîne de connexion, qui est disponible dans le portail Azure. Chaînes de connexion contiennent des informations d’identification et sont considérés comme des secrets. Ils doivent être stockés dans un coffre de clés. Une meilleure option consiste à utiliser Azure identité gérée. Avec cette méthode, vous devez uniquement les URL de point de terminaison de Configuration de l’application d’amorçage d’accès à votre magasin de configuration. Vous pouvez incorporer l’URL dans votre code d’application (par exemple, dans le *appsettings.json* fichier). Consultez [intégrer avec Azure managed identités](howto-integrate-azure-managed-service-identity.md) pour plus d’informations.

## <a name="web-app-or-function-access-to-app-configuration"></a>Application Web ou la fonction accès à la Configuration de l’application

Vous pouvez entrer la chaîne de connexion à votre magasin de Configuration de l’application dans les paramètres d’Application d’App Service via le portail Azure. Vous pouvez également le stocker dans Key Vault et [référencez-le à partir d’App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). Vous pouvez également utiliser Azure géré d’identité pour accéder au magasin de configuration. Consultez [intégrer avec Azure managed identités](howto-integrate-azure-managed-service-identity.md) pour plus d’informations.

Vous pouvez également transmettre configuration à partir de la Configuration de l’application vers App Service. Configuration de l’application fournit une fonction d’exportation (dans le portail Azure et CLI) qui envoie des données directement dans App Service. Avec cette méthode, vous n’avez pas besoin de modifier le code d’application à tout.

## <a name="next-steps"></a>Étapes suivantes

* [Clés et valeurs](./concept-key-value.md)
