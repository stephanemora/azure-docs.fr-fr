---
title: Activer la vérification de l’âge dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment identifier les mineurs à l’aide de votre application.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102525414"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Activer la vérification de l’âge dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La vérification de l’âge dans Azure Active Directory B2C (Azure AD B2C) vous permet d’identifier les mineurs qui souhaitent utiliser votre application, avec ou sans consentement parental. Vous pouvez choisir d’empêcher un mineur de se connecter à l’application. Vous pouvez également autoriser l’utilisateur à se connecter et fournir à l’application le statut de mineur. 

>[!IMPORTANT]
>Cette fonctionnalité est en version préliminaire publique. N’utilisez pas cette fonctionnalité pour les applications de production.
>

Lorsque la vérification de l’âge est activée pour un flux d’utilisateur, les utilisateurs sont invités à indiquer leur date de naissance et leur pays de résidence. Si un utilisateur qui se connecte n’a pas précédemment entré ces informations, il devra le faire à sa prochaine connexion. Les règles sont appliquées chaque fois qu’un utilisateur se connecte.

![Capture d’écran du flux de collecte des informations de vérification de l’âge](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C utilise les informations indiquées par l’utilisateur pour déterminer s’il est mineur. Le champ **ageGroup** est ensuite mis à jour dans son compte. La valeur peut être `null`, `Undefined`, `Minor`, `Adult` ou `NotAdult`.  Les champs **ageGroup** et **consentProvidedForMinor** sont ensuite utilisés pour calculer la valeur de **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Configurer la vérification de l’âge sur un locataire

Pour utiliser la vérification de l’âge dans un flux utilisateur, vous devez configurer des propriétés supplémentaires sur votre locataire.

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire et abonnement** dans le menu supérieur. Sélectionnez l’annuaire qui contient votre locataire.
1. Sélectionnez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Propriétés** pour votre locataire dans le menu de gauche.
1. Sous **Vérification de l’âge**, sélectionnez **Configurer**.
1. Une fois l’opération terminée, la vérification de l’âge est configurée sur votre locataire.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Activer la vérification de l’âge dans votre flux utilisateur

Une fois que votre locataire est configuré pour utiliser la vérification de l’âge, vous pouvez utiliser cette fonctionnalité dans les [flux utilisateur](user-flow-versions.md) dans lesquels elle est activée. Pour activer la vérification de l’âge, procédez comme suit :

1. Créez un flux utilisateur dans lequel la vérification de l’âge est activée.
1. Après avoir créé le flux utilisateur, sélectionnez **Propriétés** dans le menu.
1. Dans la section **Vérification de l’âge**, sélectionnez **Activée**.
1. Pour l'**inscription ou la connexion**, sélectionnez la façon dont vous souhaitez gérer les utilisateurs :
    - Autoriser les personnes mineures à accéder à votre application.
    - Bloquer l'accès à votre application uniquement aux personnes mineures qui n'ont pas l'âge de consentement.
    - Bloquer l'accès à votre application à toutes les personnes mineures.
1. Pour **Sur bloc**, sélectionnez l’une des options suivantes :
    - **Renvoyer un JSON à l’application** : cette option envoie à l’application une réponse indiquant que le mineur a été bloqué.
    - **Afficher une page d’erreur** : l’utilisateur voit une page l’informant qu’il ne peut pas accéder à l’application.

## <a name="test-your-user-flow"></a>Tester votre flux d’utilisateur

1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d'utilisateur**.
1. Connectez-vous à l’aide d’un compte local ou social. Sélectionnez ensuite votre pays de résidence, ainsi qu’une date de naissance qui simule un mineur. 
1. Répétez le test, puis sélectionnez une date de naissance qui simule un adulte.  

Lorsque vous vous connectez en tant que mineur, vous devez voir le message d’erreur suivant : *Malheureusement, votre connexion a été bloquée. Les lois en matière de confidentialité et de sécurité en ligne dans votre pays empêchent l’accès aux comptes appartenant à des enfants.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Activer la vérification de l’âge dans votre stratégie personnalisée

1. Obtenez un exemple de stratégie de vérification de l’âge sur [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. Dans chaque fichier, remplacez la chaîne `yourtenant` par le nom de votre locataire Azure AD B2C. Par exemple, si le nom de votre locataire B2C est *contosob2c*, toutes les instances de `yourtenant.onmicrosoft.com` deviennent `contosob2c.onmicrosoft.com`.
1. Téléchargez les fichiers de stratégie.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

- Apprendre à [Gérer les accès utilisateurs dans Azure AD BC2](manage-user-access.md).

