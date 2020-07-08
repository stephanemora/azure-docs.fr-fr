---
title: Personnaliser l’interface utilisateur de votre application avec une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Apprenez à personnaliser une interface utilisateur à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb833ff35dae4fe1c0c27204ec66fa6b4cdb82c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388882"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En effectuant les étapes de cet article, vous créez une stratégie personnalisée d’inscription et de connexion avec votre marque et votre apparence. Avec Azure Active Directory B2C (Azure AD B2C), vous contrôlerez presque entièrement le contenu HTML et CSS présenté aux utilisateurs. Lorsque vous utilisez une stratégie personnalisée, vous configurez la personnalisation de l’interface utilisateur dans le code XML au lieu d’utiliser des contrôles dans le portail Azure.

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md). Vous devez disposer d’une stratégie personnalisée fonctionnelle pour l’inscription et la connexion avec des comptes locaux.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Modifier le fichier d’extensions

Pour configurer la personnalisation de l’interface utilisateur, copiez l’élément **ContentDefinition** et ses éléments enfants du fichier de base vers le fichier d’extension.

1. Ouvrez le fichier de base de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em>. Ce fichier de base est un des fichiers de stratégie inclus dans le pack de démarrage des stratégies personnalisées, que vous avez dû obtenir en suivant la rubrique prérequise [Bien démarrer avec les stratégies personnalisées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Recherchez et copiez le contenu entier de l’élément **ContentDefinitions**.
1. Ouvrez le fichier d’extension. Par exemple, *TrustFrameworkExtensions.xml*. Recherchez l’élément **BuildingBlocks**. Si l’élément n’existe pas, ajoutez-le.
1. Collez le contenu entier de l’élément **ContentDefinitions** que vous avez copié en tant qu’enfant de l’élément **BuildingBlocks**.
1. Recherchez l’élément **ContentDefinition** contenant `Id="api.signuporsignin"` dans le code XML que vous avez copié.
1. Remplacez la valeur de **LoadUri** par l’URL du fichier HTML que vous avez chargé dans le stockage. Par exemple : `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Votre stratégie personnalisée doit se présenter comme l’extrait de code suivant :

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Enregistrez le fichier d’extensions.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Téléchargez et testez votre stratégie personnalisée mise à jour

#### <a name="51-upload-the-custom-policy"></a>5.1 Charger la stratégie personnalisée

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Charger une stratégie personnalisée**.
1. Chargez le fichier d’extensions que vous avez modifié précédemment.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Tester la stratégie personnalisée en utilisant **Exécuter maintenant**

1. Sélectionnez la stratégie que vous avez téléchargée, puis sélectionnez **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire avec une adresse e-mail.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurer l’URI du contenu de la page personnalisée dynamique

En utilisant des stratégies personnalisées Azure AD B2C, vous pouvez envoyer un paramètre dans le chemin de l’URL ou une chaîne de requête. En transmettant le paramètre à votre point de terminaison HTML, vous pouvez changer le contenu de la page de façon dynamique. Par exemple, vous pouvez changer l’image d’arrière-plan dans la page de connexion ou d’inscription Azure AD B2C en fonction d’un paramètre que vous transmettez depuis votre application web ou mobile. Le paramètre peut être n’importe quel [résolveur de revendications](claim-resolver-overview.md), comme l’ID d’application, l’ID de langue ou le paramètre de chaîne de requête personnalisée, comme `campaignId`.

### <a name="sending-query-string-parameters"></a>Envoi des paramètres de chaîne de requête

Pour envoyer des paramètres de chaîne de requête, dans la [stratégie de partie de confiance](relyingparty.md), ajoutez un élément `ContentDefinitionParameters` comme indiqué ci-dessous.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Dans votre définition de contenu, remplacez la valeur de `LoadUri` par `https://<app_name>.azurewebsites.net/home/unified`. La `ContentDefinition` de votre stratégie personnalisée doit se présenter comme l’extrait de code suivant :

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quand Azure AD B2C charge la page, il fait un appel au point de terminaison de votre serveur web :

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI du contenu d’une page dynamique

Le contenu peut être extrait de différents emplacements en fonction des paramètres utilisés. Dans votre point de terminaison activé pour CORS, configurez une structure de dossiers pour héberger le contenu. Par exemple, vous pouvez organiser le contenu selon la structure suivante. *Dossier racine/dossier par langue/vos fichiers html*. Par exemple, votre URI de page personnalisée peut ressembler à ceci :

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C envoie le code ISO à deux lettres pour la langue, `fr` pour le français :

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les éléments d’interface utilisateur qui peuvent être personnalisés, consultez le [guide de référence de la personnalisation d’interface utilisateur pour les flux utilisateur](customize-ui-overview.md).
