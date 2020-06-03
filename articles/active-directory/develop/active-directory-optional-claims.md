---
title: Fournir des revendications facultatives aux applications Azure AD
titleSuffix: Microsoft identity platform
description: Guide pratique pour ajouter de revendications personnalisées ou supplémentaires aux jetons JSON Web Token (JWT) et SAML 2.0 émis par Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 92b3e12cc078326e98df5f42e36fcaddd56bf0c6
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993693"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Procédure : Fournir des revendications facultatives à votre application Azure AD

Les développeurs d’applications peuvent utiliser des revendications facultatives dans leurs applications Azure AD pour spécifier les revendications souhaitées dans les jetons envoyés à leur application.

Vous pouvez utiliser des revendications facultatives pour :

- Sélectionner des revendications supplémentaires à inclure dans les jetons pour votre application.
- Modifier le comportement de certaines revendications retournées par Azure AD dans les jetons.
- Ajouter et accéder à des revendications personnalisées pour votre application.

Pour obtenir la liste de revendications standard, voir les documents sur les revendications [jeton d’accès](access-tokens.md) et [id_token](id-tokens.md).

Si les revendications facultatives sont prises en charge dans les jetons aux formats v1.0 et v2.0, ainsi que dans les jetons SAML, elles révèlent l’essentiel de leur valeur lors du passage de v1.0 à v2.0. L’un des objectifs du [Point de terminaison de la plateforme d’identités Microsoft v2.0](active-directory-appmodel-v2-overview.md) est de réduire la taille des jetons afin de garantir des performances optimales des clients. Ainsi, plusieurs revendications précédemment incluses dans les jetons d’accès et d’ID ne sont plus présentes dans les jetons v2.0 et doivent être demandées spécifiquement pour chaque application.

**Tableau 1 : Applicabilité**

| Type de compte               | Jetons v1.0 | Jetons v2.0 |
|----------------------------|-------------|-------------|
| Compte Microsoft personnel | N/A         | Prise en charge   |
| Compte Azure AD           | Prise en charge   | Prise en charge   |

## <a name="v10-and-v20-optional-claims-set"></a>Ensemble de revendications facultatives v1.0 et v2.0

L’ensemble de revendications facultatives disponible par défaut pour les applications est répertorié ci-dessous. Pour ajouter des revendications personnalisées facultatives pour votre application, consultez [Extensions d’annuaire](#configuring-directory-extension-optional-claims) ci-dessous. Lors de l’ajout de revendications au **jeton d’accès**, les revendications s’appliquent aux jetons d’accès demandés *pour* l’application (API web), pas les revendications demandées *par* l’application. Quelle que soit la façon dont le client accède à votre API, les données correctes seront sur le jeton d’accès qu’il utilise pour s’authentifier auprès de votre API.

> [!NOTE]
> La plupart de ces revendications peuvent figurer dans les jetons JWT pour les jetons v1.0 et v2.0, mais pas dans les jetons SAML, sauf indication contraire dans la colonne Type de jeton. Les comptes consommateur prennent en charge un sous-ensemble de ces revendications, indiqué dans la colonne « Type d’utilisateur ».  La plupart des revendications répertoriées ne s’appliquent pas aux utilisateurs consommateurs (comme ils n’ont pas de locataire, `tenant_ctry` n’a pas de valeur).

**Tableau 2 : ensemble de revendications facultatives v1.0 et v2.0**

| Nom                       |  Description   | Type de jeton | Type d’utilisateur | Notes  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Heure de dernière authentification de l’utilisateur. Voir les spécifications OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Région du locataire de ressource. | JWT        |           | |
| `home_oid`                 | Pour les utilisateurs invités, il s’agit de l’ID d’objet de l’utilisateur dans le locataire de base de l’utilisateur.| JWT        |           | |
| `sid`                      | ID de session utilisé pour la déconnexion de l’utilisateur après chaque session. | JWT        |  Comptes personnels et Azure AD.   |         |
| `platf`                    | Plateforme d’appareil.    | JWT        |           | Limité aux appareils gérés qui peuvent vérifier le type d’appareil.|
| `verified_primary_email`   | Obtenu à partir du PrimaryAuthoritativeEmail de l’utilisateur      | JWT        |           |         |
| `verified_secondary_email` | Obtenu à partir du SecondaryAuthoritativeEmail de l’utilisateur   | JWT        |           |        |
| `enfpolids`                | ID des stratégies appliquées. Liste des ID des stratégies qui ont été évaluées pour l’utilisateur actuel. | JWT |  |  |
| `vnet`                     | Informations sur le spécificateur de réseau virtuel. | JWT        |           |      |
| `fwd`                      | Adresse IP.| JWT    |   | Ajoute l’adresse IPv4 d’origine du client demandeur (quand il se trouve sur un réseau virtuel). |
| `ctry`                     | Pays/Région de l’utilisateur | JWT |  | Azure AD retourne la revendication facultative `ctry` si elle est présente. La valeur de la revendication est un code de pays/région à deux lettres standard, tel que FR, JP, SZ, etc. |
| `tenant_ctry`              | Pays/Région du locataire de la ressource | JWT | | |
| `xms_pdl`             | Emplacement de données par défaut   | JWT | | Pour les locataires multigéographiques, l’emplacement de données par défaut est le code à trois lettres indiquant la région géographique où se trouve l’utilisateur. Pour plus d’informations, voir la [documentation Azure AD Connect sur l’emplacement par défaut des données](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Par exemple : `APC` pour l’Asie-Pacifique. |
| `xms_pl`                   | Langue par défaut de l’utilisateur  | JWT ||La langue par défaut de l’utilisateur, si celle-ci a été définie. Dans les scénarios d’accès invité, provient du locataire de base. Au format langue-pays (« fr-fr »). |
| `xms_tpl`                  | Langue par défaut du locataire| JWT | | Langue par défaut du locataire de la ressource, si celle-ci est définie. Au format langue (« fr »). |
| `ztdid`                    | ID de déploiement sans intervention | JWT | | Identité d’appareil utilisée pour [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresse e-mail de l'utilisateur, le cas échéant.  | JWT, SAML | MSA, Azure AD | Cette valeur est incluse par défaut si l’utilisateur est un invité du locataire.  Pour les utilisateurs gérés (à l’intérieur du locataire), elle doit être demandée via cette revendication facultative ou, sur la version 2.0 uniquement, avec l’étendue OpenID.  Pour les utilisateurs gérés, l’adresse e-mail doit être définie dans le [portail d’administration Office](https://portal.office.com/adminportal/home#/users).|
| `groups`| Mise en forme facultative des revendications de groupe |JWT, SAML| |Utilisé conjointement avec le paramètre GroupMembershipClaims dans le [manifeste d’application](reference-app-manifest.md) qui doit également être défini. Pour plus d’informations, voir [Revendications de groupe](#configuring-groups-optional-claims) ci-dessous. Pour plus d’informations sur les revendications de groupe, voir [Comment configurer des revendications de groupe](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`                | Statut du compte utilisateur dans le client. | JWT, SAML | | Si l’utilisateur est membre du client, la valeur est `0`. S’il est un invité, la valeur est `1`. |
| `upn`                      | Revendication UserPrincipalName. | JWT, SAML  |           | Bien que cette revendication soit incluse automatiquement, vous pouvez la spécifier en tant que revendication facultative pour attacher des propriétés supplémentaires afin de modifier son comportement en cas d’utilisateur invité.  |

## <a name="v20-specific-optional-claims-set"></a>Ensemble de revendications facultatives spécifiques à v2.0

Ces revendications sont toujours incluses dans les jetons Azure AD v1.0, mais pas dans les jetons v2.0, sauf sur demande. Ces revendications s’appliquent uniquement aux jetons web JSON (jetons d’ID et jetons d’accès).

**Tableau 3 : revendications facultatives spécifiques de V2.0**

| Revendication JWT     | Nom                            | Description                                | Notes |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Adresse IP                      | Adresse IP à partir de laquelle le client s’est connecté.   |       |
| `onprem_sid`  | Identificateur de sécurité local |                                             |       |
| `pwd_exp`     | Heure d’expiration du mot de passe        | Date et heure d’expiration du mot de passe. |       |
| `pwd_url`     | Modifier l’URL de mot de passe             | URL à laquelle l’utilisateur peut accéder pour modifier son mot de passe.   |   |
| `in_corp`     | Dans le périmètre du réseau d’entreprise        | Indique si le client se connecte à partir du réseau d’entreprise. Dans le cas contraire, la revendication n’est pas incluse.   |  Basé sur les [adresses IP approuvées](../authentication/howto-mfa-mfasettings.md#trusted-ips) définies dans MFA.    |
| `family_name` | Nom                       | Fournit le nom de famille de l’utilisateur, tel que défini sur l’objet utilisateur. <br>"family_name":"Miller" | Pris en charge dans MSA et Azure AD. Nécessite l’étendue `profile`.   |
| `given_name`  | Prénom                      | Fournit le prénom de l’utilisateur, tel que défini sur l’objet utilisateur.<br>"given_name": "Frank"                   | Pris en charge dans MSA et Azure AD.  Nécessite l’étendue `profile`. |
| `upn`         | Nom d’utilisateur principal | Identificateur de l'utilisateur qui peut être utilisé avec le paramètre username_hint.  Il ne s'agit pas d'un identificateur durable pour l'utilisateur et il ne doit pas être utilisé pour saisir des données. | Consultez les [propriétés supplémentaires](#additional-properties-of-optional-claims) ci-dessous pour en savoir plus sur la configuration de la revendication. Nécessite l’étendue `profile`.|

### <a name="additional-properties-of-optional-claims"></a>Propriétés supplémentaires des revendications facultatives

Certaines revendications facultatives peuvent être configurées pour modifier la façon dont la revendication est retournée. Ces propriétés supplémentaires servent principalement à faciliter la migration d'applications locales dont les attentes sont différentes vis-à-vis des données (par exemple, `include_externally_authenticated_upn_without_hash` aide les clients qui ne prennent pas en charge les signes dièse (`#`) dans l'UPN)

**Tableau 4 : Valeurs de configuration des revendications facultatives**

| Nom de la propriété  | Nom de la propriété supplémentaire | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | Peut être utilisée pour les réponses SAML et JWT, ainsi que pour les jetons v1.0 et v2.0. |
|                | `include_externally_authenticated_upn`  | Inclut l’UPN de l’invité tel que stocké dans le locataire de ressource. Par exemple : `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Comme ci-dessus, sauf que les signes dièse (`#`) sont remplacés par des traits de soulignement (`_`), par exemple `foo_hometenant.com_EXT_@resourcetenant.com`. |

#### <a name="additional-properties-example"></a>Exemple de propriétés supplémentaires

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Cet objet OptionalClaims retourne au client le jeton d’ID pour y inclure une revendication UPN avec des informations supplémentaires sur le locataire de base et le locataire de ressource. La revendication `upn` est uniquement Modifiée dans le jeton si l’utilisateur est un invité du locataire (qui utilise un fournisseur d’identité différent pour l’authentification).

## <a name="configuring-optional-claims"></a>Configuration des revendications facultatives

> [!IMPORTANT]
> Les jetons d’accès sont **toujours** générés à l’aide du manifeste de la ressource, pas du client.  Donc, dans la requête `...scope=https://graph.microsoft.com/user.read...`, la ressource est l’API Microsoft Graph.  Ainsi, le jeton d’accès est créé à l’aide du manifeste de l’API Microsoft Graph, et non du manifeste du client.  La modification du manifeste de votre application n’entraînera jamais de changement au niveau des jetons pour l’API Microsoft Graph.  Pour vérifier que vos modifications de `accessToken` sont effectives, demandez un jeton pour votre application, pas pour une autre application.

Vous pouvez configurer des revendications facultatives pour votre application par le biais de l’interface utilisateur ou du manifeste de l’application.

1. Accédez au [portail Azure](https://portal.azure.com). Recherchez et sélectionnez **Azure Active Directory**.
1. Dans la section **Gérer**, sélectionnez **Inscriptions d’applications**.
1. Sélectionnez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives.

**Configuration de revendications facultatives par le biais de l’interface utilisateur :**

[![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Dans la section **Gérer**, sélectionnez **Configuration de jetons**.
1. Sélectionnez **Ajouter une revendication facultative**.
1. Sélectionnez le type de jeton que vous souhaitez configurer.
1. Sélectionnez les revendications facultatives à ajouter.
1. Sélectionnez **Ajouter**.

**Configuration de revendications facultatives par le biais du manifeste de l’application :**

[![montre comment configurer des revendications facultatives à l’aide du manifeste de l’application](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Dans la section **Gérer**, sélectionnez **Manifeste**. Un éditeur de manifeste web s’ouvre, vous permettant de modifier le manifeste. Si vous le souhaitez, vous pouvez sélectionner **Télécharger** et modifier localement le manifeste, puis utiliser **Charger** afin de l’appliquer de nouveau à votre application. Pour plus d’informations sur le manifeste de l’application, consultez l’[article Connaître le manifeste de l’application Azure AD](reference-app-manifest.md).

    L’entrée suivante du manifeste de l’application ajoute les revendications facultatives auth_time, ipaddr et upn aux jetons d’ID, d’accès et SAML.

    ```json
    "optionalClaims": {
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
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. Lorsque vous avez terminé, sélectionnez **Enregistrer**. Les revendications facultatives spécifiées seront désormais incluses dans les jetons de votre application.

### <a name="optionalclaims-type"></a>Type OptionalClaims

Déclare les revendications facultatives demandées par une application. Une application peut configurer des revendications facultatives à retourner dans chacun des trois types de jetons (jeton d’ID, jeton d’accès, jeton SAML 2) qu’elle peut recevoir à partir du service d’émission de jeton de sécurité. L’application peut configurer un ensemble différent de revendications facultatives à retourner dans chaque type de jeton. La propriété OptionalClaims de l’entité Application est un objet OptionalClaims.

**Tableau 5 : Propriétés de type OptionalClaims**

| Nom          | Type                       | Description                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton d’ID JWT.     |
| `accessToken` | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton d’accès JWT. |
| `saml2Token`  | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton SAML.       |

### <a name="optionalclaim-type"></a>Type OptionalClaim

Contient une revendication facultative associée à une application ou à un principal de service. Les propriétés idToken, accessToken et saml2Token du type [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) sont une collection d’OptionalClaim.
En cas de prise en charge par une revendication spécifique, vous pouvez également modifier le comportement de l’OptionalClaim à l’aide du champ AdditionalProperties.

**Tableau 6 : Propriétés de type OptionalClaim**

| Nom                   | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nom de la revendication facultative.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Source (objet d’annuaire) de la revendication. Il existe des revendications prédéfinies et définies par l’utilisateur à partir des propriétés d’extension. Si la valeur source est null, la revendication est une revendication facultative prédéfinie. Si la valeur source est user, la valeur de la propriété name est la propriété d’extension à partir de l’objet utilisateur. |
| `essential`            | Edm.Boolean             | Si la valeur est true, la revendication spécifiée par le client est nécessaire afin de garantir une expérience d’autorisation fluide pour la tâche demandée par l’utilisateur final. La valeur par défaut est false.                                                                                                                 |
| `additionalProperties` | Collection (Edm.String) | Propriétés supplémentaires de la revendication. Si une propriété existe dans cette collection, elle modifie le comportement de la revendication facultative spécifiée dans la propriété name.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Configuration des revendications facultatives d’extension d’annuaire

En plus de l’ensemble de revendications facultatives standard, vous pouvez configurer des jetons pour inclure des extensions. Pour plus d’informations, consultez la [documentation concernant l’extensionProperty de Microsoft Graph](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0).

Les extensions de schéma et les extensions ouvertes ne sont pas prises en charge par les revendications facultatives, mais uniquement par les extensions d’annuaire de style AAD-Graph. Cette fonctionnalité est utile pour joindre des informations utilisateur supplémentaires utilisables par votre application, par exemple un identificateur supplémentaire ou une option de configuration importante que l’utilisateur a définie. Pour obtenir un exemple, consultez le bas de cette page.

> [!NOTE]
> Les extensions de schéma d’annuaire sont propres à Azure AD. Par conséquent, si le manifeste de votre application demande une extension personnalisée et qu’un utilisateur MSA se connecte à votre application, ces extensions ne seront pas retournées.

### <a name="directory-extension-formatting"></a>Mise en forme d’extension d’annuaire

Lors de la configuration des revendications facultatives d’extension d’annuaire à l’aide du manifeste d’application, utilisez le nom complet de l’extension (au format : `extension_<appid>_<attributename>`). `<appid>` doit correspondre à l’ID de l’application demandant la revendication.

Dans les jetons JWT, ces revendications seront émises avec le format de nom suivant : `extn.<attributename>`.

Dans les jetons SAML, ces revendications seront émises avec le format d’URI suivant : `http://schemas.microsoft.com/identity/claims/extn.<attributename>`.

## <a name="configuring-groups-optional-claims"></a>Configuration des revendications facultatives de groupe

   > [!NOTE]
   > La possibilité d’émettre des noms de groupe pour des utilisateurs et groupes synchronisés en local est en préversion publique.

Cette section couvre les options de configuration sous les revendications facultatives pour la modification des attributs de groupe utilisés dans les revendications de groupe, de l’ObjectId de groupe par défaut aux attributs synchronisés à partir du Windows Active Directory local. Vous pouvez configurer des revendications facultatives de groupe pour votre application par le biais de l’interface utilisateur ou du manifeste de l’application.

> [!IMPORTANT]
> Pour plus de détails, dont des mises en garde importantes relatives à la préversion publique des revendications de groupe d’attributs locaux, voir [Configurer des revendications de groupe pour les applications avec Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Configuration de revendications facultatives de groupe par le biais de l’interface utilisateur :**

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
1. Sous la section **Gérer**, sélectionnez **Inscriptions d’applications**.
1. Sélectionnez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives.
1. Dans la section **Gérer**, sélectionnez **Configuration de jetons**.
1. Sélectionnez **Ajouter une revendication de groupe**.
1. Sélectionnez les types de groupes à renvoyer (**Tous les groupes**, **SecurityGroup**ou **DirectoryRole**). L’option **Tous les groupes** comprend **SecurityGroup**, **DirectoryRole**et **DistributionList**
1. Facultatif : sélectionnez les propriétés du type de jeton pour modifier la valeur de la revendication de groupe afin qu’elle contienne les attributs du groupe local, ou pour remplacer la revendication de groupe par une revendication de rôle.
1. Sélectionnez **Enregistrer**.

**Configuration de revendications facultatives de groupe par le biais du manifeste de l’application :**

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
1. Sélectionnez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives.
1. Dans la section **Gérer**, sélectionnez **Manifeste**.
1. Ajoutez l’entrée suivante à l’aide de l’éditeur de manifeste :

   Les valeurs valides sont les suivantes :

   - « All » (cette option comprend SecurityGroup, DirectoryRole et DistributionList)
   - « SecurityGroup »
   - « DirectoryRole »

   Par exemple :

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Par défaut, les ObjectId de groupe sont émis dans la valeur de revendication de groupe.  Pour modifier la valeur de revendication afin qu’elle contienne des attributs de groupe local, ou pour modifier le type de revendication en rôle, utilisez une configuration OptionalClaims comme suit :

1. Définissez des revendications facultatives de configuration de nom de groupe.

   Si vous souhaitez que les groupes dans le jeton contiennent les attributs de groupe AD local, dans la section Revendications facultatives, spécifiez la revendication facultative de type de jeton à laquelle appliquer la configuration, le nom de la revendication facultative demandée et toutes les propriétés supplémentaires souhaitées.  Plusieurs types de jetons peuvent être répertoriés :

   - idToken pour le jeton d’ID d’OIDC ;
   - accessToken pour le jeton d’accès OAuth
   - Saml2Token pour les jetons SAML.

   > [!NOTE]
   > Le type de jeton Saml2Token s’applique aux jetons de format SAML1.1 et SAML2.0

   Pour chaque type de jeton pertinent, modifiez la revendication de groupe pour utiliser la section OptionalClaims dans le manifeste. Le schéma d’OptionalClaims est le suivant :

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | Schéma de revendications facultatives | Valeur |
   |----------|-------------|
   | **name:** | Doit être « groupes » |
   | **source:** | Non utilisé. Omettez ou spécifiez la valeur null |
   | **essential:** | Non utilisé. Omettez ou spécifiez la valeur false |
   | **additionalProperties:** | Liste de propriétés supplémentaires.  Les options valides sont « sam_account_name », « dns_domain_and_sam_account_name », « netbios_domain_and_sam_account_name » et « emit_as_roles ». |

   Dans additionalProperties, une seule des options « sam_account_name », « dns_domain_and_sam_account_name » ou « netbios_domain_and_sam_account_name » est nécessaire.  Si plusieurs options sont présentes, la première est utilisée et les autres ignorées.

   Certaines applications requièrent des informations de groupe sur l’utilisateur dans la revendication de rôle.  Pour remplacer la revendication de groupe par une revendication de rôle, ajoutez « emit_as_roles » aux propriétés supplémentaires.  Les valeurs de groupe sont émises dans la revendication de rôle.

   > [!NOTE]
   > Si l’option « emit_as_roles » est utilisée, les rôles d’application configurés auxquels l’utilisateur est affecté n’apparaissent pas dans la revendication de rôle

**Exemples :**

1) Émettre des groupes en tant que noms de groupes dans des jetons d’accès OAuth au format dnsDomainName\sAMAccountName

    **Configuration de l’interface utilisateur :**

    [![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Entrée du manifeste de l’application :**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) Émet des noms de groupes à retourner au format netbiosDomain\sAMAccountName en tant que revendication de rôles dans des jetons d’ID SAML et OIDC

    **Configuration de l’interface utilisateur :**

    [![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Entrée du manifeste de l’application :**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Exemple de revendications facultatives

Dans cette section, vous allez suivre un scénario afin de voir comment utiliser la fonctionnalité de revendications facultatives pour votre application.
Plusieurs options sont disponibles pour mettre à jour les propriétés de configuration d’identité d’une application afin d’activer et de configurer des revendications facultatives :

- Vous pouvez utiliser l’interface utilisateur **Configuration du jeton** (voir l’exemple ci-dessous).
- Vous pouvez utiliser le **Manifeste** (Voir l’exemple ci-dessous). Pour obtenir une présentation du manifeste, lisez d’abord l’article [Manifeste de l’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest).
- Il est également possible d’écrire une application qui utilise l’[API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) pour mettre à jour votre application. Le type [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) dans le guide de référence de l’API Microsoft Graph peut vous aider à configurer les revendications facultatives.

**Exemple :**

Dans l’exemple ci-dessous, vous allez utiliser l’interface utilisateur **Configuration du jeton**, ainsi que le **manifeste**, pour ajouter des revendications facultatives aux jetons d’accès, d’ID et SAML destinés à votre application. Différentes revendications facultatives seront ajoutées à chaque type de jeton que l’application peut recevoir :

- Les jetons d’ID contiendront désormais l’UPN pour les utilisateurs fédérés au format complet (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
- Les jetons d’accès demandés par d’autres clients pour cette application incluront désormais la revendication auth_time
- Les jetons SAML contiendront désormais l’extension de schéma d’annuaire skypeId (dans cet exemple, l’ID d’application pour cette application est ab603c56068041afb2f6832e2a17e237). Les jetons SAML exposeront l’ID Skype en tant que `extension_skypeId`.

**Configuration de l’interface utilisateur :**

1. Connectez-vous au [portail Azure](https://portal.azure.com)

1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.

1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.

1. Sous la section **Gérer**, sélectionnez **Inscriptions d’applications**.

1. Dans la liste, recherchez l’application pour laquelle vous souhaitez configurer des revendications facultatives, puis sélectionnez-la.

1. Dans la section **Gérer**, sélectionnez **Configuration de jetons**.

1. Sélectionnez **Ajouter une revendication facultative**, sélectionnez ensuite le type de jeton **ID**, puis **upn** dans la liste des revendications. Enfin, sélectionnez **Ajouter**.

1. Sélectionnez **Ajouter une revendication facultative**, sélectionnez ensuite le type de jeton **Access**, puis **auth_time** dans la liste des revendications. Enfin, sélectionnez **Ajouter**.

1. Dans l’écran de vue d’ensemble « Configuration du jeton », sélectionnez l’icône en forme de crayon à côté d’**upn**, sélectionnez ensuite le bouton bascule **Authentifié en externe**, puis sélectionnez **Enregistrer**.

1. Sélectionnez **Ajouter une revendication facultative**, puis le type de jeton **SAML** et enfin **extn.skypeID** dans la liste des revendications (applicable uniquement si vous avez créé un objet utilisateur Azure AD appelé skypeID). Sélectionnez ensuite **Ajouter**.

    [![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuration du manifeste :**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
1. Dans la liste, recherchez l’application pour laquelle vous souhaitez configurer des revendications facultatives, puis sélectionnez-la.
1. Dans la section **Gérer**, sélectionnez **Manifeste** pour ouvrir l’éditeur de manifeste en ligne.
1. Vous pouvez modifier directement le manifeste à l’aide de cet éditeur. Le manifeste respecte le schéma de [Application entity](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) et met automatiquement en forme le manifeste une fois enregistré. Les nouveaux éléments sont ajoutés à la propriété `OptionalClaims`.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
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

1. Quand vous avez terminé de mettre à jour le manifeste, sélectionnez **Enregistrer** pour l’enregistrer.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en davantage sur les revendications standard fournies par Azure AD.

- [Jetons d’ID](id-tokens.md)
- [Jetons d’accès](access-tokens.md)
