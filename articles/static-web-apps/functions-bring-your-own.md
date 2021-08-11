---
title: Apporter vos propres fonctions à Azure Static Web Apps
description: Utilisez une application Azure Functions existante avec votre site Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/07/2021
ms.author: cshoe
ms.openlocfilehash: 6c56a9dd3a39f26fe4f050114145e0ebcbc083b2
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556133"
---
# <a name="bring-your-own-functions-to-azure-static-web-apps"></a>Apporter vos propres fonctions à Azure Static Web Apps

Les API Azure Static Web Apps sont prises en charge par deux configurations possibles : les fonctions managées et « Apportez vos propres fonctions ». Pour plus d’informations sur les deux configurations, consultez la [référence de l’API](apis.md).

Cet article montre comment lier une application Azure Functions existante à une ressource Azure Static Web Apps.

> [!NOTE]
> « Apportez vos propres fonctions » n’est disponible que dans le plan Standard d’Azure Static Web Apps.

## <a name="example"></a>Exemple

Prenons l’exemple d’une application Azure Functions existante qui expose un point de terminaison via l’emplacement suivant.

```url
https://my-functions-app.azurewebsites.net/api/getProducts
```

Une fois la liaison établie, vous pouvez accéder à ce même point de terminaison via le chemin d’accès `api` à partir de votre application web statique, comme indiqué dans cet exemple d’URL.

```url
https://red-sea-123.azurestaticapps.net/api/getProducts
```

 Les deux URL de point de terminaison pointent vers la même fonction.

## <a name="link-an-existing-azure-functions-app"></a>Lier une application Azure Functions existante

Avant d’associer une application Azure Functions existante, vous devez d’abord ajuster la configuration de votre application web statique.

1. Définissez la valeur `api_location` sur une chaîne vide (`""`) dans le fichier de [configuration du flux de travail](./github-actions-workflow.md).

1. Ouvrez votre instance Static Web Apps dans le [portail Azure](https://portal.azure.com).

1. Dans le menu _Paramètres_, sélectionnez **Fonctions**.

1. Dans la liste déroulante _Environnement_, sélectionnez **Production**.

1. À côté de l’étiquette _Source des fonctions_, sélectionnez **Lier à une application de fonction**.

1. Dans la liste déroulant _Abonnement_, sélectionnez le nome de votre abonnement Azure.

1. Dans la liste déroulante _Application de fonction_, sélectionnez le nom de l’application Azure Functions existante que vous souhaitez lier à votre application web statique.

1. Sélectionnez le bouton **Lier**.

    :::image type="content" source="media/functions-bring-your-own/azure-static-web-apps-link-existing-functions-app.png" alt-text="Lier une application Azure Functions existante":::

> [!IMPORTANT]
> Veillez à définir la valeur `api_location` sur une chaîne vide (`""`) dans le fichier de [configuration du flux de travail](./github-actions-workflow.md) avant de lier une application Azure Functions existante.

## <a name="deployment"></a>Déploiement

Vous êtes responsable de la configuration d’un [flux de travail de déploiement](../azure-functions/functions-deployment-technologies.md) pour votre application Azure Functions.

## <a name="security-constraints"></a>Contraintes de sécurité

- **Authentification et autorisation :** Si les stratégies d’authentification et d’autorisation ne sont pas déjà configurées sur votre application Azure Functions existante, l’application web statique dispose d’un accès exclusif à l’API. Pour rendre votre application Azure Functions accessible à d’autres applications, ajoutez un autre fournisseur d’identité ou modifiez les paramètres de sécurité afin d’autoriser l’accès non authentifié.

  > [!NOTE]
  > Si vous activez l’authentification et l’autorisation dans votre application Azure Functions liée, elle doit utiliser le fournisseur d’authentification et d’autorisation Azure App Service version 2.

- **Accessibilité publique obligatoire :** Une application Azure Functions existante ne doit pas appliquer les configurations de sécurité suivantes.
  - Restriction de l’adresse IP de l’application Azure Functions.
  - Restriction du trafic via une liaison privée ou des points de terminaison de service.

- **Clés d’accès aux fonctions :** Si votre fonction requiert une [clé d’accès](../azure-functions/security-concepts.md#function-access-keys), vous devez fournir la clé avec les appels de l’application statique à l’API.

## <a name="restrictions"></a>Restrictions

- Une seule application Azure Functions est disponible pour une seule application web statique.
- La valeur `api_location` dans la [configuration du flux de travail](./github-actions-workflow.md) doit être définie sur une chaîne vide.
- Prise en charge uniquement dans l’environnement de production d’Azure Static Web Apps.
- Bien que votre application Azure Functions puisse répondre à différents déclencheurs, l’application web statique peut uniquement accéder aux fonctions via des points de terminaison HTTP.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
