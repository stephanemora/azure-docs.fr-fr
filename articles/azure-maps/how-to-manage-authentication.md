---
title: Gérer l’authentification dans Microsoft Azure Maps
titleSuffix: Azure Maps
description: Familiarisez-vous avec l’authentification Azure Maps. Découvrez l’approche qui convient le mieux à ce scénario. Découvrez comment utiliser le portail pour afficher les paramètres d’authentification.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/10/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
custom.ms: subject-rbac-steps
ms.openlocfilehash: d087daf38c455fd4d87ea571ff5f3a0ab9ad0527
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454774"
---
# <a name="manage-authentication-in-azure-maps"></a>Gérer l’authentification dans Azure Maps

Lorsque vous créez un compte Azure Maps, des clés et un ID client sont générés. Les clés et l’ID client sont utilisés pour prendre en charge l’authentification Azure Active Directory (Azure AD) et l’authentification par clé partagée.

## <a name="view-authentication-details"></a>Afficher les détails de l’authentification

 >[!IMPORTANT]
 >Nous vous recommandons d’utiliser la clé primaire comme clé d’abonnement quand vous [utilisez l’authentification par clé partagée pour appeler Azure Maps](./azure-maps-authentication.md#shared-key-authentication). Le mieux est d’utiliser la clé secondaire dans des scénarios tels que les changements de clés par roulement. Pour plus d’informations, consultez [Authentification dans Azure Maps](./azure-maps-authentication.md).

Pour visualiser vos détails d’authentification Azure Maps :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez au menu du portail Azure. Sélectionnez **Toutes les ressources**, puis votre compte Azure Maps.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/select-all-resources.png" alt-text="Sélection du compte Azure Maps":::

3. Sous **Paramètres** dans le volet de gauche, sélectionnez **Authentification**.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/view-authentication-keys.png" alt-text="Détails d’authentification":::

## <a name="choose-an-authentication-category"></a>Choisir une catégorie d’authentification

En fonction de vos besoins d’application, il existe des chemins spécifiques pour sécuriser l’application. Azure AD définit des catégories d’authentification spécifiques pour prendre en charge un large éventail de flux d’authentification. Pour choisir la meilleure catégorie pour votre application, consultez [Catégories d’applications](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories).

> [!NOTE]
> Même si vous utilisez l’authentification par clé partagée, comprendre les catégories et les scénarios vous aide à sécuriser l’application.

## <a name="choose-an-authentication-and-authorization-scenario"></a>Choisir un scénario d’authentification et d’autorisation

Le tableau suivant présente les scénarios d’authentification et d’autorisation courants dans Azure Maps. Utilisez les liens pour obtenir des informations détaillées sur la configuration de chaque scénario.

> [!IMPORTANT]
> Pour les applications de production, nous vous recommandons d’implémenter Azure AD avec le contrôle d’accès en fonction du rôle Azure (Azure RBAC).

| Scénario                                                                                    | Authentification | Autorisation | Effort de développement | Effort opérationnel |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Démon approuvé/application cliente non interactive](./how-to-secure-daemon-app.md)        | Clé partagée     | N/A           | Moyenne             | Élevé               |
| [Démon approuvé/application cliente non interactive](./how-to-secure-daemon-app.md)        | Azure AD       | Élevé          | Faible                | Moyenne             |
| [Application web à page unique avec authentification unique interactive](./how-to-secure-spa-users.md) | Azure AD       | Élevé          | Moyenne             | Moyenne             |
| [Application web à page unique avec authentification non interactive](./how-to-secure-spa-app.md)      | Azure AD       | Élevé          | Moyenne             | Moyenne             |
| [Application web avec authentification unique interactive](./how-to-secure-webapp-users.md)          | Azure AD       | Élevé          | Élevé               | Moyenne             |
| [Appareil IoT/appareil avec restriction d’entrée](./how-to-secure-device-code.md)                     | Azure AD       | Élevé          | Moyenne             | Moyenne             |

## <a name="view-built-in-azure-maps-role-definitions"></a>Afficher les définitions de rôle Azure Maps intégrées

Pour afficher les définitions de rôle Azure Maps intégrées :

1. Dans le volet de gauche, sélectionnez **Contrôle d’accès (IAM)** .

2. Sélectionnez l’onglet **Rôles**.

3. Dans la zone de recherche, entrez **Azure Maps**.

Les résultats affichent les définitions de rôle intégrées disponibles pour Azure Maps.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-role-definitions.png" alt-text="Affichage des définitions de rôle Azure Maps intégrées":::

## <a name="view-role-assignments"></a>Voir les attributions de rôles

Pour voir les applications et les utilisateurs auxquels l’accès à Azure Maps a été accordé, accédez à l’option **Contrôle d’accès (IAM)** . Ensuite, sélectionnez **Attributions de rôles**, puis filtrez sur **Azure Maps**.

1. Dans le volet de gauche, sélectionnez **Contrôle d’accès (IAM)** .

2. Sélectionnez l’onglet **Attributions de rôles**.

3. Dans la zone de recherche, entrez **Azure Maps**.

Les résultats affichent les attributions de rôles Azure Maps actuelles.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-amrbac.png" alt-text="Affichage des utilisateurs et des applications auxquels l’accès a été accordé":::

## <a name="request-tokens-for-azure-maps"></a>Demander des jetons pour Azure Maps

Demandez un jeton auprès du point de terminaison de jeton Azure AD. Dans votre demande Azure AD, utilisez les informations suivantes :

| Environnement Azure      | Point de terminaison de jeton Azure AD             | ID de ressource Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Cloud public Azure     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud Azure Government | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Pour en savoir plus sur les demandes de jetons d’accès à partir d’Azure AD pour les utilisateurs et les principaux de service, voir [Scénarios d’authentification pour Azure AD](../active-directory/develop/authentication-vs-authorization.md).  Pour voir des scénarios spécifiques, consultez le [tableau des scénarios](./how-to-manage-authentication.md#choose-an-authentication-and-authorization-scenario).

## <a name="manage-and-rotate-shared-keys"></a>Gérer des clés partagées et assurer leur rotation

Vos clés d’abonnement Azure Maps sont similaires à un mot de passe racine pour votre compte Azure Maps. Veillez toujours à protéger vos clés d’abonnement. Utilisez Azure Key Vault pour gérer et effectuer la rotation de vos clés en toute sécurité. Évitez de distribuer des clés d’accès à d’autres utilisateurs, de les coder en dur ou de les enregistrer en texte brut dans un emplacement accessible à d’autres personnes. Si vous pensez que vos clés peuvent avoir été compromises, effectuez une rotation.

> [!NOTE]
> Si possible, nous vous recommandons d’utiliser Azure AD à la place de la clé partagée pour autoriser les demandes. Azure AD offre une meilleure sécurité que la clé partagée, et il est plus facile à utiliser.

### <a name="manually-rotate-subscription-keys"></a>Opérer manuellement la rotation des clés d’abonnement

Pour maintenir la sécurité de votre compte Azure Maps, nous vous recommandons d’effectuer régulièrement une rotation de vos clés d’abonnement. Si possible, utilisez Azure Key Vault pour gérer vos clés d’accès. Si vous n’utilisez pas Key Vault, vous devrez procéder à une rotation manuelle de vos clés.

Deux clés d’abonnement sont attribuées pour vous permettre d’opérer une rotation. L’existence de deux clés garantit que votre application conserver l’accès à Azure Maps tout au long du processus.

Pour opérer la rotation de vos clés d’abonnement Azure Maps dans le Portail Azure :

1. Mettez à jour le code de votre application pour référencer la clé secondaire du compte Azure Maps, et déployez.
2. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte Azure Maps.
3. Sous **Paramètres**, sélectionnez **Authentication**.
4. Pour régénérer la clé primaire de votre compte Azure Maps, sélectionnez le bouton **Régénérer** en regard de la clé primaire.
5. Mettez à jour le code de votre application pour référencer la nouvelle clé primaire, et déployez.
6. Régénérez la clé d’accès secondaire de la même manière.

> [!WARNING]
> Nous vous recommandons d’utiliser uniquement l’une des clés dans toutes vos applications en même temps. Si vous utilisez la clé 1 dans certains emplacements et la clé 2 dans d’autres, vous ne pourrez opérer aucune rotation de vos clés sans que certaines applications perdent l’accès.

## <a name="next-steps"></a>Étapes suivantes

Recherchez les métriques d’utilisation de l’API pour votre compte Azure Maps :
> [!div class="nextstepaction"]
> [Afficher les métriques d’utilisation](how-to-view-api-usage.md)

Explorez des exemples qui montrent comment intégrer Azure AD à Azure Maps :

> [!div class="nextstepaction"]
> [Exemples d’authentification Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
