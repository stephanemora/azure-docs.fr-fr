---
title: Guide de déploiement de réinitialisation de mot de passe libre-service - Azure Active Directory
description: Conseils pour réussir le lancement de la réinitialisation du mot de passe libre-service Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb9333845a78204ea1c6e56e17b3b6f9e5513c9c
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370676"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Comment réussir le lancement de la réinitialisation de mot de passe en libre-service

Pour garantir le déploiement sans problèmes de la fonctionnalité de réinitialisation du mot de passe libre-service (SSPR) d’Azure Active Directory (Azure AD), les clients procèdent généralement comme suit :

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Effectuez un déploiement pilote avec un petit sous-ensemble de votre organisation.
   * Des informations sur la façon de mener cette opération sont disponibles dans le [Tutoriel : Effectuer un déploiement pilote de réinitialisation de mot de passe en libre-service pour Azure AD](tutorial-sspr-pilot.md).
1. Formez votre support technique.
   * Comment vont-ils aider vos utilisateurs ?
   * Obligerez-vous les utilisateurs à utiliser SSPR et interdisez-vous à votre support technique d’aider les utilisateurs ?
   * Leur avez-vous fourni les URL pour l’inscription et la réinitialisation ?
      * Inscription : https://aka.ms/ssprsetup
      * Réinitialisation : https://aka.ms/sspr
1. Formez vos utilisateurs.
   * Les sections suivantes de ce document comprennent les exemples de communication, les portails de mot de passe, l’application d’inscription, et le remplissage de données d’authentification.
   * Le groupe de produits Azure Active Directory a créé un [plan de déploiement étape par étape](https://aka.ms/SSPRDeploymentPlan) que les organisations peuvent utiliser en parallèle avec la documentation disponible sur ce site pour effectuer une étude de cas et un plan de déploiement de la réinitialisation de mot de passe en libre-service.
1. Activez la réinitialisation du mot de passe en libre-service pour toute votre organisation.
   * Lorsque vous êtes prêt, activez la réinitialisation du mot de passe pour tous les utilisateurs en définissant le commutateur **Réinitialisation du mot de passe en libre-service activée** sur **Tout le monde**.

## <a name="sample-communication"></a>Exemple de communication

De nombreux clients estiment qu’une campagne par courrier électronique incluant des instructions simples d’utilisation constitue le moyen le plus simple pour que les utilisateurs aient recours à la réinitialisation du mot de passe libre-service. [Nous avons créé des e-mails simples et d’autres collatéraux que vous pouvez utiliser comme modèles pour vous aider dans votre lancement](https://www.microsoft.com/download/details.aspx?id=56768) :

* **Bientôt disponible** : modèle d’e-mail à utiliser dans les semaines ou les jours précédant le lancement, pour informer les utilisateurs qu’ils doivent effectuer certaines actions.
* **Disponible dès maintenant** : modèle d’e-mail à utiliser le jour du lancement du programme pour inciter les utilisateurs à s’inscrire et à confirmer leurs données d’authentification. En s’inscrivant maintenant, les utilisateurs pourront utiliser la réinitialisation du mot de passe libre-service lorsqu’ils en auront besoin.
* **Rappel d’inscription** : modèle d’e-mail à utiliser dans les jours ou les semaines qui suivent le déploiement pour rappeler aux utilisateurs de s’inscrire et de confirmer leurs données d’authentification.
* **Affiches SSPR** : affiches que vous pouvez personnaliser et montrer dans votre organisation dans les jours et semaines précédant votre lancement, et après celui-ci.
* **Chevalets de table SSPR** : cartes que vous pouvez placer dans la salle à manger, les salles de conférence, ou dans les bureaux pour encourager vos utilisateurs à effectuer l’inscription.
* **Autocollants SSPR** : modèles de vignette que vous pouvez personnaliser et imprimer pour les coller sur les ordinateurs portables, les écrans, les claviers ou les téléphones portables pour se rappeler comment accéder à SSPR.

![Exemples de messagerie SSPR pour le déploiement vers les utilisateurs][Email]

## <a name="create-your-own-password-portal"></a>Création de votre propre portail de mot de passe

Nombre de nos clients choisissent d’héberger une page web et de créer une entrée DNS racine, comme https://passwords.contoso.com. Ils remplissent cette page avec des liens vers les informations suivantes :

* [Portail de réinitialisation du mot de passe Azure AD - https://aka.ms/sspr](https://aka.ms/sspr)
* [Portail d’inscription à la réinitialisation de mot de passe Azure AD - https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portail de modification de mot de passe Azure AD - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Autres informations spécifiques à l’organisation

Dans toutes vos communications par courrier électronique ou prospectus, vous pouvez inclure une URL de marque, facile à retenir que les utilisateurs peuvent suivre quand ils doivent utiliser les services. Pour votre bénéfice, nous avons créé une [page d’exemple de réinitialisation du mot de passe](https://github.com/ajamess/password-reset-page) que vous pouvez utiliser et personnaliser pour les besoins de votre organisation.

## <a name="use-enforced-registration"></a>Utilisation de l’inscription forcée

Si vous souhaitez que vos utilisateurs s’inscrivent pour la réinitialisation du mot de passe, vous pouvez les obliger à s’inscrire lorsqu’ils se connectent via Azure AD. Vous pouvez activer cette option dans le volet **Réinitialisation de mot de passe** de votre répertoire en activant l’option **Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent** dans l’onglet **Inscription**.

Les administrateurs peuvent demander aux utilisateurs de se réinscrire après une période spécifique. Ils peuvent définir l’option **Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification** sur 0 à 730 jours.

Une fois que vous avez activé cette option, lorsque les utilisateurs se connectent, un message les informe que leur administrateur leur a demandé de vérifier leurs informations d’authentification.

## <a name="populate-authentication-data"></a>Renseigner les données d’authentification

Vous devez envisager de [remplir au préalable quelques données d’authentification pour vos utilisateurs](howto-sspr-authenticationdata.md). De cette façon, les utilisateurs n’ont pas besoin de s’inscrire pour la réinitialisation du mot de passe avant de pouvoir utiliser la fonctionnalité SSPR. Tant que leurs données d’authentification sont conformes à la stratégie de réinitialisation de mot de passe que vous avez définie, les utilisateurs sont en mesure de réinitialiser leurs mots de passe.

## <a name="disable-self-service-password-reset"></a>Désactiver la réinitialisation du mot de passe libre-service

Si votre organisation décide de désactiver la réinitialisation du mot de passe libre-service, c’est un processus simple. Ouvrez votre locataire Azure AD et accédez à **Réinitialisation de mot de passe** > **Propriétés**, puis sélectionnez **Aucun** sous **Réinitialisation de mot de passe en libre-service activée**. Les utilisateurs conserveront leurs méthodes d’authentification inscrites pour un usage ultérieur.

## <a name="next-steps"></a>Étapes suivantes

* [Réinitialiser ou modifier votre mot de passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](../user-help/active-directory-passwords-reset-register.md)
* [Activer l’inscription convergée pour Azure Multi-Factor Authentication et la réinitialisation de mot de passe en libre-service Azure AD](concept-registration-mfa-sspr-converged.md)
* [Vous avez une question relative à la licence ?](concept-sspr-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](howto-sspr-authenticationdata.md)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](concept-sspr-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](howto-sspr-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](howto-sspr-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](concept-sspr-howitworks.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Personnalisez ces modèles de messages électroniques en fonction des exigences de votre organisation"
