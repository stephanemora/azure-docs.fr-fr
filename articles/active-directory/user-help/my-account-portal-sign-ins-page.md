---
title: Afficher et rechercher votre activité de connexion récente à partir de la page Mes connexions – Azure Active Directory | Microsoft Docs
description: Détails sur la façon d’afficher et de rechercher votre activité de connexion récente à partir de la page Mes connexions du portail Mon compte.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/22/2021
ms.author: curtand
ms.openlocfilehash: 593ec6588e2f55cb9f379dcfac437956bcabc10b
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107951315"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Afficher et rechercher votre activité de connexion récente à partir de la page Mes connexions

Vous pouvez consulter toutes les activités récentes de connexion de votre compte professionnel ou scolaire à partir de la page **Mes connexions** du portail **Mon compte**. L’examen de votre historique de connexion vous permet de vérifier s’il y a des activités inhabituelles en vous aidant à voir :

- Si quelqu’un tente de deviner votre mot de passe.
- Si un attaquant s’est connecté à votre compte et depuis quel emplacement.
- Les applications auxquelles l’attaquant a tenté d’accéder.

>[!Note]
> Si vous voyez une erreur lors de la connexion avec un compte Microsoft personnel, vous pouvez toujours vous connecter en utilisant le nom de domaine de votre organisation (par exemple, contoso.com) ou l’**ID de locataire** de votre organisation obtenu auprès de votre administrateur dans l’une des URL suivantes :
>
>   - `https://myaccount.microsoft.com?tenantId=<your_domain_name>`
>   - `https://myaccount.microsoft.com?tenant=<your_tenant_ID>`

## <a name="view-your-recent-sign-in-activity"></a>Afficher votre activité de connexion récente

1. Connectez-vous à votre compte professionnel ou scolaire, puis accédez à votre page https://myaccount.microsoft.com/.

1. Sélectionnez **Mes connexions** dans le volet de navigation gauche ou sélectionnez le lien **Vérifier l’activité récente** à partir du bloc **Mes connexions**.

    ![Page Mon compte, avec les liens d’activité récente en surbrillance](media/my-account-portal/my-account-portal-sign-ins.png)

1. Développez et passez en revue chacun des éléments de connexion, en veillant à reconnaître chacun d’eux. Si vous trouvez un élément de connexion qui ne vous semble pas familier, modifiez votre mot de passe pour protéger votre compte au cas où il serait compromis.

    ![Page d’activité récente avec les détails de connexion étendus](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>En cas de connexion réussie

Parfois, en examinant votre activité de connexion normale, vous pouvez remarquer une connexion réussie à partir d’un emplacement, d’un navigateur ou d’un système d’exploitation inconnus. Les connexions inconnues peuvent signifier qu’une personne malveillante a obtenu l’accès à votre compte. Si vous observez une activité que vous n’avez pas autorisée, nous vous recommandons de modifier immédiatement votre mot de passe, puis d’accéder à [Informations de sécurité](https://mysignins.microsoft.com/security-info) pour mettre à jour vos paramètres de sécurité.

Avant de déterminer si quelque chose est incorrect, assurez-vous que vous ne voyez pas un faux positif (où l’élément semble douteux, mais est correct). Par exemple, nous déterminons votre emplacement approximatif et une carte en fonction de votre adresse IP. Les réseaux mobiles sont particulièrement difficiles à localiser, car ils acheminent parfois le trafic vers des emplacements distants. Même si vous vous connectez à l’aide de votre appareil mobile dans l’État de Washington, l’emplacement peut indiquer une connexion en provenance de Californie. Nous vous suggérons vivement de vérifier plus de détails, au-delà de l’emplacement. Assurez-vous également que le système d’exploitation, le navigateur et l’application sont tous logiques.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>En cas de connexion infructueuse

Si vous constatez une connexion infructueuse, cela peut signifier que vous avez tapé vos informations d’identification de manière incorrecte. Cela peut également signifier qu’un attaquant essayait de deviner votre mot de passe. Pour répondre à ce risque, vous n’êtes pas obligé de modifier votre mot de passe, mais nous vous recommandons de vous inscrire à Azure AD Multi-Factor Authentication (MFA). Grâce à l’authentification multifacteur, même si le pirate devine votre mot de passe, cela ne sera pas suffisant pour accéder au compte.

![Vignette de connexion infructueuse](media/my-account-portal-sign-ins-page/unsuccessful.png)

Si vous voyez une connexion infructueuse, avec une note sous **Activité de session** qui indique `Additional verification failed, invalid code`, cela signifie que les informations d’identification de votre authentification principale ont permis la connexion, mais pas l’authentification multifacteur. Cette condition peut signifier qu’un attaquant a correctement deviné votre mot de passe, mais n’a pas réussi à surmonter l’authentification multifacteur. Nous vous recommandons de tout de même modifier votre mot de passe, dans la mesure où l’attaquant est susceptible de le connaître, et d’accéder à [Informations de sécurité](https://mysignins.microsoft.com/security-info) pour mettre à jour vos paramètres de sécurité.

## <a name="search-for-specific-sign-in-activity"></a>Rechercher une activité de connexion spécifique

Vous pouvez effectuer une recherche dans votre activité de connexion récente à l’aide de l’une des informations disponibles. Par exemple, vous pouvez rechercher votre activité de connexion récente par système d’exploitation, emplacement, application, et ainsi de suite.

1. Sur la page **Vérifier l’activité récente**, saisissez les informations que vous souhaitez rechercher dans la barre de **Recherche**. Par exemple, saisissez `Unsuccessful` pour rechercher toutes les activités de connexion infructueuse collectées par l’application Mon compte.

2. Sélectionnez le bouton **Rechercher** pour commencer la recherche.

    ![Page Activité récente, avec la barre de recherche, le bouton de recherche et les résultats en surbrillance](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Confirmer une activité inhabituelle

Les connexions signalées comme des activités inhabituelles peuvent être confirmées dans la vignette de cette activité dans la page **Mes connexions**.

![Vignette de connexion inhabituelle pour confirmer que vous êtes ou non à l’origine de la tentative de connexion](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="view-sign-in-activity-on-android"></a>Consulter l'activité de connexion sous Android

Chaque fois que vous consultez ou modifiez des informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire pour continuer.

1. Ouvrez l'application Microsoft Authenticator et sélectionnez la page d'accueil de votre compte professionnel ou scolaire.

1. Sélectionnez **Activité récente du compte**.

    ![Page d'accueil du compte professionnel ou scolaire sur laquelle apparaît en surbrillance le lien d'accès aux activités de connexion les plus récentes](media/my-account-portal-sign-ins-page/android-recent-activity.png)

1. Si votre connexion aboutit, passez en revue chacune des activités de connexion, en vous assurant que vous les reconnaissez toutes. Si vous trouvez un élément de connexion qui ne vous semble pas familier, modifiez votre mot de passe pour protéger votre compte au cas où il serait compromis.

    ![Page relative à l'activité récente contenant les détails de connexion](media/my-account-portal-sign-ins-page/android-activity-page.png)

## <a name="change-your-password-on-android"></a>Changer votre mot de passe sous Android

Votre administrateur peut vous permettre de réinitialiser votre mot de passe à partir de l'application Authenticator. Chaque fois que vous connectez pour consulter ou modifier des informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire pour continuer.

1. Ouvrez l'application Microsoft Authenticator et sélectionnez la page d'accueil de votre compte professionnel ou scolaire.

1. Sélectionnez **Modifier le mot de passe**.

    ![Page d'accueil du compte professionnel ou scolaire sur laquelle apparaît en surbrillance le lien Modifier le mot de passe](media/my-account-portal-sign-ins-page/android-change-password.png)

1. Si votre connexion aboutit, vous pouvez mettre à jour votre mot de passe sur la page **Modifier le mot de passe**.

    ![Page relative à l'activité récente contenant l'ancien et le nouveau mot de passe](media/my-account-portal-sign-ins-page/android-password-page.png)

## <a name="update-security-info-on-android"></a>Mettre à jour les informations de sécurité sous Android

Chaque fois que vous connectez pour consulter ou modifier des informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire pour continuer.

1. Ouvrez l'application Microsoft Authenticator et sélectionnez la page d'accueil de votre compte professionnel ou scolaire.

1. Sélectionnez **Mettre à jour les informations de sécurité**.

    ![Page d'accueil du compte professionnel ou scolaire sur laquelle apparaît en surbrillance le lien Mettre à jour les informations de sécurité](media/my-account-portal-sign-ins-page/android-update-security-info.png)

1. Si votre connexion aboutit, vous pouvez mettre à jour vos détails de sécurité sur la page **Informations de sécurité**.

    ![Page relative à l'activité récente contenant les informations de sécurité](media/my-account-portal-sign-ins-page/android-security-info-page.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir consulté vos informations de connexion récentes, vous pouvez :

- Afficher ou gérer vos [informations de sécurité](./security-info-setup-signin.md).

- Afficher ou gérer vos [appareils](my-account-portal-devices-page.md) connectés.

- Afficher ou gérer vos [organisations](my-account-portal-organizations-page.md).

- Afficher la façon dont votre organisation [utilise vos données relatives à la confidentialité](my-account-portal-privacy-page.md).

- Modifier vos [paramètres du portail Mon compte](my-account-portal-settings.md)