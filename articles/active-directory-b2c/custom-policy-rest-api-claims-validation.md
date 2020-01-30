---
title: Échanges de revendications de l’API REST en tant que validation
titleSuffix: Azure AD B2C
description: Procédure à suivre pour créer un parcours utilisateur Azure AD B2C qui interagit avec les services RESTful.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 209cddcc8648f7d396f048057bb4367e2ae9f66c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850360"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L’infrastructure d’expérience d’identité sur laquelle repose Azure Active Directory B2C (Azure AD B2C) permet au développeur d’identité d’intégrer une interaction avec une API RESTful dans un parcours utilisateur.

À la fin de cette procédure pas à pas, vous pourrez créer un parcours utilisateur Azure AD B2C qui interagit avec les services RESTful.

L’IEF envoie des données dans des revendications et reçoit en retour des données via des revendications. L’interaction avec l’API :

- Peut être conçue comme un échange de revendications de l’API REST ou comme un profil de validation, qui s’effectue dans une étape d’orchestration.
- Valide en général une entrée de l’utilisateur. Si la valeur de l’utilisateur est rejetée, il peut essayer à nouveau d’entrer une valeur valide, avec la possibilité de retourner un message d’erreur.

Vous pouvez aussi concevoir l’interaction comme une étape d’orchestration. Pour plus d’informations, consultez [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration](custom-policy-rest-api-claims-exchange.md).

Pour l’exemple de profil de validation, nous allons utiliser le parcours utilisateur de modification de profil dans le fichier ProfileEdit.xml du pack de démarrage.

Nous pouvons vérifier que le nom fourni par l’utilisateur dans la modification de profil ne figure sur aucune liste d’exclusions.

## <a name="prerequisites"></a>Conditions préalables requises

- Un locataire Azure AD B2C configuré pour effectuer une inscription/connexion à un compte local, comme décrit dans [Bien démarrer](custom-policy-get-started.md).
- Un point de terminaison API REST avec lequel vous allez interargir. Pour cette procédure pas à pas, nous avons configuré un site de démonstration appelé [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) avec un service de l’API REST.

## <a name="step-1-prepare-the-rest-api-function"></a>Étape 1 : Préparer la fonction de l’API REST

> [!NOTE]
> Cet article ne traite pas de la configuration des fonctions de l’API REST. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) fournit un excellent kit de ressources pour la création de services RESTful dans le cloud.

Nous avons créé une fonction Azure qui reçoit une revendication qu’elle attend en tant que `playerTag`. La fonction vérifie si cette revendication existe. Vous pouvez accéder au code complet de la fonction Azure dans [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

L’infrastructure d’expérience d’identité attend la revendication `userMessage` retournée par la fonction Azure. Cette revendication est présentée sous forme de chaîne à l’utilisateur si la validation échoue, comme quand un état de conflit 409 est retourné dans l’exemple précédent.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Étape 2 : Configurer l’échange de revendications de l’API RESTful comme profil technique dans votre fichier TrustFrameworkExtensions.xml

Le profil technique est la configuration complète de l’échange souhaité avec le service RESTful. Ouvrez le fichier TrustFrameworkExtensions.xml et ajoutez l’extrait de code XML suivant à l’intérieur de l’élément `<ClaimsProviders>`.

> [!NOTE]
> Dans le code XML suivant, le fournisseur RESTful `Version=1.0.0.0` est décrit comme étant le protocole. Considérez-le comme étant la fonction qui interagit avec le service externe. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

L’élément `InputClaims` définit les revendications qui seront envoyées depuis l’IEF vers le service REST. Dans cet exemple, le contenu de la revendication `givenName` sera envoyé au service REST en tant que `playerTag`. Dans cet exemple, l’infrastructure d’expérience d’identité n’attend pas de revendications en retour. Au lieu de cela, elle attend une réponse du service REST et agit en fonction des codes d’état qu’elle reçoit.

Les commentaires ci -dessus `AuthenticationType` et `AllowInsecureAuthInProduction` indiquent les modifications que vous devez effectuer lorsque vous passez à un environnement de production. Pour en savoir plus sur la sécurisation de vos API RESTful pour la production, consultez la page [Sécuriser les API RESTful avec une authentification de base](secure-rest-api-dotnet-basic-auth.md) et [Sécuriser les API RESTful avec une authentification par certificat](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Étape 3 : Intégrer l’échange de revendications du service RESTful dans le profil technique autodéclaré où vous voulez valider l’entrée de l’utilisateur

L’étape de validation est le plus souvent utilisée dans le cadre d’une interaction avec un utilisateur. Toutes les interactions où une saisie de l’utilisateur est attendue sont des *profils techniques autodéclarés*. Pour cet exemple, nous ajoutons la validation au profil technique Self-Asserted-ProfileUpdate. Il s’agit du profil technique utilisé par le fichier de stratégie de la partie de confiance `Profile Edit`.

Pour ajouter l’échange de revendications au profil technique autodéclaré :

1. Ouvrez le fichier TrustFrameworkBase.xml et recherchez `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Passez en revue la configuration de ce profil technique. Notez comment l’échange avec l’utilisateur est défini sous la forme de revendications demandées à l’utilisateur (revendications d’entrée) et de revendications attendues en retour par le fournisseur autodéclaré (revendications de sortie).
3. Recherchez `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`. Notez que ce profil est appelé au titre de l’étape d’orchestration 5 de `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Étape 4 : Charger et tester le fichier de stratégie de partie de confiance de modification de profil

1. Chargez la nouvelle version du fichier TrustFrameworkExtensions.xml.
2. Sélectionnez **Exécuter maintenant** pour tester le fichier de stratégie de partie de confiance Modification de profil.
3. Testez la validation en fournissant un des noms existants (par exemple mcvinny) dans le champ **Prénom**. Si tout est correctement configuré, vous devez normalement recevoir un message indiquant à l’utilisateur que l’étiquette player est déjà utilisée.

## <a name="next-steps"></a>Étapes suivantes

[Changer la modification de profil et l’inscription des utilisateurs pour collecter des informations supplémentaires auprès de vos utilisateurs](custom-policy-custom-attributes.md)

[Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration](custom-policy-rest-api-claims-exchange.md)

[Reference : Profil technique RESTful](restful-technical-profile.md)

Pour en savoir plus sur la sécurisation de vos API, consultez les articles suivants :

* [Azure Active Directory B2C: Secure your RESTful services using HTTP basic authentication](secure-rest-api-dotnet-basic-auth.md) (Azure Active Directory B2C : Sécuriser vos services RESTful à l’aide de l’authentification HTTP de base)
* [Sécuriser votre API RESTful avec des certificats clients](secure-rest-api-dotnet-certificate-auth.md)
