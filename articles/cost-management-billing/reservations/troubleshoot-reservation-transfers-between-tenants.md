---
title: Changer un répertoire de réservation Azure
description: Cet article aide les propriétaires de réservations à transférer un ordre de réservation d’un locataire Azure Active Directory (répertoire) vers un autre.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.openlocfilehash: 183071454cbe6c9185ecb1868abfe1ac217e9519
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108143662"
---
# <a name="change-an-azure-reservation-directory-between-tenants"></a>Changer un répertoire de réservation Azure entre des locataires

Cet article aide les propriétaires de réservations à changer le répertoire d’un ordre de réservation d’un locataire Azure Active Directory (répertoire) vers un autre. Lorsque vous modifiez le répertoire d’une commande de réservation,cela supprime tout accès Azure RBAC à la commande de réservation et aux réservations dépendantes. Vous serez le seul à y avoir accès après la modification. La modification du répertoire ne change pas la propriété de facturation de la commande de réservation. Le répertoire est modifié pour la commande de réservation parent et les réservations dépendantes.

Il n’est pas nécessaire d’échanger ou d’annuler une réservation pour changer le répertoire d’un ordre de réservation.

Après avoir changé le répertoire d’une réservation à un autre locataire, vous pouvez également ajouter d’autres propriétaires à la réservation. Pour plus d’informations, consultez [Qui peut gérer une réservation par défaut](view-reservations.md#who-can-manage-a-reservation-by-default).

Lorsque vous changez le répertoire d’un ordre de réservation, toutes les réservations sous cet ordre sont transférées avec lui.

## <a name="change-a-reservation-orders-directory"></a>Changer le répertoire d’un ordre de réservation

Procédez comme suit pour changer le répertoire d’un ordre de réservation et ses réservations dépendantes vers un autre locataire.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Si vous n’êtes pas administrateur de facturation, mais que vous êtes propriétaire de la réservation, accédez à **Réservations**, puis passez à l’étape 5.
1. Accédez à **Cost Management + facturation**.
    - Si vous êtes un administrateur d’entreprise, dans le menu de gauche, sélectionnez **Étendues de facturation**, puis, dans la liste des étendues de facturation, sélectionnez-en une.
    - Si vous êtes propriétaire d’un profil de facturation Contrat client Microsoft, dans le menu de gauche, sélectionnez **Profils de facturation**. Dans la liste des profils de facturation, sélectionnez-en un.
1. La liste complète des réservations pour votre profil d’inscription ou de facturation d’administrateur d’entreprise s’affiche.
1. Sélectionnez la réservation que vous souhaitez transférer.
1. Dans les détails de la réservation, sélectionnez l’ID de la commande de réservation.
1. Dans la commande de réservation, sélectionnez **Modifier le répertoire**.
1. Dans le volet Modifier le répertoire, sélectionnez le répertoire Azure AD vers lequel vous souhaitez transférer la réservation, puis sélectionnez **Confirmer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations, consultez [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md).