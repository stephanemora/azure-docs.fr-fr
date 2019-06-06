---
title: Exemples JavaScript - Azure Active Directory B2C | Microsoft Docs
description: En savoir plus sur l’utilisation de JavaScript dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9e19df7c50ca9d2c57ab385a567f4911b200c5e2
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510887"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Exemples JavaScript pour une utilisation dans Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Vous pouvez ajouter votre propre code côté client JavaScript à vos applications Azure Active Directory (Azure AD) B2C. Pour activer JavaScript pour vos applications, vous devez ajouter un élément à votre [stratégie personnalisée](active-directory-b2c-overview-custom.md), sélectionnez un [contrat de page](page-contract.md)et utiliser [b2clogin.com](b2clogin.md) dans vos requêtes. Cet article décrit comment vous pouvez modifier votre stratégie personnalisée pour permettre l’exécution du script.

> [!NOTE]
> Si vous souhaitez activer JavaScript pour les flux d’utilisateurs, consultez [JavaScript et page de contrat de versions dans Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Conditions préalables

Sélectionnez un contrat de page pour les éléments d’interface utilisateur de votre application. Si vous prévoyez d’utiliser JavaScript, vous devez définir une version de contrat de page pour l’ensemble de vos définitions de contenu dans votre stratégie personnalisée.

## <a name="add-the-scriptexecution-element"></a>Ajouter l’élément ScriptExecution

Pour activer l’exécution des scripts, ajoutez l’élément **ScriptExecution** à l’élément [RelyingParty](relyingparty.md).

1. Ouvrez votre fichier de stratégie personnalisée. Par exemple *SignUpOrSignin.xml*.
2. Ajoutez l’élément **ScriptExecution** à l’élément **UserJourneyBehaviors** de **RelyingParty** :

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Enregistrez et chargez le fichier.

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
    - Azure AD B2C utilise une version spécifique de jQuery. N’incluez pas une autre version de jQuery. L’utilisation de plusieurs versions sur la même page provoque des problèmes.
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

Dans le code, remplacez `termsOfUseUrl` par le lien pointant vers vos conditions d’utilisation. Pour votre annuaire, créer un nouvel attribut utilisateur nommé **termsOfUse** , puis inclure **termsOfUse** comme un attribut utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment personnaliser l’interface utilisateur de vos applications, consultez [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
