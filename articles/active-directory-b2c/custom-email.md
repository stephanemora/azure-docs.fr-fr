---
title: Vérifications d’e-mail personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment personnaliser l'e-mail de vérification envoyé à vos clients lorsqu'ils s'inscrivent pour utiliser vos applications Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3d9316f42c8d0ac5b44cda2e484ca4c92110813d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474727"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Vérification d’e-mail personnalisée dans Azure Active Directory B2C

Utilisez un e-mail personnalisé dans Azure Active Directory B2C (Azure AD B2C) afin d’envoyer un e-mail personnalisé aux utilisateurs qui s'inscrivent pour utiliser vos applications. En utilisant [DisplayControls](display-controls.md) (actuellement en préversion) et un fournisseur de messagerie tiers, vous pouvez utiliser votre propre modèle d’e-mail ainsi qu’une adresse *De :* et un objet, en bénéficiant également de la prise en charge de la localisation et des paramètres personnalisés de mot de passe à usage unique (OTP).

La vérification d’e-mail personnalisée nécessite l’utilisation d'un fournisseur de messagerie tiers comme [SendGrid](https://sendgrid.com) ou [SparkPost](https://sparkpost.com), d’une API REST personnalisée ou de tout fournisseur d'e-mail basé sur HTTP (y compris le vôtre). Cet article décrit la configuration d'une solution basée sur SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Création d'un compte SendGrid

Le cas échéant, commencez par créer un compte SendGrid (les clients Azure peuvent débloquer 25 000 e-mails gratuits chaque mois). Pour les instructions de configuration, voir la section [Créer un compte SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) de la rubrique [Envoi de courriers électroniques à l'aide de SendGrid avec Azure](../sendgrid-dotnet-how-to-send-email.md).

Veillez à compléter la section dans laquelle vous [créez une clé API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Enregistrez la clé API pour l'utiliser à une étape ultérieure.

## <a name="create-azure-ad-b2c-policy-key"></a>Créer une clé de stratégie Azure AD B2C

Stockez ensuite la clé API SendGrid dans une clé de stratégie Azure AD B2C pour que vos stratégies puissent y faire référence.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Annuaire + abonnement** dans le menu du haut, puis choisissez votre annuaire Azure AD B2C.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `SendGridSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
1. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Sélectionnez **Create** (Créer).

## <a name="create-sendgrid-template"></a>Créer un modèle SendGrid

Avec un compte SendGrid créé et une clé API SendGrid stockée dans une clé de stratégie Azure AD B2C, créez un [modèle transactionnel dynamique](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/) SendGrid.

1. Sur le site SendGrid, ouvrez la page des [modèles transactionnels](https://sendgrid.com/dynamic_templates) et sélectionnez **Créer un modèle**.
1. Entrez un nom de modèle unique comme `Verification email`, puis sélectionnez **Enregistrer**.
1. Pour commencer à modifier votre nouveau modèle, sélectionnez **Ajouter une version**.
1. Sélectionnez **Éditeur de code** puis **Continuer**.
1. Dans l'éditeur HTML, collez le modèle HTML suivant ou utilisez le vôtre. Les paramètres `{{otp}}` et `{{email}}` seront remplacés dynamiquement par la valeur du mot de passe à usage unique et l'adresse e-mail de l'utilisateur.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Développez **Réglages** sur la partie gauche, et pour **Objet du message électronique**, entrez `{{subject}}`.
1. Sélectionnez **Enregistrer le modèle**.
1. Retournez à la page **Modèles transactionnels** en sélectionnant la flèche de retour.
1. Enregistrez l’**ID** du modèle que vous avez créé pour l'utiliser à une étape ultérieure. Par exemple : `d-989077fbba9746e89f3f6411f596fb96`. Vous spécifiez cet ID lorsque vous [ajoutez la transformation de revendications](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Ajouter des types de revendications Azure AD B2C

Dans votre stratégie, ajoutez les types de revendications suivants à l'élément `<ClaimsSchema>` dans `<BuildingBlocks>`.

Ces types de revendications sont nécessaires pour générer et vérifier l’adresse e-mail à l'aide d'un code de mot de passe à usage unique (OTP).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Ajouter la transformation de revendications

Ensuite, vous avez besoin d'une transformation de revendication pour générer une revendication de chaîne JSON qui constituera le corps de la requête envoyée à SendGrid.

La structure de l'objet JSON est définie par les ID en notation par points des éléments InputParameters et des éléments TransformationClaimTypes de InputClaims. Les nombres dans la notation par points impliquent des tableaux. Les valeurs proviennent des valeurs InputClaims et des propriétés « Value » des éléments InputParameters. Pour plus d'informations sur les transformations de revendications JSON, voir [Transformations de revendications JSON](json-transformations.md).

Ajoutez la transformation de revendications suivante à l'élément `<ClaimsTransformations>` dans `<BuildingBlocks>`. Effectuez les mises à jour suivantes dans le code XML de la transformation de revendications :

* Mettez à jour la valeur InputParameter `template_id` avec l'ID du modèle transactionnel SendGrid que vous avez créé précédemment dans [Créer un modèle SendGrid](#create-sendgrid-template).
* Mettez à jour la valeur de l'adresse `from.email`. Utilisez une adresse e-mail valide pour éviter que l’e-mail de vérification ne soit considéré comme un courrier indésirable.
* Mettez à jour la valeur du paramètre de saisie de la ligne d’objet `personalizations.0.dynamic_template_data.subject` avec une ligne d’objet appropriée pour votre organisation.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Ajouter la définition du contenu DataUri

Sous les transformations de revendications dans `<BuildingBlocks>`, ajoutez l’élément [ContentDefinition](contentdefinitions.md) suivant pour faire référence à l'URI de données de la version 2.0.0 :

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Créer un élément DisplayControl

Un contrôle d’affichage de vérification est utilisé pour vérifier l’adresse e-mail à l’aide d’un code de vérification envoyé à l’utilisateur.

Cet exemple de contrôle d'affichage est configuré pour :

1. Recueillir le type de revendication d'adresse `email` auprès de l'utilisateur.
1. Attendre que l'utilisateur fournisse le type de réclamation `verificationCode` à l’aide du code qui lui a été envoyé.
1. Retourner l’élément `email` au profil technique autodéclaré qui contient une référence à ce contrôle d'affichage.
1. En utilisant l’action `SendCode`, générer un code OTP et envoyer un e-mail avec le code OTP à l'utilisateur.

![Action d’envoi du code de vérification par e-mail](media/custom-email/display-control-verification-email-action-01.png)

Sous les définitions de contenu, toujours dans `<BuildingBlocks>`, ajoutez à votre stratégie l’élément [DisplayControl](display-controls.md) suivant de type [VerificationControl](display-control-verification.md).

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Ajouter des profils techniques OTP

Le profil technique `GenerateOtp` génère un code pour l’adresse e-mail. Le profil technique `VerifyOtp` vérifie le code associé à l’adresse e-mail. Vous pouvez modifier la configuration du format et l’expiration du mot de passe à usage unique. Pour plus d'informations sur les profils techniques OTP, voir [Définir un profil technique de mot de passe à usage unique](one-time-password-technical-profile.md).

Ajoutez les profils techniques suivants à l'élément `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessage.VerificationHasExpired">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessage.MaxRetryAttemped">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessage.InvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessage.ServerError">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Ajouter un profil technique API REST

Ce profil technique API REST génère le contenu de l’e-mail (en utilisant le format SendGrid). Pour plus d'informations sur les profils techniques RESTful, voir [Définir un profil technique RESTful](restful-technical-profile.md).

Comme pour les profils techniques OTP, ajoutez les profils techniques suivants à l'élément `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Faire référence à DisplayControl

Dans l'étape finale, ajoutez une référence à l’élément DisplayControl que vous avez créé. Remplacez votre profil technique autodéclaré `LocalAccountSignUpWithLogonEmail` existant par le profil suivant si vous avez utilisé une version antérieure de la stratégie Azure AD B2C. Ce profil technique utilise `DisplayClaims` avec une référence à l’élément DisplayControl.

Pour plus d'informations, voir [Profil technique autodéclaré](restful-technical-profile.md) et [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez un exemple de stratégie de vérification d’e-mail personnalisée sur GitHub :

[Vérification d’e-mail personnalisée - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Pour plus d'informations sur l'utilisation d'une API REST personnalisée ou d’un fournisseur de messagerie SMTP basé sur HTTP, voir [Définir un profil technique RESTful dans une stratégie personnalisée Azure AD B2C](restful-technical-profile.md).
