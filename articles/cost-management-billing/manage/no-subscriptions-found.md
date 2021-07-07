---
title: Erreur Aucun abonnement trouvé - Connexion au portail Azure
description: Fournit la solution à un problème qui déclenche l'erreur « Aucun abonnement trouvé » lors de la connexion au portail Azure.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 05/13/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 72a8170b6dffb79d8828d60389728e0bf4d42a41
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110458738"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal"></a>Erreur Aucun abonnement trouvé lors de la connexion au portail Azure

Le message d'erreur « Aucun abonnement trouvé » peut apparaître lorsque vous essayez de vous connecter au [portail Azure](https://portal.azure.com/). Cet article fournit une solution à ce problème.

## <a name="symptom"></a>Symptôme

Le message d'erreur suivant apparaît lorsque vous essayez de vous connecter au [portail Azure](https://portal.azure.com/) : « Aucun abonnement trouvé ».

## <a name="cause"></a>Cause

Ce problème se produit si vous avez sélectionné le mauvais annuaire ou si votre compte ne dispose pas des autorisations suffisantes.

## <a name="solution"></a>Solution

### <a name="scenario-error-message-is-received-in-the-azure-portal"></a>Scénario : un message d'erreur apparaît sur le [portail Azure](https://portal.azure.com)

Pour résoudre ce problème :

* Vérifiez que le répertoire Azure correct soit sélectionné en cliquant sur votre compte en haut à droite.

  ![Sélectionnez l’annuaire en haut à droite du portail Azure.](./media/no-subscriptions-found/directory-switch.png)
* Si le répertoire Azure correct est sélectionné, mais que vous recevez néanmoins le message d’erreur, [affectez le rôle Propriétaire à votre compte](../../role-based-access-control/role-assignments-portal.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
