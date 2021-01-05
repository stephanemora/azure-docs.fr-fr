---
title: Personnalisation de la langue dans Azure Active Directory B2C
description: Apprenez à personnaliser l’expérience linguistique de vos flux utilisateur dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1012dad1b7e0a314687a38e420ff9ad4780051da
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97110956"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personnalisation de la langue dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La personnalisation de la langue dans Azure Active Directory B2C (Azure AD B2C) permet à votre flux utilisateur de prendre en charge plusieurs langues pour répondre aux besoins de votre client. Microsoft fournit les traductions en [36 langues](#supported-languages), mais vous pouvez également fournir vos propres traductions pour n’importe quelle langue. Même si votre expérience est disponible dans une seule langue, vous pouvez personnaliser n’importe quel texte sur les pages.

## <a name="how-language-customization-works"></a>Fonctionnement de la personnalisation de la langue

La personnalisation de la langue vous permet de sélectionner les langues dans lesquelles votre flux utilisateur est disponible. Une fois que la fonctionnalité est activée, vous pouvez fournir le paramètre de chaîne de requête, `ui_locales`, de votre application. Lorsque vous appelez dans Azure AD B2C, votre page est traduite dans la langue régionale que vous avez indiquée. Ce type de configuration vous permet de contrôler intégralement les langues de votre flux utilisateur et ignore les paramètres de langue du navigateur du client.

Vous pouvez ne pas avoir besoin de ce niveau de contrôle sur les langues que votre client voit. Si vous n’indiquez pas de paramètre `ui_locales`, l’expérience du client est régie par les paramètres de son navigateur. Vous pouvez toujours contrôler les langues dans lesquelles votre flux utilisateur est traduit en les ajoutant en tant que langues prises en charge. Si le navigateur d’un client est défini pour afficher une langue que vous ne souhaitez pas prendre en charge, c’est la langue sélectionnée par défaut dans les cultures prises en charge qui est affichée à la place.

* **Langue spécifiée par le paramètre ui-locales** : après avoir activé la personnalisation de la langue, votre flux utilisateur est traduit dans la langue indiquée ici.
* **Langue requise par le navigateur** : si aucun paramètre `ui_locales` n’a été spécifié, votre flux utilisateur est traduit dans la langue requise par le navigateur, *si la langue est prise en charge*.
* **Langue par défaut de la stratégie** : si le navigateur ne spécifie aucune langue ou s’il en spécifie une qui n’est pas prise en charge, le flux utilisateur est traduit dans la langue par défaut de ce dernier.

> [!NOTE]
> Si vous utilisez des attributs utilisateur personnalisés, vous devez fournir vos propres traductions. Pour plus d’informations, consultez [Personnaliser vos chaînes](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

La localisation implique trois étapes : 

1. Configurer la liste explicite des langues prises en charge
1. Fournir des chaînes et collections spécifiques de la langue
1. Modifier la [définition de contenu](contentdefinitions.md) de la page 

::: zone-end 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Prendre en charge les langues demandées par le paramètre ui_locales

Les stratégies créées avant la disponibilité générale de la personnalisation de la langue doivent d’abord activer cette fonctionnalité. La personnalisation de la langue est activée par défaut dans les stratégies et flux d’utilisateurs créés après.

En activant la personnalisation de la langue sur un flux utilisateur, vous pouvez contrôler la langue de ce flux utilisateur en ajoutant le paramètre `ui_locales`.

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux utilisateur pour lequel vous souhaitez activer la traduction.
1. Sélectionnez **Langues**.
1. Sélectionnez **Activer la personnalisation de la langue**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Sélectionnez les langues activées dans votre flux utilisateur

Activez un ensemble de langues dans lesquelles votre flux utilisateur peut être traduit lorsque le navigateur le demande sans paramètre `ui_locales`.

1. Vérifiez que la personnalisation de la langue est activée dans votre flux utilisateur, comme vu précédemment.
1. Dans la page **Langues** du flux utilisateur, sélectionnez une langue que vous souhaitez prendre en charge.
1. Dans le volet Propriétés, configurez **Activé** sur **Oui**.
1. Sélectionnez **Enregistrer** dans la partie supérieure du volet.

>[!NOTE]
>Si aucun paramètre `ui_locales` n’est fourni, la page est traduite dans la langue du navigateur de l’utilisateur uniquement si elle est activée.
>

## <a name="customize-your-strings"></a>Personnaliser vos chaînes

La personnalisation de la langue vous permet de personnaliser les chaînes de votre flux utilisateur.

1. Vérifiez que la personnalisation de la langue est activée dans votre flux utilisateur, comme vu précédemment.
1. Dans la page **Langues** du flux utilisateur, sélectionnez une langue que vous souhaitez personnaliser.
1. Sous **Fichiers de ressource au niveau de la page**, sélectionnez la page que vous souhaitez modifier.
1. Sélectionnez **Télécharger les valeurs par défaut** (ou **Télécharger les valeurs de remplacements** si vous avez déjà modifié cette langue).

À la suite de ces opérations, vous obtenez un fichier JSON que vous pouvez utiliser pour commencer à modifier vos chaînes.

### <a name="change-any-string-on-the-page"></a>Modifier des chaînes sur la page

1. Ouvrez le fichier JSON téléchargé précédemment dans un éditeur JSON.
1. Recherchez l’élément que vous souhaitez modifier. Vous pouvez trouver le `StringId` de la chaîne que vous recherchez, ou recherchez l’attribut `Value` que vous souhaitez modifier.
1. Mettez à jour l’attribut `Value` avec ce que vous souhaitez afficher.
1. Pour chaque chaîne que vous souhaitez modifier, modifiez `Override` en `true`.
1. Enregistrez le fichier et chargez vos modifications. (Vous pouvez trouver le contrôle de téléchargement au même emplacement que celui d’où vous avez téléchargé le fichier JSON).

> [!IMPORTANT]
> Si vous devez remplacer une chaîne, veillez à définir la valeur `Override` sur `true`. Si la valeur n’est pas modifiée, l’entrée est ignorée.

### <a name="change-extension-attributes"></a>Modifier les attributs d’extension

Si vous souhaitez modifier la chaîne d’un attribut utilisateur personnalisé, ou en ajouter un au fichier JSON, le format suivant est utilisé :

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Remplacez `<ExtensionAttribute>` par le nom de votre attribut utilisateur personnalisé.

Remplacez `<ExtensionAttributeValue>` par la nouvelle chaîne à afficher.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fournir la liste des valeurs à l’aide de LocalizedCollections

Si vous souhaitez fournir une liste définie de valeurs pour les réponses, vous devez créer un attribut `LocalizedCollections`. Un élément `LocalizedCollections` est un tableau de paires `Name` et `Value`. L’ordre des éléments sera l’ordre dans lequel ils apparaissent. Pour ajouter `LocalizedCollections`, utilisez le format suivant :

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` est l’attribut utilisateur auquel cet attribut `LocalizedCollections` apporte une réponse.
* `Name` est la valeur affichée à l’utilisateur.
* `Value` est ce qui est renvoyé dans la revendication lorsque cette option est sélectionnée.

### <a name="upload-your-changes"></a>Charger vos modifications

1. Une fois que vous avez terminé de modifier votre fichier JSON, revenez à votre client B2C.
1. Sélectionnez **Flux d’utilisateurs** et cliquez sur celui pour lequel vous souhaitez activer la traduction.
1. Sélectionnez **Langues**.
1. Sélectionnez la langue dans laquelle vous voulez traduire.
1. Sélectionnez la page pour laquelle vous souhaitez fournir des traductions.
1. Sélectionnez l’icône de dossier et sélectionnez le fichier JSON à charger.

Ces changements sont automatiquement enregistrés dans votre flux utilisateur.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personnaliser l’interface utilisateur de la page à l’aide de la personnalisation de la langue

Il existe deux façons de localiser du [contenu HTML](customize-ui-with-html.md). Vous pouvez tout d’abord activer la [personnalisation de la langue](language-customization.md). L’activation de cette fonctionnalité permet à Azure AD B2C de transmettre le paramètre Open ID Connect `ui-locales` à votre point de terminaison. Votre serveur de contenu peut utiliser ce paramètre pour fournir des pages HTML personnalisées qui sont spécifiques à la langue.

Vous pouvez également extraire le contenu de plusieurs emplacements selon les paramètres régionaux utilisés. Dans votre point de terminaison avec CORS activé, vous pouvez configurer une structure de dossiers pour héberger du contenu pour des langues spécifiques. Vous devez appeler celui qui convient si vous utilisez la valeur générique `{Culture:RFC5646}`. Imaginons, par exemple, qu’il s’agit de l’URI de votre page personnalisée :

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Vous pouvez charger la page dans `fr`. La page extrait le contenu HTML et CSS à partir de :

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Ajouter des langues personnalisées

Vous pouvez également ajouter des langues pour lesquelles Microsoft ne fournit pas encore de traductions. Vous devrez fournir les traductions de toutes les chaînes du flux utilisateur. Les codes de langues et de paramètres régionaux se limitent à ceux de la norme ISO 639-1.

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
2. Sélectionnez le flux utilisateur dans lequel vous souhaitez ajouter des langues personnalisées, puis cliquez sur **Langues**.
3. Sélectionnez **Ajouter une langue personnalisée** en haut de la page.
4. Dans le volet contextuel qui s’ouvre, identifiez la langue pour laquelle vous fournissez des traductions en entrant un code de paramètres régionaux valide.
5. Pour chaque page, vous pouvez télécharger un ensemble de remplacements pour l’anglais et travailler sur les traductions.
6. Une fois que vous avez terminé avec les fichiers JSON, vous pouvez les télécharger pour chaque page.
7. Sélectionnez **Activer** et votre flux utilisateur peut désormais afficher cette langue pour votre utilisateur.
8. Enregistrez la langue.

>[!IMPORTANT]
>Vous devez activer les langues personnalisées ou charger des langues de substitution pour celles-ci avant de pouvoir enregistrer.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Configurer la liste des langues prises en charge

Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez l’élément `Localization` avec les langues prises en charge : anglais (par défaut) et espagnol.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Fournir des étiquettes propres à la langue

[LocalizedResources](localization.md#localizedresources) de l’élément `Localization` contient la liste des chaînes traduites. L’élément des ressources traduites comprend un identificateur servant à identifier de façon unique les ressources traduites. Cet identificateur est utilisé ultérieurement dans l’élément de [définition de contenu](contentdefinitions.md).

Vous configurez des éléments de ressources traduites pour la définition de contenu et toute langue que vous souhaitez prendre en charge. Afin de personnaliser les pages d’inscription ou de connexion unifiées pour l’anglais et l’espagnol, ajoutez les éléments `LocalizedResources` suivants après la fermeture de l’élément `</SupportedLanguages>`.

> [!NOTE]
> Dans l’exemple qui suit, nous avons ajouté le symbole dièse `#` au début de chaque ligne, ce qui vous permet de repérer facilement les étiquettes traduites à l’écran.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Modifier la définition de contenu avec la traduction

Collez le contenu entier de l’élément ContentDefinitions que vous avez copié en tant qu’enfant de l’élément BuildingBlocks.

Dans l’exemple suivant, les chaînes personnalisées en anglais (en) et en espagnol (es) sont ajoutées à la page d’inscription ou de connexion, ainsi qu’à la page d’inscription du compte local. L’identificateur **LocalizedResourcesReferenceId** pour chaque référence **LocalizedResourcesReference** est le même que celui des paramètres régionaux, mais vous pouvez utiliser n’importe quelle chaîne comme identificateur. Pour chaque combinaison de langue et de page, vous pointez sur l’élément **LocalizedResources** correspondant que vous avez créé précédemment.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Téléchargez et testez votre stratégie personnalisée mise à jour

### <a name="upload-the-custom-policy"></a>Téléchargez la stratégie personnalisée

1. Enregistrez le fichier d’extensions.
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Charger une stratégie personnalisée**.
1. Chargez le fichier d’extensions que vous avez modifié précédemment.

### <a name="test-the-custom-policy-by-using-run-now"></a>Tester la stratégie personnalisée à l’aide de l’option **Exécuter maintenant**

1. Sélectionnez la stratégie que vous avez téléchargée, puis sélectionnez **Exécuter maintenant**.
1. Vous devez pouvoir afficher la page d’inscription ou de connexion traduite.
1. Cliquez sur le lien d’inscription, vous devez être en mesure de voir la page d’inscription traduite.
1. Basculez la langue par défaut du navigateur sur l’espagnol. Sinon, vous pouvez ajouter le paramètre de chaîne de requête `ui_locales` à la demande d’autorisation. Par exemple : 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Informations supplémentaires

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Étiquettes de personnalisation de l’interface utilisateur de la page comme remplacements

Lorsque vous activez la personnalisation de la langue, les modifications précédemment appliquées aux étiquettes via la personnalisation de l’interface utilisateur de la page sont conservées dans un fichier JSON pour l’anglais (en). Vous pouvez continuer à modifier vos étiquettes et les autres chaînes en chargeant des ressources de langue dans « Personnalisation de la langue ».

::: zone-end

### <a name="up-to-date-translations"></a>Traductions à jour

Microsoft s’engage à fournir des traductions à jour pour que vous puissiez les utiliser. Nous ne cessons d’améliorer nos traductions pour qu’elles soient conformes à vos attentes. Microsoft identifiera les bogues et les modifications dans la terminologie globale et créera les mises à jour qui s’adapteront en toute transparence à votre flux utilisateur.

### <a name="support-for-right-to-left-languages"></a>Prise en charge des langues s’écrivant de droite à gauche

Microsoft n’assure actuellement pas la prise en charge des langues s’écrivant de droite à gauche. Pour cela, vous pouvez utiliser des paramètres régionaux personnalisés et modifier la manière dont les chaînes sont affichées à l’aide de CSS. Si vous avez besoin de cette fonctionnalité, votez pour elle sur [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traductions des fournisseurs d’identité de réseaux sociaux

Microsoft fournit le paramètre OIDC `ui_locales` pour les connexions aux réseaux sociaux. Mais certains fournisseurs d’identité de réseaux sociaux, notamment Facebook et Google, ne les respectent pas.

### <a name="browser-behavior"></a>Comportement du navigateur

Chrome et Firefox nécessitent la langue qui a été définie. S’il s’agit d’une langue prise en charge, elle s’affiche avant la langue par défaut. Actuellement, Microsoft Edge n’exige pas de langue particulière et affiche directement la langue par défaut.

## <a name="supported-languages"></a>Langues prises en charge

Azure AD B2C inclut la prise en charge des langues suivantes. Les langues de flux utilisateur sont fournies par Azure AD B2C. Les langues de notification d'authentification multifacteur (MFA) sont fournies par [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Langage              | Code langue | Flux d’utilisateurs         | Notifications MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabe                | ar            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Bulgare             | bg            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Bangla                | bn            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Catalan               | ca            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Tchèque                 | cs            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Danois                | da            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Allemand                | de            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Grec                 | el            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Anglais               | en            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Espagnol               | es            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Estonien              | et            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Basque                | eu            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Finnois               | fi            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Français                | fr            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Galicien              | gl            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Goudjrati              | gu            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Hébreu                | he            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Croate              | hr            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Hongrois             | hu            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Indonésien            | id            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Italien               | it            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Japonais              | ja            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Kazakh                | kk            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Kannada               | kn            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Coréen                | ko            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Lituanien            | lt            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Letton               | lv            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Marathi               | mr            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Malais                 | ms            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Norvégien Bokmal      | nb            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Néerlandais                 | nl            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Norvégien             | non            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Pendjabi               | pa            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Polonais                | pl            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Portugais - Brésil   | pt-br         | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Portugais - Portugal | pt-pt         | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Roumain              | ro            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Russe               | ru            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Slovaque                | sk            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Slovène             | sl            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Serbe - Cyrillique    | sr-cryl-cs    | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Serbe - Latin       | sr-latn-cs    | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Suédois               | sv            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Tamoul                 | ta            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Télougou                | te            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![X indiquant non.](./media/user-flow-language-customization/no.png) |
| Thaï                  | th            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Turc               | tr            | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Ukrainien             | uk            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Vietnamien            | vi            | ![X indiquant non.](./media/user-flow-language-customization/no.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Chinois - simplifié  | zh-hans       | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |
| Chinois - traditionnel | zh-hant       | ![Coche verte.](./media/user-flow-language-customization/yes.png) | ![Coche verte.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Étapes suivantes

::: zone pivot="b2c-user-flow"

Pour savoir comment personnaliser l’interface utilisateur de vos applications, consultez [Personnalisation de l’interface utilisateur d’une application dans Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Apprenez-en davantage sur l’élément [localization](localization.md) dans la référence IEF.
- Consultez la liste des [ID de chaîne de traduction](localization-string-ids.md) disponible dans Azure AD B2C.

::: zone-end 
