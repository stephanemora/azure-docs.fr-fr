---
title: Migration de compte du Portail Cloud Partner vers l’Espace partenaires - Place de marché commerciale pour Azure
description: Découvrez comment migrer votre compte du Portail Cloud Partner vers l'Espace partenaires. - Place de marché commerciale pour Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 8c7680896507cd3738fa3bce0d30a516d08509c4
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383285"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migration de compte du Portail Cloud Partner vers l’Espace partenaires

Quand vos offres sont migrées du portail Cloud Partner vers l’Espace partenaires, elles sont verrouillées et non modifiables dans le portail Cloud Partner. À ce stade, les paramètres de votre compte doivent être migrés vers l'Espace partenaires. Les paramètres de votre compte, de même que vos offres, peuvent être gérés dans l'Espace partenaires.

## <a name="account-migration-process"></a>Processus de migration de compte

Quand les offres sont migrées depuis le portail Cloud Partner, votre compte est configuré pour la migration. 
 
Si vous disposez du rôle Propriétaire dans le Portail Cloud Partner pour un compte donné, une bannière jaune s’affiche sur votre page Profil d'éditeur. Vous êtes invité à déplacer vos paramètres de compte vers l’Espace partenaires. 

Cliquez sur la bannière pour lancer le processus de migration de votre compte. Vous devez entrer les éléments suivants :

### <a name="work-email-address"></a>Adresse e-mail professionnelle

Dans la plupart des cas, vous vous connectez avec l’adresse e-mail que vous utilisez pour vous connecter au portail Cloud Partner. Dans certains cas, vous devez utiliser une autre adresse e-mail :

* Compte Microsoft : Si le compte du portail Cloud Partner est un compte Microsoft, vous devez entrer une adresse e-mail professionnelle valide associée au locataire pour lequel l’ID MPN est inscrit.

* Incompatibilité du locataire : Si votre adresse e-mail professionnelle n’appartient pas au locataire associé à l’ID Microsoft Partner Network présent sur votre compte du portail Cloud Partner, une erreur s’affiche. Pour y remédier, entrez une adresse e-mail associée au locataire. Un message d’erreur indiquera le nom du locataire.

### <a name="sign-up-for-microsoft-partner-network-program"></a>S'inscrire au programme Microsoft Partner Network

Si votre compte Portail Cloud Partner n'a pas d'ID Microsoft Partner Network ou si celui-ci n'est pas valide, vous devrez vous inscrire au programme Microsoft Partner Network, lors du processus d’activation.

## <a name="after-account-migration-is-complete"></a>Une fois la migration du compte terminée

La migration ne doit être effectuée qu’une seule fois pour un compte donné. Lorsqu'un partenaire a migré le compte, tous les Propriétaires verront ce comportement sur leur page Profil d'éditeur :

1. Vous voyez la page Paramètre du partenaire dans Microsoft Partner Network, où vous pouvez désormais gérer les paramètres du compte. 
2. Une bannière jaune sur la page de votre profil d’éditeur dans le portail Cloud Partner s’affiche pour les utilisateurs qui ont le rôle Propriétaire, en leur demandant de gérer leurs paramètres de compte dans l’Espace partenaires.
3. La page des paramètres de compte dans le portail Cloud Partner passe en mode lecture seule.

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Déplacer des solutions basées sur Dynamics 365 vers l’Espace partenaires

Si vous avez créé des solutions Dynamics 365 for Customer Engagement ou Dynamics 365 for Finance and Operations dans le portail One Commercial Partner GTM, **ces solutions doivent désormais être gérées dans l’Espace partenaires**.

**Si vous n’avez pas déplacé vos solutions avant le 31 août 2019**, effectuez les étapes ci-dessous dès que possible. Tant que vous n’avez pas procédé à cette opération :
- Les éditeurs de logiciels indépendants n’ont pas accès aux avantages marketing
- La co-vente en priorité va perdre son état
- Ceux qui nécessitent Cloud Embed ne seront pas conformes après le 15 octobre 2019

> [!NOTE]
> Si votre compte d’appartenance MPN a été initialement créé dans Partner Membership Center (PMC), connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) pour vérifier que ce compte a été migré avant d’effectuer les étapes ci-dessous. Si vous voyez un écran de profil avec votre ID MPN, vous pouvez continuer. Si ce n’est pas le cas, vous devez démarrer la migration de votre compte en suivant les invites affichées dans [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Si vous avez besoin d’aide pour cette étape, consultez la page de [support](https://partner.microsoft.com/support?issueid=100-0077).

1. Accédez à la [page Vue d’ensemble de la Place de marché commerciale dans l’Espace partenaires](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Si vous voyez la mention « Place de marché commerciale » dans le volet de navigation de gauche, vous êtes inscrit et vous pouvez passer à l’étape suivante. Sinon, [inscrivez-vous dès maintenant sur la Place de marché commerciale](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv).
2. Vérifiez que vos offres sont proposées dans AppSource [en recherchant vos offres](https://appsource.microsoft.com/). Si vos offres se trouvent déjà dans AppSource, passez à l’étape suivante. Si l’une de vos offres n’est pas dans AppSource, créez une [offre Dynamics 365 for Customer](create-new-customer-engagement-offer.md) ou une [offre Dynamics 365 for Operations](create-new-operations-offer.md).
3. Vérifiez votre inscription au programme Business Applications ISV Connect :
  
   * Dans la page [Contrats](https://partner.microsoft.com/dashboard/account/agreements) de l’Espace partenaires, assurez-vous d’avoir accepté l’**addenda du Programme Business Applications ISV** pour l’inscription au programme.
   * Dans la page [Paramètres du compte](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile), indiquez vos informations de facturation.

4. Soumettez toutes vos offres nouvelles ou existantes pour certification, même si elles ont déjà été certifiées. Si vous êtes éligible, vous pouvez demander à participer au niveau Premium au cours de ce processus. Si votre offre a été précédemment certifiée, **vous devez effectuer une recertification d’application avant le 15 octobre 2019.** La certification ou la recertification nécessite que votre application prenne en charge la version la plus récente de notre plateforme Business Applications.
5. Accédez au portail [One Commercial Partner GTM](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) et ajoutez l’URL de votre contenu AppSource dans la section Marketplace Links (Liens vers la Place de marché). Si vous avez besoin d’aide pour cette étape, envoyez-nous un e-mail à cosell@microsoft.com.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer votre compte Place de marché commerciale dans l’Espace partenaires](./manage-account.md) 
