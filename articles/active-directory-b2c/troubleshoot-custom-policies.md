---
title: Résoudre les problèmes liés aux stratégies personnalisées dans Azure Active Directory B2C
description: Découvrez les approches pour résoudre les erreurs lorsque vous utilisez des stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103907"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Résoudre les problèmes liés aux stratégies personnalisées Azure AD B2C

Si vous utilisez des [stratégies personnalisées](custom-policy-overview.md) Azure Active Directory B2C (Azure AD B2C), vous pouvez rencontrer des difficultés avec le format XML du langage de la stratégie ou des problèmes d’exécution. Cet article décrit certains outils et conseils qui peuvent vous aider à identifier et résoudre les problèmes. 

Cet article est axé sur la résolution des problèmes de configuration de stratégie personnalisée Azure AD B2C. Il ne traite pas de l’application par partie de confiance ou de sa bibliothèque d’identités.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Présentation de l’ID de corrélation Azure AD B2C

L’ID de corrélation Azure AD B2C est une valeur d’identificateur unique jointe aux demandes d’autorisation. Il franchit toutes les étapes d’orchestration qu’un utilisateur doit exécuter. L’ID de corrélation vous permet d’effectuer les opérations suivantes :

- Identifier l’activité de connexion dans votre application et suivre les performances de votre stratégie
- Rechercher les journaux Azure Application Insights de la demande de connexion
- Transmettre l’ID de corrélation à votre API REST et l’utiliser pour identifier le flux de connexion 

L’ID de corrélation est modifié chaque fois qu’une nouvelle session est établie. Lors du débogage de vos stratégies, assurez-vous de fermer les onglets existants du navigateur. Vous pouvez également ouvrir un nouveau navigateur en mode privé.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Obtenir l’ID de corrélation Azure AD B2C

Vous trouverez l’ID de corrélation sur la page d’inscription ou de connexion Azure AD B2C. Dans votre navigateur, sélectionnez **afficher la source**. La corrélation apparaît sous la forme d’un commentaire en haut de la page.

![Capture d’écran de l’affichage de la source de la page de connexion Azure AD B2C.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Copiez l’ID de corrélation, puis poursuivez le flux de connexion. L’ID de corrélation permet d’observer le comportement de connexion. Pour plus d’informations, consultez [Résolution des problèmes avec Application Insights](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Répéter l’ID de corrélation Azure AD B2C

Vous pouvez inclure l’ID de corrélation dans vos jetons de Azure AD B2C. Pour inclure l’ID de corrélation :

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez la revendication de ville à l’élément **ClaimsSchema**.  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Ouvrez le fichier de la partie de confiance de votre stratégie. Exemple de fichier : <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. Après un parcours utilisateur réussi, la revendication de sortie est ajoutée au jeton et envoyée à l’application. Modifiez l’élément de profil technique dans la section de la partie de confiance pour ajouter la ville en tant que revendication de sortie.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Résolution des problèmes avec Application Insights

Pour diagnostiquer les problèmes liés à vos stratégies personnalisées, utilisez [Application Insights](troubleshoot-with-application-insights.md). Application Insights assure la trace de l’activité de votre parcours utilisateur de stratégie personnalisée. Il permet de diagnostiquer les exceptions et d’observer l’échange de revendications entre Azure AD B2C et les divers fournisseurs de revendications définis par des profils techniques, tels que les fournisseurs d’identité, les services basés sur l’API, le répertoire utilisateur Azure AD B2C et d’autres services.  

Nous vous recommandons d’installer l’[extension Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) pour [VS Code](https://code.visualstudio.com/). Grâce à l’extension Azure AD B2C, les journaux sont organisés pour vous par nom de stratégie, par ID de corrélation (Application Insights présente le premier chiffre de l’ID de corrélation) et par timestamp du journal. Cette fonctionnalité vous aide à trouver le journal pertinent en fonction du timestamp local et à voir le parcours utilisateur tel qu’il est exécuté par Azure AD B2C. 

> [!NOTE]
> - Il y a un court délai, généralement moins de cinq minutes, avant que les nouveaux journaux d’activité s’affichent dans Application Insights.
> - La communauté a développé l’extension Visual Studio Code pour Azure AD B2C afin d’aider les développeurs travaillant dans le domaine de l’identité. L’extension n’est pas prise en charge par Microsoft et est mise à disposition strictement telle quelle.

Un seul flux de connexion peut émettre plusieurs traces Azure Application Insights. Dans la capture d’écran suivante, la stratégie *B2C_1A_signup_signin* contient trois journaux. Chaque journal représente une partie du flux de connexion.

La capture d’écran suivante montre l’extension Azure AD B2C pour VS Code avec l’Explorateur de traces Azure Application Insights.

![Capture d’écran de l’extension Azure AD B2C pour VS Code avec trace Azure Application Insights.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Filtrer le journal des traces

Lorsque l’Explorateur de traces Azure AD B2C est sélectionné, commencez à taper le premier chiffre de l’ID de corrélation ou une heure à rechercher. Vous verrez une zone de filtre dans le coin supérieur droit de l’Explorateur de traces Azure AD B2C qui affiche ce que vous avez tapé jusqu’à présent. Les journaux des traces correspondants sont mis en surbrillance.  

![Capture d’écran de la mise en surbrillance du filtre Explorateur de traces Azure AD B2C de l’extension Azure AD B2C.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Si vous pointez sur la zone de filtre et sélectionnez **Activer le filtre sur le type**, seuls les journaux des traces correspondants s’affichent. Utilisez le **bouton Effacer « X »** pour effacer le filtre.

![Capture d’écran du filtre Explorateur de traces Azure AD B2C de l’extension Azure AD B2C.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Détails du journal des traces d’Application Insights

Si vous sélectionnez une trace Azure Application Insights, l’extension ouvre la fenêtre **Détails d’Application Insights** avec les informations suivantes :

- **Application Insights** : informations génériques sur le journal des traces, notamment le nom de la stratégie, l’ID de corrélation, l’ID de trace Azure Application Insights et le timestamp de la trace.
- **Profils techniques** : liste des profils techniques qui s’affichent dans le journal des traces.
- **Revendications** : liste alphabétique des revendications qui s’affichent dans le journal des traces et leurs valeurs. Si une revendication apparaît plusieurs fois dans le journal des traces avec des valeurs différentes, un signe `=>` désigne la valeur la plus récente. Vous pouvez passer ces revendications en revue pour déterminer si les valeurs de revendication attendues sont correctement définies. Par exemple, si vous avez une condition préalable qui vérifie une valeur de revendication, la section des revendications peut vous aider à déterminer la raison pour laquelle un flux attendu se comporte différemment.
- **Transformation des revendications** : liste des transformations de revendications qui apparaissent dans le journal des traces. Chaque transformation de revendications contient les revendications d’entrée, les paramètres d’entrée et les revendications de sortie. La section de transformation des revendications permet d’obtenir des informations sur les données envoyées et le résultat de la transformation des revendications.
- **Jetons** : liste des jetons qui s’affichent dans le journal des traces. Les jetons incluent les jetons des fournisseurs d’identité OAuth et OpenId Connect fédérés sous-jacents. Le jeton du fournisseur d’identité fédéré fournit des détails sur la façon dont le fournisseur d’identité renvoie les revendications à Azure AD B2C afin que vous puissiez mapper les revendications de sortie du profil technique du fournisseur d’identité. 
- **Exceptions** : liste des exceptions ou erreurs irrécupérables qui apparaissent dans le journal des traces.
- **Application Insights JSON** : données brutes retournées par Application Insights.

La capture d’écran suivante montre un exemple de fenêtre des détails du journal des traces Application Insights.  

![Capture d’écran du rapport des traces Azure AD B2C de l’extension Azure AD B2C.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Résoudre les problèmes de jetons JWT

Pour la validation et le débogage du jeton JWT, vous pouvez décoder JWT via un site comme [https://jwt.ms](https://jwt.ms). Créez une application de test pouvant rediriger vers `https://jwt.ms` pour l’inspection des jetons. Si ce n’est déjà fait, [inscrivez une application web](tutorial-register-applications.md) et [activez l’octroi implicite de jeton d’ID](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Capture d’écran de la préversion des jetons JWT.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Utilisez **Exécuter maintenant** et `https://jwt.ms` pour tester vos stratégies indépendamment de votre application web ou mobile. Ce site web agit comme une application par partie de confiance. Il affiche le contenu du jeton JWT (JSON Web Token) généré par votre stratégie Azure AD B2C.

## <a name="troubleshoot-saml-protocol"></a>Résoudre les problèmes de protocole SAML

Pour faciliter la configuration et le débogage de l’intégration avec un fournisseur de services, vous pouvez utiliser une extension de navigateur pour le protocole SAML, par exemple l’[Extension SAML DevTools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) pour Chrome, le [Traceur SAML](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) pour Firefox ou les [Outils de développement Edge ou IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

La capture d’écran suivante montre comment l’extension SAML DevTools présente la requête SAML qu’Azure AD B2C envoie au fournisseur d’identité et la réponse SAML.

![Capture d’écran du journal des traces du protocole SAML.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

À l’aide de ces outils, vous pouvez vérifier l’intégration entre votre application et Azure AD B2C. Par exemple :

- Vérifiez si la requête SAML contient une signature, et déterminez l’algorithme utilisé pour la connexion à la demande d’autorisation.
- Vérifiez si Azure AD B2C renvoie un message d’erreur.
- Vérifiez si la section d’assertion est chiffrée.
- Obtenez le nom des revendications renvoyées par le fournisseur d’identité.

Vous pouvez également assurer la trace de l’échange de messages entre votre navigateur client et Azure AD B2C avec [Fiddler](https://www.telerik.com/fiddler). Il peut vous aider à obtenir une indication de l’endroit où vos étapes d’orchestration échouent dans le parcours utilisateur.

## <a name="troubleshoot-policy-validity"></a>Résoudre les problèmes de validité de stratégie

Une fois que vous avez terminé le développement de votre stratégie, vous chargez la stratégie dans Azure AD B2C. Votre stratégie peut poser certains problèmes. Utilisez les méthodes suivantes pour garantir l’intégrité/la validité de votre stratégie.

L’erreur la plus courante lors de la configuration des stratégies personnalisées concerne la mise en forme du code XML. Un bon éditeur XML est quasiment essentiel. Il affiche le code XML en mode natif, applique un code couleur au contenu, prérenseigne les termes courants, maintient les éléments indexés et peut valider contre un schéma XML.

Nous vous recommandons d’utiliser [Visual Studio Code](https://code.visualstudio.com/). Installez ensuite une extension XML, telle que la [Prise en charge du langage XML par Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). L’extension XML vous permet de valider le schéma XML avant de charger votre fichier XML à l’aide de la définition de schéma [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) de stratégie personnalisée.

Vous pouvez utiliser la stratégie d’association de fichiers XML pour lier le fichier XML au fichier XSD en ajoutant les paramètres suivants dans votre fichier VS Code `settings.json`. Pour cela, procédez de la façon suivante :

1. Dans VS Code, cliquez sur **Paramètres**. Pour plus d’informations, consultez [Paramètres de l’utilisateur et de l’espace de travail](https://code.visualstudio.com/docs/getstarted/settings).
1. Recherchez **fileAssociations**, puis, sous l'**extension**, sélectionnez le **fichier XML**.
1. Sélectionnez **Modifier dans settings.json**.

    ![Capture d’écran de la validation de schéma XML VS Code.](./media/troubleshoot-custom-policies/xml-validation.png)
1. Dans settings.json, ajoutez le code JSON suivant :

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

L’exemple suivant montre une erreur de validation XML. Lorsque vous placez le pointeur de la souris sur le nom de l’élément, l’extension répertorie les éléments attendus.

![Capture d’écran de l’indicateur d’erreur de validation de schéma XML VS Code.](./media/troubleshoot-custom-policies/xml-validation-error.png)

Dans le cas suivant, l'élément `DisplayName` est correct, mais dans un ordre incorrect. `DisplayName` doit être avant l'élément `Protocol`. Pour résoudre le problème, déplacez votre souris sur l'élément `DisplayName` dans l’ordre correct des éléments.

![Capture d’écran de l’erreur d’ordre de validation de schéma XML VS Code.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Chargement et validation de stratégies

La validation du fichier de stratégie XML s’effectue automatiquement lors du chargement. La plupart des erreurs provoquent l’échec du chargement. La validation inclut le fichier de stratégie que vous chargez. Elle inclut également la chaîne de fichiers à laquelle le fichier de chargement fait référence (le fichier de stratégie de la partie de confiance, le fichier d’extensions et le fichier de base).

> [!TIP]
> Azure AD B2C exécute une validation supplémentaire pour la stratégie de la partie de confiance. Lorsque vous rencontrez un problème avec votre stratégie, même si vous modifiez uniquement la stratégie d’extension, il est recommandé de charger également la stratégie de la partie de confiance. 

Cette section contient les erreurs de validation courantes et les solutions probables.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Erreur de validation de schéma détectée... comporte un élément enfant non valide « {name} »

Votre stratégie contient un élément XML non valide ou l’élément XML est valide, mais semble être dans un ordre incorrect. Pour corriger ce type d’erreur, consultez la section [Résoudre les problèmes de validité de stratégie](#troubleshoot-policy-validity).

### <a name="there-is-a-duplicate-key-sequence-number"></a>Une séquence de clés « {number} » existe en double 

Un [parcours](userjourneys.md) ou un [sous-parcours](subjourneys.md) utilisateur est composé d’une liste ordonnée d’étapes d’orchestration qui sont exécutées de manière séquentielle. Après avoir renommé votre parcours, renumérotez les étapes séquentiellement sans ignorer les entiers de 1 à N.

> [!TIP]
> Vous pouvez utiliser la commande `(Shift+Ctrl+r)` de l'[extension Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) pour [VS Code](https://code.visualstudio.com/) afin de renuméroter toutes les étapes de l’orchestration des parcours et des sous-parcours utilisateur dans votre stratégie.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... était supposé avoir une étape avec l’ordre « {number} », mais est introuvable...

Vérifiez l’erreur précédente.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>Ordre des étapes d’orchestration « {number} » dans le parcours utilisateur « {name} »... est suivi d’une étape de sélection du fournisseur de revendications et doit être un échange de revendications, mais est de type...

Le type des étapes d’orchestration de `ClaimsProviderSelection` et `CombinedSignInAndSignUp` contient une liste d’options parmi lesquelles un utilisateur peut effectuer un choix. Doit être suivi par le type `ClaimsExchange` avec un ou plusieurs échanges de revendications.

Les étapes d’orchestration suivantes provoquent ce type ou cette erreur. La deuxième étape d’orchestration doit être de type `ClaimsExchange`, et non `ClaimsProviderSelection`.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... étape {number} avec 2 échanges de revendications. Elle doit être précédée d’une sélection de fournisseurs de revendications afin de déterminer l’échange de revendications qui peut être utilisé

Le type d’une étape d’orchestration `ClaimsExchange` doit avoir une seule `ClaimsExchange`, à moins que l’étape précédente soit de type `ClaimsProviderSelection` ou `CombinedSignInAndSignUp`. Les étapes d’orchestration suivantes provoquent ce type d’erreur. La sixième étape contient deux échanges de revendications. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Pour corriger ce type d’erreur, utilisez deux étapes d’orchestration. Chaque étape d’orchestration avec un échange de revendications.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Une séquence de clés « {name} » existe en double

Un parcours comporte plusieurs `ClaimsExchange` avec le même `Id`. Les étapes suivantes provoquent ce type d’erreur. L’ID *AADUserWrite* apparaît deux fois dans le parcours utilisateur.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Pour corriger ce type d’erreur, remplacez l’échange de revendications des étapes d’orchestration par un nom unique, par exemple *Call-REST-API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... fait référence à ClaimType avec l’ID « {claim name} », mais ni la stratégie, ni aucune de ses stratégies de base ne contient un tel élément

Ce type d’erreur se produit lorsque votre stratégie fait référence à une revendication qui n’est pas déclarée dans le [schéma de revendications](claimsschema.md). Les revendications doivent être définies dans au moins l’un des fichiers de la stratégie. 

Par exemple, un profil technique avec la revendication de sortie *schoolId*. Toutefois, la revendication de sortie *schoolId* n’est jamais déclarée dans la stratégie ni dans une stratégie ancêtre.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Pour corriger ce type d’erreur, vérifiez si la valeur `ClaimTypeReferenceId` est mal orthographiée ou n’existe pas dans le schéma. La revendication est définie dans la stratégie d’extensions, mais elle est également utilisée dans la stratégie de base. Assurez-vous que la revendication est définie dans la stratégie utilisée ou dans une stratégie de niveau supérieur.

L’ajout de la revendication au schéma de revendications résout ce type d’erreur.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... fait référence à un élément ClaimsTransformation avec l’ID...

La cause de cette erreur est similaire à celle de l’erreur de revendication. Vérifiez l’erreur précédente.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>L’utilisateur est actuellement connecté en tant qu’utilisateur du locataire « yourtenant.onmicrosoft.com »...

Vous vous connectez avec un compte d’un locataire qui est différent de la stratégie que vous essayez de charger. Par exemple, vous vous connectez avec admin@contoso.onmicrosoft.com, tandis que votre stratégie `TenantId` est définie sur `fabrikam.onmicrosoft.com`.

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Vérifiez que la valeur `TenantId` des éléments `<TrustFrameworkPolicy\>` et `<BasePolicy\>` correspond à votre locataire Azure AD B2C cible.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>Le type de revendication « {name} » est la revendication de sortie du profil technique de la partie de confiance, mais il ne s’agit pas d’une revendication de sortie dans l’une des étapes du parcours utilisateur...

Dans une stratégie de partie de confiance, vous avez ajouté une revendication de sortie, mais la revendication de sortie n’est pas une revendication de sortie dans l’une des étapes du parcours utilisateur. Azure AD B2C ne peut pas lire la valeur de revendication à partir du conteneur de revendications.

Dans l’exemple suivant, la revendication *schoolId* est une revendication de sortie du profil technique de la partie de confiance, mais il ne s’agit pas d’une revendication de sortie dans l’une des étapes du parcours utilisateur *SignUpOrSignIn*.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Pour corriger ce type d’erreur, assurez-vous que les revendications de sortie s’affichent dans au moins une collection de revendications de sortie du profil technique des étapes de l’orchestration. Si votre parcours utilisateur ne peut pas générer la revendication, dans le profil technique de la partie de confiance, définissez une valeur par défaut, telle qu’une chaîne vide.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>Le format de la chaîne d'entrée est incorrect

Vous définissez un type de valeur incorrect pour une revendication d’un autre type. Par exemple, vous définissez une revendication d’entier.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Ensuite, vous tentez de définir une valeur de chaîne :

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Pour corriger ce type d’erreur, assurez-vous de définir la valeur correcte, par exemple `DefaultValue="0"`.


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>Le locataire « {name} » a déjà une stratégie avec l’ID « {name} ». Une autre stratégie avec le même ID ne peut pas être stockée

Vous essayez de charger une stratégie sur votre locataire, mais une stratégie portant le même nom est déjà chargée dans votre locataire. 

Pour corriger ce type d’erreur, lorsque vous chargez la stratégie, cochez la case **Remplacer la stratégie personnalisée si elle existe déjà**.

![Capture d’écran illustrant la procédure de remplacement de la stratégie personnalisée si elle existe déjà.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Collecter les journaux Azure Active Directory B2C avec Application Insights](troubleshoot-with-application-insights.md).

