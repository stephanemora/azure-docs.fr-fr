---
title: Créer une révision d’accès des rôles Azure AD dans PIM - Azure AD | Microsoft Docs
description: Découvrez comment créer une révision d’accès des rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 4/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aba8d9de5e068cd98675f67cb26b0eac8d1ad6d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552821"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Créer une révision d’accès des rôles Azure AD dans Privileged Identity Management

Pour réduire les risques associés aux attributions de rôles obsolètes, vous devez vérifier les accès régulièrement. Vous pouvez utiliser Azure AD Privileged Identity Management (PIM) pour créer des révisions d’accès pour les rôles Azure AD privilégiés. Vous pouvez également configurer des révisions d’accès périodiques qui interviennent automatiquement.

Cet article explique comment créer une ou plusieurs révisions d’accès pour les rôles Azure AD privilégiés.

## <a name="prerequisite-license"></a>Licence requise

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. Pour plus d’informations sur les licences pour PIM, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md).

> [!Note]
>  Actuellement, vous pouvez étendre une révision d’accès aux principaux de service ayant accès à Azure AD et aux rôles de ressource Azure (préversion) avec une édition Azure Active Directory Premium P2 active dans votre locataire. Le modèle de licence pour les principaux de service sera finalisé pour la disponibilité générale de cette fonctionnalité, et des licences supplémentaires peuvent être nécessaires.

## <a name="prerequisites"></a>Prérequis

[Administrateur de rôle privilégié](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Ouvrir les révisions d’accès

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un nom d’utilisateur qui est membre du rôle Administrateur de rôle privilégié.

1. Sélectionnez **Identity Governance**
 
1. Sélectionnez **Rôles Azure AD** sous **Azure Active Directory Privileged Identity Management**.
 
1. Sélectionnez de nouveau **Rôles Azure AD** sous **Gérer**.

1. Sous Gérer, sélectionnez **Révisions d’accès**, puis sélectionnez **Nouveau**.

    ![Rôles d’Azure AD : liste des révisions d’accès indiquant l’état de toutes les révisions](./media/pim-how-to-start-security-review/access-reviews.png)

Cliquez sur **Nouveau** pour créer une révision d’accès.

1. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

    ![Créer une révision d’accès - Nom et description de la révision](./media/pim-how-to-start-security-review/name-description.png)

1. Définissez la **Date de début**. Par défaut, une révision d’accès se produit une seule fois, démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

    ![Date de début, fréquence, durée, fin, nombre de tentatives et date de fin](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Pour que la révision d’accès soit périodique, modifiez le paramètre **Fréquence** en remplaçant la valeur **Une fois** par **Hebdomadaire**, **Mensuel**, **Trimestriel**, **Annuel** ou **Semestriel**. Utilisez la zone de texte ou le curseur **Durée** pour définir le nombre de jours pendant lequel chaque révision de la série périodique pourra être modifiée par les réviseurs. Par exemple, la durée maximale d’une révision mensuelle est de 27 jours, ce qui permet d’éviter le chevauchement des révisions.

1. Utilisez le paramètre **Fin** pour spécifier comment mettre fin à la série de révisions d’accès récurrentes. Les séries accès récurrentes peuvent se terminer de trois façons : elles peuvent s’exécuter de façon continue pour démarrer des révisions indéfiniment, s’exécuter jusqu’à une date spécifique ou s’exécuter jusqu’à ce qu’un nombre défini d’occurrences se soient produites. Un autre administrateur d’utilisateur, ou un autre administrateur d’entreprise ou vous-même pouvez arrêter la série après sa création en modifiant la date définie dans la zone **Paramètres**, afin qu’elle s’arrête à cette date.

1. Dans la section **Étendue des utilisateurs**, sélectionnez l’étendue de la révision. Pour réviser les utilisateurs et les groupes ayant accès au rôle Azure AD, sélectionnez **Utilisateurs et groupes**, ou sélectionnez **(Préversion) Principaux de service** pour réviser les comptes d’ordinateurs ayant accès au rôle Azure AD.

    ![Étendue des utilisateurs pour la révision d’une appartenance à un rôle](./media/pim-how-to-start-security-review/users.png)

1. Sous **Réviser l’appartenance au rôle**, sélectionnez les rôles Azure AD privilégiés à réviser. 

    > [!NOTE]
    > - Les rôles sélectionnés ici incluent des [rôles permanents et éligibles](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - La sélection de plusieurs rôles crée plusieurs révisions d’accès. Par exemple, en sélectionnant cinq rôles, vous créez cinq révisions d’accès distinctes.
    > - Pour les rôles associés à des groupes, l’accès à chaque groupe lié au rôle en cours de révision est examiné dans le cadre de la révision d’accès.
    Si vous créez une révision d’accès des **rôles Azure AD**, l’exemple suivant présente une liste Révision d’appartenance.

    ![Volet Révision d’appartenance répertoriant les rôles Azure AD que vous pouvez sélectionner](./media/pim-how-to-start-security-review/review-membership.png)

    Si vous créez une révision d’accès des **rôles de ressource Azure**, l’image suivant présente une liste Révision d’appartenance.

    ![Volet Révision d’appartenance répertoriant les rôles de ressource Azure que vous pouvez sélectionner](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. Dans la section **Réviseurs**, sélectionnez une ou plusieurs personnes pour vérifier tous les utilisateurs. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès.

    ![Liste pour les réviseurs des utilisateurs ou membres sélectionnés (auto)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Utilisateurs sélectionnés** : utilisez cette option pour désigner un utilisateur spécifique pour terminer la révision. Cette option est disponible quelle que soit l’étendue de la révision, et les réviseurs sélectionnés peuvent réviser des utilisateurs, de groupes et des principaux du service. 
    - **Membres (auto)** : utilisez cette option pour demander aux utilisateurs de réviser leurs propres attributions de rôles. Les groupes assignés au rôle ne feront pas partie de la révision quand cette option est sélectionnée. Cette option est disponible uniquement si la révision est limitée à des **Utilisateurs et groupes**.
    - **Manager** : utilisez cette option pour que le manager de l’utilisateur révise son attribution de rôle. Cette option n’est disponible que si la révision est limitée à des **Utilisateurs et groupes**. En sélectionnant Manager, vous aurez également la possibilité d’indiquer un réviseur de secours. Les réviseurs de secours sont invités à réviser un utilisateur lorsque celui-ci n’a aucun manager spécifié dans le répertoire. Les groupes affectés au rôle seront examinés par le réviseur de secours, le cas échéant. 

### <a name="upon-completion-settings"></a>Paramètres de saisie semi-automatique

1. Pour spécifier ce qui se passe après la fin d’une révision, développez la section **Paramètres de saisie semi-automatique**.

    ![Paramètres de saisie semi-automatique pour appliquer automatiquement et « Le réviseur ne doit pas répondre »](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Si vous voulez supprimer automatiquement l’accès pour les utilisateurs qui ont été refusés, définissez **Appliquer automatiquement les résultats à ressource** sur **Activer**. Si vous voulez appliquer manuellement les résultats quand la révision est terminée, cliquez sur **Désactiver**.

1. Utilisez la liste **Le réviseur ne doit pas répondre** pour spécifier ce qui se passe pour les utilisateurs qui ne sont pas vérifiés par le réviseur au cours de la révision. Ce paramètre n’affecte pas les utilisateurs qui ont été vérifiés manuellement par les réviseurs. Si la décision finale du réviseur est Refuser, l’accès de l’utilisateur est supprimé.

    - **Aucune modification** : laisser l’accès de l’utilisateur inchangé
    - **Supprimer l’accès** : supprimer l’accès de l’utilisateur
    - **Approuver l’accès** : approuver l’accès de l’utilisateur
    - **Accepter les recommandations** : accepter la recommandation du système sur le refus ou l’approbation de la prolongation de l’accès de l’utilisateur

### <a name="advanced-settings"></a>Paramètres avancés

1. Pour spécifier d’autres paramètres, développez la section **Paramètres avancés**.

    ![Paramètres avancés pour afficher des recommandations, exiger la raison lors de l’approbation, notifications par courrier électronique et rappels](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Définissez **Afficher les recommandations** sur **Activer** pour montrer aux réviseurs les recommandations du système basées sur les informations des accès de l’utilisateur.

1. Définissez **Exiger la raison lors de l’approbation** sur **Activer** afin d’exiger que le réviseur indique un motif pour l’approbation.

1. Définissez **Notifications par e-mail** sur **Activer** pour qu’Azure AD envoie des notifications par e-mail aux réviseurs quand une révision d’accès commence et aux administrateurs quand une révision se termine.

1. Définissez **Rappels** sur **Activer** pour qu’Azure AD envoie des rappels concernant les révisions d’accès en cours aux réviseurs qui n’ont pas terminé leur révision.
1. Le contenu de l’e-mail envoyé aux réviseurs est généré automatiquement en fonction des détails de la révision, comme le nom de la révision, le nom de la ressource, la date d’échéance, etc. Si vous souhaitez communiquer des informations supplémentaires telles que des instructions ou des coordonnées, vous pouvez les ajouter dans l’**e-mail Contenu supplémentaire pour le réviseur** qui sera inclus dans les e-mails d’invitation et de rappel envoyés aux réviseurs désignés. Ces informations seront affichées dans la section en surbrillance ci-dessous.

    ![Contenu de l’e-mail envoyé aux réviseurs avec mise en évidence des points importants](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>Démarrer la révision d’accès

Une fois que vous avez spécifié les paramètres pour une révision d’accès, sélectionnez **Démarrer**. La révision d’accès s’affiche dans votre liste, avec un indicateur de son état.

![Liste des révisions d’accès indiquant l’état des révisions démarrées](./media/pim-how-to-start-security-review/access-reviews-list.png)

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur montrer les instructions relatives à la [révision d’accès des rôles Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès

Vous pouvez suivre la progression des révisions des réviseurs sur la page **Vue d’ensemble** de la révision d’accès. Aucun droit d’accès n’est modifié dans le répertoire avant que [la révision ne soit terminée](pim-how-to-complete-review.md).

![Page de vue d’ensemble des révisions d’accès montrant les détails de la révision](./media/pim-how-to-start-security-review/access-review-overview.png)

S’il s’agit d’une révision unique, une fois la période de révision d’accès terminée, ou si l’administrateur interrompt la révision d’accès, suivez les étapes de la [réalisation d’une révision d’accès des rôles Azure AD](pim-how-to-complete-review.md) pour voir et appliquer les résultats.  

Pour gérer une série de révisions d’accès, accédez à la révision d’accès et vous y voyez les occurrences à venir dans les révisions planifiées, pour lesquelles vous pouvez modifier la date de fin, ou ajouter/supprimer des réviseurs en conséquence.

En fonction de vos sélections dans **Paramètres de saisie semi-automatique**, l’application automatique est exécutée après la date de fin de la révision ou lorsque vous arrêtez manuellement la révision. La révision passe alors de l’état **Terminé** à divers états intermédiaires, comme **Application en cours**, pour arriver enfin à l’état **Appliqué**. Les utilisateurs dont l’accès est refusé doivent perdre leurs rôles au bout de quelques minutes.

## <a name="next-steps"></a>Étapes suivantes

- [Revoir l’accès aux rôles Azure AD](pim-how-to-perform-security-review.md)
- [Terminer une révision d’accès des rôles Azure AD](pim-how-to-complete-review.md)
- [Créer une révision d’accès des rôles de ressources Azure](pim-resource-roles-start-access-review.md)
