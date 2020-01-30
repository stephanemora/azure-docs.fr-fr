---
title: Gestion de sessions d’authentification unique avec des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment gérer des sessions d’authentification unique (SSO) à l’aide de stratégies personnalisées dans Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ee32b13820cb50fc1649672b78b34e7e293d65b5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76851120"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestion de session d’authentification unique dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Le gestion de session d’authentification unique (SSO) dans Azure Active Directory B2C (Azure AD B2C) permet à un administrateur de contrôler l’interaction avec un utilisateur une fois celui-ci authentifié. Par exemple, l’administrateur peut contrôler si la sélection des fournisseurs d’identité s’affiche, ou si des détails de compte local doivent être entrés à nouveau. Cet article décrit comment configurer les paramètres d’authentification unique pour Azure AD B2C.

La gestion de session d’authentification unique comporte deux parties. La première a trait aux interactions de l’utilisateur directement avec Azure AD B2C, et la deuxième aux interactions de l’utilisateur avec des tiers externes, tels que Facebook. Azure AD B2C ne peut ni remplacer, ni contourner des sessions d’authentification unique qui pourraient être tenues par des parties externes. Au lieu de cela, l’itinéraire parcouru via Azure AD B2C pour accéder à la partie externe est « mémorisé », ce qui évite de devoir réinviter l’utilisateur à sélectionner son fournisseur d’identité sociale ou d’entreprise. La décision finale en lien avec l’authentification unique appartient au tiers externe.

La gestion de session d’authentification unique utilise la même sémantique que tout autre profil technique dans des stratégies personnalisées. Quand une étape d’orchestration est exécutée, le profil technique associé à l’étape est interrogé pour obtenir une référence `UseTechnicalProfileForSessionManagement`. S’il en existe une, le fournisseur de la session de l’authentification unique référencé est vérifié pour voir si l’utilisateur est un participant de la session. Dans l’affirmative, le fournisseur de session d’authentification unique est utilisé pour remplir à nouveau la session. De même, lors de l’exécution d’une étape d’orchestration est terminée, le fournisseur est utilisé pour stocker les informations de la session si un fournisseur de session d’authentification unique a été spécifié.

Azure AD B2C a défini un certain nombre de fournisseurs de session d’authentification unique qui peuvent être utilisés :

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Les classes de gestion de l’authentification unique sont spécifiées à l’aide de l’élément `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` d’un profil technique.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Comme son nom l’indique, ce fournisseur ne fait rien. Ce fournisseur peut être utilisé pour supprimer un comportement d’authentification unique pour un profil technique spécifique.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ce fournisseur peut être utilisé pour le stockage de revendications dans une session. Ce fournisseur est en général référencé dans un profil technique utilisé pour la gestion de comptes locaux. Lorsque vous utilisez le DefaultSSOSessionProvider pour stocker des revendications dans une session, vous devez vous assurer que toutes les revendications devant être retournées à l’application ou utilisées par les conditions préalables dans les étapes suivantes, sont stockées dans la session ou complétées par une lecture depuis le profil des utilisateurs dans le répertoire. Cela garantit que votre parcours d’authentification n’échouera pas sur les revendications manquantes.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Pour ajouter des revendications dans la session, utilisez l’élément `<PersistedClaims>` du profil technique. Lorsque le fournisseur est utilisé pour remplir à nouveau la session, les revendications persistantes sont ajoutées au jeu de revendications. `<OutputClaims>` est utilisé pour récupérer des revendications de la session.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ce fournisseur est utilisé pour supprimer l’écran permettant de choisir le fournisseur d’identité. Il est généralement référencé dans un profil technique configuré pour un fournisseur d’identité externe, tel que Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ce fournisseur est utilisé pour gérer les sessions SAML d’Azure AD B2C entre applications ainsi que les fournisseurs d’identité SAML externes.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Le profil technique contient deux éléments de métadonnées :

| Élément | Valeur par défaut | Valeurs possibles | Description
| --- | --- | --- | --- |
| IncludeSessionIndex | true | true/false | Indique au fournisseur que l’index de la session doit être stocké. |
| RegisterServiceProviders | true | true/false | Indique que le fournisseur doit inscrire tous les fournisseurs de services SAML auxquels une assertion a été envoyée. |

Lorsque vous utilisez le fournisseur pour stocker une session de fournisseur d’identité SAML, les éléments ci-dessus doivent tous deux avoir la valeur false. Lorsque vous utilisez le fournisseur pour stocker la session SAML B2C, les éléments ci-dessus doivent avoir la valeur true. Cette valeur peut également être omise, car les valeurs de ces éléments sont true par défaut. La fermeture de la session SAML requiert les valeurs `SessionIndex` et `NameID` pour aboutir.

