---
title: Résoudre les problèmes de transferts de réservation Azure entre les locataires
description: Cet article aide les propriétaires de réservations à transférer un ordre de réservation d’un locataire Azure Active Directory (répertoire) vers un autre.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055798"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Résoudre les problèmes de transferts de réservation entre les locataires

Cet article aide les propriétaires de réservations à transférer un ordre de réservation d’un locataire Azure Active Directory (répertoire) vers un autre. Lorsque vous modifiez le répertoire d’une commande de réservation,cela supprime tout accès Azure RBAC à la commande de réservation et aux réservations dépendantes. Vous serez le seul à y avoir accès après la modification. La modification du répertoire ne change pas la propriété de facturation de la commande de réservation. Le répertoire est modifié pour la commande de réservation parent et les réservations dépendantes.

Il n’est pas nécessaire d’échanger ou d’annuler une réservation pour passer d’un locataire à l’autre.

Après avoir transféré une réservation à un autre locataire, vous pouvez également ajouter d’autres propriétaires à la réservation. Pour plus d’informations, consultez [Qui peut gérer une réservation par défaut](view-reservations.md#who-can-manage-a-reservation-by-default).

Lorsque vous transférez une commande de réservation, toutes les réservations sous la commande sont transférées avec elle.

## <a name="transfer-a-reservation"></a>Transférer une réservation

Procédez comme suit pour transférer une commande de réservation et ses réservations dépendantes à un autre locataire.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Si vous n’êtes pas administrateur de facturation, mais que vous êtes propriétaire de la réservation, accédez à **Réservations**, puis passez à l’étape 6.
1. Accédez à **Cost Management + facturation**.
    - Si vous êtes un administrateur d’entreprise, dans le menu de gauche, sélectionnez **Étendues de facturation**, puis, dans la liste des étendues de facturation, sélectionnez-en une.
    - Si vous êtes propriétaire d’un profil de facturation Contrat client Microsoft, dans le menu de gauche, sélectionnez **Profils de facturation**. Dans la liste des profils de facturation, sélectionnez-en un.
1. Dans le menu de gauche, sélectionnez **Transactions de réservation**. La liste des transactions de réservation est affichée.
1. Une bannière en haut de la page indique *Les administrateurs de facturation peuvent désormais gérer les réservations. Cliquez ici pour gérer les réservations.* Sélectionnez la bannière.
1. La liste complète des réservations pour votre profil d’inscription ou de facturation d’administrateur d’entreprise s’affiche.
1. Sélectionnez la réservation que vous souhaitez transférer.
1. Dans les détails de la réservation, sélectionnez l’ID de la commande de réservation.
1. Dans la commande de réservation, sélectionnez **Modifier le répertoire**.
1. Dans le volet Modifier le répertoire, sélectionnez le répertoire Azure AD vers lequel vous souhaitez transférer la réservation, puis sélectionnez **Confirmer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations, consultez [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md).