---
title: Identités managées dans Azure SignalR Service
description: Découvrez comment fonctionnent les identités managées dans Azure SignalR Service et comment utiliser une identité managée dans les scénarios serverless.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731582"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Identités managées pour Azure SignalR Service

Cet article explique comment créer une identité managée pour le service Azure SignalR Service et comment l’utiliser dans les scénarios serverless.

> [!Important] 
> Azure SignalR Service ne peut prendre en charge qu’une seule identité managée. Cela signifie que vous pouvez ajouter soit une identité affectée par le système, soit une identité affectée par l’utilisateur. 

## <a name="add-a-system-assigned-identity"></a>Ajouter une identité affectée par le système

Pour configurer une identité managée dans le portail Azure, vous allez d’abord créer une instance Azure SignalR Service, puis activer la fonctionnalité.

1. Créez une instance Azure SignalR Service dans le portail comme vous le faites en temps normal. Accédez-y dans le portail.

2. Sélectionnez **Identité**.

4. Dans l’onglet **Attribuée par le système**, définissez **État** sur **Activé**. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Ajouter une identité attribuée par le système dans le portail":::

## <a name="add-a-user-assigned-identity"></a>Ajouter une identité attribuée par l’utilisateur

La création d’une instance Azure SignalR Service avec une identité attribuée par l’utilisateur nécessite la création de l’identité, puis l’ajout de son identificateur de ressource à votre service.

1. Créez une ressource d’identité managée attribuée par l’utilisateur en suivant [ces instructions](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Créez une instance Azure SignalR Service dans le portail comme vous le faites en temps normal. Accédez-y dans le portail.

3. Sélectionnez **Identité**.

4. Dans l’onglet **Attribuée par l’utilisateur**, sélectionnez **Ajouter**.

5. Recherchez l’identité que vous avez créée précédemment et sélectionnez-la. Sélectionnez **Ajouter**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Ajouter une identité attribuée par l’utilisateur dans le portail":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Utilisation d’une identité managée dans les scénarios serverless

Azure SignalR Service est un service entièrement managé et vous ne pouvez donc pas utiliser d’identité managée pour obtenir les jetons manuellement. Au lieu de cela, Azure SignalR Service utilise l’identité managée que vous avez définie pour obtenir un jeton d’accès. Le service définit ensuite le jeton d’accès dans un en-tête de `Authorization` dans une requête en amont dans les scénarios serverless.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Activer l’authentification de l’identité managée dans les paramètres en amont

1. Ajoutez une identité affectée par le système ou une identité affectée par l’utilisateur.

2. Ajoutez un paramètre en amont et cliquez sur un astérisque pour accéder à une page détaillée comme illustré ci-dessous.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pre-msi-setting":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. Dans les paramètres d’authentification de l’identité managée, pour dans **Ressource**, vous pouvez spécifier la ressource cible. La ressource devient une revendication `aud` dans le jeton d’accès obtenu, qui peut être utilisé dans le cadre de la validation dans vos points de terminaison en amont. La ressource peut prendre l’une des valeurs suivantes :
    - Vide
    - ID d’application (client) du principal de service
    - URI de l’ID d’application du principal de service
    - [ID de ressource d’un service Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Si vous validez un jeton d’accès par vous-même dans votre service, vous pouvez choisir un des formats de ressources. Assurez-vous simplement que la valeur **Ressource** dans les paramètres d’**authentification** et que la validation est cohérente. Si vous utilisez le Contrôle d'accès en fonction du rôle Azure (Azure RBAC) pour un plan de données, vous devez utiliser la ressource demandée par le fournisseur de services.

### <a name="validate-access-tokens"></a>Validation des jetons d’accès

Le jeton dans l’en-tête `Authorization` est un [jeton d’accès à la plateforme d’identité Microsoft](../active-directory/develop/access-tokens.md#validating-tokens).

Afin de valider les jetons d’accès, votre application doit également valider l’audience et les jetons de signature. Ces éléments doivent être validés d’après les valeurs du document de découverte OpenID. Par exemple, consultez la [version indépendante du client du document](https://login.microsoftonline.com/common/.well-known/openid-configuration).

L’intergiciel Azure Active Directory (Azure AD) intègre des fonctionnalités permettant de valider les jetons d’accès. Vous pouvez parcourir nos [exemples](../active-directory/develop/sample-v2-code.md) pour en trouver un dans le langage de votre choix.

Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer la validation des jetons. Il existe également plusieurs bibliothèques de partenaire open source disponibles pour la validation de JSON Web Token (JWT). Il existe au moins une option pour la plupart des plateformes et des langues. Pour plus d’informations sur les exemples de code et les bibliothèques d’authentification d’Azure AD, consultez [Bibliothèques d’authentification de plateforme d’identité Microsoft](../active-directory/develop/reference-v2-libraries.md).

#### <a name="authentication-in-function-app"></a>Authentification dans application de fonction

La définition de la validation de jeton d’accès dans une application de fonction est facile et efficace sans que le code fonctionne.

1. Dans la page **Authentification / Autorisation**, basculez **Authentification App Service** sur **Activée**.

2. Dans **Mesure à prendre quand une demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

3. Dans Fournisseur d’authentification, cliquez sur **Azure Active Directory**

4. Dans la nouvelle page. Sélectionnez **Express**, **Créer une application AD**, puis cliquez sur **OK** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="Fonction Aad":::

5. Accédez à SignalR Service, puis suivez les [étapes](howto-use-managed-identity.md#add-a-system-assigned-identity) pour ajouter une identité affectée par le système ou une identité affectée par l’utilisateur.

6. Dans SignalR Service, accédez à **Paramètres en amont**, puis choisissez **Utiliser une identité gérée** et **Sélectionner des applications existantes**. Sélectionnez l’application que vous avez créée.

Après ce paramétrage, l’application de fonction rejette les demandes sans jeton d’accès dans l’en-tête.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Utiliser une identité managée pour une référence Azure Key Vault

SignalR Service peut accéder à Azure Key Vault pour recevoir un secret en utilisant l’identité managée.

1. Ajoutez une identité affectée par le système ou une identité affectée par l’utilisateur pour Azure SignalR Service.

2. Accordez une autorisation de lecture de secret pour l’identité managée dans les stratégies d’accès du Key Vault. Consultez [Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure](../key-vault/general/assign-access-policy-portal.md)

Actuellement, cette fonctionnalité peut être utilisée dans les scénarios suivants :

- [Référencer un secret dans un modèle d’URL en amont](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>Étapes suivantes

- [Développement et configuration Azure Functions avec Azure SignalR Service](signalr-concept-serverless-development-config.md)