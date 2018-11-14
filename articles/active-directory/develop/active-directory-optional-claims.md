---
title: Découvrez comment fournir des revendications facultatives à votre application Azure AD | Microsoft Docs
description: Guide pour l’ajout de revendications personnalisées ou supplémentaires aux jetons web JSON (JWT) et SAML 2.0 émis par Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: dcc27992c318a970a86f1ff5c60723daeef881b6
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914649"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app-public-preview"></a>Comment : fournir des revendications facultatives à votre application Azure AD (Préversion publique) ?

Cette fonctionnalité est utilisée par les développeurs d’applications pour spécifier les revendications qu’ils souhaitent inclure dans les jetons envoyés à leur application. Vous pouvez utiliser des revendications facultatives pour :
- Sélectionner des revendications supplémentaires à inclure dans les jetons pour votre application.
- Modifier le comportement de certaines revendications retournées par Azure AD dans les jetons.
- Ajouter et accéder à des revendications personnalisées pour votre application. 

> [!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique. Soyez prêt à rétablir ou à supprimer les modifications. La fonctionnalité est disponible dans n’importe quel abonnement Azure AD tant que la préversion publique est utilisée. Toutefois, quand la fonctionnalité sera généralement disponible, il se peut que certains de ses aspects nécessitent un abonnement Azure AD Premium.

Pour obtenir la liste des revendications standard et découvrir comment elles sont utilisées dans les jetons, consultez [cette présentation des jetons émis par Azure AD](v1-id-and-access-tokens.md). 

L’un des objectifs du point de terminaison [v2.0 Azure AD](active-directory-appmodel-v2-overview.md) est de réduire la taille des jetons afin de garantir des performances optimales par les clients. Ainsi, plusieurs revendications précédemment incluses dans les jetons d’accès et d’ID ne sont plus présentes dans les jetons v2.0 et doivent être demandées spécifiquement pour chaque application.

**Tableau 1 : Applicabilité**

| Type de compte | Point de terminaison V1.0 | Point de terminaison V2.0  |
|--------------|---------------|----------------|
| Compte Microsoft personnel  | N/A - Des tickets RPS sont utilisés à la place | Bientôt pris en charge |
| Compte Azure AD          | Pris en charge                          | Pris en charge avec des mises en garde      |

> [!IMPORTANT]
> Les applications qui prennent en charge à la fois les comptes personnels et Azure AD (enregistrées via le [portail d’inscription des applications](https://apps.dev.microsoft.com)) ne peuvent pas utiliser de revendications facultatives. Cependant, les applications enregistrées uniquement pour Azure AD à l’aide du point de terminaison v2.0 peuvent obtenir les revendications facultatives requises dans le manifeste. Dans le portail Azure, vous pouvez utiliser l’éditeur de manifeste d’application dans l’espace **Inscriptions d'applications** existant pour modifier vos revendications facultatives. Toutefois, cette fonctionnalité n’est pas encore disponible avec l’éditeur de manifeste d’application dans le nouvel espace **Inscriptions d’applications**.

## <a name="standard-optional-claims-set"></a>Ensemble de revendications facultatives standard

L’ensemble de revendications facultatives disponible par défaut pour les applications est répertorié ci-dessous. Pour ajouter des revendications personnalisées facultatives pour votre application, consultez [Extensions d’annuaire](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions) ci-dessous. Notez qu’au moment d’ajouter des revendications au **jeton d’accès**, cela s’appliquera aux jetons d’accès demandés *pour* l’application (une API web), et non à ceux demandés *par* l’application. Cela garantit que, quelque soit le client qui accède à votre API, les données correctes seront sur le jeton d’accès qu’il utilise pour s’authentifier auprès de votre API.

> [!NOTE]
> La plupart de ces revendications peuvent figurer dans les jetons JWT pour les jetons v1.0 et v2.0, mais pas dans les jetons SAML, sauf indication contraire dans la colonne Type de jeton. En outre, bien que les revendications facultatives soient actuellement prises en charge uniquement pour les utilisateurs AAD, la prise en charge du compte de service administré (MSA) sera bientôt ajoutée. Quand le compte MSA prendra en charge les revendications facultatives sur le point de terminaison v2.0, la colonne Type d’utilisateur indiquera si une revendication est disponible pour un utilisateur AAD ou MSA. 

**Tableau 2 : Ensemble de revendications facultatives standard**

| NOM                        | Description   | Type de jeton | Type d’utilisateur | Notes  |
|-----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Heure de dernière authentification de l’utilisateur. Voir les spécifications OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Région du locataire de ressource. | JWT        |           | |
| `signin_state`             | Revendication d’état de connexion.   | JWT        |           | Six valeurs de retour, comme indiqué :<br> « dvc_mngd » : l’appareil est géré.<br> « dvc_cmp » : l’appareil est conforme.<br> « dvc_dmjd » : l’appareil est joint à un domaine.<br> « dvc_mngd_app » : l’appareil est géré par le biais de la Gestion des appareils mobiles.<br> « inknownntwk » : l’appareil est à l’intérieur d’un réseau connu.<br> « kmsi » : Maintenir la connexion a été utilisé. <br> |
| `controls`                 | Revendication à valeurs multiples contenant les contrôles de session appliqués par les stratégies d’accès conditionnel. | JWT        |           | Trois valeurs :<br> « app_res » : l’application doit appliquer des restrictions plus granulaires. <br> « ca_enf » : l’application de l’accès conditionnel a été différée et est toujours nécessaire. <br> « type no_cookie » : ce jeton est insuffisant pour l’échange d’un cookie dans le navigateur. <br>  |
| `home_oid`                 | Pour les utilisateurs invités, il s’agit de l’ID d’objet de l’utilisateur dans le locataire de base de l’utilisateur.| JWT        |           | |
| `sid`                      | ID de session utilisé pour la déconnexion utilisateur pour chaque session. | JWT        |           |         |
| `platf`                    | Plateforme d’appareil.    | JWT        |           | Limité aux appareils gérés qui peuvent vérifier le type d’appareil.|
| `verified_primary_email`   | Obtenu à partir du PrimaryAuthoritativeEmail de l’utilisateur.      | JWT        |           |         |
| `verified_secondary_email` | Obtenu à partir du SecondaryAuthoritativeEmail de l’utilisateur.   | JWT        |           |        |
| `enfpolids`                | ID des stratégies appliquées. Liste des ID des stratégies qui ont été évaluées pour l’utilisateur actuel. | JWT |  |  |
| `vnet`                     | Informations sur le spécificateur de réseau virtuel. | JWT        |           |      |
| `fwd`                      | Adresse IP.| JWT    |   | Ajoute l’adresse IPv4 d’origine du client demandeur (quand il se trouve sur un réseau virtuel). |
| `ctry`                     | Pays de l’utilisateur. | JWT |           | Azure AD retourne la revendication facultative `ctry` si elle est présente. La valeur de la revendication est un code de pays à deux lettres standard, tel que FR, JP, SZ, etc. |
| `tenant_ctry`              | Pays du locataire de ressource. | JWT | | |
| `xms_pdl`          | Emplacement de données par défaut   | JWT | | Pour les locataires multirégions, il s’agit du code à trois lettres qui indique à quelle région géographique appartient l’utilisateur. Pour plus d’informations, consultez la [documentation Azure AD Connect sur l’emplacement des données par défaut](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation). <br> Par exemple : `APC` pour l’Asie-Pacifique. |
| `xms_pl`                   | Langue par défaut de l’utilisateur  | JWT ||La langue par défaut de l’utilisateur, si celle-ci a été définie. Dans les scénarios d’accès invité, provient du locataire de base. Au format langue-pays (« fr-fr »). |
| `xms_tpl`                  | Langue par défaut du locataire| JWT | | Langue par défaut du locataire de la ressource, si celle-ci est définie. Au format langue (« fr »). |
| `ztdid`                    | ID de déploiement sans intervention | JWT | | Identité d’appareil utilisée pour [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `acct`             | Statut du compte utilisateur dans le client. | JWT, SAML | | Si l’utilisateur est membre du client, la valeur est `0`. S’il est un invité, la valeur est `1`. |
| `upn`                      | Revendication UserPrincipalName. | JWT, SAML  |           | Bien que cette revendication soit incluse automatiquement, vous pouvez la spécifier en tant que revendication facultative pour attacher des propriétés supplémentaires afin de modifier son comportement en cas d’utilisateur invité. <br> Propriétés supplémentaires : <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash` |

### <a name="v20-optional-claims"></a>Revendications facultatives v2.0

Ces revendications sont toujours incluses dans les jetons v1.0, mais elles ne sont pas incluses dans les jetons v2.0, sauf demande contraire. Ces revendications s’appliquent uniquement aux jetons web JSON (jetons d’ID et jetons d’accès). 

**Tableau 3 : Revendications facultatives propres à V2.0**

| Revendication JWT     | NOM                            | Description                                | Notes |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | Adresse IP                      | Adresse IP à partir de laquelle le client s’est connecté.   |       |
| `onprem_sid`  | Identificateur de sécurité local |                                             |       |
| `pwd_exp`     | Heure d’expiration du mot de passe        | Date et heure d’expiration du mot de passe. |       |
| `pwd_url`     | Modifier l’URL de mot de passe             | URL à laquelle l’utilisateur peut accéder pour modifier son mot de passe.   |       |
| `in_corp`     | Dans le périmètre du réseau d’entreprise        | Indique si le client se connecte à partir du réseau d’entreprise. Si ce n’est pas le cas, la revendication n’est pas incluse.   |       |
| `nickname`    | Surnom                        | Autre nom de l’utilisateur, distinct du nom de famille et du prénom. |       |                                                                                                                |       |
| `family_name` | Nom                       | Fournit le nom de famille de lutilisateur tel que défini dans l’objet utilisateur Azure AD. <br>"family_name":"Miller" |       |
| `given_name`  | Prénom                      | Fournit le prénom de l’utilisateur tel que défini dans l’objet utilisateur Azure AD.<br>"given_name": "Frank"                   |       |

### <a name="additional-properties-of-optional-claims"></a>Propriétés supplémentaires des revendications facultatives

Certaines revendications facultatives peuvent être configurées pour modifier la façon dont la revendication est retournée. Ces propriétés supplémentaires servent principalement à faciliter la migration d’applications locales ayant des attentes différentes vis-à-vis des données (par exemple, `include_externally_authenticated_upn_without_hash` aide les clients qui ne prennent pas en charge les signes dièse (`#`) dans l’UPN)

**Tableau 4 : Valeurs de configuration des revendications facultatives standard**

| Nom de la propriété                                     | Nom de la propriété supplémentaire                                                                                                             | Description |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |  Peut être utilisée pour les réponses SAML et JWT.        |
| | `include_externally_authenticated_upn`              | Inclut l’UPN de l’invité tel que stocké dans le locataire de ressource. Par exemple, `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | Comme ci-dessus, sauf que les signes dièse (`#`) sont remplacés par des traits de soulignement (`_`), par exemple `foo_hometenant.com_EXT_@resourcetenant.com`. |             

> [!Note]
>Le fait de spécifier la revendication facultative UPN sans propriété supplémentaire ne change pas le comportement ; pour qu’une nouvelle revendication soit émise dans le jeton, il faut ajouter au moins l’une des propriétés supplémentaires. 

#### <a name="additional-properties-example"></a>Exemple de propriétés supplémentaires

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Cet objet OptionalClaims renvoie au client le jeton d’ID pour y inclure un autre UPN avec des informations supplémentaires sur le locataire de base et le locataire de ressource. Cela modifie uniquement la revendication `upn` du jeton si l’utilisateur est un invité du locataire (qui utilise un fournisseur d’identité différent pour l’authentification). 

## <a name="configuring-optional-claims"></a>Configuration des revendications facultatives

Vous pouvez configurer des revendications facultatives pour votre application en modifiant le manifeste de l’application (voir l’exemple ci-dessous). Pour plus d’informations, consultez l’[article de présentation des manifestes d’applications Azure AD](reference-app-manifest.md).

**Exemple de schéma :**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "auth_time", 
                   "essential": false
              }
        ],
 "accessToken": [ 
             {
                    "name": "ipaddr", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Type OptionalClaims

Déclare les revendications facultatives demandées par une application. Une application peut configurer des revendications facultatives à retourner dans chacun des trois types de jetons (jeton d’ID, jeton d’accès, jeton SAML 2) qu’elle peut recevoir à partir du service d’émission de jeton de sécurité. L’application peut configurer un ensemble différent de revendications facultatives à retourner dans chaque type de jeton. La propriété OptionalClaims de l’entité Application est un objet OptionalClaims.

**Tableau 5 : Propriétés de type OptionalClaims**

| NOM        | type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton d’ID JWT. |
| `accessToken` | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton d’accès JWT. |
| `saml2Token`  | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton SAML.   |

### <a name="optionalclaim-type"></a>Type OptionalClaim

Contient une revendication facultative associée à une application ou à un principal de service. Les propriétés idToken, accessToken et saml2Token du type [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) sont une collection d’OptionalClaim.
En cas de prise en charge par une revendication spécifique, vous pouvez également modifier le comportement de l’OptionalClaim à l’aide du champ AdditionalProperties.

**Tableau 6 : Propriétés de type OptionalClaim**

| NOM                 | type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nom de la revendication facultative.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Source (objet d’annuaire) de la revendication. Il existe des revendications prédéfinies et définies par l’utilisateur à partir des propriétés d’extension. Si la valeur source est null, la revendication est une revendication facultative prédéfinie. Si la valeur source est user, la valeur de la propriété name est la propriété d’extension à partir de l’objet utilisateur. |
| `essential`            | Edm.Boolean             | Si la valeur est true, la revendication spécifiée par le client est nécessaire afin de garantir une expérience d’autorisation fluide pour la tâche demandée par l’utilisateur final. La valeur par défaut est false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Propriétés supplémentaires de la revendication. Si une propriété existe dans cette collection, elle modifie le comportement de la revendication facultative spécifiée dans la propriété name.                                                                                                                                               |
## <a name="configuring-custom-claims-via-directory-extensions"></a>Configuration de revendications personnalisées par le biais d’extensions d’annuaire

En plus de l’ensemble de revendications facultatives standard, les jetons peuvent également être configurés pour inclure des extensions de schéma d’annuaire (pour plus d’informations, consultez l’[article sur les extensions de schéma d’annuaire](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)). Cette fonctionnalité est utile pour joindre des informations utilisateur supplémentaires utilisables par votre application, par exemple un identificateur supplémentaire ou une option de configuration importante que l’utilisateur a définie. 

> [!Note]
> Les extensions de schéma d’annuaire sont une fonctionnalité AAD uniquement. Par conséquent, si le manifeste de votre application demande une extension personnalisée et qu’un utilisateur MSA se connecte à votre application, ces extensions ne sont pas retournées. 

### <a name="values-for-configuring-additional-optional-claims"></a>Valeurs de configuration des revendications supplémentaires facultatives

Pour les attributs d’extension, utilisez le nom complet de l’extension (au format `extension_<appid>_<attributename>`) dans le manifeste de l’application. `<appid>` doit correspondre à l’ID de l’application demandant la revendication. 

Dans les jetons JWT, ces revendications seront émises avec le format de nom suivant : `extn.<attributename>`.

Dans les jetons SAML, ces revendications seront émises avec le format d’URI suivant : `http://schemas.microsoft.com/identity/claims/extn.<attributename>`.

## <a name="optional-claims-example"></a>Exemple de revendications facultatives

Dans cette section, vous allez suivre un scénario afin de voir comment utiliser la fonctionnalité de revendications facultatives pour votre application.
Plusieurs options sont disponibles pour mettre à jour les propriétés de configuration d’identité d’une application afin d’activer et de configurer des revendications facultatives :
-   Vous pouvez modifier le manifeste de l’application. L’exemple ci-dessous applique cette méthode pour effectuer la configuration. Pour obtenir une présentation du manifeste, lisez d’abord l’article [Manifeste de l’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest).
-   Il est également possible d’écrire une application qui utilise l’[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) pour mettre à jour votre application. La [Référence des types complexes et des entités](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) dans le guide de référence de l’API Graph peut vous aider à configurer les revendications facultatives.

**Exemple :** dans l’exemple ci-dessous, vous allez modifier le manifeste d’une application pour ajouter des revendications aux jetons d’accès, d’ID et SAML destinés à l’application.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
1. Sélectionnez **Inscription des applications** dans la partie gauche.
1. Recherchez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives, puis cliquez dessus.
1. Dans la page de l’application, cliquez sur **Manifeste** pour ouvrir l’éditeur de manifeste en ligne. 
1. Vous pouvez modifier directement le manifeste à l’aide de cet éditeur. Le manifeste respecte le schéma de l’[entité Application](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity) et met automatiquement en forme le manifeste une fois enregistré. Les nouveaux éléments sont ajoutés à la propriété `OptionalClaims`.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      Ici, différentes revendications facultatives ont été ajoutées à chaque type de jeton que l’application peut recevoir. Les jetons d’ID contiendront désormais l’UPN pour les utilisateurs fédérés au format complet (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Les jetons d’accès demandés par d’autres clients pour cette application incluront désormais la revendication auth_time. Les jetons SAML contiendront désormais l’extension de schéma d’annuaire skypeId (dans cet exemple, l’ID d’application pour cette application est ab603c56068041afb2f6832e2a17e237). Les jetons SAML exposeront l’ID Skype en tant que `extension_skypeId`.

1. Quand vous avez terminé de mettre à jour le manifeste, cliquez sur **Enregistrer** pour enregistrer le manifeste.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en davantage sur les revendications standard fournies par Azure AD.

- [Jetons d’ID](id-tokens.md)
- [Jetons d’accès](access-tokens.md)
