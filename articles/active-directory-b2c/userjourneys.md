---
title: UserJourneys | Microsoft Docs
description: Spécifiez l’élément UserJourneys d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e09435b09811ef31057f4dc257fc55fa72909d83
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714907"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Les parcours utilisateur spécifient des chemins explicites par le biais desquels une stratégie autorise une application par partie de confiance à obtenir les revendications souhaitées pour un utilisateur. L’utilisateur est guidé par ces chemins pour récupérer les revendications qui doivent être présentés à la partie de confiance. Autrement dit, les parcours utilisateur définissent la logique métier de ce par quoi passe un utilisateur final pendant que l’Infrastructure d’expérience d’identité Azure AD B2C traite la requête.

Ces parcours utilisateur peuvent être considérés comme des modèles disponibles pour répondre aux besoins fondamentaux des différentes parties de confiance de la communauté d’intérêt. Les parcours utilisateur facilitent la définition de la portion « partie de confiance » d’une stratégie. Une stratégie peut définir plusieurs parcours utilisateur. Chaque parcours utilisateur est une séquence d’étapes d’orchestration.

Pour définir les parcours utilisateur pris en charge par la stratégie, un élément **UserJourneys** est ajouté sous l’élément de niveau supérieur du fichier de stratégie. 

L’élément **UserJourneys** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Parcours utilisateur qui définit toutes les constructions nécessaires pour un flux d’utilisateur complet. | 

L’élément **UserJourney** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ID | Oui | Identificateur d’un parcours utilisateur qui peut être utilisé pour le référencer à partir d’autres éléments dans la stratégie. L’élément **DefaultUserJourney** de la [stratégie de partie de confiance](relyingparty.md) pointe vers cet attribut. |

L’élément **UserJourney** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Séquence d’orchestration qui doit être suivie pour que la transaction réussisse. Chaque parcours utilisateur est composé d’une liste ordonnée d’étapes d’orchestration qui sont exécutées de manière séquentielle. Si une étape échoue, la transaction échoue. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Un parcours utilisateur est représenté en tant que séquence d’orchestration qui doit être suivie pour que la transaction réussisse. Si une étape échoue, la transaction échoue. Ces étapes d’orchestration référencent à la fois les composants et les fournisseurs de revendications autorisés dans le fichier de stratégie. Toute étape d’orchestration responsable de l’affichage d’une expérience utilisateur a également une référence à l’identificateur de définition de contenu correspondant.

Étapes d’orchestration peuvent être conditionnelle exécutées, selon les conditions préalables définies dans l’élément d’étape d’orchestration. Par exemple, vous pouvez vérifier pour effectuer une étape d’orchestration uniquement si des revendications spécifiques existe, si une revendication est égale ou ne pas la valeur spécifiée. 

Pour spécifier la liste ordonnée d’étapes d’orchestration, un élément **OrchestrationSteps** est ajouté à la stratégie. Cet élément est obligatoire.

L’élément **OrchestrationSteps** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Étape d’orchestration ordonnée. | 

L’élément **OrchestrationSteps** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| `Order` | Oui | Ordre des étapes d’orchestration. | 
| `Type` | Oui | Type de l’étape d’orchestration. Valeurs possibles : <ul><li>**ClaimsProviderSelection** : indique que l’étape d’orchestration présente divers fournisseurs de revendications à l’utilisateur afin qu’il en sélectionne un.</li><li>**CombinedSignInAndSignUp** : indique que l’étape d’orchestration présente une page combinée d’inscription de compte local et de connexion au fournisseur d’identité sociale.</li><li>**ClaimsExchange** : indique que l’étape d’orchestration échange des revendications avec un fournisseur de revendications.</li><li>**SendClaims** : indique que l’étape d’orchestration envoie les revendications à la partie de confiance avec un jeton émis par un émetteur de revendications.</li></ul> | 
| ContentDefinitionReferenceId | Non  | Identificateur de la [définition de contenu](contentdefinitions.md) associée à cette étape d’orchestration. L’identificateur de référence de définition de contenu est généralement défini dans le profil technique autodéclaré, mais il existe certains cas où Azure AD B2C doit afficher quelque chose sans profil technique, par exemple si le type de l’étape d’orchestration est `ClaimsProviderSelection` ou `CombinedSignInAndSignUp`. Azure AD B2C doit afficher la sélection du fournisseur d’identité sans avoir de profil technique. | 
| CpimIssuerTechnicalProfileReferenceId | Non  | Le type de l’étape d’orchestration est `SendClaims`. Cette propriété définit l’identificateur de profil technique du fournisseur de revendications qui émet le jeton pour la partie de confiance.  Si elle est absente, aucun jeton de partie de confiance n’est créé. |


L’élément **OrchestrationStep** peut contenir les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- | 
| Preconditions | 0:n | Liste de conditions préalables qui doivent être remplies pour que l’étape d’orchestration s’exécute. | 
| ClaimsProviderSelections | 0:n | Liste de sélection de fournisseur de revendications pour l’étape d’orchestration. | 
| ClaimsExchanges | 0:n | Liste d’échanges de revendications pour l’étape d’orchestration. | 

### <a name="preconditions"></a>Preconditions

L’élément **Preconditions** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- | 
| Precondition | 0:n | En fonction du profil technique utilisé, redirige le client d’après la sélection de fournisseur de revendications ou effectue un appel au serveur pour échanger des revendications. | 


#### <a name="precondition"></a>Precondition

L’élément **Precondition** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| `Type` | Oui | Type de vérification ou de requête à exécuter pour cette condition préalable. La valeur peut être **ClaimsExist**, qui indique que les actions doivent être effectuées si les revendications spécifiées existent dans le jeu de revendications actuel de l’utilisateur, ou **ClaimEquals**, qui indique que les actions doivent être effectuées si la revendication spécifiée existe et que sa valeur est égale à la valeur spécifiée. |
| `ExecuteActionsIf` | Oui | Utilisez un test true ou false pour décider si les actions mentionnées dans la condition préalable doivent être effectuées. | 

L’élément **Precondition** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| Valeur | 1:n | ClaimTypeReferenceId à interroger. Un autre élément de valeur contient la valeur à vérifier.</li></ul>|
| Action | 1:1 | Action à effectuer si la vérification de condition préalable dans une étape d’orchestration a la valeur true. Si la valeur de `Action` est `SkipThisOrchestrationStep`, l’élément `OrchestrationStep` associé ne doit pas être exécuté. | 

#### <a name="preconditions-examples"></a>Exemples de conditions préalables

Les conditions préalables suivantes vérifient l’existence de l’objectId de l’utilisateur. Dans le parcours utilisateur, l’utilisateur a choisi de se connecter à l’aide d’un compte local. Si l’objectId existe, ignorez cette étape d’orchestration.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Les conditions préalables suivantes vérifient si l’utilisateur s’est connecté avec un compte social. Une tentative est effectuée pour trouver le compte d’utilisateur dans l’annuaire. Si l’utilisateur se connecte ou s’inscrit avec un compte local, ignorez cette étape d’orchestration.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Plusieurs conditions préalables peuvent être vérifiées. L’exemple suivant vérifie l’existence d’« objectId » et d’« email ». Si la première condition est true, le parcours passe à l’étape d’orchestration suivante.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>      
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Une étape d’orchestration de type `ClaimsProviderSelection` ou `CombinedSignInAndSignUp` peut contenir une liste de fournisseurs de revendications avec lesquels un utilisateur peut se connecter. L’ordre des éléments à l’intérieur des éléments `ClaimsProviderSelections` détermine l’ordre des fournisseurs d’identité présentés à l’utilisateur.

L’élément **ClaimsProviderSelection** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 0:n | Fournit la liste des fournisseurs de revendications qui peuvent être sélectionnés.|

L’élément **ClaimsProviderSelection** contient les attributs suivants : 

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Non  | Identificateur de l’échange de revendications, qui est exécuté à l’étape d’orchestration suivante de la sélection du fournisseur de revendications. Cet attribut ou l’attribut ValidationClaimsExchangeId doit être spécifié, mais pas les deux. | 
| ValidationClaimsExchangeId | Non  | Identificateur de l’échange de revendications, qui est exécuté lors de l’étape d’orchestration en cours afin de valider la sélection du fournisseur de revendications. Cet attribut ou l’attribut TargetClaimsExchangeId doit être spécifié, mais pas les deux. |

### <a name="claimsproviderselection-example"></a>Exemple de ClaimsProviderSelection

Dans l’étape suivante de l’orchestration, l’utilisateur peut choisir pour vous connecter avec Facebook, LinkedIn, Twitter, Google ou un compte local. Si l’utilisateur sélectionne l’un des fournisseurs d’identité sociale, la deuxième étape d’orchestration s’exécute avec l’échange de revendication sélectionné spécifié dans l’attribut `TargetClaimsExchangeId`. La deuxième étape d’orchestration redirige l’utilisateur vers le fournisseur d’identité sociale pour terminer le processus de connexion. Si l’utilisateur choisit de se connecter avec le compte local, Azure AD B2C reste à la même étape d’orchestration (la même page d’inscription ou de connexion) et ignore la deuxième étape d’orchestration.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange" 
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

L’élément **ClaimsExchanges** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| ClaimsExchange | 0:n | En fonction du profil technique utilisé, redirige le client conformément à l’élément ClaimsProviderSelection sélectionné, ou effectue un appel au serveur pour échanger des revendications. | 

L’élément **ClaimsExchange** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ID | Oui | Identificateur de l’étape d’échange de revendications. L’identificateur est utilisé pour référencer l’échange de revendications à partir d’une étape de sélection de fournisseur de revendications dans la stratégie. | 
| TechnicalProfileReferenceId | Oui | Identificateur du profil technique qui doit être exécuté. |
