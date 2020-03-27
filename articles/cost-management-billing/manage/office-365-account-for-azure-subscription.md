---
title: S’inscrire à Azure avec un compte Office 365
description: Apprenez à créer un abonnement à Azure avec un compte Office 365.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b42a5b83f5442755614a3cb2ae81cffda09b4b3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238106"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Souscrire un abonnement Azure avec un compte Office 365
Si vous avez un abonnement à Office 365, vous pouvez utiliser votre compte Office 365 pour créer un abonnement Azure. Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre nom d’utilisateur et votre mot de passe Office 365. Si vous souhaitez configurer des machines virtuelles ou utiliser d’autres services Azure, vous devez souscrire un abonnement Azure. Vous pouvez partager votre abonnement Azure avec d’autres personnes et [utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès à vos ressources et abonnement Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Si vous avez déjà à la fois un compte Office 365 et un abonnement Azure, consultez la page [Associer un client Office 365 à un abonnement Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Obtenir un abonnement Azure avec un compte Office 365

Gagnez du temps et évitez la prolifération de comptes en vous inscrivant sur Azure à l’aide de vos nom d’utilisateur et mot de passe Office 365.

1. Inscrivez-vous sur [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Connectez-vous avec votre nom d’utilisateur et votre mot de passe Office 365. Le compte que vous utilisez n’a pas besoin de disposer d’autorisations d’administrateur. Si vous avez plusieurs comptes Office 365, veillez à utiliser les informations d’identification du compte Office 365 que vous souhaitez associer à votre abonnement Azure.

   ![Capture d’écran montrant la page de connexion.](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Entrez les informations requises et terminez le processus d’inscription. Certaines informations ne sont peut-être pas nécessaires si vous avez déjà un compte Office 365.

    ![Capture d’écran montrant le formulaire d’inscription.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Si vous devez ajouter d’autres personnes de votre organisation à l’abonnement Azure, consultez la page [Bien démarrer avec la gestion des accès sur le Portail Azure](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">En savoir plus sur les abonnements Azure et Office 365</a>
Office 365 et Azure utilisent le service Azure AD pour gérer les utilisateurs et les abonnements. Le répertoire Azure est comme un conteneur qui vous sert à regrouper les abonnements et les utilisateurs. Pour utiliser les mêmes comptes d’utilisateur pour vos abonnements Azure et Office 365, vous devez vous assurer que les abonnements Azure sont créés dans le même répertoire que les abonnements Office 365. Gardez à l’esprit les points suivants :

* Un abonnement est créé dans un répertoire.
* Les utilisateurs appartiennent à des répertoires.
* Un abonnement est créé dans le répertoire de l’utilisateur qui crée l’abonnement. Par conséquent, votre abonnement à Office 365 est lié au même compte que votre abonnement Azure.
* Les abonnements Azure sont détenus par des utilisateurs individuels dans le répertoire.
* Les abonnements Office 365 sont détenus par le répertoire proprement dit. Les utilisateurs possédant les autorisations correspondantes dans le répertoire peuvent gérer ces abonnements.

![Capture d’écran montrant la relation entre le répertoire, les utilisateurs et les abonnements.](./media/office-365-account-for-azure-subscription/19-background-information.png)

Pour plus d’informations, consultez la page [Association des abonnements Azure avec Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
