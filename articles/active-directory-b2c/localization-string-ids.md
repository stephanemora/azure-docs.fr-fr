---
title: ID de chaînes de localisation - Azure Active Directory B2C | Microsoft Docs
description: Spécifiez les ID d’une définition de contenu avec un Id api.signuporsignin dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c6cabcc89e66e9fbf153f2931bfe8b9b70c5f426
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682706"
---
# <a name="localization-string-ids"></a>ID de chaînes de localisation

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L’élément **Localization** vous permet de prendre en charge plusieurs paramètres régionaux ou langues dans la stratégie pour les parcours utilisateur. Cet article fournit la liste des ID de localisation que vous pouvez utiliser dans votre stratégie. Pour vous familiariser avec la localisation de l’interface utilisateur, consultez [Localisation](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Éléments de page de connexion ou d’inscription

Les ID suivants sont utilisés pour une définition de contenu avec un ID `api.signuporsignin`.

| ID | Valeur par défaut |
| -- | ------------- |
| **local_intro_email** | Connectez-vous avec votre compte existant |
| **logonIdentifier_email** | Adresse de messagerie |
| **requiredField_email** | Entrez votre adresse e-mail |
| **invalid_email** | Entrez une adresse e-mail valide |
| **email_pattern** | ^ [a-zA-Z0-9. ! #$% &''*c++ / = ? ^ _\`{\|} ~-]+@[a-zA-Z0-9-]+ ( ? :\\. [ un-zA-Z0 - 9-] +)*$ |
| **local_intro_username** | Connectez-vous avec votre nom d'utilisateur |
| **logonIdentifier_username** | Nom d’utilisateur |
| **requiredField_username** | Entrez votre nom d'utilisateur |
| **mot de passe** | Mot de passe |
| **requiredField_password** | Entrez votre mot de passe |
| **invalid_password** | Le mot de passe que vous avez entré n'est pas au format attendu. |
| **forgotpassword_link** | Vous avez oublié votre mot de passe ? |
| **createaccount_intro** | Vous n’avez pas de compte ? |
| **createaccount_link** | ici |
| **divider_title** | Ou |
| **cancel_message** | L'utilisateur a oublié son mot de passe |
| **button_signin** | Se connecter |
| **social_intro** | Connectez-vous avec votre compte social |
  **remember_me** |Maintenir la connexion|
| **unknown_error** | Nous rencontrons des problèmes pour vous connecter. Veuillez réessayer plus tard. |

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription ou de connexion :

![Éléments de l’expérience utilisateur de page d’inscription ou de connexion](./media/localization-string-ids/localization-susi.png)

L’ID des fournisseurs d’identité est configuré dans l’élément **ClaimsExchange** du parcours utilisateur. Pour localiser le titre du fournisseur d’identité, `ClaimsProvider` est affecté comme valeur d’**ElementType**, tandis que l’ID de `ClaimsExchange` est affecté comme valeur de **StringId**.

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
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

L’exemple suivant localise le fournisseur d’identité Facebook pour l’arabe :

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Messages d’erreur d’inscription ou de connexion

| ID | Valeur par défaut |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Votre mot de passe est incorrect. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nous ne trouvons pas votre compte. |
| **UserMessageIfOldPasswordUsed** | Apparemment, vous utilisez un ancien mot de passe. |  
| **DefaultMessage** | Nom d’utilisateur ou mot de passe non valide. |  
| **UserMessageIfUserAccountDisabled** | Votre compte a été verrouillé. Contactez votre support technique pour le déverrouiller, puis réessayez. |  
| **UserMessageIfUserAccountLocked** | Votre compte est temporairement verrouillé pour éviter toute utilisation non autorisée. Réessayez ultérieurement. |  
| **AADRequestsThrottled** | Il y a trop de demandes pour l’instant. Veuillez patienter quelques instants, puis réessayez. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Éléments d’interface utilisateur des pages d’inscription et autodéclarées

Voici les ID pour une définition de contenu avec un ID `api.localaccountsignup` ou pour toute définition de contenu qui commence par `api.selfasserted`, telle que `api.selfasserted.profileupdate` et `api.localaccountpasswordreset`.

| ID | Valeur par défaut |
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
| **month** | Mois |
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
| **alert_no** | Non  |
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

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Messages d’erreur des pages d’inscription et autodéclarées

| ID | Valeur par défaut |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Un utilisateur avec l’ID spécifié existe déjà. Choisissez-en un autre. |
| **UserMessageIfClaimNotVerified** | Revendication non vérifiée : {0} |
| **UserMessageIfIncorrectPattern** | Modèle incorrect pour : {0} |
| **UserMessageIfMissingRequiredElement** | Élément requis manquant : {0} |
| **UserMessageIfValidationError** | Erreur lors de la validation par : {0} |
| **UserMessageIfInvalidInput** | {0} a une entrée non valide. |
| **ServiceThrottled** | Il y a trop de demandes pour l’instant. Veuillez patienter quelques instants, puis réessayez. |

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription :

![Éléments de l’expérience utilisateur de page d’inscription](./media/localization-string-ids/localization-sign-up.png)

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription, une fois que l’utilisateur a cliqué sur le bouton Envoyer le code de vérification :

![Éléments de l’expérience utilisateur de vérification d’e-mail dans la page d’inscription](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Éléments d’interface utilisateur de la page d’authentification (facteur de forme de téléphone)

Voici les ID pour une définition de contenu avec un ID `api.phonefactor`. 

| ID | Valeur par défaut |
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
| **invalid_code** | Entrez le code à 6 chiffres que vous avez reçu |
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
| **countryList** | {\"Par défaut\":\"pays/région\",\"AF\":\"Afghanistan\",\"AX\":\"Åland Îles\",\"AL\":\"Albanie\",\"DZ\":\"Algérie\",\"AS\":\" Samoa américaines\",\"AD\":\"Andorre\",\"AO\":\"Angola\",\"AI\": \"Anguilla\",\"AQ\":\"Antarctique\",\"AG\":\"Antigua-et-Barbuda\",\"AR\":\"Argentine\",\"AM\":\"Arménie\",\"AW\":\"Aruba. \",\"Australie\":\"Australie\",\"à\":\"Autriche\",\" AZ\":\"Azerbaïdjan\",\"BS\":\"Bahamas\",\"BH\":\" Bahreïn\",\"BD\":\"Bangladesh\",\"BB\":\"Barbade\",\" PAR\":\"Bélarus\",\"BE\":\"Belgique\",\"est\":\" Belize\",\"BJ\":\"Bénin\",\"BM\":\"Bermudes\",\"BT\":\"Bhoutan\",\"BO\":\"Bolivie\",\"BQ\":\" Bonaire\",\"BA\":\"Bosnie-Herzégovine\",\"BW\":\"Botswana<span class="notransla class=""></span class="notransla> Îles éloignées\",\"VI\":\"U.S. Îles Vierges\",\"UG\":\"Ouganda\",\"UA\":\"Ukraine\",\"AE\":\"Émirats Arabes Unis\",\"Go\":\"Royaume-Uni\",\"US\":\"États-Unis\",\"UY \":\"Uruguay\",\"UZ\":\"Ouzbékistan\",\"UEV\":\"Vanuatu\", \"VA\":\"Cité du Vatican\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam \",\"WF\":\"Wallis et Futuna\",\"YE\":\"Yémen\",\"ZM\":\"Zambie\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | Le numéro de téléphone que vous avez fourni n'est pas accessible. |
| **error_449** | L'utilisateur a dépassé le nombre de nouvelles tentatives. |
| **verification_code_input_placeholder_text** | Code de vérification |

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page d’inscription MFA :

![Éléments de l’expérience utilisateur de vérification d’e-mail dans la page d’inscription](./media/localization-string-ids/localization-mfa1.png)

L’exemple suivant illustre l’utilisation de certains des éléments d’interface utilisateur dans la page de validation MFA :

![Éléments de l’expérience utilisateur de vérification d’e-mail dans la page d’inscription](./media/localization-string-ids/localization-mfa2.png)







