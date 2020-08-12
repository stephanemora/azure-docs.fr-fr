---
title: ID de chaînes de localisation - Azure Active Directory B2C | Microsoft Docs
description: Spécifiez les ID d’une définition de contenu avec un ID api.signuporsignin dans une stratégie personnalisée au sein d'Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 611d676f5f588ff32f981692456160e269642a43
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428121"
---
# <a name="localization-string-ids"></a>ID de chaînes de localisation

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L’élément **Localization** vous permet de prendre en charge plusieurs paramètres régionaux ou langues dans la stratégie pour les parcours utilisateur. Cet article fournit la liste des ID de localisation que vous pouvez utiliser dans votre stratégie. Pour vous familiariser avec la localisation de l’interface utilisateur, consultez [Localisation](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Éléments de page de connexion ou d’inscription

Les ID suivants sont utilisés pour une définition de contenu avec un ID `api.signuporsignin`.

| id | Valeur par défaut |
| -- | ------------- |
| **local_intro_email** | Connectez-vous avec votre compte existant |
| **logonIdentifier_email** | Adresse de messagerie |
| **requiredField_email** | Entrez votre adresse e-mail |
| **invalid_email** | Entrez une adresse e-mail valide |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&'' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Connectez-vous avec votre nom d'utilisateur |
| **logonIdentifier_username** | Nom d’utilisateur |
| **requiredField_username** | Entrez votre nom d'utilisateur |
| **mot de passe** | Mot de passe |
| **requiredField_password** | Entrez votre mot de passe |
| **invalid_password** | Le mot de passe que vous avez entré n'est pas au format attendu. |
| **forgotpassword_link** | Vous avez oublié votre mot de passe ? |
| **createaccount_intro** | Vous n’avez pas de compte ? |
| **createaccount_link** | ici |
| **divider_title** | OR |
| **cancel_message** | L'utilisateur a oublié son mot de passe |
| **button_signin** | Se connecter |
| **social_intro** | Connectez-vous avec votre compte social |
  **remember_me** |Maintenir la connexion|
| **unknown_error** | Nous rencontrons des problèmes pour vous connecter. Veuillez réessayer plus tard. |

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription ou de connexion :

![Éléments de l’expérience utilisateur de page d’inscription ou de connexion](./media/localization-string-ids/localization-susi.png)

L’ID des fournisseurs d’identité est configuré dans l’élément **ClaimsExchange** du parcours utilisateur. Pour localiser le titre du fournisseur d’identité, `ClaimsProvider` est affecté comme valeur d’**ElementType**, tandis que l’ID de `ClaimsExchange` est affecté comme valeur de **StringId**.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

L’exemple suivant localise le fournisseur d’identité Facebook pour l’arabe :

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Messages d’erreur d’inscription ou de connexion

| id | Valeur par défaut |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Votre mot de passe est incorrect. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nous ne trouvons pas votre compte. |
| **UserMessageIfOldPasswordUsed** | Apparemment, vous utilisez un ancien mot de passe. |
| **DefaultMessage** | Nom d’utilisateur ou mot de passe non valide. |
| **UserMessageIfUserAccountDisabled** | Votre compte a été verrouillé. Contactez votre support technique pour le déverrouiller, puis réessayez. |
| **UserMessageIfUserAccountLocked** | Votre compte est temporairement verrouillé pour éviter toute utilisation non autorisée. Réessayez plus tard. |
| **AADRequestsThrottled** | Il y a trop de demandes pour l’instant. Veuillez patienter quelques instants, puis réessayez. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Éléments d’interface utilisateur des pages d’inscription et autodéclarées

Voici les ID pour une définition de contenu avec un ID `api.localaccountsignup` ou pour toute définition de contenu qui commence par `api.selfasserted`, telle que `api.selfasserted.profileupdate` et `api.localaccountpasswordreset`.

| id | Valeur par défaut |
| -- | ------------- |
| **ver_sent** | Le code de vérification a été envoyé à : |
| **ver_but_default** | Default |
| **cancel_message** | L'utilisateur a annulé la saisie d'informations auto-confirmées |
| **preloader_alt** | Veuillez patienter |
| **ver_but_send** | Envoyer le code de vérification |
| **alert_yes** | Oui |
| **error_fieldIncorrect** | Un ou plusieurs champs sont incorrectement renseignés. Vérifiez vos entrées et réessayez. |
| **year** | Year |
| **verifying_blurb** | Veuillez patienter pendant que nous traitons vos informations. |
| **button_cancel** | Annuler |
| **ver_fail_no_retry** | Vous avez effectué trop de tentatives incorrectes. Veuillez réessayer plus tard. |
| **month** | Month |
| **ver_success_msg** | Adresse e-mail vérifiée. Vous pouvez maintenant continuer. |
| **months** | Janvier, février, mars, avril, mai, juin, juillet, août, septembre, octobre, novembre, décembre |
| **ver_fail_server** | Nous rencontrons des problèmes de la vérification de votre adresse e-mail. Entrez une adresse e-mail valide et réessayez. |
| **error_requiredFieldMissing** | Un champ obligatoire est manquant. Veuillez remplir tous les champs obligatoires, puis réessayez. |
| **initial_intro** | Fournissez les détails suivants. |
| **ver_but_resend** | Envoyer le nouveau code |
| **button_continue** | Créer |
| **error_passwordEntryMismatch** | Les champs d’entrée de mot de passe ne correspondent pas. Entrez le même mot de passe dans les deux champs et réessayez. |
| **ver_incorrect_format** | Format incorrect. |
| **ver_but_edit** | Modifier l'adresse e-mail |
| **ver_but_verify** | Vérifier le code |
| **alert_no** | Non |
| **ver_info_msg** | Le code de vérification a été envoyé à votre boîte de réception. Veuillez le copier dans la zone d’entrée ci-dessous. |
| **day** | jour |
| **ver_fail_throttled** | Il y a eu trop de demandes pour vérifier cette adresse e-mail. Veuillez patienter quelques instants, puis réessayez. |
| **helplink_text** | Qu'est-ce que c'est ? |
| **ver_fail_retry** | Ce code est incorrect. Réessayez. |
| **alert_title** | Annuler la saisie de vos informations |
| **required_field** | Ces informations sont obligatoires. |
| **alert_message** | Voulez-vous vraiment annuler la saisie de vos informations ? |
| **ver_intro_msg** | La vérification est nécessaire. Cliquez sur le bouton Envoyer. |
| **ver_input** | Code de vérification |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Messages d’erreur des pages d’inscription et autodéclarées

| id | Valeur par défaut |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Un utilisateur avec l’ID spécifié existe déjà. Choisissez-en un autre. |
| **UserMessageIfClaimNotVerified** | Revendication non vérifiée : {0} |
| **UserMessageIfIncorrectPattern** | Modèle incorrect pour : {0} |
| **UserMessageIfMissingRequiredElement** | Élément requis manquant : {0} |
| **UserMessageIfValidationError** | Erreur lors de la validation par : {0} |
| **UserMessageIfInvalidInput** | {0} a une entrée non valide. |
| **ServiceThrottled** | Il y a trop de demandes pour l’instant. Veuillez patienter quelques instants, puis réessayez. |

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription :

![Page d’inscription avec ses noms d’éléments d’interface utilisateur étiquetés](./media/localization-string-ids/localization-sign-up.png)

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription, une fois que l’utilisateur a cliqué sur le bouton Envoyer le code de vérification :

![Éléments de l’expérience utilisateur de vérification d’e-mail dans la page d’inscription](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Éléments d’interface utilisateur de la page d’authentification (facteur de forme de téléphone)

Voici les ID pour une définition de contenu avec un ID `api.phonefactor`.

| id | Valeur par défaut |
| -- | ------------- |
| **button_verify** | M’appeler |
| **country_code_label** | Indicatif de pays |
| **cancel_message** | L'utilisateur a annulé l'authentification multifacteur |
| **text_button_send_second_code** | envoyer un nouveau code |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Nous avons le numéro suivant en mémoire pour vous. Nous pouvons envoyer un code par SMS ou nous pouvons appeler pour vous authentifier. |
| **intro_mixed_p** | Nous avons les numéros suivants en mémoire pour vous. Choisissez un numéro que nous pouvons appeler ou auquel envoyer un code par SMS afin de vous authentifier. |
| **button_verify_code** | Vérifier le code |
| **requiredField_code** | Entrez le code de vérification que vous avez reçu |
| **invalid_code** | Entrez le code à 6 chiffres que vous avez reçu |
| **button_cancel** | Annuler |
| **local_number_input_placeholder_text** | Numéro de téléphone |
| **button_retry** | Recommencer |
| **alternative_text** | Je n'ai pas mon téléphone |
| **intro_phone_p** | Nous avons les numéros suivants en mémoire pour vous. Choisissez un numéro que nous pouvons appeler pour vous authentifier. |
| **intro_phone** | Nous avons le numéro suivant en mémoire pour vous. Nous allons appeler afin de vous authentifier. |
| **enter_code_text_intro** | Entrez votre code de vérification ci-dessous, ou  |
| **intro_entry_phone** | Entrez un numéro ci-dessous que nous pouvons appeler pour vous authentifier. |
| **intro_entry_sms** | Entrez un numéro ci-dessous auquel nous pouvons envoyer un code par SMS pour vous authentifier. |
| **button_send_code** | Envoyer le code |
| **invalid_number** | Entrez un numéro de téléphone valide |
| **intro_sms** | Nous avons le numéro suivant en mémoire pour vous. Nous enverrons un code par SMS afin de vous authentifier. |
| **intro_entry_mixed** | Entrez un numéro ci-dessous auquel nous pouvons envoyer un code par SMS ou que nous pouvons appeler pour vous authentifier. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Nous avons les numéros suivants en mémoire pour vous. Choisissez un numéro auquel nous pouvons envoyer un code par SMS afin de vous authentifier. |
| **requiredField_countryCode** | Sélectionnez le code de votre pays |
| **requiredField_number** | Entrez votre numéro de téléphone |
| **country_code_input_placeholder_text** |Pays ou région |
| **number_label** | Numéro de téléphone |
| **error_tryagain** | Le numéro de téléphone que vous avez fourni est occupé ou non disponible. Veuillez vérifier le numéro et réessayer. |
| **error_incorrect_code** | Le code de vérification que vous avez entré ne correspond pas à nos enregistrements. Réessayez ou demandez un nouveau code. |
| **countryList** | Consultez [la liste des pays](#countries-list). |
| **error_448** | Le numéro de téléphone que vous avez fourni n'est pas accessible. |
| **error_449** | L'utilisateur a dépassé le nombre de nouvelles tentatives. |
| **verification_code_input_placeholder_text** | Code de vérification |

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription MFA :

![Éléments de l’expérience utilisateur de vérification d’e-mail dans la page d’inscription](./media/localization-string-ids/localization-mfa1.png)

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page de validation MFA :

![Éléments de l’expérience utilisateur de vérification d’e-mail dans la page d’inscription](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Éléments de l’interface utilisateur du contrôle de l’affichage de vérification

Voici les ID d’un [contrôle d’affichage de vérification](display-control-verification.md)

| id | Valeur par défaut |
| -- | ------------- |
|verification_control_but_change_claims |Modifier |
|verification_control_fail_send_code |Impossible d’envoyer le code, réessayez plus tard. |
|verification_control_fail_verify_code |Impossible de vérifier le code, réessayez plus tard. |
|verification_control_but_send_code |Envoyer le code |
|verification_control_but_send_new_code |Envoyer le nouveau code |
|verification_control_but_verify_code |Vérifier le code |
|verification_control_code_sent| Le code de vérification a été envoyé. Veuillez le copier dans la zone d’entrée ci-dessous. |

### <a name="example"></a>Exemple

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Messages d’erreur du service Restful

Voici les ID des messages d’erreur de [profil technique du service Restful](restful-technical-profile.md) :

| id | Valeur par défaut |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Échec de l’établissement de la connexion au point de terminaison du service Restful. URL du service Restful : {0} |
|UserMessageIfCircuitOpen | {0}URL du service Restful : {1} |
|UserMessageIfDnsResolutionFailed | Échec de la résolution du nom d’hôte du point de terminaison du service Restful. URL du service Restful : {0} |
|UserMessageIfRequestTimeout | Échec de l’établissement de la connexion au point de terminaison du service Restful dans la limite de délai d'expiration de {0} secondes. URL du service Restful : {1} |


### <a name="example"></a>Exemple

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-mfa-error-messages"></a>Messages d’erreur Azure MFA

Voici les ID d’un message d’erreur de [profil technique Azure MFA](multi-factor-auth-technical-profile.md) :

| id | Valeur par défaut |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Impossible d’envoyer un SMS au téléphone. Essayez un autre numéro de téléphone. |
|UserMessageIfInvalidFormat | Le format de votre numéro de téléphone n’est pas valide. Corrigez-le, puis réessayez.|
|UserMessageIfMaxAllowedCodeRetryReached | Code erroné entré trop de fois. Réessayez plus tard.|
|UserMessageIfServerError | Impossible d'utiliser le service MFA. Réessayez plus tard.|
|UserMessageIfThrottled | Votre requête a été limitée, Réessayez plus tard.|
|UserMessageIfWrongCodeEntered|Code entré erroné. Réessayez.|

### <a name="example"></a>Exemple

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

Voici les ID des messages d’erreur de [profil technique Azure AD SSPR](aad-sspr-technical-profile.md) :

| id | Valeur par défaut |
| -- | ------------- |
|UserMessageIfChallengeExpired | Le code a expiré.|
|UserMessageIfInternalError | Le service de courrier a rencontré une erreur interne. Réessayez plus tard.|
|UserMessageIfThrottled | Vous avez envoyé trop de requêtes. Réessayez plus tard.|
|UserMessageIfVerificationFailedNoRetry | Vous avez dépassé le nombre maximal de tentatives de vérification.|
|UserMessageIfVerificationFailedRetryAllowed | La vérification a échoué. Réessayez.|


### <a name="example"></a>Exemple

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Messages d’erreur du mot de passe à usage unique

Voici les ID d’un message d’erreur de [profil technique à mot de passe à usage unique](one-time-password-technical-profile.md)

| id | Valeur par défaut |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Le mot de passe unique fourni pour la vérification a dépassé le nombre maximum de tentatives |
|UserMessageIfSessionDoesNotExist |Une session de vérification du mot de passe à usage unique a expiré |
|UserMessageIfSessionConflict |Une session de vérification du mot de passe à usage unique a un conflit |
|UserMessageIfInvalidCode |Le mot de passe à usage unique fourni pour la vérification est incorrect |
|UserMessageIfVerificationFailedRetryAllowed |Ce code est incorrect. Réessayez. | 

### <a name="example"></a>Exemple

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Messages d'erreur relatifs aux transformations de revendications

Voici les ID des messages d’erreur relatifs aux transformations de revendications :

| id | Transformation de revendications | Valeur par défaut |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Échec de comparaison des valeurs de la revendication booléenne pour le type de revendication « inputClaim ».| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Échec de comparaison des valeurs de la revendication : L’opérande de gauche fourni est supérieur à l’opérande de droite.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Échec de comparaison des valeurs de la revendication à l’aide de StringComparison « OrdinalIgnoreCase ».|

### <a name="example"></a>Exemple

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="countries-list"></a>Liste des pays

Voici les valeurs countryList utilisées par l’authentification multifacteur.

```JSON
{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}
```

## <a name="next-steps"></a>Étapes suivantes

Pour voir des exemples de localisation, consultez les articles suivants :

- [Personnalisation de la langue avec la stratégie personnalisée dans Azure Active Directory B2C](custom-policy-localization.md)
- [Personnalisation de la langue avec les flux d’utilisateurs dans Azure Active Directory B2C](user-flow-language-customization.md)