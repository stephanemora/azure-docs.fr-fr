---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951520"
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



