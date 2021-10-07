---
title: Créer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans PIM – Azure AD | Microsoft Docs
description: Découvrez comment créer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.subservice: pim
ms.date: 9/3/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 933b66a3a8277763112c0a1838f13fef6dd81544
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452657"
---
# <a name="create-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Créer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans PIM

Les besoins d’accès aux rôles de ressources Azure privilégiés et aux rôles Azure AD par les employés évoluent au fil du temps. Pour réduire les risques associés aux attributions de rôles obsolètes, vous devez vérifier les accès régulièrement. Vous pouvez utiliser Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pour créer des révisions d’accès pour l’accès privilégié aux rôles de ressources Azure et aux rôles Azure AD. Vous pouvez également configurer des révisions d’accès périodiques qui interviennent automatiquement. Cet article explique comment créer une ou plusieurs révisions d’accès.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] Pour plus d’informations sur les licences pour PIM, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md).

 Pour créer des révisions d’accès pour les ressources Azure, vous devez être affecté au rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) pour les ressources Azure. Pour créer des révisions d’accès pour les rôles Azure AD, vous devez être affecté au rôle [Administrateur général](../roles/permissions-reference.md#global-administrator) ou [Administrateur de rôle privilégié](../roles/permissions-reference.md#privileged-role-administrator).

## <a name="create-access-reviews"></a>Créer des révisions d’accès

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’utilisateur auquel est attribué l’un des rôles prérequis.

2. Sélectionnez **Identity Governance**.
 
3. Pour **Rôles Azure AD**, sélectionnez **Rôles Azure AD** sous **Privileged Identity Management**. Pour **Ressources Azure**, sélectionnez **Ressources Azure** sous **Privileged Identity Management**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png" alt-text="Capture d’écran de la sélection de la gouvernance des identités dans Portail Azure." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png"::: 
 
4. Pour **Rôles Azure AD**, sélectionnez de nouveau **Rôles Azure AD** sous **Gérer**. Pour **Ressources Azure**, sélectionnez la ressource que vous souhaitez gérer, par exemple, un abonnement.


5. Sous Gérer, sélectionnez **Révisions d’accès**, puis sélectionnez **Nouveau** pour créer une révision d’accès.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-reviews.png" alt-text="Capture d’écran de la liste des révisions d’accès pour les rôles Azure AD indiquant l’état de toutes les révisions.":::
 
6. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/name-description.png" alt-text="Capture d’écran de la création d’une révision d’accès : nom et description de la révision.":::

7. Définissez la **Date de début**. Par défaut, une révision d’accès se produit une seule fois, démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

   :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/start-end-dates.png" alt-text="Capture d’écran des éléments date de début, fréquence, durée, fin, nombre de tentatives et date de fin.":::

8. Pour que la révision d’accès soit périodique, modifiez le paramètre **Fréquence** en remplaçant la valeur **Une fois** par **Hebdomadaire**, **Mensuel**, **Trimestriel**, **Annuel** ou **Semestriel**. Utilisez la zone de texte ou le curseur **Durée** pour définir le nombre de jours pendant lequel chaque révision de la série périodique pourra être modifiée par les réviseurs. Par exemple, la durée maximale d’une révision mensuelle est de 27 jours, ce qui permet d’éviter le chevauchement des révisions.

9. Utilisez le paramètre **Fin** pour spécifier comment mettre fin à la série de révisions d’accès récurrentes. Les séries accès récurrentes peuvent se terminer de trois façons : elles peuvent s’exécuter de façon continue pour démarrer des révisions indéfiniment, s’exécuter jusqu’à une date spécifique ou s’exécuter jusqu’à ce qu’un nombre défini d’occurrences se soient produites. Vous, ou un autre administrateur qui peut gérer les révisions, pouvez arrêter la série après sa création en modifiant la date dans **Paramètres**, de sorte qu’elle se termine à cette date.


10. Dans la section **Étendue des utilisateurs**, sélectionnez l’étendue de la révision. Pour **Rôles Azure AD**, la première option d’étendue est Utilisateurs et groupes. Les utilisateurs attribués directement et les [groupes assignables à un rôle](../roles/groups-concept.md) seront inclus dans cette sélection. Pour **Rôles de ressources Azure**, la première étendue est Utilisateurs. Les groupes affectés aux rôles de ressources Azure sont développés pour afficher les attributions transitives d’utilisateurs dans la révision avec cette sélection. Vous pouvez également sélectionner **Principaux de service** pour examiner les comptes d’ordinateur ayant un accès direct au rôle de ressources Azure ou au rôle Azure AD.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/users.png" alt-text="Capture d’écran de l’étendue Utilisateurs pour la révision d’une appartenance à un rôle.":::

11. Sous **Réviser l’appartenance au rôle**, sélectionnez les rôles de ressources Azure ou Azure AD privilégiés à réviser.

    > [!NOTE]
    > La sélection de plusieurs rôles crée plusieurs révisions d’accès. Par exemple, en sélectionnant cinq rôles, vous créez cinq révisions d’accès distinctes.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/review-role-membership.png" alt-text="Capture d’écran de la révision d’une appartenance à un rôle.":::

12. Dans **type d’affectation**, étendez la révision en fonction de la façon dont le rôle a été affecté au principal. Choisissez **Affectations éligibles uniquement** pour passer en revue les affectations éligibles (quel que soit l’état d’activation quand l’évaluation est créée) ou **Affectations actives uniquement** pour passer en revue les affectations actives. Choisissez **toutes les affectations actives et éligibles** pour passer en revue toutes les attributions, quel que soit le type.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/assignment-type-select.png" alt-text="Capture d’écran de la liste des réviseurs des types d’affectations.":::

13. Dans la section **Réviseurs**, sélectionnez une ou plusieurs personnes pour vérifier tous les utilisateurs. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reviewers.png" alt-text="Liste pour les réviseurs des utilisateurs ou membres sélectionnés (auto)":::

    - **Utilisateurs sélectionnés** : utilisez cette option pour désigner un utilisateur spécifique pour terminer la révision. Cette option est disponible quelle que soit l’étendue de la révision, et les réviseurs sélectionnés peuvent réviser des utilisateurs, des groupes et des principaux du service.
    - **Membres (auto)** : utilisez cette option pour demander aux utilisateurs de réviser leurs propres attributions de rôles. Cette option n’est disponible que si la révision est définie sur l’étendue **Utilisateurs et groupes** ou **Utilisateurs**. Pour **Rôles Azure AD**, les groupes assignables à un rôle ne feront pas partie de la révision lorsque cette option est sélectionnée. 
    - **Manager** : utilisez cette option pour que le manager de l’utilisateur révise son attribution de rôle. Cette option n’est disponible que si la révision est définie sur l’étendue **Utilisateurs et groupes** ou **Utilisateurs**. En sélectionnant Manager, vous aurez également la possibilité d’indiquer un réviseur de secours. Les réviseurs de secours sont invités à réviser un utilisateur lorsque celui-ci n’a aucun manager spécifié dans le répertoire. Pour **Rôles Azure AD**, les groupes assignables à un rôle seront examinés par le réviseur de secours, le cas échéant. 

### <a name="upon-completion-settings"></a>Paramètres de saisie semi-automatique

1. Pour spécifier ce qui se passe après la fin d’une révision, développez la section **Paramètres de saisie semi-automatique**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings.png" alt-text="Capture d’écran de la section Paramètres de saisie semi-automatique, configuration des options Appliquer automatiquement et Si les réviseurs ne répondent pas.":::

2. Si vous voulez supprimer automatiquement l’accès pour les utilisateurs qui ont été refusés, définissez **Appliquer automatiquement les résultats à ressource** sur **Activer**. Si vous voulez appliquer manuellement les résultats quand la révision est terminée, cliquez sur **Désactiver**.

3. Utilisez la liste **Si les réviseurs ne répondent pas** pour spécifier ce qui se passe pour les utilisateurs qui ne sont pas vérifiés par le réviseur au cours de la révision. Ce paramètre n’a pas d’impact sur les utilisateurs qui ont été vérifiés par les réviseurs.

    - **Aucune modification** : laisser l’accès de l’utilisateur inchangé
    - **Supprimer l’accès** : supprimer l’accès de l’utilisateur
    - **Approuver l’accès** : approuver l’accès de l’utilisateur
    - **Accepter les recommandations** : accepter la recommandation du système sur le refus ou l’approbation de la prolongation de l’accès de l’utilisateur
    
4. Utilisez la liste **Action à appliquer aux utilisateurs invités refusés** pour spécifier ce qui arrive aux utilisateurs invités s’ils sont refusés. Ce paramètre n’est pas modifiable pour l’instant pour les révisions des rôles de ressources Azure et des rôles Azure AD ; les utilisateurs invités, comme tous les utilisateurs, perdent toujours l’accès à la ressource si elle est refusée.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/action-to-apply-on-denied-guest-users.png" alt-text="Capture d’écran de la section Paramètres de saisie semi-automatique, Action à appliquer aux utilisateurs invités refusés.":::

5. Vous pouvez envoyer des notifications à des utilisateurs ou groupes supplémentaires pour recevoir des mises à jour d’achèvement de révision. Cette fonctionnalité permet d’informer les parties prenantes autres que le créateur de la révision de la progression de la révision. Pour utiliser cette fonctionnalité, sélectionnez **Sélectionner un ou plusieurs utilisateurs ou groupes** et ajoutez un utilisateur ou groupe supplémentaire pour lequel vous souhaitez recevoir l’état d’achèvement.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings-additional-receivers.png" alt-text="Capture d’écran de la section Paramètres de saisie semi-automatique, Ajout d’utilisateurs pour la réception des notifications.":::

### <a name="advanced-settings"></a>Paramètres avancés

1. Pour spécifier d’autres paramètres, développez la section **Paramètres avancés**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/advanced-settings.png" alt-text="Capture d’écran de la section Paramètres avancés pour afficher des recommandations, exiger la raison lors de l’approbation, envoyer des notifications par e-mail et des rappels.":::

1. Définissez **Afficher les recommandations** sur **Activer** pour montrer aux réviseurs les recommandations du système basées sur les informations des accès de l’utilisateur.

1. Définissez **Exiger la raison lors de l’approbation** sur **Activer** afin d’exiger que le réviseur indique un motif pour l’approbation.

1. Définissez **Notifications par e-mail** sur **Activer** pour qu’Azure AD envoie des notifications par e-mail aux réviseurs quand une révision d’accès commence et aux administrateurs quand une révision se termine.

1. Définissez **Rappels** sur **Activer** pour qu’Azure AD envoie des rappels concernant les révisions d’accès en cours aux réviseurs qui n’ont pas terminé leur révision.
1. Le contenu de l’e-mail envoyé aux réviseurs est généré automatiquement en fonction des détails de la révision (nom de la révision, nom de la ressource, date d’échéance, etc.). Si vous souhaitez communiquer des informations supplémentaires telles que des instructions ou des coordonnées, vous pouvez les ajouter dans l’**e-mail Contenu supplémentaire pour le réviseur** qui sera inclus dans les e-mails d’invitation et de rappel envoyés aux réviseurs désignés. Ces informations seront affichées dans la section en surbrillance ci-dessous.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/email-info.png" alt-text="Contenu de l’e-mail envoyé aux réviseurs avec mise en évidence des points importants"::: 

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès

Vous pouvez suivre la progression des révisions des réviseurs sur la page **Vue d’ensemble** de la révision d’accès. Aucun droit d’accès n’est modifié dans le répertoire avant que la révision ne soit terminée. Vous trouverez ci-dessous une capture d’écran montrant la page de présentation des révisions d’accès des **rôles de ressources Azure** et des **rôles Azure AD**.

:::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png" alt-text="Capture d’écran de la page de présentation des révisions d’accès montrant les détails de la révision d’accès pour des rôles Azure AD." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png":::

S’il s’agit d’une révision unique, une fois la période de révision d’accès terminée, ou si l’administrateur interrompt la révision d’accès, suivez les étapes de la [réalisation d’une révision d’accès des rôles de ressources Azure et des rôles Azure AD](pim-complete-azure-ad-roles-and-resource-roles-review.md) pour voir et appliquer les résultats.  

Pour gérer une série de révisions d’accès, accédez à la révision d’accès et vous y voyez les occurrences à venir dans les révisions planifiées, pour lesquelles vous pouvez modifier la date de fin, ou ajouter/supprimer des réviseurs en conséquence.

En fonction de vos sélections dans **Paramètres de saisie semi-automatique**, l’application automatique est exécutée après la date de fin de la révision ou lorsque vous arrêtez manuellement la révision. La révision passe alors de l’état **Terminé** à divers états intermédiaires, comme **Application en cours**, pour arriver enfin à l’état **Appliqué**. Les utilisateurs dont l’accès est refusé doivent perdre leurs rôles au bout de quelques minutes.

> [!IMPORTANT]
> Si un groupe est affecté à des **rôles de ressources Azure**, le réviseur du rôle de ressource Azure verra la liste développée des utilisateurs indirects ayant un accès attribué par l’intermédiaire d’un groupe imbriqué. Si un réviseur refuse un membre d’un groupe imbriqué, le résultat de ce refus ne sera pas appliqué correctement pour le rôle, car l’utilisateur n’est pas supprimé du groupe imbriqué. Pour **Rôles Azure AD**, les [groupes assignables à un rôle](../roles/groups-concept.md) apparaîtront dans la révision au lieu de développer les membres du groupe, et un réviseur approuvera ou refusera l’accès au groupe entier.

## <a name="update-the-access-review"></a>Mettre à jour la révision d’accès

Après le lancement d’une ou de plusieurs révisions d’accès, vous souhaiterez peut-être modifier ou mettre à jour les paramètres de vos révisions d’accès existantes. Voici quelques scénarios courants que vous pouvez envisager :

- **Ajout et suppression de réviseurs** : Lors de la mise à jour des révisions d’accès, vous pouvez choisir d’ajouter un réviseur de secours en plus du réviseur principal. Les réviseurs principaux peuvent être supprimés lors de la mise à jour d’une révision d’accès. Toutefois, les réviseurs de secours ne peuvent pas être supprimés du fait de la conception.

    > [!Note]
    > Les réviseurs de secours ne peuvent être ajoutés que lorsque le type de réviseur est Manager. Les réviseurs principaux peuvent être ajoutés lorsque le type de réviseur est un utilisateur sélectionné.

- **Rappel aux réviseurs** : Lors de la mise à jour des révisions d’accès, vous pouvez choisir d’activer l’option de rappel sous Paramètres avancés. Une fois cette option activée, les utilisateurs recevront une notification par e-mail à mi-parcours de la période de révision, qu’ils aient terminé ou non la révision. 

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reminder-setting.png" alt-text="Capture d’écran de l’option de rappel sous les paramètres de révision d’accès.":::

- **Mise à jour des paramètres** : Si une révision d’accès est récurrente, il existe des paramètres distincts sous « Actuel » et sous « Série ». La mise à jour des paramètres sous « Actuel » appliquera uniquement les modifications apportées à la révision d’accès actuelle, tandis que la mise à jour des paramètres sous « Série » mettra à jour le paramètre pour toutes les futures révisions.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png" alt-text="Capture d’écran de la page Paramètres sous Révisions d’accès." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png":::
    
## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une révision d’accès des ressources Azure et des rôles Azure AD dans PIM](pim-perform-azure-ad-roles-and-resource-roles-review.md)
- [Effectuez une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans PIM](pim-complete-azure-ad-roles-and-resource-roles-review.md)
