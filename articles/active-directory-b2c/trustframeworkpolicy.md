---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: Spécifiez l’élément TrustFrameworkPolicy d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c964a7bde0b7db9357c73fc79d2df3170075fcc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186384"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Une stratégie personnalisée est représentée par un ou plusieurs fichiers au format XML qui se font mutuellement référence dans une chaîne hiérarchique. Les éléments XML définissent des éléments de la stratégie tels que les schéma de revendications, transformations de revendications, définitions de contenu, fournisseurs de revendications, profils techniques, parcours utilisateur et étapes d’orchestration. Chaque fichier de stratégie est défini dans l’élément **TrustFrameworkPolicy** de niveau supérieur d’un fichier de stratégie.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


L’élément **TrustFrameworkPolicy** contient les attributs suivants :

| Attribut | Obligatoire | Description |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Oui | Version du schéma à utiliser pour exécuter la stratégie. La valeur doit être `0.3.0.0`. |
| TenantObjectId | Non | Identificateur d’objet unique du locataire Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Oui | Identificateur unique du locataire auquel appartient cette stratégie. |
| PolicyId | Oui | Identificateur unique de la stratégie. Cet identificateur doit avoir le préfixe *B2C_1A_* . |
| PublicPolicyUri | Oui | URI de la stratégie, qui est une combinaison de l’ID de locataire et de l’ID de stratégie. |
| DeploymentMode | Non | Valeurs possibles : `Production` ou `Development`. `Production` est la valeur par défaut. Cette propriété permet de déboguer votre stratégie. Pour plus d’informations, voir [Collecte de journaux d’activité](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Non | Point de terminaison utilisé quand **DeploymentMode** est défini sur `Development`. La valeur doit être `urn:journeyrecorder:applicationinsights`. Pour plus d’informations, voir [Collecte de journaux d’activité](troubleshoot-with-application-insights.md). |


L’exemple suivant montre comment spécifier l’élément **TrustFrameworkPolicy** :

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Modèle d’héritage

Les types de fichiers de stratégie suivants sont généralement utilisés dans un parcours utilisateur :

- Fichier **de base** contenant la plupart des définitions. Pour faciliter la résolution des problèmes et la maintenance à long terme de vos stratégies, il est recommandé d’éviter autant que possible de modifier ce fichier.
- Fichier d’**extensions** contenant les modifications de configuration propres à votre locataire. Ce fichier de stratégie est dérivé du fichier de base. Utilisez-le pour ajouter de nouvelles fonctionnalités ou remplacer des fonctionnalités existantes. Par exemple, utilisez-le pour fédérer avec de nouveaux fournisseurs d’identité.
- Fichier de **partie de confiance** qui est le fichier centré sur une tâche unique, qui est appelé directement par une application par partie de confiance, telle que vos applications web, mobiles ou de bureau. Chaque tâche unique telle qu’une inscription ou une connexion, une réinitialisation de mot de passe ou une modification du profil, nécessite son propre fichier de stratégie de partie de confiance. Ce fichier de stratégie est dérivé du fichier d’extensions.

Une application par partie de confiance appelle le fichier de stratégie de partie de confiance pour exécuter une tâche spécifique. Par exemple, pour démarrer le flux de connexion. L’Infrastructure d’expérience d’identité dans Azure AD B2C ajoute tous les éléments du fichier de base, puis du fichier d’extensions, et enfin du fichier de stratégie de partie de confiance pour assembler la stratégie en vigueur. Les éléments du même type et du même nom dans le fichier de partie de confiance remplacent ceux du fichier d’extensions qui remplacent ceux du fichier de base. Le diagramme suivant montre la relation entre les fichiers de stratégie et les applications par partie de confiance.

![Diagramme indiquant le modèle d’héritage de stratégie d’infrastructure de confiance](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Le modèle d’héritage est le suivant :

- Les stratégies parente et enfant ont le même schéma.
- La stratégie enfant peut hériter, à tous les niveaux, de la stratégie parente et l’étendre en ajoutant de nouveaux éléments.
- Le nombre de niveaux n’est pas limité.

Pour plus d’informations, consultez [Bien démarrer avec les stratégies personnalisées](custom-policy-get-started.md).

## <a name="base-policy"></a>Stratégie de base

Pour hériter d’une autre stratégie, un élément **BasePolicy** doit être déclaré sous l’élément **TrustFrameworkPolicy** du fichier de stratégie. L’élément **BasePolicy** est une référence à la stratégie de base dont cette stratégie est dérivée.

L’élément **BasePolicy** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identificateur de votre locataire Azure AD B2C. |
| PolicyId | 1:1 | Identificateur de la stratégie parente. |


L’exemple suivant montre comment spécifier une stratégie de base. Cette stratégie **B2C_1A_TrustFrameworkExtensions** est dérivée de la stratégie **B2C_1A_TrustFrameworkBase**.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Exécution de stratégie

Une application par partie de confiance, telle qu’une application web, mobile ou de bureau, appelle la [stratégie de partie de confiance](relyingparty.md). Le fichier de stratégie de partie de confiance exécute une tâche spécifique, telle qu’une connexion, une réinitialisation de mot de passe ou une modification de profil. La stratégie de partie de confiance configure la liste des revendications que l’application par partie de confiance reçoit dans le jeton émis. Plusieurs applications peuvent utiliser la même stratégie. Toutes les applications reçoivent le même jeton contenant des revendications, et l’utilisateur suit le même parcours utilisateur. Une application unique peut utiliser plusieurs stratégies.

Dans le fichier de stratégie de partie de confiance, vous spécifiez l’élément **DefaultUserJourney** qui pointe vers le [UserJourney](userjourneys.md). Le parcours utilisateur est généralement défini dans le fichier de stratégie de base ou d’extensions.

Stratégie B2C_1A_signup_signin :

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase ou B2C_1A_TrustFrameworkExtensionPolicy :

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Un parcours utilisateur définit la logique métier des étapes qu’un utilisateur suit. Chaque parcours utilisateur est un ensemble d’étapes d’orchestration qui effectuent une série d’actions, de façon séquentielle, en termes d’authentification et de collecte d’informations.

Le fichier de stratégie **SocialAndLocalAccounts** dans le [pack de démarrage](custom-policy-get-started.md#custom-policy-starter-pack) contient les parcours utilisateur SignUpOrSignIn, ProfileEdit et PasswordReset. Vous pouvez ajouter des parcours utilisateur pour d’autres scénarios, tels que la modification d’une adresse e-mail ou l’association et la dissociation d’un compte de réseau social.

Les étapes d’orchestration peuvent appeler un [profil technique](technicalprofiles.md). Un profil technique fournit une infrastructure avec un mécanisme intégré pour communiquer avec différents types de parties. Par exemple, un profil technique peut effectuer, entre autres, les actions suivantes :

- Afficher une expérience utilisateur.
- Autoriser des utilisateurs à se connecter avec un compte de réseau social ou d’entreprise, tel qu’un compte Facebook, Microsoft, Google, Salesforce ou de tout autre fournisseur d’identité.
- Configurer une vérification téléphonique pour une authentification multifacteur.
- Lire et écrire des données dans un magasin d’identités Azure AD B2C.
- Appeler un service d’API Restful personnalisé.

![Diagramme indiquant le workflow d’exécution de la stratégie](./media/trustframeworkpolicy/custom-policy-execution.png)

 L’élément **TrustFrameworkPolicy** contient les éléments suivants :

- BasePolicy tel que spécifié ci-dessus
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
