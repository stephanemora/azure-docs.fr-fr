---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674227"
---
## <a name="configure-the-relying-party-policy"></a>Configurer la stratégie de partie de confiance

La stratégie de partie de confiance, par exemple [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), spécifie le parcours utilisateur à partir duquel Azure AD B2C s’exécutera. Recherchez l’élément **DefaultUserJourney** dans la [partie de confiance](../articles/active-directory-b2c/relyingparty.md). Mettez à jour la valeur **ReferenceId** afin qu’elle corresponde à l’ID du parcours utilisateur auquel vous avez ajouté le fournisseur d'identité. 

Dans l’exemple suivant, pour le parcours utilisateur `CustomSignUpOrSignIn`, la valeur **ReferenceId** est définie sur `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Téléchargez la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les deux fichiers de stratégie que vous avez modifiés, dans l’ordre suivant : la stratégie d’extension, par exemple `TrustFrameworkExtensions.xml`, puis la stratégie de la partie de confiance, par exemple `SignUpSignIn.xml`.

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`
1. Pour **Application**, sélectionnez une application web que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

