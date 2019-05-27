---
title: Revendications d’API REST échanges - Azure Active Directory B2C | Microsoft Docs
description: Ajouter des échanges de revendications de l’API REST pour les stratégies personnalisées dans Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e705c12782310597ea14d5253aba8b6a1a004e6d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952784"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Ajouter des échanges de revendications de l’API REST pour les stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vous pouvez ajouter une interaction avec une API RESTful pour votre [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory (Azure AD) B2C. Cet article vous montre comment créer un parcours utilisateur Azure AD B2C qui interagit avec les services RESTful.

L’interaction inclut un échange de revendications d’informations entre les revendications de l’API REST et Azure AD B2C. Échanges de revendications présentent les caractéristiques suivantes :

- Peut être conçue comme une étape d’orchestration.
- Peut déclencher une action externe. Par exemple, elle peut enregistrer un événement dans une base de données externe.
- Peut être utilisée pour extraire une valeur puis la stocker dans la base de données utilisateur.
- Peut modifier le flux d’exécution. 

Le scénario est représenté dans cet article inclut les actions suivantes :

1. Rechercher l’utilisateur dans un système externe.
2. Obtenir la ville dans laquelle cet utilisateur est inscrit.
3. Retourner cet attribut à l’application sous forme de revendication.

## <a name="prerequisites"></a>Conditions préalables

- Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Un point de terminaison API REST avec lequel vous allez interargir. Ce utilise article un simple Azure fonctionner comme un exemple. Pour créer la fonction Azure, consultez [créer votre première fonction dans le portail Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Préparer l’API

Dans cette section, vous préparez la fonction Azure pour recevoir une valeur pour `email`et puis renvoyer la valeur de `city` qui peut être utilisé par Azure AD B2C en tant que revendication.

Modifiez le fichier run.csx pour la fonction Azure que vous avez créé pour utiliser le code suivant : 

```
public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Configuration de l’échange de revendications

Un profil technique fournit la configuration pour l’échange de revendications. 

Ouvrez le *TrustFrameworkExtensions.xml* fichier, puis ajoutez les éléments XML suivants à l’intérieur de la **ClaimsProvider** élément.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Le **InputClaims** élément définit les revendications qui sont envoyées au service REST. Dans cet exemple, la valeur de la revendication `givenName` est envoyé au service REST en tant que la revendication `email`. Le **OutputClaims** élément définit les revendications qui sont attendues du service REST.

## <a name="add-the-claim-definition"></a>Ajoutez la définition de revendication

Ajoutez une définition pour `city` à l’intérieur de la **BuildingBlocks** élément. Vous pouvez trouver cet élément au début du fichier TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Ajouter une étape d’orchestration

Vous pouvez utiliser l’appel de l’API REST comme étape d’orchestration dans de nombreux cas d’utilisation. Il peut par exemple être utilisé comme mise à jour pour un système externe une fois qu’un utilisateur a terminé une tâche, comme sa première inscription ou une mise à jour de son profil, afin de synchroniser les informations. Dans ce cas, il est utilisé pour transférer les informations fournies à l’application après la modification du profil.

Ajoutez une étape pour le parcours utilisateur modification de profil. Une fois que l’utilisateur est authentifié (étapes d’orchestration 1 à 4 dans le code XML suivant), et l’utilisateur a fourni les informations de profil mis à jour (étape 5). Copie le profil de modifier le code XML du parcours utilisateur à partir de la *TrustFrameworkBase.xml* de fichiers à votre *TrustFrameworkExtensions.xml* de fichiers à l’intérieur de la **UserJourneys** élément. Effectuez la modification en tant qu’étape 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Le XML pour le parcours utilisateur final doit ressembler à cet exemple :

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Ajoutez la revendication

Modifier le *ProfileEdit.xml* fichier, puis ajoutez `<OutputClaim ClaimTypeReferenceId="city" />` à la **OutputClaims** élément.

Après avoir ajouté la nouvelle revendication, le profil technique ressemble à cet exemple :

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Charger et tester vos modifications

1. (Facultatif :) Enregistrez la version existante (en la téléchargeant) des fichiers avant de continuer.
2. Télécharger le *TrustFrameworkExtensions.xml* et *ProfileEdit.xml* et sélectionnez cette option pour remplacer le fichier existant.
3. Sélectionnez **B2C_1A_ProfileEdit**.
4. Pour **sélectionnez application** dans la page Vue d’ensemble de la stratégie personnalisée, sélectionnez l’application web nommée *application Web 1* que vous avez inscrite précédemment. Assurez-vous que le **URL de réponse** est `https://jwt.ms`.
4. Sélectionnez **exécuter maintenant**. Connectez-vous avec vos informations d’identification de compte, puis cliquez sur **continuer**.

Si tout est correctement configuré, le jeton inclut la nouvelle revendication `city`, avec la valeur `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez aussi concevoir l’interaction comme un profil de validation. Pour plus d’informations, consultez [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur](active-directory-b2c-rest-api-validation-custom.md).
- [Changer la modification de profil pour rassembler des informations supplémentaires de vos utilisateurs](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
