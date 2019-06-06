---
title: Configurer et approuver l’accès juste-à-temps pour les Applications gérées Azure
description: Décrit comment les consommateurs des Applications managées Azure approuver les demandes pour l’accès juste-à-temps à une application managée.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481713"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configurer et approuver l’accès juste-à-temps pour les Applications gérées Azure

En tant que consommateur d’une application managée, vous ne serez pas à l’aise le serveur de publication de ce qui donne un accès permanent à un groupe de ressources gérées. Pour vous donner plus grand contrôle sur l’accès aux ressources gérées, les Applications gérées Azure fournit une fonctionnalité appelée l’accès juste-à-temps (JIT), qui est actuellement en version préliminaire. Il vous permet d’approuver quand et pour la durée pendant laquelle le serveur de publication a accès au groupe de ressources. Le serveur de publication peut rendre les mises à jour requises pendant ce temps, mais lorsque ce temps est terminée, l’accès du serveur de publication expire.

Le flux de travail pour accorder l’accès est :

1. Le serveur de publication ajoute une application managée à la place de marché et spécifie que l’accès JIT est disponible.

1. Au cours du déploiement, vous activez l’accès JIT pour votre instance de l’application managée.

1. Après le déploiement, vous pouvez modifier les paramètres pour l’accès JIT.

1. Le serveur de publication envoie une demande d’accès.

1. Vous approuvez la demande.

Cet article se concentre sur les actions de consommateurs pour activer l’accès JIT et approuver les demandes. Pour en savoir plus sur la publication d’une application managée avec accès JIT, consultez [demander l’accès juste-à-temps dans les Applications gérées Azure](request-just-in-time-access.md).

> [!NOTE]
> Pour utiliser l’accès juste-à-temps, vous devez avoir un [licence Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Activation pendant le déploiement

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Rechercher une entrée de la place de marché pour une application gérée avec JIT est activé. Sélectionnez **Créer**.

1. Tout en fournissant des valeurs pour la nouvelle application managée, le **Configuration JIT** étape vous permet d’activer ou désactiver l’accès JIT pour l’application managée. Sélectionnez **Oui** pour **activer l’accès JIT**. Cette option est sélectionnée par défaut pour les applications managées définies avec JIT est activée dans la place de marché.

   ![Configurer l’accès](./media/approve-just-in-time-access/configure-jit-access.png)

   Vous pouvez uniquement activer l’accès JIT au cours du déploiement. Si vous sélectionnez **non**, le serveur de publication Obtient un accès permanent au groupe de ressources managé. Vous ne pouvez pas activer l’accès JIT ultérieurement.

1. Pour modifier les paramètres d’approbation par défaut, sélectionnez **personnaliser la Configuration de JIT**.

   ![Personnaliser l’accès](./media/approve-just-in-time-access/customize-jit-access.png)

   Par défaut, une application gérée avec JIT activé avec les paramètres suivants :

   * Mode d’approbation – automatique
   * Durée maximale d’accès – 8 heures
   * Approbateurs : aucun

   Quand le mode d’approbation est défini sur **automatique**, les approbateurs recevoir une notification pour chaque demande, mais la demande est approuvée automatiquement. Lorsque la valeur **manuelle**, les approbateurs reçoivent une notification pour chaque demande, et un d’eux doit l’approuver.

   La durée maximale d’activation spécifie la quantité maximale de temps qu'un serveur de publication peut demander pour l’accès au groupe de ressources managé.

   La liste des approbateurs est les utilisateurs Azure Active Directory qui peuvent approuver des demandes d’accès JIT. Pour ajouter un approbateur, sélectionnez **ajouter un approbateur** et recherchez l’utilisateur.

   Après la mise à jour le paramètre, sélectionnez **enregistrer**.

## <a name="update-after-deployment"></a>Mettre à jour après le déploiement

Vous pouvez modifier les valeurs de la façon dont les demandes sont approuvées. Toutefois, si vous n’avez pas activé l’accès JIT au cours du déploiement, vous ne pouvez pas l’activer ultérieurement.

Pour modifier les paramètres pour une application managée déployée :

1. Dans le portail, sélectionnez l’application de gérer.

1. Sélectionnez **Configuration JIT** et modifier les paramètres en fonction des besoins.

   ![Modifier les paramètres de l’accès](./media/approve-just-in-time-access/change-settings.png)

1. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

## <a name="approve-requests"></a>Approuver les demandes

Lorsque le serveur de publication demande l’accès, vous êtes averti de la demande. Vous pouvez approuver des demandes d’accès JIT directement par le biais de l’application managée, soit sur toutes les applications managées via le service Azure AD Privileged Identity Management. Pour utiliser l’accès juste-à-temps, vous devez avoir un [licence Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

Pour approuver les demandes via l’application managée :

1. Sélectionnez **accès JIT** pour l’application gérée, puis sélectionnez **approuver les demandes**.

   ![Approuver les demandes](./media/approve-just-in-time-access/approve-requests.png)
 
1. Sélectionnez la demande à approuver.

   ![Sélectionnez la demande](./media/approve-just-in-time-access/select-request.png)

1. Dans le formulaire, indiquez la raison de l’approbation et sélectionnez **approuver**.

Pour approuver les demandes via Azure AD Privileged Identity Management :

1. Sélectionnez **tous les services** et commencer la recherche pour **Azure AD Privileged Identity Management**. Sélectionner parmi les options disponibles.

   ![Recherchez le service](./media/approve-just-in-time-access/search.png)

1. Sélectionnez **approuver les demandes**.

   ![Sélectionnez approuve les demandes](./media/approve-just-in-time-access/select-approve-requests.png)

1. Sélectionnez **applications managées Azure**, puis sélectionnez la demande à approuver.

   ![Sélectionner les demandes](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la publication d’une application managée avec accès JIT, consultez [demander l’accès juste-à-temps dans les Applications gérées Azure](request-just-in-time-access.md).
