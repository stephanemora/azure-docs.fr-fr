---
title: Fermer votre compte professionnel ou scolaire dans un répertoire non géré - Azure Active Directory | Microsoft Docs
description: Comment fermer votre compte professionnel ou scolaire dans Azure Active Directory non managé.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39b359ef7feeaec541ba17e98a5d1e9b74c6403a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957999"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Fermer votre compte professionnel ou scolaire dans un répertoire non géré

Si vous êtes un utilisateur dans une organisation Azure Active Directory (Azure AD) non managée et que vous n’avez plus besoin d’utiliser des applications à partir de cette organisation ou conserver toute association ayant est, vous pouvez fermer votre compte à tout moment. Un répertoire non géré n’a pas un administrateur Global. Les utilisateurs dans un répertoire non géré peuvent fermer leurs comptes eux-mêmes, sans avoir à contacter un administrateur.

Les utilisateurs dans un répertoire non géré sont souvent créées au cours de libre-service d’abonnement. Un exemple peut être un travailleur de l’information dans une organisation qui s’inscrit pour un service gratuit. Pour plus d’informations sur l’inscription en libre-service, consultez [What ' s libre-service d’abonnement pour Azure Active Directory ?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Avant de commencer

Avant que vous pouvez fermer votre compte, vous devez vérifier les éléments suivants :

* Vérifiez que vous êtes un utilisateur d’un Azure non géré annuaire AD. Vous ne pouvez pas fermer votre compte si vous appartenez à un répertoire géré. Si vous appartenez à un répertoire géré et que vous souhaitez fermer votre compte, vous devez contacter votre administrateur. Pour plus d’informations sur la façon de déterminer si vous appartenez à un répertoire non géré, consultez [supprimer l’utilisateur de client non géré](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Enregistrez toutes les données que vous souhaitez conserver. Pour plus d’informations sur la façon de soumettre une demande d’exportation, consultez [accès et exportation des journaux générés par le système pour les clients non gérés](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Fermeture de votre compte est irréversible. Lorsque vous fermez votre compte, toutes les données personnelles sont supprimées. Ne plus avoir accès à votre compte et les données associées à votre compte.

## <a name="close-your-account"></a>Clôturer votre compte

Pour fermer un non managé compte professionnel ou scolaire, suivez ces étapes :

1. Connectez-vous à [fermer votre compte](https://go.microsoft.com/fwlink/?linkid=873123), en utilisant le compte que vous souhaitez fermer.

1. Sur **mes demandes de données**, sélectionnez **fermer le compte**.

    ![Mes demandes de données - fermeture de compte](./media/users-close-account/close-account.png)

1. Passez en revue le message de confirmation, puis sélectionnez **Oui**.

    ![Mes demandes de données - confirmer fermer](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Étapes suivantes

- [What ' s libre-service d’abonnement pour Azure Active Directory ?](directory-self-service-signup.md)
- [Supprimer l’utilisateur de client non géré](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Accès et exportation des journaux générés par le système pour les clients non gérés](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
