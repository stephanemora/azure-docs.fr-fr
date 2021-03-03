---
title: Ajouter AD FS en tant que fournisseur d’identités SAML en utilisant des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Configurer AD FS 2016 à l’aide du protocole SAML et de stratégies personnalisées dans Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6dda65be98934ce90e985b241078ae8019afb7e0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361262"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Ajouter AD FS en tant que fournisseur d’identités SAML à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment activer la connexion d’un compte d’utilisateur AD FS à l’aide de [stratégies personnalisées](custom-policy-overview.md) dans Azure Active Directory B2C (Azure AD B2C). Vous allez activer la connexion en ajoutant un [profil technique de fournisseur d’identité SAML](saml-identity-provider-technical-profile.md) à une stratégie personnalisée.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez enregistrer votre certificat dans votre client Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Upload`.
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `SAMLSigningCert`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Recherchez et sélectionnez votre fichier de certificat .pfx contenant la clé privée.
9. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte AD FS, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte AD FS en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie. Pour plus d’informations, voir [Définir un profil technique de fournisseur d’identité SAML](saml-identity-provider-technical-profile.md).

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Remplacez `your-AD-FS-domain` par le nom de votre domaine AD FS et remplacez la valeur de la revendication de sortie **identityProvider** par votre DNS (valeur arbitraire qui indique votre domaine).

1. Recherchez la section `<ClaimsProviders>` et ajoutez l’extrait de code XML suivant. Si votre stratégie contient déjà le profil technique `SM-Saml-idp`, passez à l’étape suivante. Pour plus d’informations, voir [Gestion de sessions d’authentification unique](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Enregistrez le fichier .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Configurer une approbation de partie de confiance AD FS

Pour utiliser AD FS comme fournisseur d’identité dans AAD B2C, vous devez créer une approbation de partie de confiance AD FS avec les métadonnées SAML d’AAD B2C. L’exemple suivant montre une adresse URL aux métadonnées SAML d’un profil technique d’AAD B2C :

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Remplacez les valeurs suivantes :

- **your-tenant** par le nom de votre locataire, par exemple, your-tenant.onmicrosoft.com.
- **your-policy** par le nom de votre stratégie. Par exemple, B2C_1A_signup_signin_adfs.
- **your-technical-profile** par le nom du profil technique de votre fournisseur d’identité SAML. Par exemple, Contoso-SAML2.

Ouvrez un navigateur et accédez à l’URL. Veillez à taper l’URL est correcte pour avoir accès au fichier de métadonnées XML. Pour ajouter une nouvelle approbation de partie de confiance en utilisant le composant logiciel enfichable de gestion ADFS et configurer manuellement les paramètres, effectuez la procédure suivante sur un serveur de fédération. L’appartenance au groupe **Administrateurs** ou à un groupe équivalent sur l’ordinateur local est la condition minimale requise pour effectuer cette procédure.

1. Dans Gestionnaire de serveur, sélectionnez **Outils**, puis **Gestion AD FS**.
2. Sélectionnez **Ajouter une approbation de partie de confiance**.
3. Dans la page **Bienvenue**, choisissez **Prise en charge des revendications**, puis cliquez sur **Démarrer**.
4. Dans la page **Sélectionner une source de données**, sélectionnez **Importer des données sur la partie de confiance publiées en ligne ou sur un réseau local**, fournissez votre URL de métadonnées AAD B2C, puis cliquez sur **Suivant**.
5. Dans la page **Indiquer le nom complet**, entrez un **Nom d’affichage**. Sous **Notes**, entrez une description pour cette approbation de partie de confiance, puis cliquez sur **Suivant**.
6. Dans la page **Sélectionner une stratégie de contrôle d’accès**, choisissez une stratégie, puis cliquez sur **Suivant**.
7. Dans la page **Prêt à ajouter l'approbation**, vérifiez les paramètres, puis cliquez sur **Suivant** pour enregistrer les informations de la nouvelle approbation de partie de confiance.
8. Sur la page **Terminer**, cliquez sur **Fermer**, cette action affiche automatiquement la boîte de dialogue **Modifier les règles de revendication**.
9. Sélectionnez **Ajouter une règle**.
10. Dans **Modèle de règle de revendication**, sélectionnez **Envoyer les attributs LDAP en tant que revendications**.
11. Fournissez un **Nom de règle de revendication**. Pour le **Magasin d’attributs** choisissez **Sélectionner Active Directory**, ajoutez les revendications suivantes, puis cliquez sur **Terminer** et **OK**.

    | Attribut LDAP | Type de revendication sortante |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    Notez que ces noms ne s’affichent pas dans la liste déroulante Type de revendication sortante. Vous devez les saisir manuellement. (La liste déroulante est modifiable).

12.  Selon votre type de certificat, il se peut que vous deviez définir l’algorithme de hachage. Dans la fenêtre de propriétés de l’approbation de partie de confiance (Démo B2C), sélectionnez l’onglet **Avancé**, remplacez l’**Algorithme de hachage sécurisé** par `SHA-256`, puis cliquez sur **OK**.
13. Dans Gestionnaire de serveur, sélectionnez **Outils**, puis **Gestion AD FS**.
14. Sélectionnez l’approbation de partie de confiance que vous avez créée, choisissez **Mettre à jour à partir des métadonnées de fédération**, puis cliquez sur **Mettre à jour**.

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.
## <a name="troubleshooting-ad-fs-service"></a>Dépannage du service AD FS  

AD FS est configuré pour utiliser le journal des applications Windows. Si vous rencontrez des difficultés lors de la configuration d’AD FS en tant que fournisseur d’identité SAML à l’aide de stratégies personnalisées dans Azure AD B2C, vous pouvez consulter le journal des événements AD FS :

1. Dans le **Volet de recherche** Windows, tapez **Observateur d’événements**, puis sélectionnez l’application de bureau **Observateur d’événements**.
1. Pour afficher le journal d’un autre ordinateur, cliquez avec le bouton droit sur **Observateur d’événements (local)** . Sélectionnez **Se connecter à un autre ordinateur**, puis renseignez les champs de la boîte de dialogue **Sélectionner un ordinateur**.
1. Dans **l’Observateur d’événements**, ouvrez **Journaux des applications et des services**.
1. Sélectionnez **AD FS**, puis **Administrateur**. 
1. Pour obtenir plus de détails sur un événement particulier, double-cliquez sur cet événement.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>La demande SAML n’est pas signée avec l’événement d’algorithme de signature attendu

Cette erreur indique que la demande SAML envoyée par Azure AD B2C n’est pas signée avec l’algorithme de signature attendu configuré dans AD FS. Par exemple, la demande SAML est signée avec l’algorithme de signature `rsa-sha256`, tandis que l’algorithme de signature attendu est `rsa-sha1`. Pour résoudre ce problème, assurez-vous qu’Azure AD B2C et AD FS sont configurés avec le même algorithme de signature.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Option 1 : Définir l’algorithme de signature dans Azure AD B2C  

Vous pouvez configurer la manière de signer la demande SAML dans Azure AD B2C. Les métadonnées [XmlSignatureAlgorithm](saml-identity-provider-technical-profile.md#metadata) contrôlent la valeur du paramètre `SigAlg` (chaîne de requête ou paramètre d’envoi) dans la demande SAML. L’exemple suivant configure Azure AD B2C pour utiliser l’algorithme de signature `rsa-sha256`.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Option n°2 : Définir l’algorithme de signature dans AD FS 

Vous pouvez également configurer l’algorithme de signature de demande SAML attendu dans AD FS.

1. Dans Gestionnaire de serveur, sélectionnez **Outils**, puis **Gestion AD FS**.
1. Sélectionnez l'**approbation de la partie de confiance** que vous avez créée précédemment.
1. Sélectionner **Propriétés**, puis **Avancer**
1. Configurez l’**Algorithme de hachage sécurisé**, puis sélectionnez **OK** pour enregistrer les modifications.

::: zone-end
