---
title: Échanges de revendications de l’API REST - Azure Active Directory B2C
description: Ajoutez des échanges de revendications de l’API REST aux stratégies personnalisées dans Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1caf0b3f6396cc008e77bb96e686fe78f462850b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76851124"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Ajouter des échanges de revendications d’API REST aux stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vous pouvez ajouter une interaction avec une API RESTful à vos [stratégies personnalisées](custom-policy-overview.md) dans Azure Active Directory B2C (Azure AD B2C). Cet article vous montre comment créer un parcours utilisateur Azure AD B2C qui interagit avec les services RESTful.

Cette interaction inclut un échange de revendications d’informations entre les revendications de l’API REST et Azure AD B2C. Les échanges de revendications présentent les caractéristiques suivantes :

- Peut être conçue comme une étape d’orchestration.
- Peut déclencher une action externe. Par exemple, elle peut enregistrer un événement dans une base de données externe.
- Peut être utilisée pour extraire une valeur puis la stocker dans la base de données utilisateur.
- Peut modifier le flux d’exécution.

Le scénario qui est représenté dans cet article inclut les actions suivantes :

1. Rechercher l’utilisateur dans un système externe.
2. Obtenir la ville dans laquelle cet utilisateur est inscrit.
3. Retourner cet attribut à l’application sous forme de revendication.

## <a name="prerequisites"></a>Conditions préalables requises

- Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md).
- Un point de terminaison API REST avec lequel vous allez interargir. Cet article utilise une fonction Azure simple comme exemple. Pour créer la fonction Azure, consultez [Créer votre première fonction à l’aide du Portail Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Préparer l’API

Dans cette section, vous préparez la fonction Azure pour recevoir une valeur pour `email`, puis retournez la valeur de `city` qui peut être utilisée par Azure AD B2C comme revendication.

Modifiez le fichier run.csx pour la fonction Azure que vous avez créée pour utiliser le code suivant :

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

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

## <a name="configure-the-claims-exchange"></a>Configurer l’échange de revendications

Un profil technique fournit la configuration pour l’échange de revendications.

Ouvrez le fichier *TrustFrameworkExtensions.xml* et ajoutez l’élément XML **ClaimsProvider** suivant dans l’élément **ClaimsProviders**.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
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

L’élément **InputClaims** définit les revendications qui sont envoyées au service REST. Dans cet exemple, la valeur de la revendication `givenName` est envoyée au service REST en tant que revendication `email`. L’élément **OutputClaims** définit les revendications qui sont attendues du service REST.

Les commentaires ci -dessus `AuthenticationType` et `AllowInsecureAuthInProduction` indiquent les modifications que vous devez effectuer lorsque vous passez à un environnement de production. Pour en savoir plus sur la sécurisation de vos API RESTful pour la production, consultez la page [Sécuriser les API RESTful avec une authentification de base](secure-rest-api-dotnet-basic-auth.md) et [Sécuriser les API RESTful avec une authentification par certificat](secure-rest-api-dotnet-certificate-auth.md).

## <a name="add-the-claim-definition"></a>Ajouter la définition de revendication

Ajoutez une définition pour `city` à l’intérieur de l’élément **BuildingBlocks**. Vous pouvez trouver cet élément au début du fichier TrustFrameworkExtensions.xml.

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

Ajoutez une étape dans le parcours utilisateur de modification de profil. Une fois que l’utilisateur a été authentifié (étapes d’orchestration 1 à 4 du fichier XML suivant) et qu’il a fourni les informations de profil mises à jour (étape 5). Copiez le code XML du parcours utilisateur de modification de profil du fichier *TrustFrameworkBase.xml* vers votre fichier *TrustFrameworkExtensions.xml*, à l’intérieur de l’élément **UserJourneys**. Effectuez ensuite la modification dans l’étape 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Le code XML final pour le parcours utilisateur doit ressembler à l’exemple suivant :

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
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Ajouter la revendication

Modifiez le fichier *ProfileEdit.xml* et ajoutez `<OutputClaim ClaimTypeReferenceId="city" />` à l’élément **OutputClaims**.

Une fois que vous avez ajouté la nouvelle revendication, le profil technique ressemble à l’exemple suivant :

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Charger et tester vos modifications

1. (Facultatif :) Enregistrez la version existante des fichiers (en la téléchargeant) avant de continuer.
2. Chargez *TrustFrameworkExtensions.xml* et *ProfileEdit.xml*, et sélectionnez le remplacement du fichier existant.
3. Sélectionnez **B2C_1A_ProfileEdit**.
4. Pour **sélectionner une application** dans la page de présentation de la stratégie personnalisée, sélectionnez l’application web nommée *webapp1* que vous avez inscrite précédemment. Assurez-vous que l’**URL de réponse** soit `https://jwt.ms`.
4. Sélectionnez **Exécuter maintenant**. Connectez-vous avec vos informations d’identification de compte et cliquez sur **Continuer**.

Si tout est bien configuré, le jeton inclut la nouvelle revendication `city`, avec la valeur `Redmond`.

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

Vous pouvez aussi concevoir l’interaction comme un profil de validation. Pour plus d’informations, consultez [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur](custom-policy-rest-api-claims-validation.md).

[Changer la modification de profil pour rassembler des informations supplémentaires de vos utilisateurs](custom-policy-custom-attributes.md)

[Reference : Profil technique RESTful](restful-technical-profile.md)

Pour en savoir plus sur la sécurisation de vos API, consultez les articles suivants :

* [Azure Active Directory B2C: Secure your RESTful services using HTTP basic authentication](secure-rest-api-dotnet-basic-auth.md) (Azure Active Directory B2C : Sécuriser vos services RESTful à l’aide de l’authentification HTTP de base)
* [Sécuriser votre API RESTful avec des certificats clients](secure-rest-api-dotnet-certificate-auth.md)
