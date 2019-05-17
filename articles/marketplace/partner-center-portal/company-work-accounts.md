---
title: Comptes de travail d’entreprise et les partenaires
description: Comment vérifier si votre entreprise possède un compte professionnel configuré avec Microsoft, créez un nouveau compte de travail ou configurer plusieurs comptes de travail à utiliser avec les partenaires.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: df8ab370e8874e07f8985ecfb3a772848a2e2b21
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806280"
---
# <a name="company-work-accounts-and-partner-center"></a>Comptes de travail d’entreprise et les partenaires

Partenaires utilise des comptes professionnels de l’entreprise, également connu sous les locataires Azure Active Directory (AD), pour gérer l’accès de compte pour plusieurs utilisateurs, les autorisations de contrôle, les groupes hôtes et les applications et conserver les données de profil. En liant le domaine de compte de messagerie de travail de votre société à votre compte espace partenaires, employés de votre entreprise peuvent se connecter aux partenaires pour gérer des offres de la place de marché à l’aide de leurs propres noms d’utilisateur du compte de travail et les mots de passe.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Vérifiez si votre entreprise possède déjà un compte professionnel

Si votre entreprise s’est abonné à un service de cloud Microsoft comme Azure, Microsoft Intune ou Office 365, vous disposez déjà d’un travail compte domaine de messagerie (également appelé un locataire Azure Active Directory) qui peut être utilisé avec les partenaires.

Suivez ces étapes pour vérifier :
1. Connectez-vous au portail d’administration Azure à https://portal.azure.com.
2. Sélectionnez **Azure Active Directory** dans le menu de navigation de gauche, puis sélectionnez **noms de domaine personnalisés**.
3. Si vous avez déjà un compte professionnel, votre nom de domaine s’afficheront.

Si votre entreprise n’a pas encore un compte professionnel, un sera créé pendant le processus d’inscription de partenaires pour vous.

## <a name="set-up-multiple-work-accounts"></a>Configurer plusieurs comptes de travail

Avant de décider d’utiliser un compte de travail existant, déterminer combien d’utilisateurs dans le compte de travail aurez besoin accéder aux partenaires. Si vous avez des utilisateurs dans le compte de travail qui ne doivent accéder aux partenaires, vous souhaiterez envisager de créer plusieurs comptes de travail, afin qu’uniquement aux utilisateurs qui devront accéder partenaires sont représentés sur un compte particulier.

## <a name="create-a-new-work-account"></a>Créer un compte professionnel.

Pour créer un nouveau compte de travail pour votre entreprise, suivez les étapes ci-dessous. Vous devrez peut-être demander une assistance à partir de toute personne qui dispose des autorisations administratives sur le compte Microsoft Azure de votre société.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).
2. Dans le menu de navigation de gauche, sélectionnez le **Azure Active Directory** -> **utilisateurs**.
3. Sélectionnez **nouvel utilisateur** et créer un nouveau compte de travail Azure en entrant une nom et adresse e-mail. Vérifiez que le **rôle d’annuaire** a la valeur **utilisateur** et sélectionnez le **afficher le mot de passe** case à cocher en bas pour afficher et prenez note du mot de passe généré automatiquement.
4. Sélectionnez **créer** pour enregistrer le nouvel utilisateur.

L’adresse de messagerie du compte d’utilisateur doit être un nom de domaine vérifié dans votre répertoire. Vous pouvez répertorier tous les domaines vérifiés dans votre répertoire en sélectionnant **Azure Active Directory** -> **noms de domaine personnalisés** dans le menu de navigation de gauche.

Pour en savoir plus sur l’ajout de domaines personnalisés dans Azure Active Directory, consultez [ajouter ou associer un domaine dans Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Résoudre les problèmes travail e-mail connectez-vous

Si vous ne parvenez pas à vous connecter à votre compte professionnel (également appelé votre client Azure AD), voir le scénario sur le diagramme ci-dessous qui correspond le mieux correspond à votre situation et suivez les étapes recommandées.

![Diagramme pour la résolution des problèmes de connexion du travail compte](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Étapes suivantes

- [Gérer votre compte de la place de marché Commercial dans Partner Center](./manage-account.md) 
