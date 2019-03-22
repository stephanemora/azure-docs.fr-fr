---
title: Obtenir de l’aide avec le portail mes applications - Azure Active Directory | Microsoft Docs
description: Obtenir de l’aide avec l’ouverture de session et effectuer des tâches courantes dans le portail mes applications.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340108"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Résoudre les problèmes avec le portail mes applications
Si vous rencontrez des problèmes liés à l’ouverture de session ou à l’aide de la **mes applications** portail, essayez ces conseils de dépannage avant de contacter le support technique ou votre administrateur de l’aide.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Je ne parviens pas à installer l’Extension de connexion sécurisée à mes applications
Si vous rencontrez des problèmes pendant l’installation de l’Extension de connexion sécurisée à mes applications :

- Vérifiez que vous utilisez un navigateur pris en charge, y compris :

    - **Microsoft Edge.** En cours d’exécution sur Windows 10 Édition anniversaire ou version ultérieure.
    - **Google Chrome.** En cours d’exécution sur Windows 7 ou version ultérieure et sur Mac OS X ou version ultérieure.
    - **Mozilla Firefox 26.0 ou version ultérieure.** En cours d’exécution sur Windows XP SP2 ou version ultérieure et sur Mac OS X 10.6 ou version ultérieure.
    - **Internet Explorer 11.** En cours d’exécution sur Windows 7 ou version ultérieure (prise en charge limitée).

- Assurez-vous que vos paramètres d’extension de navigateur sont sous tension.

- Essayez le redémarrage de votre navigateur et en vous connectant à la **mes applications** à nouveau portail.

- Essayez d’effacer les cookies de votre navigateur, puis redémarrez et connectez-vous à la **mes applications** à nouveau portail.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Je ne peux pas me connecter à la **mes applications** portail
Si vous ne parvenez pas à vous connecter à la **mes applications** portail, vous pouvez essayer ce qui suit :

- Vérifiez que vous utilisez la bonne URL. Il doit être https://myapps.microsoft.com ou une page personnalisée pour votre organisation, tel que https://myapps.microsoft.com/contoso.com.

- Assurez-vous que votre mot de passe est correct et qu’il n’a pas expiré. Pour plus d’informations, consultez [réinitialiser votre mot de passe Professionnel ou scolaire](active-directory-passwords-update-your-own-password.md).

- Assurez-vous que vos informations de vérification soient actuelles et exactes. Pour plus d’informations, consultez [que signifie l’authentification multifacteur Azure pour moi ?](multi-factor-authentication-end-user.md) ou [modifier vos méthodes d’informations de sécurité et les informations](security-info-add-update-methods-overview.md).

- Ajouter le **My App** portal URL vers le **propriétés Internet > sécurité > sites de confiance** paramètre.

- Effacer le cache de votre navigateur et essayez de vous connecter à nouveau.

## <a name="my-password-isnt-working"></a>Mon mot de passe ne fonctionne pas
Si vous avez oublié votre mot de passe, jamais reçu un de votre organisation, êtes verrouillé au sein de votre compte ou à modifier votre mot de passe, consultez [j’ai oublié mon mot de passe Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Je souhaite être en mesure de réinitialiser mon propre mot de passe
Pour être en mesure de réinitialiser votre mot de passe, votre administrateur doit tout d’abord activer la fonctionnalité pour votre organisation, et vous devez mettre à jour, puis vérifier vos méthodes de vérification nécessaire. Pour plus d’informations sur comment mettre à jour de vos méthodes de vérification, consultez [s’inscrire pour la réinitialisation du mot de passe libre-service](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>J’obtiens un message accès refusé lorsque je démarre une application
Si vous obtenez un **accès refusé** message une fois que vous démarrez une application à partir de la **My App** portail, vous pouvez essayer ce qui suit :

- Assurez-vous que vous avez installé le [sécurisée à mes applications-Extension de connexion](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) et que vous utilisez un [navigateur pris en charge](my-apps-portal-end-user-access.md#supported-browsers).

- Assurez-vous que vous utilisez la bonne URL pour l’application, et que l’URL est sur votre **propriétés Internet > sécurité > sites de confiance** liste.

- Assurez-vous que votre mot de passe est correct et qu’il n’a pas expiré. Pour plus d’informations, consultez [réinitialiser votre mot de passe Professionnel ou scolaire](active-directory-passwords-update-your-own-password.md).

- Assurez-vous que vos informations de vérification soient actuelles et exactes. Pour plus d’informations, consultez [que signifie l’authentification multifacteur Azure pour moi ?](multi-factor-authentication-end-user.md) ou [modifier vos méthodes d’informations de sécurité et les informations](security-info-add-update-methods-overview.md).

- Effacer le cache de votre navigateur et essayez de vous connecter à nouveau.

Si après avoir essayé de ces éléments vous toujours pas accéder à votre application, vous devez contacter le support technique de votre organisation pour obtenir une assistance.

## <a name="next-steps"></a>Étapes suivantes
Après vous être connecté à la **mes applications** portail, vous pouvez également mettre à jour votre profil et les informations de compte, vos informations de groupe et accès passez en revue des informations (si vous avez l’autorisation).

- [Accéder à et utiliser des applications sur le portail mes applications](my-apps-portal-end-user-access.md).

- [Modifier vos informations de profil](my-apps-portal-end-user-update-profile.md).

- [Afficher et mettre à jour vos informations de groupes](my-apps-portal-end-user-groups.md).

- [Effectuer votre propre révisions d’accès](my-apps-portal-end-user-access-reviews.md).