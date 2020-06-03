---
title: Désactiver la vérification par e-mail lors de l’inscription du client avec une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Découvrez comment désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51cb46d3ce1b74681c2ee3e53104cc57e73f1c5d
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84192226"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Désactiver la vérification par e-mail lors de l’inscription cliente au moyen d’une stratégie personnalisée dans Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md). Vous devez disposer d’une stratégie personnalisée fonctionnelle pour l’inscription et la connexion avec des comptes locaux et de réseaux sociaux.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Ajouter les métadonnées au profil technique autodéclaré

Le profil technique **LocalAccountSignUpWithLogonEmail** est [autodéclaré](self-asserted-technical-profile.md) ; il est appelé pendant le déroulement de l’inscription. Pour désactiver la vérification par e-mail, définissez les métadonnées `EnforceEmailVerification` sur false. Remplacez les profils techniques LocalAccountSignUpWithLogonEmail dans le fichier d’extension. 

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.
1. Recherchez l’élément `ClaimsProviders`. Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez le fournisseur de revendications suivant à l’élément `ClaimsProviders` :

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les deux fichiers de stratégie que vous avez modifiés.
2. Sélectionnez la stratégie d’inscription et de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
3. Vous devriez pouvoir vous inscrire au moyen d’une adresse mail sans la validation.


## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur le [profil technique autodéclaré](self-asserted-technical-profile.md) dans la référence IEF.
