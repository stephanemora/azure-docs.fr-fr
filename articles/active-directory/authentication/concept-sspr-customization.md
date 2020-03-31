---
title: Personnaliser la réinitialisation de mot de passe en libre-service - Azure Active Directory
description: Options de personnalisation de la réinitialisation du mot de passe libre-service Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979458"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personnaliser les fonctionnalités d’Azure AD pour la réinitialisation du mot passe libre-service

Les professionnels de l’informatique qui souhaitent déployer la réinitialisation du mot de passe libre-service dans Azure Active directory (Azure AD) peuvent personnaliser l’expérience en fonction des besoins de leurs utilisateurs.

## <a name="customize-the-contact-your-administrator-link"></a>Personnaliser le lien « Contactez votre administrateur »

Les utilisateurs de la réinitialisation de mot de passe en libre-service disposent d’un lien « Contactez votre administrateur » dans le portail de réinitialisation de mot de passe. Si un utilisateur sélectionne ce lien, il effectue l’une des deux opérations suivantes :

* S'il reste dans son état par défaut :
   * Un e-mail est envoyé à vos administrateurs pour demander une assistance lors de la modification du mot de passe de l’utilisateur. Consultez [l'exemple d'e-mail](#sample-email) ci-dessous.
* S'il est personnalisé :
   * Il renvoie l'utilisateur vers une page web ou une adresse e-mail spécifiée par l’administrateur afin d'obtenir de l’aide.

> [!TIP]
> Si vous le personnalisez, nous vous recommandons d'opter pour une solution que les utilisateurs connaissent.

> [!WARNING]
> Si vous personnalisez ce paramètre avec une adresse e-mail et un compte nécessitant une réinitialisation du mot de passe, l’utilisateur peut ne pas être en mesure de demander de l’aide.

### <a name="sample-email"></a>Exemple de message électronique

![Exemple d’e-mail de demande de réinitialisation envoyé à l’administrateur][Contact]

L’e-mail de contact est envoyé aux destinataires suivants dans cet ordre :

1. Si les rôles **Administrateur du support technique** ou **Administrateur de mot de passe** est affecté, les administrateurs disposant de ces rôles sont avertis.
1. Si aucun administrateur du support technique ou administrateur de mot de passe n’est affecté, les administrateurs disposant du rôle **Administrateur d’utilisateurs** sont avertis.
1. Si aucun des rôles précédents n’a été affecté, les **administrateurs globaux** sont avertis.

Dans tous les cas, un maximum de 100 destinataires sont informés.

Pour en savoir plus sur les différents rôles d’administrateur et sur la façon de les affecter, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Désactiver les e-mails « Contactez votre administrateur »

Si votre organisation ne souhaite pas avertir les administrateurs au sujet des demandes de réinitialisation de mot de passe, vous pouvez activer la configuration suivante :

* Activez la réinitialisation du mot de passe en libre-service pour tous les utilisateurs finaux. Cette option se trouve sous **Réinitialisation de mot de passe** > **Propriétés**. Si vous ne voulez pas que les utilisateurs réinitialisent leurs propres mots de passe, vous pouvez étendre l’accès à un groupe vide. *Nous ne recommandons pas cette option.*
* Personnalisez le lien du support technique pour fournir une URL web ou un lien mailto: que les utilisateurs peuvent utiliser pour obtenir une assistance. Cette option se trouve sous **Réinitialisation de mot de passe** > **Personnalisation** > **Adresse e-mail ou URL du support technique**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personnaliser la page de connexion AD FS pour la réinitialisation du mot de passe libre-service

Les administrateurs des services de fédération Active Directory (AD FS) peuvent ajouter un lien à leur page de connexion à l’aide des instructions figurant dans l’article [Ajouter la description de la page de connexion](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Pour ajouter un lien vers la page de connexion AD FS, exécutez la commande suivante sur votre serveur AD FS. Les utilisateurs peuvent utiliser cette page pour accéder au flux de travail de réinitialisation du mot de passe libre-service.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personnaliser l’apparence du panneau d’accès et de la page de connexion

Vous pouvez personnaliser la page de connexion. Vous pouvez ajouter un logo qui apparaît à côté de l’image qui correspond à la marque de votre société.

Les graphiques que vous choisissez s’affichent dans les circonstances suivantes :

* Quand un utilisateur entre son nom d’utilisateur
* Si l’utilisateur accède à l’URL personnalisée :
   * En passant le paramètre `whr` à la page de réinitialisation du mot de passe, par exemple `https://login.microsoftonline.com/?whr=contoso.com`
   * En passant le paramètre `username` à la page de réinitialisation du mot de passe, par exemple `https://login.microsoftonline.com/?username=admin@contoso.com`

Retrouvez plus d’informations sur la configuration de la marque de société dans l’article [Ajouter une marque de société à votre page de connexion dans Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nom de l’annuaire

Vous pouvez changer l’attribut de nom de l’annuaire sous **Azure Active Directory** > **Propriétés**. Vous pouvez afficher un nom d’organisation convivial visible dans le portail et dans les communications automatisées. Cette option est essentiellement visible dans les e-mails automatiques dans les formes qui suivent :

* Le nom convivial dans l’e-mail, par exemple « Microsoft pour le compte de la démonstration CONTOSO »
* La ligne d’objet dans l’e-mail, par exemple « Code de vérification d’e-mail pour le compte de démonstration CONTOSO »

## <a name="next-steps"></a>Étapes suivantes

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](howto-sspr-deployment.md)
* [Réinitialiser ou modifier votre mot de passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](../user-help/active-directory-passwords-reset-register.md)
* [Vous avez une question relative à la licence ?](concept-sspr-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](howto-sspr-authenticationdata.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](concept-sspr-howitworks.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](concept-sspr-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](howto-sspr-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](howto-sspr-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](concept-sspr-howitworks.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Si nécessaire, contactez votre administrateur pour réinitialiser votre exemple d’e-mail de mot le passe"
