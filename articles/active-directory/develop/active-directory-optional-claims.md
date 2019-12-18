---
title: Fournir des revendications facultatives aux applications Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Guide pratique pour ajouter de revendications personnalisées ou supplémentaires aux jetons JSON Web Token (JWT) et SAML 2.0 émis par Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967206"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Activation Fournir des revendications facultatives à votre application Azure AD

Les développeurs d’applications peuvent utiliser des revendications facultatives dans leurs applications Azure AD pour spécifier les revendications souhaitées dans les jetons envoyés à leur application. 

Vous pouvez utiliser des revendications facultatives pour :

- Sélectionner des revendications supplémentaires à inclure dans les jetons pour votre application.
- Modifier le comportement de certaines revendications retournées par Azure AD dans les jetons.
- Ajouter et accéder à des revendications personnalisées pour votre application.

Pour obtenir la liste de revendications standard, voir les documents sur les revendications [jeton d’accès](access-tokens.md) et [id_token](id-tokens.md). 

Si les revendications facultatives sont prises en charge dans les jetons aux formats v1.0 et v2.0, ainsi que dans les jetons SAML, elles révèlent l’essentiel de leur valeur lors du passage de v1.0 à v2.0. L’un des objectifs du [Point de terminaison de la plateforme d’identités Microsoft v2.0](active-directory-appmodel-v2-overview.md) est de réduire la taille des jetons afin de garantir des performances optimales des clients. Ainsi, plusieurs revendications précédemment incluses dans les jetons d’accès et d’ID ne sont plus présentes dans les jetons v2.0 et doivent être demandées spécifiquement pour chaque application.

**Tableau 1 : Applicabilité**

| Type de compte | Jetons v1.0 | Jetons v2.0  |
|--------------|---------------|----------------|
| Compte Microsoft personnel  | N/A  | Pris en charge |
| Compte Azure AD      | Pris en charge | Pris en charge |

## <a name="v10-and-v20-optional-claims-set"></a>Ensemble de revendications facultatives v1.0 et v2.0

L’ensemble de revendications facultatives disponible par défaut pour les applications est répertorié ci-dessous. Pour ajouter des revendications personnalisées facultatives pour votre application, consultez [Extensions d’annuaire](#configuring-directory-extension-optional-claims) ci-dessous. Lors de l’ajout de revendications au **jeton d’accès**, cela s’applique aux jetons d’accès demandés *pour* l’application (API web), et non à ceux demandés *par* l’application. Cela garantit que, quelque soit le client qui accède à votre API, les données correctes seront sur le jeton d’accès qu’il utilise pour s’authentifier auprès de votre API.

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
| `verified_primary_email`   | Obtenu à partir du PrimaryAuthoritativeEmail de l’utilisateur.      | JWT        |           |         |
| `verified_secondary_email` | Obtenu à partir du SecondaryAuthoritativeEmail de l’utilisateur.   | JWT        |           |        |
| `enfpolids`                | ID des stratégies appliquées. Liste des ID des stratégies qui ont été évaluées pour l’utilisateur actuel. | JWT |  |  |
| `vnet`                     | Informations sur le spécificateur de réseau virtuel. | JWT        |           |      |
| `fwd`                      | Adresse IP.| JWT    |   | Ajoute l’adresse IPv4 d’origine du client demandeur (quand il se trouve sur un réseau virtuel). |
| `ctry`                     | Pays de l’utilisateur. | JWT |  | Azure AD retourne la revendication facultative `ctry` si elle est présente. La valeur de la revendication est un code de pays à deux lettres standard, tel que FR, JP, SZ, etc. |
| `tenant_ctry`              | Pays du locataire de ressource. | JWT | | |
| `xms_pdl`          | Emplacement de données par défaut   | JWT | | Pour les locataires multigéographiques, il s’agit du code à trois lettres indiquant la région géographique où se trouve l’utilisateur. Pour plus d’informations, voir la [documentation Azure AD Connect sur l’emplacement par défaut des données](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Par exemple : `APC` pour l’Asie-Pacifique. |
| `xms_pl`                   | Langue par défaut de l’utilisateur  | JWT ||La langue par défaut de l’utilisateur, si celle-ci a été définie. Dans les scénarios d’accès invité, provient du locataire de base. Au format langue-pays (« fr-fr »). |
| `xms_tpl`                  | Langue par défaut du locataire| JWT | | Langue par défaut du locataire de la ressource, si celle-ci est définie. Au format langue (« fr »). |
| `ztdid`                    | ID de déploiement sans intervention | JWT | | Identité d’appareil utilisée pour [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresse e-mail de l'utilisateur, le cas échéant.  | JWT, SAML | MSA, Azure AD | Cette valeur est incluse par défaut si l’utilisateur est un invité du locataire.  Pour les utilisateurs gérés (à l’intérieur du locataire), elle doit être demandée via cette revendication facultative ou, sur la version 2.0 uniquement, avec l’étendue OpenID.  Pour les utilisateurs gérés, l’adresse e-mail doit être définie dans le [portail d’administration Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Mise en forme facultative des revendications de groupe |JWT, SAML| |Utilisé conjointement avec le paramètre GroupMembershipClaims dans le [manifeste d’application](reference-app-manifest.md) qui doit également être défini. Pour plus d’informations, voir [Revendications de groupe](#configuring-groups-optional-claims) ci-dessous. Pour plus d’informations sur les revendications de groupe, voir [Comment configurer des revendications de groupe](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Statut du compte utilisateur dans le client. | JWT, SAML | | Si l’utilisateur est membre du client, la valeur est `0`. S’il est un invité, la valeur est `1`. |
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
| `nickname`    | Surnom                        | Autre nom de l’utilisateur, distinct du nom de famille et du prénom. | 
| `family_name` | Nom                       | Fournit le nom de famille de l’utilisateur, tel que défini sur l’objet utilisateur. <br>"family_name":"Miller" | Pris en charge dans MSA et Azure AD   |
| `given_name`  | Prénom                      | Fournit le prénom de l’utilisateur, tel que défini sur l’objet utilisateur.<br>"given_name": "Frank"                   | Pris en charge dans MSA et Azure AD  |
| `upn`         | Nom d’utilisateur principal | Identificateur de l'utilisateur qui peut être utilisé avec le paramètre username_hint.  Il ne s'agit pas d'un identificateur durable pour l'utilisateur et il ne doit pas être utilisé pour saisir des données. | Consultez les [propriétés supplémentaires](#additional-properties-of-optional-claims) ci-dessous pour en savoir plus sur la configuration de la revendication. |

### <a name="additional-properties-of-optional-claims"></a>Propriétés supplémentaires des revendications facultatives

Certaines revendications facultatives peuvent être configurées pour modifier la façon dont la revendication est retournée. Ces propriétés supplémentaires servent principalement à faciliter la migration d'applications locales dont les attentes sont différentes vis-à-vis des données (par exemple, `include_externally_authenticated_upn_without_hash` aide les clients qui ne prennent pas en charge les signes dièse (`#`) dans l'UPN)

**Tableau 4 : Valeurs de configuration des revendications facultatives**

| Nom de la propriété  | Nom de la propriété supplémentaire | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | Peut être utilisée pour les réponses SAML et JWT, ainsi que pour les jetons v1.0 et v2.0. |
|                | `include_externally_authenticated_upn`  | Inclut l’UPN de l’invité tel que stocké dans le locataire de ressource. Par exemple, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Comme ci-dessus, sauf que les signes dièse (`#`) sont remplacés par des traits de soulignement (`_`), par exemple `foo_hometenant.com_EXT_@resourcetenant.com`. |

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

> [!IMPORTANT]
> Les jetons d’accès sont **toujours** générés à l’aide du manifeste de la ressource, pas du client.  Donc, dans la requête `...scope=https://graph.microsoft.com/user.read...`, la ressource est Graph.  Ainsi, le jeton d’accès est créé à l’aide du manifeste Graph, et non du manifeste du client.  Si vous modifiez le manifeste de votre application, les jetons pour Graph ne changent jamais.  Pour vérifier que vos modifications de `accessToken` sont effectives, demandez un jeton pour votre application, pas pour une autre application.  

Vous pouvez configurer des revendications facultatives pour votre application par le biais de l’interface utilisateur ou du manifeste de l’application.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
1. Dans la section **Gérer**, sélectionnez **Inscriptions d’applications**.
1. Sélectionnez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives.

**Configuration de revendications facultatives par le biais de l’interface utilisateur :**

[![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Dans la section **Gérer**, sélectionnez **Configuration de jetons (préversion)** .
2. Sélectionnez **Ajouter une revendication facultative**.
3. Sélectionnez le type de jeton que vous souhaitez configurer.
4. Sélectionnez les revendications facultatives à ajouter.
5. Cliquez sur **Add**.

**Configuration de revendications facultatives par le biais du manifeste de l’application :**

[![montre comment configurer des revendications facultatives à l’aide du manifeste de l’application](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Dans la section **Gérer**, sélectionnez **Manifeste**. Un éditeur de manifeste web s’ouvre, vous permettant de modifier le manifeste. Si vous le souhaitez, vous pouvez sélectionner **Télécharger** et modifier localement le manifeste, puis utiliser **Charger** afin de l’appliquer de nouveau à votre application. Pour plus d’informations sur le manifeste de l’application, consultez l’[article Connaître le manifeste de l’application Azure AD](reference-app-manifest.md).

    L’entrée suivante du manifeste de l’application ajoute les revendications facultatives auth_time, ipaddr et upn aux jetons d’ID, d’accès et SAML.

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

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Par défaut, les ObjectId de groupe sont émis dans la valeur de revendication de groupe.  Pour modifier la valeur de revendication afin qu’elle contienne des attributs de groupe local, ou pour modifier le type de revendication en rôle, utilisez une configuration OptionalClaims comme suit :

3. Définissez des revendications facultatives de configuration de nom de groupe.

   Si vous souhaitez que les groupes dans le jeton contiennent les attributs de groupe AD local, dans la section Revendications facultatives, spécifiez la revendication facultative de type de jeton à laquelle appliquer la configuration, le nom de la revendication facultative demandée et toutes les propriétés supplémentaires souhaitées.  Plusieurs types de jetons peuvent être répertoriés :

   - idToken pour le jeton d’ID d’OIDC ;
   - accessToken pour le jeton d’accès OAuth/OIDC ;
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
   | **additionalProperties:** | Liste de propriétés supplémentaires.  Les options valides sont « sam_account_name », « dns_domain_and_sam_account_name », « netbios_domain_and_sam_account_name » et « emit_as_roles » |

   Dans additionalProperties une seule des options « sam_account_name », « dns_domain_and_sam_account_name » ou « netbios_domain_and_sam_account_name » est requise.  Si plusieurs options sont présentes, la première est utilisée et les autres ignorées.

   Certaines applications requièrent des informations de groupe sur l’utilisateur dans la revendication de rôle.  Pour modifier le type de revendication de revendication de groupe en revendication de rôle, ajoutez « emit_as_roles » aux propriétés supplémentaires.  Les valeurs de groupe sont émises dans la revendication de rôle.

   > [!NOTE]
   > Si l’option « emit_as_roles » est utilisée, les rôles d’application configurés auxquels l’utilisateur est affecté n’apparaissent pas dans la revendication de rôle

**Exemples :**

1) Émettre des groupes en tant que noms de groupes dans des jetons d’accès OAuth au format dnsDomainName\sAMAccountName

    
    **Configuration de l’interface utilisateur :**

    [![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Entrée du manifeste de l’application :**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Émet des noms de groupes à retourner au format netbiosDomain\sAMAccountName en tant que revendication de rôles dans des jetons d’ID SAML et OIDC

    **Configuration de l’interface utilisateur :**

    [![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Entrée du manifeste de l’application :**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>Exemple de revendications facultatives

Dans cette section, vous allez suivre un scénario afin de voir comment utiliser la fonctionnalité de revendications facultatives pour votre application.
Plusieurs options sont disponibles pour mettre à jour les propriétés de configuration d’identité d’une application afin d’activer et de configurer des revendications facultatives :
-    Vous pouvez utiliser l’interface utilisateur **Configuration du jeton (préversion)** (Voir l’exemple ci-dessous)
-    Vous pouvez utiliser le **Manifeste** (Voir l’exemple ci-dessous). Pour obtenir une présentation du manifeste, lisez d’abord l’article [Manifeste de l’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest).
-   Il est également possible d’écrire une application qui utilise l’[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) pour mettre à jour votre application. La [Référence des types complexes et des entités](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) dans le guide de référence de l’API Graph peut vous aider à configurer les revendications facultatives.

**Exemple :** Dans l’exemple ci-dessous, vous allez utiliser l’interface utilisateur **Configuration du jeton (préversion)** et le **Manifeste** pour ajouter des revendications facultatives aux jetons d’accès, d’ID et SAML destinés à votre application. Différentes revendications facultatives seront ajoutées à chaque type de jeton que l’application peut recevoir :
-    Les jetons d’ID contiendront désormais l’UPN pour les utilisateurs fédérés au format complet (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Les jetons d’accès demandés par d’autres clients pour cette application incluront désormais la revendication auth_time
-    Les jetons SAML contiendront désormais l’extension de schéma d’annuaire skypeId (dans cet exemple, l’ID d’application pour cette application est ab603c56068041afb2f6832e2a17e237). Les jetons SAML exposeront l’ID Skype en tant que `extension_skypeId`.

**Configuration de l’interface utilisateur :**

1. Connectez-vous au [portail Azure](https://portal.azure.com)

1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.

1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.

1. Sous la section **Gérer**, sélectionnez **Inscriptions d’applications**.

1. Recherchez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives, puis cliquez dessus.

1. Sous la section **Gérer**, cliquez sur **Configuration du jeton (préversion)** .

1. Sélectionnez **Ajouter une revendication facultative**, sélectionnez ensuite le type de jeton **ID**, puis **upn** dans la liste des revendications et cliquez enfin sur **Ajouter**.

1. Sélectionnez **Ajouter une revendication facultative**, sélectionnez ensuite le type de jeton **Access**, puis **auth_time** dans la liste des revendications et cliquez enfin sur **Ajouter**.

1. À partir de l’écran de vue d’ensemble Configuration du jeton, cliquez sur l’icône en forme de crayon à côté d’**upn**, cliquez ensuite sur le bouton bascule **Authentifié en externe**, puis sur **Enregistrer**.

1. Sélectionnez **Ajouter une revendication facultative**, puis le type de jeton **SAML** et enfin **extn.skypeID** dans la liste des revendications (applicable uniquement si vous avez créé un objet utilisateur Azure AD appelé skypeID). Cliquez ensuite sur **Ajouter**.

    [![montre comment configurer des revendications facultatives à l’aide de l’interface utilisateur](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuration du manifeste :**
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
1. Recherchez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives, puis cliquez dessus.
1. Sous la section **Gérer**, cliquez sur **Manifeste** pour ouvrir l’éditeur de manifeste en ligne.
1. Vous pouvez modifier directement le manifeste à l’aide de cet éditeur. Le manifeste respecte le schéma de [Application entity] (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) et met automatiquement en forme le manifeste une fois enregistré. Les nouveaux éléments sont ajoutés à la propriété `OptionalClaims`.

    ```json
            "optionalClaims": {
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
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
