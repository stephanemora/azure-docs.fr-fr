---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674226"
---
## <a name="add-a-user-journey"></a>Ajouter un parcours utilisateur 

À ce stade, le fournisseur d’identité a été configuré, mais il n’est encore disponible dans aucune des pages de connexion. Si vous n’avez pas votre propre parcours utilisateur personnalisé, créez un doublon d’un modèle de parcours utilisateur existant ; sinon, passez à l’étape suivante. 

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
1. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
1. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
1. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
1. Renommez l’ID du parcours utilisateur. Par exemple : `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Ajoutez le fournisseur d’identité à un parcours utilisateur 

Maintenant que vous disposez d’un parcours utilisateur, ajoutez-y le nouveau fournisseur d’identité. Vous ajoutez d’abord un bouton de connexion, puis vous liez le bouton à une action. L’action représente le profil technique que vous avez créé plus haut.

1. Recherchez l’élément d’étape d’orchestration comprenant `Type="CombinedSignInAndSignUp"` ou `Type="ClaimsProviderSelection"` dans le parcours utilisateur. Il s’agit généralement de la première étape d’orchestration. L’élément **ClaimsProviderSelections** contient une liste de fournisseurs d’identité auxquels un utilisateur peut se connecter. L’ordre des éléments détermine l’ordre des boutons de connexion présentés à l’utilisateur. Ajoutez un élément XML **ClaimsProviderSelection**. Définissez la valeur **TargetClaimsExchangeId** sur un nom convivial.

1. À la prochaine étape d’orchestration, ajoutez un élément **ClaimsExchange**. Définissez **ID** sur la valeur de l’ID d’échange des revendications cible. Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’ID du profil technique que vous avez créé précédemment.

Le code XML suivant montre les deux premières étapes d’orchestration d’un parcours utilisateur avec le fournisseur d’identité :