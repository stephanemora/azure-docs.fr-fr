---
title: Atténuer les attaques d’informations d’identification - Azure AD B2C
titleSuffix: Azure AD B2C
description: Découvrez les techniques de détection et d’atténuation des attaques d’informations d’identification (attaques par mot de passe) dans Azure Active Directory B2C, y compris les fonctionnalités de verrouillage intelligent de compte.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 183abae52d8b8dc4b78f48118866d6d667aaeaed
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110061621"
---
# <a name="mitigate-credential-attacks-in-azure-ad-b2c"></a>Atténuer les attaques d’informations d’identification dans Azure AD B2C

Les attaques d’informations d’identification entraînent un accès non autorisé aux ressources. Les mots de passe définis par les utilisateurs doivent être d’une complexité raisonnable. Azure AD B2C dispose de techniques d’atténuation pour contrer les attaques d’informations d’identification. L’atténuation comprend la détection des attaques par force brute et des attaques par informations d’identification d’annuaire. À l’aide de différents signaux, Azure Active Directory B2C (Azure AD B2C) analyse l’intégrité des demandes. Azure AD B2C est conçu pour différencier intelligemment les utilisateurs prévus des pirates et botnets.

Azure AD B2C applique une stratégie sophistiquée pour verrouiller les comptes. Le verrouillage des comptes est basé sur l’adresse IP de la requête et sur les mots de passe entrés. La durée du verrouillage augmente également en fonction de la probabilité de l’existence d’une attaque. Après 10 tentatives d’entrée d’un mot de passe sans succès (le seuil de tentative par défaut), le compte est verrouillé pendant une minute. La prochaine fois qu’une connexion échoue après le déverrouillage du compte (c’est-à-dire après que le compte a été automatiquement déverrouillé par le service à l’expiration de la période de verrouillage), un autre verrouillage d’une minute se produit et se poursuit pour chaque connexion non réussie. L’entrée du même mot de passe à plusieurs reprises n’est pas considérée comme plusieurs connexions ayant échoué.

> [!NOTE]
> Cette fonctionnalité est prise en charge par des [flux d’utilisateurs, les stratégies personnalisées](user-flow-overview.md) et les flux [ROPC](add-ropc-policy.md). Elle est activée par défaut, ce qui signifie que vous n’avez pas besoin de la configurer dans vos flux d’utilisateurs ou vos stratégies personnalisées.

## <a name="unlock-accounts"></a>Déverrouiller les comptes

Les 10 premiers verrouillages durent une minute. Les 10 verrouillages suivants durent un peu plus longtemps, et leur durée augmente tous les 10 verrouillages. Le compteur de verrouillage se remet à zéro après une connexion réussie lorsque le compte n’est pas verrouillé. Les périodes de verrouillage peuvent durer jusqu'à cinq heures. Les utilisateurs doivent attendre que la durée de verrouillage expire. Mais l’utilisateur peut déverrouiller un compte à l’aide d’un [flux d’utilisateur de mot de passe](add-password-reset-policy.md) en libre-service.

## <a name="manage-password-protection-settings"></a>Gérer les paramètres de protection par mot de passe

Pour gérer les paramètres de protection par mot de passe, y compris le seuil de verrouillage :

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Utilisez le filtre **Annuaire + abonnement** dans le menu du haut pour sélectionner l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Sécurité**, sélectionnez **Méthodes d’authentification (préversion)**, puis **Protection par mot de passe**.
1. Sous **Verrouillage intelligent personnalisé**, entrez les paramètres de protection de mot de passe de votre choix :

   - **Seuil de verrouillage** : nombre de tentatives de connexion ayant échoué avant le premier verrouillage du compte. Si la première connexion après un verrouillage échoue également, le compte est de nouveau verrouillé.
   - **Durée de verrouillage en secondes** : durée minimale de chaque verrouillage, en secondes. Si un compte est verrouillé à plusieurs reprises, cette durée augmente.

       ![Page de protection par mot de passe du portail Azure dans les paramètres Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Réglage du seuil de verrouillage à 5 dans les paramètres de **Protection par mot de passe***.

1. Sélectionnez **Enregistrer**.

## <a name="testing-the-password-protection-settings"></a>Test des paramètres de protection par mot de passe

La fonctionnalité de verrouillage intelligent utilise de nombreux facteurs pour déterminer quand un compte doit être verrouillé, mais le facteur principal reste le modèle de mot de passe. La fonctionnalité de verrouillage intelligent considère un ensemble de légères variantes de mot de passe, comptées comme une seule tentative. Exemple :

- Les mots de passe tels que 12456! et 1234567! (ou newAccount1234 et newAccount1234) sont tellement similaires que l’algorithme les interprète comme des erreurs humaines et les compte comme une seule tentative.
- Des variations plus importantes dans le modèle, telles que 12456 ! et ABCD2 !, sont comptabilisés comme des tentatives distinctes.

Lorsque vous testez la fonctionnalité de verrouillage intelligent, utilisez un modèle distinctif pour chaque mot de passe que vous entrez. Vous pouvez utiliser des applications Web de génération de mots de passe, comme [https://passwordsgenerator.net/](https://passwordsgenerator.net/).

Lorsque le seuil de verrouillage intelligent est atteint, le message suivant s’affiche lorsque le compte est verrouillé : **Votre compte est temporairement verrouillé pour éviter toute utilisation non autorisée. Réessayez plus tard**. Les messages d’erreur peuvent être [localisés](localization-string-ids.md#sign-up-or-sign-in-error-messages).

## <a name="viewing-locked-out-accounts"></a>Affichage des comptes verrouillés

Pour obtenir des informations sur les comptes verrouillés, vous pouvez consulter le [rapport d’activité de connexion](../active-directory/reports-monitoring/concept-sign-ins.md) d’Active Directory. Sous **État**, sélectionnez **Échec**. Échec des tentatives de connexion avec un **code d’erreur de connexion** de `50053` indique un compte verrouillé :

![Section du rapport de connexion Azure AD montrant un compte verrouillé](./media/threat-management/portal-01-locked-account.png)

Pour en savoir plus sur l’affichage du rapport d’activité de connexion dans Azure Active Directory, consultez [Codes d’erreur du rapport d’activité de connexion](../active-directory/reports-monitoring/concept-sign-ins.md).

