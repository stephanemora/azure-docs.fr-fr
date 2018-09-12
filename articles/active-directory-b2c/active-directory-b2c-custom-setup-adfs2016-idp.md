---
title: Ajouter ADFS en tant que fournisseur d’identité SAML en utilisant les stratégies personnalisées dans Azure Active Directory B2C | Microsoft Docs
description: Configurer ADFS 2016 à l’aide du protocole SAML et de stratégies personnalisées dans Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669224"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Ajouter ADFS en tant que fournisseur d’identités SAML à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’un compte d’utilisateur ADFS à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Conditions préalables

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Ajouter la clé d’application de compte ADFS à Azure AD B2C

Une fédération avec un compte ADFS nécessite une clé secrète client pour que le compte ADFS puisse faire confiance à AAD B2C pour le compte de l’application. Vous devez enregistrer votre certificat ADFS dans votre client Azure AD B2C. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez **Changer de répertoire**, puis choisissez le répertoire contenant le locataire que vous avez créé. Ce didacticiel utilise le répertoire *contoso* contenant le locataire nommé *contoso0522Tenant.onmicrosoft.com*.

    ![Changer de répertoires](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**. Vous devriez maintenant utiliser votre locataire.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre locataire, puis cliquez sur **Ajouter**.
6. Choisissez l’option **Charger**.
7. Entrez le nom `ADFSSamlCert`. Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
8. Recherchez et sélectionnez votre fichier de certificat .pfx contenant la clé privée. Ce certificat contenant la clé privée doit être le même que celui émis et utilisé pour la partie de confiance d’ADFS.
9. Cliquez sur **Créer**, puis vérifiez que vous avez créé la clé `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Ajouter un fournisseur de revendications à une stratégie d’extension

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte ADFS, vous devez définir le compte comme fournisseur de revendications. Pour ce faire, spécifiez un point de terminaison avec lequel AAD B2C communique. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez ADFS comme fournisseur de revendications en ajoutant l’élément **ClaimsProvider** dans votre fichier de stratégie d’extension.

1. Dans votre répertoire de travail, ouvrez le fichier de stratégie *TrustFrameworkExtensions.xml*. Si vous avez besoin d’un éditeur XML, [essayez Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.
2. Ajoutez le code XML suivant sous l’élément **ClaimsProviders**, remplacez **your-ADFS-domain** par le nom de votre domaine ADFS, remplacez la valeur de la revendication de sortie **identityProvider** par votre serveur DNS (valeur arbitraire indiquant votre domaine), puis enregistrez le fichier. 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>Inscrire le fournisseur de revendications pour l’inscription et la connexion

Pour rendre le fournisseur d’identité de compte ADFS disponible dans les pages d’inscription et de connexion, vous devez l’ajouter à votre parcours utilisateur **SignUpOrSignIn**. 

Effectuez une copie d’un modèle de parcours utilisateur existant, puis modifiez-le pour qu’il inclue le fournisseur d’identité ADFS :

>[!NOTE]
>Si vous avez précédemment copié l’élément **UserJourneys**  à partir du fichier de base de votre stratégie vers le fichier d’extension (*TrustFrameworkExtensions.xml*), vous pouvez ignorer cette section.

1. Ouvrez le fichier de base de votre stratégie. Par exemple, ouvrez *TrustFrameworkBase.xml*.
2. Copiez le contenu entier de l’élément **UserJourneys**.
3. Ouvrez le fichier d’extension (*TrustFrameworkExtensions.xml*), et collez le contenu entier de l’élément **UserJourneys** que vous avez copié dans le fichier d’extension.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelections** définit la liste et l’ordre des sélections de fournisseur de revendications.  L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur une page d’inscription et la connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte ADFS, un nouveau bouton s’affiche quand un utilisateur voit la page. Pour ajouter cet élément :

1. Dans l’élément **UserJourney** avec un identificateur `SignUpOrSignIn` dans les parcours utilisateur que vous avez copiés, localisez l’élément **OrchestrationStep** de `Order="1"`.
2. Ajoutez l’élément **ClaimsProviderSelection** suivant sous l’élément **ClaimsProviderSelections** :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec le compte ADFS pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications compte ADFS :

1. Recherchez l’étape **OrchestrationStep** `Order="2"` sous l’élément **UserJourney**.
2. Ajoutez l’élément **ClaimsExchange** suivant sous l’élément **ClaimsExchanges** :

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Vérifiez que la valeur de `Id` est identique à la valeur de `TargetClaimsExchangeId` dans la section précédente.
> * Assurez-vous que `TechnicalProfileReferenceId` est défini sur le profil de technique que vous avez créé plus haut (Contoso-SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[Facultatif] Inscrire le fournisseur de revendications pour la modification du profil

Vous pouvez également ajouter le fournisseur d’identité de compte ADFS à votre parcours utilisateur de modification de profil.

### <a name="display-the-button"></a>Afficher le bouton

1. Ouvrez le fichier d’extension de votre stratégie. Par exemple, *TrustFrameworkExtensions.xml*.
2. Dans l’élément **UserJourney** avec un identificateur `ProfileEdit` dans les parcours utilisateur que vous avez copiés, localisez l’élément **OrchestrationStep** de `Order="1"`.
3. Ajoutez l’élément **ClaimsProviderSelection** suivant sous l’élément **ClaimsProviderSelections** :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

1. Recherchez l’étape **OrchestrationStep** `Order="2"` sous l’élément **UserJourney**.
2. Ajoutez l’élément **ClaimsExchange** suivant sous l’élément **ClaimsExchanges** :

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>Charger la stratégie sur un client

1. Dans le portail Azure, sélectionnez **Toutes les stratégies**.
2. Sélectionnez **Charger la stratégie**.
3. Activez **Remplacer la stratégie si elle existe**.
4. Recherchez et sélectionnez votre fichier de stratégie *TrustFrameworkExtensions.xml*, puis choisissez **Charger**. Assurez-vous que la validation a réussi.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurer une approbation de partie de confiance ADFS

Pour utiliser ADFS comme fournisseur d’identité dans AAD B2C, vous devez créer une approbation de partie de confiance ADFS avec les métadonnées SAML d’AAD B2C. L’exemple suivant montre une adresse URL aux métadonnées SAML d’un profil technique d’AAD B2C :

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Remplacez les valeurs suivantes :

- **your-tenant** par le nom de votre locataire, par exemple, your-tenant.onmicrosoft.com.
- **your-policy** par le nom de votre stratégie. Utilisez la stratégie dans laquelle vous configurez le profil technique du fournisseur SAML, ou une stratégie qui hérite de cette stratégie.
- **your-technical-profile** par le nom du profil technique de votre fournisseur d’identité SAML.
 
Ouvrez un navigateur et accédez à l’URL. Veillez à taper l’URL est correcte pour avoir accès au fichier de métadonnées XML.

Pour ajouter une nouvelle approbation de partie de confiance en utilisant le composant logiciel enfichable de gestion ADFS et configurer manuellement les paramètres, effectuez la procédure suivante sur un serveur de fédération. L’appartenance au groupe **Administrateurs** ou à un groupe équivalent sur l’ordinateur local est la condition minimale requise pour effectuer cette procédure. Examinez les informations relatives à l’utilisation des comptes et des appartenances de groupe appropriés dans la page consacrée aux [Groupes de domaine et locaux par défaut](http://go.microsoft.com/fwlink/?LinkId=83477).

1. Dans Gestionnaire de serveur, sélectionnez **Outils**, puis **Gestion ADFS**.
2. Sélectionnez **Ajouter une approbation de partie de confiance**.
3. Dans la page **Bienvenue**, choisissez **Prise en charge des revendications**, puis cliquez sur **Démarrer**.
4. Dans la page **Sélectionner une source de données**, sélectionnez **Importer des données sur la partie de confiance publiées en ligne ou sur un réseau local**, fournissez votre URL de métadonnées AAD B2C, puis cliquez sur **Suivant**.
5. Dans la page **Indiquer le nom complet**, entrez un **Nom d’affichage**. Sous **Notes**, entrez une description pour cette approbation de partie de confiance, puis cliquez sur **Suivant**.
6. Dans la page **Sélectionner une stratégie de contrôle d’accès**, choisissez une stratégie, puis cliquez sur **Suivant**.
7. Sur la page **Prêt à ajouter l’approbation**, passez en revue les paramètres, puis cliquez sur **Suivant** pour enregistrer vos informations d’approbation de partie de confiance.
8. Sur la page **Terminer**, cliquez sur **Fermer**, cette action affiche automatiquement la boîte de dialogue **Modifier les règles de revendication**.
9. Sélectionnez **Ajouter une règle**.  
10. Dans **Modèle de règle de revendication**, sélectionnez **Envoyer les attributs LDAP en tant que revendications**.
11. Fournissez un **Nom de règle de revendication**. Pour le **Magasin d’attributs** choisissez **Sélectionner Active Directory**, ajoutez les revendications suivantes, puis cliquez sur **Terminer** et **OK**.

    ![Définir les propriétés de règle](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  Selon votre type de certificat, il se peut que vous deviez définir l’algorithme de hachage. Dans la fenêtre de propriétés de l’approbation de partie de confiance (Démo B2C), sélectionnez l’onglet **Avancé**, modifiez l’**Algorithme de hachage sécurisé** en `SHA-1` ou `SHA-256`, puis cliquez sur **OK**.  

### <a name="update-the-relying-party-metadata"></a>Mettre à jour les métadonnées de la partie de confiance

La modification du profil technique SAML vous oblige à mettre à jour ADFS avec la version des métadonnées mises à jour. Vous n’avez pas besoin de mettre à jour les métadonnées lorsque vous créez l’application basée sur les revendications mais, lorsque vous apportez une modification, vous mettez à jour les métadonnées dans ADFS.

1. Dans Gestionnaire de serveur, sélectionnez **Outils**, puis **Gestion ADFS**.
2. Sélectionnez l’approbation de partie de confiance que vous avez créée, choisissez **Mettre à jour à partir des métadonnées de fédération**, puis cliquez sur **Mettre à jour**. 

### <a name="test-the-policy-by-using-run-now"></a>Tester la stratégie en utilisant Exécuter maintenant

1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.
2.  Ouvrez **B2C_1A_ProfileEdit**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**. Vous devriez être en mesure de vous connecter à l’aide de votre compte ADFS.

## <a name="download-the-complete-policy-files"></a>Télécharger les fichiers de stratégie complets

Facultatif : vous pouvez créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée après avoir effectué les étapes décrites dans [Prise en main des stratégies personnalisées](active-directory-b2c-get-started-custom.md). Pour des exemples de fichiers, voir [Exemples de fichiers de stratégie pour référence uniquement](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
