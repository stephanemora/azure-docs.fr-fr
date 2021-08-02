---
title: JavaScript et versions de mise en page
titleSuffix: Azure AD B2C
description: Découvrez comment activer JavaScript et utiliser des versions de mise en page dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/07/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 652551635b84c18020bf928194923d0e6ca86149
ms.sourcegitcommit: 89c889a9bdc2e72b6d26ef38ac28f7a6c5e40d27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111565269"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versions de mise en page et JavaScript dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C fournit un ensemble de contenu packagé contenant des données HTML, CSS et JavaScript pour les éléments d’interface utilisateur dans vos flux d’utilisateurs et stratégies personnalisées. Pour activer JavaScript pour vos applications :

::: zone pivot="b2c-user-flow"

* Sélectionnez une [mise en page](page-layout.md).
* Activez-le sur le flux d'utilisateurs à l'aide du portail Azure.
* Utilisez [b2clogin.com](b2clogin.md) dans vos requêtes.

::: zone-end

::: zone pivot="b2c-custom-policy"

* Sélectionnez une [mise en page](page-layout.md).
* Ajoutez un élément à votre [stratégie personnalisée](custom-policy-overview.md).
* Utilisez [b2clogin.com](b2clogin.md) dans vos requêtes.

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Sélectionner une version de mise en page

Si vous prévoyez d’activer le code JavaScript côté client, les éléments sur lesquels vous basez votre JavaScript doivent être immuables. S’ils ne le sont pas, toute modification pourrait provoquer un comportement inattendu sur vos pages d’utilisateurs. Pour prévenir ce type de problème, imposez l'utilisation d'une mise en page et spécifiez une version de mise en page afin de veiller à ce que les définitions de contenu sur lesquelles vous avez basé votre JavaScript soient immuables. Même si vous ne souhaitez pas activer JavaScript, vous pouvez spécifier une version de mise en page pour vos pages.

::: zone pivot="b2c-user-flow"

Pour spécifier la version de la mise en page de vos pages de flux utilisateur, procédez comme suit : 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Sélectionnez **Mises en page**. Choisissez un **Nom de disposition**, puis la **Version de mise en page**.

Pour plus d'informations sur les différentes versions de mise en page, consultez le [Journal des modifications des versions de mise en page](page-layout.md).

![Paramètres de mise en page dans le portail montrant la liste déroulante des versions de mise en page](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Sélectionnez une [mise en page](contentdefinitions.md#select-a-page-layout) pour les éléments d’interface utilisateur de votre application.

Définissez une [version de mise en page](contentdefinitions.md#migrating-to-page-layout) avec la version `contract` de la page pour *toutes* les définitions de contenu de votre stratégie personnalisée. Le format de la valeur doit contenir le mot `contract`: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_. Découvrez comment [migrer vers la mise en page](contentdefinitions.md#migrating-to-page-layout) avec la version de la page.

L’exemple suivant montre les identificateurs de définition de contenu et les **DataUri** correspondants avec le contrat de page : 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>Activer JavaScript

::: zone pivot="b2c-user-flow"

Dans le flux d'utilisateurs **Propriétés**, vous pouvez activer JavaScript. L'activation de JavaScript impose également l'utilisation d’une mise en page. Vous pouvez ensuite définir la version de mise en page du flux d'utilisateur comme décrit dans la section suivante.

![Page des propriétés du flux d'utilisateur avec le paramètre Activer JavaScript en surbrillance](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Pour activer l’exécution des scripts, ajoutez l’élément **ScriptExecution** à l’élément [RelyingParty](relyingparty.md).

1. Ouvrez votre fichier de stratégie personnalisée. Par exemple *SignUpOrSignin.xml*.
1. Ajoutez l’élément **ScriptExecution** à l’élément **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Enregistrez et chargez le fichier.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Instructions pour l’utilisation de JavaScript

Suivez ces instructions lorsque vous personnalisez l’interface de votre application à l’aide de JavaScript :

- Ne pas lier un événement Click aux éléments HTML `<a>`.
- Ne pas prendre de dépendance sur le code ou les commentaires Azure AD B2C.
- Ne pas modifier la séquence ou la hiérarchie des éléments HTML d’Azure AD B2C. Utiliser une stratégie Azure AD B2C pour contrôler l’ordre des éléments d’interface utilisateur.
- Vous pouvez appeler n’importe quel service RESTful en gardant ce qui suit à l’esprit :
    - Vous devrez peut-être définir les éléments CORS de votre service RESTful pour autoriser les appels HTTP côté client.
    - Assurez-vous que votre service RESTful est sécurisé et qu’il utilise uniquement le protocole HTTPS.
    - N’utilisez pas JavaScript directement pour appeler les points de terminaison Azure AD B2C.
- Vous pouvez incorporer votre JavaScript, ou vous pouvez définir un lien vers des fichiers JavaScript externes. Lorsque vous utilisez un fichier JavaScript externe, vérifiez que vous utilisez son URL absolue et pas une URL relative.
- Frameworks JavaScript :
    - Azure AD B2C utilise une [version spécifique de jQuery](page-layout.md#jquery-version). N’incluez pas une autre version de jQuery. L’utilisation de plusieurs versions sur la même page provoque des problèmes.
    - L’utilisation de RequireJS n’est pas prise en charge.
    - La plupart des frameworks JavaScript ne sont pas pris en charge par Azure AD B2C.
- Les paramètres d’Azure AD B2C peuvent être lus en appelant des objets `window.SETTINGS`, `window.CONTENT`, tels que la langue d’interface utilisateur actuelle. Ne modifiez pas la valeur de ces objets.
- Pour personnaliser le message d’erreur Azure AD B2C, utilisez la localisation dans une stratégie.
- Si un objectif peut être atteint à l’aide d’une stratégie, c’est généralement la méthode recommandée.

## <a name="javascript-samples"></a>Exemples JavaScript

### <a name="show-or-hide-a-password"></a>Afficher ou masquer un mot de passe

Une méthode courante pour aider vos clients à réussir leur inscription est de leur permettre d’afficher le mot de passe saisi. Cette option aide les utilisateurs à s’inscrire en leur permettant d’afficher facilement leur mot de passe et à y apporter des corrections si nécessaire. Tous les champs de saisie de mot de passe présentent une case à cocher proposant d’**Afficher le mot de passe**.  Cela permet à l’utilisateur d’afficher le mot de passe en texte brut. Intégrez cet extrait de code dans votre modèle d’inscription ou de connexion pour une page autodéclarée :

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Ajouter des conditions d’utilisation

Incorporez le code suivant dans votre page à l’endroit où vous souhaitez inclure une case à cocher **Conditions d’utilisation**. Cette case à cocher est généralement nécessaire dans vos pages de création de compte local et dans vos pages de création de compte social.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Dans le code, remplacez `termsOfUseUrl` par le lien pointant vers vos conditions d’utilisation. Pour votre répertoire, créez un nouvel attribut utilisateur nommé **termsOfUse**, puis incluez **termsOfUse** comme attribut utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment personnaliser l’interface utilisateur de vos applications, consultez [Personnalisation de l’interface utilisateur d’une application dans Azure Active Directory B2C](customize-ui-with-html.md).
