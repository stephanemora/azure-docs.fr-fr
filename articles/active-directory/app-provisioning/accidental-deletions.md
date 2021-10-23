---
title: Activer la prévention des suppressions accidentelles dans l’approvisionnement d’applications dans Azure Active Directory
description: Activez la prévention des suppressions accidentelles dans l’approvisionnement d’applications dans Azure Active Directory.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: bc100df9a0d666048ac64ae4d496c68a793deabb
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991904"
---
# <a name="enable-accidental-deletions-prevention-in-the-azure-ad-provisioning-service-preview"></a>Activer la prévention des suppressions accidentelles dans le service d’approvisionnement d’Azure AD (préversion)

Le service d’approvisionnement Azure AD inclut une fonctionnalité permettant d’éviter les suppressions accidentelles. Cette fonctionnalité garantit que les utilisateurs ne sont pas désactivés ou supprimés dans une application de manière inattendue. 

La fonctionnalité vous permet de spécifier un seuil de suppression, au-delà duquel un administrateur doit choisir explicitement les suppressions à traiter.

> [!NOTE]
> Les suppressions accidentelles ne sont pas prises en charge pour nos intégrations Workday/SuccessFactors. Elles ne sont pas non plus prises en charge pour les modifications d’étendue (par exemple, la modification d’un filtre d’étendue ou la modification de « Synchroniser l’ensemble des utilisateurs et groupes » en « Synchroniser les utilisateurs et groupes assignés ». Tant que la fonctionnalité de prévention des suppressions accidentelles n’est pas entièrement en production, vous devez accéder au portail Azure à l’aide de cette URL : https://aka.ms/AccidentalDeletionsPreview


## <a name="configure-accidental-deletion-prevention"></a>Configurer la prévention des suppressions accidentelles
Pour activer la prévention de la suppression accidentelle :
1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
2.  Sélectionnez **Applications d’entreprise**, puis choisissez votre application.
3.  Sélectionnez **Approvisionnement** puis, dans la page de configuration, sélectionnez **Modifier l’approvisionnement**.
4. Sous **Paramètres**, activez la case à cocher **Empêcher les suppressions accidentelles**, puis spécifiez un seuil de suppression. Assurez-vous également que l’adresse de messagerie de notification est renseignée. Si le seuil de suppression est atteint, un e-mail est envoyé.
5. Sélectionnez **Enregistrer** pour enregistrer les modifications.

Lorsque le seuil de suppression est atteint, le travail est mis en quarantaine et un e-mail de notification est envoyé. Le travail mis en quarantaine peut ensuite être autorisé ou rejeté. Pour en savoir plus sur les états de quarantaine, consultez [Approvisionnement d’application en état de quarantaine](application-provisioning-quarantine-status.md).

## <a name="known-limitations"></a>Limitations connues
Il y a deux limitations clés dont il faut être conscient, que nous nous efforçons de résoudre :
- L’approvisionnement piloté par les RH à partir de Workday et SuccessFactors ne prend pas en charge la fonctionnalité des suppressions accidentelles. 
- Les modifications apportées à la configuration de l’approvisionnement (par exemple, changement d’étendue) ne sont pas prises en charge par la fonctionnalité des suppressions accidentelles. 

## <a name="recovering-from-an-accidental-deletion"></a>Récupération d’une suppression accidentelle
Si vous rencontrez une suppression accidentelle, vous la verrez sur la page d’état de l’approvisionnement.  Elle stipule : **L’approvisionnement a été mis en quarantaine. Pour plus d’informations, consultez les détails de la mise en quarantaine.**

Vous pouvez cliquer sur **Autoriser les suppressions** ou **Afficher les journaux d’approvisionnement**.

### <a name="allowing-deletions"></a>Autorisation des suppressions

L’action **Autoriser les suppressions** permet de supprimer les objets qui ont déclenché le seuil de suppression accidentelle.  Utilisez la procédure suivante pour accepter les suppressions.  

1. Sélectionnez **Autoriser les suppressions**.
2. Cliquez sur **Oui** dans le message de confirmation pour autoriser les suppressions.
3. Vous recevez une confirmation que les suppressions ont été acceptées et que l’état va redevenir sain lors du cycle suivant.

### <a name="rejecting-deletions"></a>Refus des suppressions

Si vous ne voulez pas autoriser les suppressions, vous devez effectuer les étapes suivantes :
- Déterminez la source des suppressions. Vous pouvez utiliser les journaux d’approvisionnement pour plus d’informations.
- Empêchez la suppression en réaffectant l’utilisateur ou le groupe à l’application, en restaurant l’utilisateur ou le groupe, ou en mettant à jour votre configuration d’approvisionnement.
- Une fois que vous avez apporté les modifications nécessaires pour empêcher la suppression de l’utilisateur ou du groupe, redémarrez l’approvisionnement. Ne redémarrez pas l’approvisionnement tant que vous n’avez pas apporté les modifications nécessaires pour empêcher la suppression des utilisateurs ou des groupes. 


### <a name="test-deletion-prevention"></a>Tester la prévention des suppressions
Pour tester la fonctionnalité, vous pouvez déclencher des événements de désactivation/suppression en définissant le seuil sur un nombre bas, par exemple 3, puis en modifiant les filtres d’étendue, en annulant l’affectation d’utilisateurs et en supprimant des utilisateurs de l’annuaire (voir les scénarios courants dans la section suivante). 

Laissez le travail d’approvisionnement s’exécuter (20 à 40 minutes), puis revenez à la page d’approvisionnement. Vous voyez le travail d’approvisionnement en quarantaine et pouvez choisir d’autoriser les suppressions ou d’examiner les journaux d’approvisionnement pour comprendre pourquoi les suppressions se sont produites.

## <a name="common-de-provisioning-scenarios-to-test"></a>Scénarios courants de désapprovisionnement à tester
- Supprimer un utilisateur ou le placer dans la corbeille.
- Bloquer la connexion d’un utilisateur.
- Annuler l’affectation d’un utilisateur ou d’un groupe à partir de l’application.
- Supprimer un utilisateur d’un groupe qui lui donne accès à l’application.

Pour en savoir plus sur les scénarios de désapprovisionnement, consultez [Fonctionnement de l’approvisionnement d’applications](how-provisioning-works.md#de-provisioning).

## <a name="frequently-asked-questions"></a>Forum Aux Questions

### <a name="what-scenarios-count-toward-the-deletion-threshold"></a>Quels scénarios comptent dans le seuil de suppression ?
Quand un utilisateur est configuré pour être supprimé de l’application cible, il est comptabilisé par rapport au seuil de suppression. Voici des scénarios susceptibles d’entraîner la suppression d’un utilisateur de l’application cible : annulation de l’affectation de l’utilisateur à partir de l’application et suppression réversible ou définitive d’un utilisateur dans l’annuaire. Groupes évalués pour le décompte des suppressions par rapport au seuil de suppression. Outre les suppressions, la même fonctionnalité opère également pour les désactivations.

### <a name="what-is-the-interval-that-the-deletion-threshold-is-evaluated-on"></a>Quel est l’intervalle d’évaluation du seuil de suppression ?
Il est évaluée à chaque cycle. Si le nombre de suppressions ne dépasse pas le seuil au cours d’un cycle unique, le « disjoncteur » n’est pas déclenché. Si plusieurs cycles sont nécessaires pour atteindre un état stable, le seuil de suppression est évalué par cycle.

### <a name="how-are-these-deletion-events-logged"></a>Comment ces événements de suppression sont-ils journalisés ?
Vous pouvez trouver des utilisateurs qui devraient être désactivés ou supprimés, mais qui ne le sont pas en raison du seuil de suppression. Accédez à **Journaux d’approvisionnement**, puis filtrez **Action** avec *StagedAction* ou *StagedDelete*.


## <a name="next-steps"></a>Étapes suivantes 

- [Comment fonctionne le provisionnement d’applications](how-provisioning-works.md)
- [Planifier un déploiement de provisionnement d’applications](plan-auto-user-provisioning.md)
