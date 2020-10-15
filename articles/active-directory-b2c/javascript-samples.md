---
title: Exemples JavaScript
titleSuffix: Azure AD B2C
description: En savoir plus sur l’utilisation de JavaScript dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.custom: devx-track-js
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9faca55f8440a28a845e892ee38df2de3489f97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259490"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Exemples JavaScript pour une utilisation dans Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Vous pouvez ajouter votre propre code JavaScript côté client à vos applications Azure Active Directory B2C (Azure AD B2C).

Pour activer JavaScript pour vos applications :

* Ajoutez un élément à votre [stratégie personnalisée](custom-policy-overview.md).
* Sélectionnez une [mise en page](page-layout.md).
* Utilisez [b2clogin.com](b2clogin.md) dans vos requêtes.

Cet article explique comment vous pouvez modifier votre stratégie personnalisée pour permettre l’exécution des scripts.

> [!NOTE]
> Si vous voulez activer JavaScript pour les flux utilisateur, consultez [Versions des mises en page et JavaScript dans Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Conditions préalables requises

### <a name="select-a-page-layout"></a>Sélectionner une mise en page

* Sélectionnez une [mise en page](contentdefinitions.md#select-a-page-layout) pour les éléments d’interface utilisateur de votre application.

    Si vous prévoyez d’utiliser JavaScript, vous devez [définir une version de mise en page](contentdefinitions.md#migrating-to-page-layout) avec la version de page `contract` pour *toutes* les définitions de contenu de votre stratégie personnalisée.

## <a name="add-the-scriptexecution-element"></a>Ajouter l’élément ScriptExecution

Pour activer l’exécution des scripts, ajoutez l’élément **ScriptExecution** à l’élément [RelyingParty](relyingparty.md).

1. Ouvrez votre fichier de stratégie personnalisée. Par exemple *SignUpOrSignin.xml*.
2. Ajoutez l’élément **ScriptExecution** à l’élément **UserJourneyBehaviors** de **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Enregistrez et chargez le fichier.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

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

Pour savoir comment personnaliser l’interface utilisateur de vos applications, consultez [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C](custom-policy-ui-customization.md).
