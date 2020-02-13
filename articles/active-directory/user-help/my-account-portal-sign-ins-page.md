---
title: Afficher et rechercher votre activité de connexion récente à partir de la page Ma connexion (préversion) – Azure Active Directory | Microsoft Docs
description: Détails sur la façon d’afficher et de rechercher votre activité de connexion récente à partir de la page Mes connexions du portail Mon compte.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064017"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Afficher et rechercher votre activité de connexion récente à partir de la page Mes connexion (préversion)

Vous pouvez consulter toutes les activités récentes de connexion de votre compte professionnel ou scolaire à partir de la page **Mes connexions** du portail **Mon compte**. L’examen de votre historique de connexion vous permet de vérifier s’il y a des activités inhabituelles en vous aidant à voir :

- Si quelqu’un tente de deviner votre mot de passe.

- Si un attaquant s’est connecté à votre compte et depuis quel emplacement.

- Les applications auxquelles l’attaquant a tenté d’accéder.

## <a name="view-your-recent-sign-in-activity"></a>Afficher votre activité de connexion récente

1. Connectez-vous à votre compte professionnel ou scolaire, puis accédez à votre page https://myprofile.microsoft.com/.

2. Sélectionnez **Mes connexions (préversion)** dans le volet de navigation gauche ou sélectionnez le lien **Vérifier l’activité récente** à partir du bloc **Mes connexions (préversion)** .

    ![Page Mon compte, avec les liens d’activité récente en surbrillance](media/my-account-portal/my-account-portal-sign-ins.png)

3. Développez et passez en revue chacun des éléments de connexion, en veillant à reconnaître chacun d’eux. Si vous trouvez un élément de connexion qui ne vous semble pas familier, nous vous recommandons fortement de changer votre mot de passe pour protéger votre compte s’il est compromis.

    ![Page d’activité récente avec les détails de connexion étendus](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Si vous voyez une connexion réussie

Vous devez reconnaître votre propre activité comme étant normale. Toutefois, si vous constatez une connexion réussie à partir d’un emplacement, navigateur ou système d’exploitation étrange, cela peut signifier qu’un attaquant a obtenu l’accès à votre compte. Dans ce cas, nous vous recommandons de modifier immédiatement votre mot de passe, puis d’accéder à la page [Informations de sécurité](https://mysignins.microsoft.com/security-info) pour mettre à jour vos paramètres de sécurité.

Avant de déterminer si quelque chose est incorrect, assurez-vous que vous ne voyez pas un faux positif (où l’élément semble douteux, mais est correct). Par exemple, nous déterminons l’emplacement et le mappage approximatifs en fonction de votre adresse IP. Les réseaux mobiles sont particulièrement difficiles à localiser, car ils acheminent parfois le trafic vers des emplacements distants. Ainsi, si vous vous êtes connecté à l’aide de votre appareil mobile dans l’État de Washington, l’emplacement peut indiquer la connexion en provenance de Californie. Pour cette raison, nous vous suggérons vivement de vérifier plus de détails, au-delà de l’emplacement. Vous devez également vous assurer que le système d’exploitation, le navigateur et l’application sont tous logiques.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Si vous voyez une connexion non réussie

Une connexion non réussie, sans activité de session, signifie que votre méthode de vérification principale (nom d’utilisateur/mot de passe) a échoué. Cela peut signifier que vous avez mal saisi votre nom d’utilisateur ou mot de passe, mais cela peut également signifier qu’un attaquant essayait de deviner votre mot de passe. Si vous pensez que l’attaquant n’a pas réussi à deviner votre mot de passe, vous n’êtes pas obligé de modifier votre mot de passe, mais nous vous suggérons vivement de vous inscrire à Azure Multi-Factor Authentication (MFA). Grâce à l’authentification multifacteur, même si le pirate finit par deviner votre mot de passe, cela ne sera pas suffisant pour accéder à votre compte.

Si vous voyez une connexion non réussie, avec une note sous Activité de session qui indique « **Échec de la vérification supplémentaire, code non valide** », cela signifie que votre authentification principale (nom d’utilisateur/mot de passe) a réussi, mais que la MFA a échoué. S’il s’agissait d’un attaquant, il a correctement deviné votre mot de passe, mais n’a toujours pas réussi à passer outre la MFA. Dans ce cas, nous vous recommandons de tout de même modifier votre mot de passe, dans la mesure où l’attaquant a trouvé cette partie, puis d’accéder à la page [Informations de sécurité](https://mysignins.microsoft.com/security-info) pour mettre à jour vos paramètres de sécurité.

## <a name="search-for-specific-sign-in-activity"></a>Rechercher une activité de connexion spécifique

Vous pouvez effectuer une recherche dans votre activité de connexion récente à l’aide de l’une des informations disponibles. Par exemple, vous pouvez rechercher votre activité de connexion récente par système d’exploitation, emplacement, application, et ainsi de suite.

1. Sur la page **Vérifier l’activité récente**, saisissez les informations que vous souhaitez rechercher dans la barre de**Recherche**. Par exemple, saisissez `My Account` pour rechercher toutes les activités collectées par l’application Mon compte.

2. Sélectionnez le bouton **Rechercher** pour commencer la recherche.

    ![Page Activité récente, avec la barre de recherche, le bouton de recherche et les résultats en surbrillance](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir consulté votre activité de connexion récente, vous pouvez :

- Afficher ou gérer vos [informations de sécurité](user-help-security-info-overview.md).

- Afficher ou gérer vos [appareils](my-account-portal-devices-page.md) connectés.

- Afficher ou gérer vos [organisations](my-account-portal-organizations-page.md).

- Afficher la façon dont votre organisation [utilise vos données relatives à la confidentialité](my-account-portal-privacy-page.md).
