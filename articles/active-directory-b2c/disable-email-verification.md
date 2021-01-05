---
title: Désactiver la vérification par e-mail lors de l’inscription du client
titleSuffix: Azure AD B2C
description: Découvrez comment désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585018"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Par défaut, Azure Active Directory B2C (Azure AD B2C) vérifie l’adresse e-mail de votre client pour les comptes locaux (comptes des utilisateurs qui s’inscrivent avec une adresse e-mail ou un nom d’utilisateur). Azure AD B2C vérifie la validité de l’adresse e-mail en demandant au client de la confirmer pendant le processus d’inscription. Il empêche également des acteurs malveillants d’utiliser des processus automatisés pour générer des comptes frauduleux dans vos applications.

Certains développeurs d’applications préfèrent ignorer la vérification par e-mail pendant le processus d’inscription et amener les clients à vérifier l’adresse e-mail ultérieurement. Pour ce faire, Azure Active Directory B2C peut être configuré afin de désactiver la vérification par e-mail. Cette opération crée un processus de connexion plus simple et offre aux développeurs la possibilité de différencier les clients qui ont vérifié leur adresse e-mail de ceux qui ne l’ont pas encore fait.

> [!WARNING]
> La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. Si vous désactivez la vérification par e-mail par défaut d’Azure AD B2C, nous vous recommandons d’implémenter un système de vérification de remplacement.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Désactiver la vérification e-mail

::: zone pivot="b2c-user-flow"

Pour désactiver la vérification par e-mail, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Utilisez le filtre **Annuaire + abonnement** dans le menu du haut pour sélectionner l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez le flux d’utilisateur pour lequel vous souhaitez désactiver la vérification par e-mail. Par exemple, *B2C_1_signinsignup*.
1. Sélectionnez **Mises en page**.
1. Sélectionnez **Page d’inscription à un compte Local**.
1. Sous **Attributs utilisateur**, sélectionnez **Adresse e-mail**.
1. Dans la liste déroulante **REQUIERT UNE VÉRIFICATION**, sélectionnez **Non**.
1. Sélectionnez **Enregistrer**. La vérification par e-mail est maintenant désactivée pour ce flux d’utilisateur.

::: zone-end

::: zone pivot="b2c-custom-policy"
Le profil technique **LocalAccountSignUpWithLogonEmail** est [autodéclaré](self-asserted-technical-profile.md) ; il est appelé pendant le déroulement de l’inscription. Pour désactiver la vérification par e-mail, définissez les métadonnées `EnforceEmailVerification` sur false. Remplacez les profils techniques LocalAccountSignUpWithLogonEmail dans le fichier d’extension. 

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>.
1. Recherchez l’élément `ClaimsProviders`. Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez le fournisseur de revendications suivant à l’élément `ClaimsProviders` :

```xml
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
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Tester votre stratégie 

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Utilisez le filtre **Annuaire + abonnement** dans le menu du haut pour sélectionner l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez le flux d’utilisateur pour lequel vous souhaitez désactiver la vérification par e-mail. Par exemple, *B2C_1_signinsignup*.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.
1. Vous devriez pouvoir vous inscrire au moyen d’une adresse mail sans la validation.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les deux fichiers de stratégie que vous avez modifiés.
1. Sélectionnez la stratégie d’inscription et de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire au moyen d’une adresse mail sans la validation.

::: zone-end


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [personnaliser l’interface utilisateur dans Azure Active Directory B2C](customize-ui-with-html.md)

