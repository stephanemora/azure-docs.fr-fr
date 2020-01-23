---
title: ContentDefinitions - Azure Active Directory B2C | Microsoft Docs
description: Spécifiez l’élément ContentDefinitions d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ce564767fe9664604687d8cbaced58507e6b8b3
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119650"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vous pouvez personnaliser l’apparence de tout [profil technique autodéclaré](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) exécute le code dans le navigateur de votre client et utilise une approche moderne appelée Partage des ressources Cross-Origin (CORS).

Pour personnaliser l’interface utilisateur, vous spécifiez une URL dans l’élément **ContentDefinition**, avec du contenu HTML personnalisé. Dans le profil technique autodéclaré ou **OrchestrationStep**, vous pointez vers cet identificateur de définition de contenu. La définition de contenu peut contenir un élément **LocalizedResourcesReferences** qui spécifie une liste de ressources localisées à charger. Azure AD B2C fusionne des éléments d’interface utilisateur avec le contenu HTML chargé à partir de votre URL, puis affiche la page à l’utilisateur.

L’élément **ContentDefinitions** contient des URL de modèles HTML5 utilisables dans un parcours utilisateur. L’URI de page HTML5 est utilisé pour une étape d’interface utilisateur spécifiée. Il peut s’agir, par exemple, de pages de connexion ou d’inscription, de réinitialisation de mot de passe ou d’erreur. Vous pouvez modifier l’apparence en remplaçant l’URI LoadUri du fichier HTML5. Vous pouvez aussi créer des définitions de contenu correspondant à vos besoins. Cet élément peut contenir une référence de ressources localisées à l’identificateur de localisation spécifié dans l’élément [Localization](localization.md).

L’exemple suivant montre l’identificateur de définition de contenu et la définition de ressources localisées :

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Les métadonnées du profil technique autodéclaré **LocalAccountSignUpWithLogonEmail** contiennent l’identificateur de définition de contenu **ContentDefinitionReferenceId** défini sur `api.localaccountsignup`.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```


## <a name="contentdefinition"></a>ContentDefinition

L’élément **ContentDefinitionParameter** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Id | Oui | Un identificateur de définition de contenu. La valeur est l’une de celles spécifiées dans la section **ID de définition de contenu** plus loin dans cette page. |

L’élément **ContentDefinition** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Chaîne contenant l’URL de la page HTML5 de la définition de contenu. |
| RecoveryUri | 0:1 | Chaîne contenant l’URL de la page HTML pour l’affichage d’une erreur liée à la définition de contenu. |
| DataUri | 1:1 | Chaîne contenant l’URL relative d’un fichier HTML qui fournit l’expérience utilisateur à appeler pour l’étape. |
| Métadonnées | 1:1 | Collection de paires clé/valeur contenant les métadonnées utilisées par la définition de contenu. |
| LocalizedResourcesReferences | 0:1 | Collection de références de ressources localisées. Cet élément permet de personnaliser la localisation d’une interface utilisateur et d’un attribut de revendications. |

### <a name="datauri"></a>DataUri

L’élément **DataUri** est utilisé pour spécifier l’identificateur de page. Azure AD B2C utilise l’identificateur de page pour charger et lancer des éléments d’interface utilisateur et du code JavaScript côté client. Le format de la valeur est `urn:com:microsoft:aad:b2c:elements:page-name:version`.  Le tableau suivant répertorie des identificateurs de page que vous pouvez utiliser.

| Valeur |   Description |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Affiche une page d’erreur quand une exception ou une erreur sont rencontrées. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent choisir au moment de la connexion. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Affiche un formulaire pour la connexion avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Cette valeur fournit également la fonctionnalité de maintien de connexion et le lien de rappel de mot de passe. . |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Affiche un formulaire pour la connexion avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Vérifie des numéros de téléphone (par voie textuelle ou vocale) au cours d’une inscription ou d’une connexion. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Affiche un formulaire permettant aux utilisateurs de créer ou mettre à jour leur profil. |


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

L’élément **LocalizedResourcesReferences** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Liste de références de ressources localisées pour la définition de contenu. |

L’élément **LocalizedResourcesReferences** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Langage | Oui | Chaîne contenant une langue prise en charge pour la stratégie conformément à la spécification RFC 5646 - Tags pour l’identification des langues. |
| LocalizedResourcesReferenceId | Oui | Identificateur de l’élément **LocalizedResources**. |

L’exemple suivant montre une définition de contenu d’inscription ou de connexion :

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

L’exemple suivant montre une définition de contenu d’inscription ou de connexion avec une référence à la localisation pour l’anglais, le français et l’espagnol :

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Pour savoir comment ajouter la prise en charge de la localisation à vos définitions de contenu, voir [Localisation](localization.md).

## <a name="content-definition-ids"></a>ID de définition de contenu

L’attribut d’ID de l’élément **ContentDefinition** spécifie le type de page qui se rapporte à la définition de contenu. L’élément définit le contexte que va appliquer un modèle HTML5/CSS personnalisé. Le tableau suivant décrit l’ensemble d’ID de définition de contenu reconnus par l’Infrastructure d’expérience d’identité, ainsi que les types de pages associés. Vous pouvez créer vos propres définitions de contenu avec un identificateur arbitraire.

| id | Modèle par défaut | Description |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Page d’erreur** : affiche une page d’erreur quand une exception ou une erreur sont rencontrées. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Page de sélection de fournisseur d’identité** : répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent choisir au moment de la connexion. Il s’agit généralement de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sélection du fournisseur d’identité pour l’inscription** : répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent choisir au moment de l’inscription. Il s’agit généralement de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’oubli de mot de passe** : affiche un formulaire que les utilisateurs doivent remplir pour lancer une réinitialisation de mot de passe. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page de connexion de compte local** : affiche un formulaire pour la connexion avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir une zone de saisie de texte et une zone de saisie de mot de passe. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription de compte local** : affiche un formulaire pour l’inscription avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles qu’une zone de saisie de texte, une zone de saisie de mot de passe, un bouton radio, des zones de liste déroulante à sélection unique ou des cases à sélection multiples. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Page d’authentification multifacteur** : vérifie des numéros de téléphone (par voie textuelle ou vocale) au cours d’une inscription ou d’une connexion. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription de compte social** : affiche un formulaire que les utilisateurs doivent remplir au moment de l’inscription à l’aide d’un compte existant d’un fournisseur d’identité de réseau social. Cette page est similaire à la page d’inscription au compte de réseau social ci-dessus, à l’exception des champs de saisie de mot de passe. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Page de mise à jour de profil** : affiche un formulaire auquel les utilisateurs peuvent accéder pour mettre à jour leur profil. Cette page est similaire à la page d’inscription au compte de réseau social, à l’exception des champs de saisie de mot de passe. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Page d’inscription ou de connexion unifiée** : gère le processus d’inscription et de connexion des utilisateurs. Les utilisateurs peuvent utiliser des fournisseurs d’identité d’entreprise, des fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou des comptes locaux. |

