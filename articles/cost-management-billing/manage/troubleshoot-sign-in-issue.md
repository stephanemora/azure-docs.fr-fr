---
title: Résoudre les problèmes de connexion à l’abonnement Azure
description: Aide à résoudre les problèmes de connexion au Portail Azure ou au Centre des comptes Azure.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 74cf3f197895823bcbd94db0bf3c7bd8065df767
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75984469"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Résoudre les problèmes de connexion à l’abonnement Azure

Ce guide aide à résoudre les problèmes de connexion au Portail Azure ou au Centre des comptes Azure.

## <a name="issues"></a>Problèmes

### <a name="page-hangs-in-the-loading-status"></a>La page se bloque à l’état de chargement

Si la page de votre navigateur Internet se bloque, essayez chacune des étapes suivantes jusqu’à ce que vous puissiez accéder au Portail Azure.

- Actualisez la page.
- Utilisez un autre navigateur Internet.
- Utilisez le mode de navigation privée de votre navigateur. Pour Internet Explorer : Cliquez sur **Outils** > **Sécurité** > **Navigation InPrivate**, puis accédez et connectez-vous au [Portail Azure](https://portal.azure.com/) ou au [Centre de comptes Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Vous êtes automatiquement connecté comme un autre utilisateur

Ce problème peut se produire si vous utilisez plusieurs comptes d’utilisateur dans un navigateur Internet.

Pour résoudre le problème, essayez l’une des méthodes suivantes :

- Effacez la mémoire cache et supprimez les cookies Internet. Dans Internet Explorer, cliquez sur **Outils** > **Options Internet** > **Supprimer**. Vérifiez que les cases à cocher pour les fichiers temporaires, les cookies, les mots de passe et l’historique de navigation sont sélectionnées, puis cliquez sur Supprimer.
- Réinitialisez les paramètres Internet Explorer pour rétablir les paramètres personnels que vous avez définis. Cliquez sur **Outils** > **Options Internet** > **Avancé** > cochez la case **Supprimer les paramètres personnels** > **Réinitialiser**.
- Utilisez le mode de navigation privée de votre navigateur. Pour Internet Explorer :  Cliquez sur **Outils** > **Sécurité** > **Navigation InPrivate**, puis accédez et connectez-vous au [Portail Azure](https://portal.azure.com/) ou au [Centre de comptes Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Je peux me connecter mais le message *Aucun abonnement trouvé* s’affiche

Ce problème se produit si vous avez sélectionné le mauvais annuaire ou si votre compte ne dispose pas des autorisations suffisantes.

**Scénario 1 :** Le message d’erreur s’affiche sur le [portail Azure](https://portal.azure.com/)

Pour résoudre ce problème :

- Vérifiez que le répertoire Azure correct soit sélectionné en cliquant sur votre compte en haut à droite.
- Si le répertoire Azure correct est sélectionné, mais que vous recevez néanmoins le messager d’erreur, [faites ajouter votre compte en tant que Propriétaire](add-change-subscription-administrator.md).

**Scénario 2 :** Le message d’erreur s’affiche dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions)

Vérifiez que le compte utilisé correspond à l’administrateur du compte. Pour vérifier qui est l’administrateur du compte, procédez comme suit :

1. Connectez-vous à la [vue Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.

3. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .

## <a name="additional-help-resources"></a>Ressources d'aide supplémentaires

Autres articles sur la résolution des problèmes liés à la facturation et aux abonnements Azure

- [Carte refusée](troubleshoot-declined-card.md)
- [Problèmes de souscription à l’abonnement](troubleshoot-azure-sign-up.md)
- [Aucun abonnement trouvé](no-subscriptions-found.md)
- [Désactivation de l’affichage des coûts d’entreprise](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contactez-nous pour obtenir de l'aide

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- [Documentation sur la facturation Azure](../../billing/index.md)
