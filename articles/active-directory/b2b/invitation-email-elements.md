---
title: Éléments de l’e-mail d’invitation de B2B - Azure Active Directory | Microsoft Docs
description: Modèle d’e-mail d’invitation de collaboration d’Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768367"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Éléments de l’e-mail d’invitation de collaboration B2B - Azure Active Directory

Les e-mails d’invitation sont un composant essentiel pour intégrer des partenaires comme utilisateurs de collaboration B2B dans Azure AD. Vous pouvez les utiliser pour augmenter le niveau de confiance du destinataire. Vous pouvez ajouter de la légitimité et une preuve sociale à l’e-mail de sorte ce que le destinataire sélectionne sans crainte le bouton **Prise en main** pour accepter l’invitation. Cette approbation est un élément clé pour réduire les problèmes de partage. Et elle vous permet aussi d’envoyer de superbes e-mails !

![Capture d’écran montrant l’e-mail d’invitation B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Explication de l’e-mail
Examinons quelques-uns des éléments de l’e-mail pour savoir comment utiliser au mieux ces fonctionnalités.

### <a name="subject"></a>Objet
L’objet de l’e-mail suit le modèle suivant : vous êtes invité à l’organisation &lt;nom_tenant&gt;.

### <a name="from-address"></a>Adresse de l’expéditeur
Nous utilisons un modèle similaire à LinkedIn pour l’adresse De.  Vous devez faire apparaître clairement qui est l’inviteur et à quelle entreprise il appartient, mais aussi indiquer que l’e-mail provient d’une adresse e-mail Microsoft. Le format est le suivant : Invitations Microsoft<invites@microsoft.com> ou &lt;nom d’affichage de l’inviteur&gt; de &lt;tenantname&gt; (par le biais de Microsoft) <invites@microsoft.com>.

### <a name="reply-to"></a>Adresse de réponse
L’adresse e-mail de réponse est définie sur l’adresse e-mail de l’inviteur quand elle est disponible : ainsi, répondre à l’e-mail envoie un e-mail en retour à l’inviteur.

### <a name="branding"></a>Personnalisation
Les e-mails d’invitation de votre locataire utilisent la personnalisation d’entreprise que vous avez éventuellement définie pour votre locataire. Si vous voulez tirer parti de cette capacité, [voici](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) les informations de configuration. Le logo de la bannière s’affiche dans l’e-mail. Suivez [ces instructions](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) relatives à la taille et à la qualité de l’image pour obtenir un résultat optimal. En outre, le nom de l’entreprise apparaît également dans l’invite à l’action.

### <a name="call-to-action"></a>Invite à l’action
L’invite à l’action se compose de deux parties : explication de la raison pour laquelle le destinataire a reçu l’e-mail et ce qu’il est demandé comme action au destinataire.
- La section « Pourquoi » peut être gérée au format suivant : vous êtes invité à accéder aux applications de l’organisation &lt;nom_tenant&gt;.

- Et la section « Ce qu’il vous est demandé de faire » est indiquée par la présence du bouton **Bien démarrer**. Quand le destinataire a été ajouté sans que des invitations soient nécessaires, ce bouton ne s’affiche pas.

### <a name="inviters-information"></a>Informations de l’inviteur
Le nom d’affichage de l’inviteur est inclus dans l’e-mail. En outre, si vous avez configuré une image de profil pour votre compte Azure AD, l’e-mail d’invitation inclut également cette image. Ces deux éléments sont destinés à améliorer la confiance de votre destinataire dans l’e-mail.

Si vous n’avez pas encore configuré votre image de profil, une icône comportant les initiales de l’inviteur remplace l’image, comme ceci :

  ![Capture d’écran illustrant l’invitation avec l’affichage des initiales de l’inviteur](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>body
Le corps contient le message composé par l’inviteur pour [inviter un utilisateur invité dans un répertoire, un groupe ou une application](add-users-administrator.md) ou [avec l’API d’invitation](customize-invitation-api.md). Il s’agit d’une simple zone de texte qui ne traite pas les balises HTML pour des raisons de sécurité.

  ![Capture d’écran illustrant le corps de l’e-mail d’invitation](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Section Pied de page
Le pied de page contient la marque de la société Microsoft et permet au destinataire de savoir si l’e-mail a été envoyé à partir d’un alias non surveillé. 

Cas particuliers :

- L’inviteur n’a pas d’adresse e-mail dans la location de l’inviteur

  ![Capture d’écran illustrant le cas où l’inviteur n’a pas l’e-mail dans la location de l’inviteur](media/invitation-email-elements/inviter-no-email.png)


- Le destinataire n’a pas besoin de réclamer l’invitation

  ![Capture d’écran illustrant le cas où le destinataire n’a pas besoin d’accepter l’invitation](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Comment la langue est-elle déterminée ?
Les paramètres suivants déterminent la langue présentée à l’utilisateur invité dans l’e-mail d’invitation. Ces paramètres sont répertoriés par ordre de priorité. Si un paramètre n’est pas configuré, le paramètre suivant de la liste détermine la langue. 
- Propriété **messageLanguage** de l'objet [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) si l'API Créer une invitation est utilisée
-   Propriété **preferredLanguage** spécifiée dans l'[objet utilisateur](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) de l'invité
-   **Langue de notification** définie dans les propriétés du client de base de l'utilisateur invité (pour les clients Azure AD uniquement)
-   **Langue de Notification** définie dans les propriétés du client de la ressource

Si aucun de ces paramètres n'est configuré, l'anglais (États-Unis) est la langue par défaut.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu’est-ce qu’Azure AD B2B Collaboration ?](what-is-b2b.md)
- [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](add-users-administrator.md)
- [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](add-users-information-worker.md)
- [Utilisation d’une invitation B2B Collaboration](redemption-experience.md)
- [Ajouter des utilisateurs B2B Collaboration sans invitation](add-user-without-invite.md)
