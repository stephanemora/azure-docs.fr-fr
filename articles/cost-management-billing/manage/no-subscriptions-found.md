---
title: Erreur Aucun abonnement trouvé - Connexion au portail Azure | Microsoft Docs
description: Fournit la solution pour un problème dans lequel le message d’erreur Aucun abonnement trouvé s’affiche lorsque vous vous connectez au portail Azure ou au centre des comptes Azure.
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 1573a5d5d9b537b208b2f6d6aea29b9738ddad3e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75984461"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Erreur Aucun abonnement trouvé pour le portail Azure ou le Centre des comptes Azure

Vous pouvez recevoir un message d’erreur « Aucun abonnement trouvé » quand vous essayez de vous connecter au [portail Azure](https://portal.azure.com/) ou au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions). Cet article fournit une solution à ce problème.

## <a name="symptom"></a>Symptôme

Lorsque vous essayez de vous connecter au [portail Azure](https://portal.azure.com/) ou au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions), vous recevez le message d’erreur suivant : « Aucun abonnement trouvé ».

## <a name="cause"></a>Cause :

Ce problème se produit si vous avez sélectionné le mauvais annuaire ou si votre compte ne dispose pas des autorisations suffisantes. 

## <a name="solution"></a>Solution

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scénario 1 : Le message d’erreur s’affiche sur le [portail Azure](https://portal.azure.com)

Pour résoudre ce problème :

* Vérifiez que le répertoire Azure correct soit sélectionné en cliquant sur votre compte en haut à droite.

  ![Sélectionnez l’annuaire en haut à droite du portail Azure.](./media/no-subscriptions-found/directory-switch.png)
* Si le répertoire Azure correct est sélectionné, mais que vous recevez néanmoins le message d’erreur, [affectez le rôle Propriétaire à votre compte](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scénario 2 : Le message d’erreur s’affiche dans le [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions)

Vérifiez que le compte utilisé correspond à l’administrateur du compte. Pour vérifier qui est l’administrateur du compte, procédez comme suit :

1. Connectez-vous à la [vue Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .  

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
