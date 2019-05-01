---
title: Demander l’accès à un package d’accès dans la gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: Découvrez comment utiliser le portail de mon accès pour demander l’accès à un package d’accès dans la gestion des droits Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541554"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Demander l’accès à un package d’accès dans la gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Connectez-vous au portail mon accès

La première étape consiste à se connecter au portail mon accès où vous pouvez demander l’accès à un package d’accès.

**Rôle :** Demandeur

1. Recherchez un message électronique ou un message à partir du Gestionnaire de projet ou d’entreprise que vous travaillez avec. L’e-mail doit inclure un lien vers le package d’accès que vous devrez accéder à. Le lien commence par :

    `https://myaccess.microsoft.com`

1. Ouvrez le lien.

1. Connectez-vous au portail mon accès.

    Veillez à qu'utiliser votre compte professionnel. Si vous le savez pas, vérifiez auprès de votre responsable de projet ou d’entreprise.

## <a name="request-an-access-package"></a>Demander un accès package

Une fois que vous avez trouvé le package de l’accès dans le portail mon accès, vous pouvez soumettre une demande.

**Rôle :** Demandeur

1. Cliquez sur la coche pour sélectionner le package de l’accès.

    ![Mon portail d’accès - packages Access](./media/entitlement-management-shared/my-access-access-packages.png)

1. Cliquez sur **demander l’accès** pour ouvrir le volet d’accès de requête.

1. Si le **justification professionnelle** s’affiche, fournissez une justification pour avoir accès.

1. Si **demande pendant une période spécifique ?** est activé, sélectionnez **Oui** ou **non**.

1. Si nécessaire, spécifiez la date de début et date de fin.

    ![Mon portail d’accès - demander l’accès](./media/entitlement-management-shared/my-access-request-access.png)

1. Lorsque vous avez terminé, cliquez sur **Submit** pour envoyer votre demande.

1. Cliquez sur **historique des demandes** pour afficher la liste de vos demandes et l’état.

    Si le package de l’accès nécessite une approbation, la demande est désormais dans un état en attente d’approbation.

## <a name="cancel-a-request"></a>Annuler une demande

Si vous envoyez une demande d’accès et de la demande est toujours dans le **en attente d’approbation** d’état, vous pouvez annuler la demande.

**Rôle :** Demandeur

1. Dans le portail mon accès, sur la gauche, cliquez sur **historique des demandes** pour afficher la liste de vos demandes et l’état.

1. Cliquez sur le **vue** lien pour la demande que vous souhaitez annuler.

1. Si la demande est toujours dans le **en attente d’approbation** d’état, vous pouvez cliquer sur **demande d’annulation** pour annuler la demande.

    ![Mon portail d’accès - demande d’annulation](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Cliquez sur **historique des demandes** pour confirmer la demande a été annulée.

## <a name="select-a-policy"></a>Sélectionner une stratégie

Si vous demandent d’accéder à un package d’accès qui a plusieurs stratégies qui s’appliquent, vous devrez peut-être sélectionner une stratégie. Par exemple, un gestionnaire de package d’accès peut configurer un package d’accès avec les deux stratégies pour les deux groupes d’employés internes. La première stratégie peut autoriser l’accès pendant 60 jours et nécessiter une approbation. La deuxième stratégie peut autoriser l’accès à 2 jours et ne nécessitent pas d’approbation. Si vous rencontrez ce scénario, vous devez sélectionner la stratégie que vous souhaitez utiliser.

**Rôle :** Demandeur

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)
- [Demande de processus et les notifications électroniques](entitlement-management-process.md)
