---
title: Fichier Include
description: Fichier Include
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 6f2b5eb96eeb1c4b7d07219d5fe54a8a0ca9e28a
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73412966"
---
## <a name="for-users-in-your-directory"></a>Pour les utilisateurs dans votre répertoire

Suivez ces étapes si vous souhaitez autoriser les utilisateurs de votre annuaire à demander ce package d’accès. Lors de la définition de la stratégie de demandes, vous pouvez spécifier des utilisateurs individuels ou plus souvent des groupes d’utilisateurs. Par exemple, votre organisation a peut-être déjà un groupe tel que **Tous les employés**.  Si ce groupe est ajouté dans la stratégie pour des utilisateurs qui peuvent demander l’accès, tout membre de ce groupe peut ensuite demander l’accès.

1. Dans la section **Utilisateurs pouvant demander l’accès**, cliquez sur **Pour les utilisateurs dans votre annuaire**.

    Quand vous sélectionnez cette option, de nouvelles options s’affichent pour vous permettre de préciser qui dans votre annuaire peut demander ce package d’accès.

    ![Package d’accès - Demandes - Pour les utilisateurs de votre annuaire](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Sélectionnez l’une des options suivantes :

    |  |  |
    | --- | --- |
    | **Utilisateurs et groupes spécifiques** | Choisissez cette option si vous souhaitez que seuls les utilisateurs et les groupes de votre annuaire que vous spécifiez puissent demander ce package d’accès. |
    | **Tous les membres (à l’exclusion des invités)** | Choisissez cette option si vous souhaitez que tous les utilisateurs membres de votre annuaire puissent demander ce package d’accès. Cette option n’inclut pas les utilisateurs invités que vous avez invités dans votre annuaire. |
    | **Tous les utilisateurs (y compris les invités)** | Choisissez cette option si vous souhaitez que tous les utilisateurs membres et les utilisateurs invités de votre annuaire puissent demander ce package d’accès. |

    Les utilisateurs invités font référence à des utilisateurs externes qui ont été invités dans votre annuaire avec [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Pour plus d’informations sur les différences entre les utilisateurs membres et les utilisateurs invités, consultez [Quelles sont les autorisations utilisateur par défaut dans Azure Active Directory ?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Si vous avez sélectionné **Utilisateurs et groupes spécifiques**, cliquez sur **Ajouter des utilisateurs et des groupes**.

1. Dans le volet Sélectionnez des utilisateurs et des groupes, sélectionnez les utilisateurs et les groupes à ajouter.

    ![Package d’accès - Demandes - Sélectionner des utilisateurs et des groupes](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Cliquez sur **Sélectionner** pour ajouter les utilisateurs et les groupes.

1. Passez à la section [Approbation](#approval) plus bas.

## <a name="for-users-not-in-your-directory"></a>Pour les utilisateurs qui ne sont pas dans votre répertoire

Suivez ces étapes si vous souhaitez autoriser les utilisateurs absents de votre annuaire à demander ce package d’accès. Les **utilisateurs absents de votre annuaire** sont des utilisateurs qui se trouvent dans un autre annuaire ou domaine Azure AD et n’ont peut-être pas encore été invités dans votre annuaire. Les annuaires Azure AD doivent être configurés pour autoriser les invitations dans **Restrictions de collaboration**. Pour plus d’informations, consultez [Permettre une collaboration B2B externe et gérer les utilisateurs autorisés à en inviter d’autres](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Un compte d’utilisateur invité sera créé pour un utilisateur qui n’est pas encore dans votre répertoire et dont la demande est approuvée ou approuvée automatiquement. L’invité est invité, mais ne recevra pas d’invitation par e-mail. Il recevra un e-mail une fois l’attribution au package d’accès fournie. Par défaut, quand cet utilisateur invité n’a plus aucune attribution de package d’accès (pour cause d’expiration ou d’annulation), son compte ne peut plus se connecter et est supprimé par la suite. Si vous voulez que des utilisateurs invités demeurent indéfiniment dans votre répertoire, même s’ils n’ont plus d’attribution de package d’accès, vous pouvez modifier les paramètres de votre configuration de gestion des droits d’utilisation. Pour plus d’informations sur l’objet utilisateur invité, consultez [Propriétés d’un utilisateur Azure Active Directory B2B Collaboration](../articles/active-directory/b2b/user-properties.md).

1. Dans la section **Utilisateurs pouvant demander l’accès**, cliquez sur **Pour les utilisateurs dans votre annuaire**.

    Quand vous sélectionnez cette option, de nouvelles options s’affichent.

    ![Package d’accès - Demandes - Pour les utilisateurs qui ne sont pas dans votre annuaire](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Sélectionnez l’une des options suivantes :

    |  |  |
    | --- | --- |
    | **Organisations connectées spécifiques** | Choisissez cette option si vous souhaitez sélectionner parmi une liste d’organisations que votre administrateur a ajoutées précédemment. Tous les utilisateurs des organisations sélectionnées pourront demander ce package d’accès. |
    | **Toutes les organisations connectées** | Choisissez cette option si vous souhaitez que tous les utilisateurs de toutes les organisations connectées puissent demander ce package d’accès. |

    Une organisation connectée est un domaine ou un annuaire Azure AD externe avec lequel vous collaborez.

1. Si vous avez sélectionné **Organisations connectées spécifiques**, cliquez sur **Ajouter des annuaires** pour effectuer une sélection parmi une liste d’organisations connectées que votre administrateur a ajoutées précédemment.

1. Tapez le nom ou le nom de domaine pour rechercher une organisation précédemment connectée.

    ![Package d’accès - Demandes - Sélectionner des annuaires](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Si l’organisation avec laquelle vous souhaitez collaborer ne figure pas dans la liste, vous pouvez demander à votre administrateur de l’ajouter en tant qu’organisation connectée. Pour plus d’informations, voir [Ajouter une organisation connectée](../articles/active-directory/governance/entitlement-management-organization.md).

1. Une fois que vous avez sélectionné toutes les organisations connectées, cliquez sur **Sélectionner**.

    > [!NOTE]
    > Tous les utilisateurs des organisations connectées sélectionnées pourront demander ce package d’accès. Cela comprend les utilisateurs Azure AD de tous les sous-domaines associés à l’organisation, à moins que ces domaines soient bloqués par la liste d’autorisation ou de refus d’Azure B2B. Pour plus d’informations, consultez [Autoriser ou bloquer des invitations aux utilisateurs B2B à partir d’organisations spécifiques](../articles/active-directory/b2b/allow-deny-list.md).

1. Passez à la section [Approbation](#approval) plus bas.

## <a name="none-administrator-direct-assignments-only"></a>Aucune (attributions direct administrateur uniquement)

Effectuez ces étapes si vous souhaitez contourner les demandes d’accès et autoriser les administrateurs à attribuer directement des utilisateurs spécifiques à ce package d’accès. Les utilisateurs n’auront pas à demander le package d’accès. Vous pouvez toujours définir des paramètres de cycle de vie, mais il n’y a aucun paramètre de demande.

1. Dans la section **Utilisateurs pouvant demander l’accès**, cliquez sur **Aucune (attributions direct administrateur uniquement)** .

    ![Package d’accès - Demandes - Aucun (attributions directes d’administrateur uniquement)](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Après avoir créé le package d’accès, vous pouvez directement attribuer des utilisateurs internes et externes spécifiques à ce package d’accès. Si vous spécifiez un utilisateur externe, un compte d’utilisateur invité est créé dans votre répertoire. Pour plus d’informations sur l’attribution directe d’un utilisateur, voir [Afficher, ajouter et supprimer les attributions pour un package d’accès](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Passez à la section [Activer les demandes](#enable-requests) plus bas.

## <a name="approval"></a>Approbation

Dans la section Approbation, vous spécifiez si une approbation est nécessaire quand les utilisateurs demandent ce package d’accès. Les paramètres d’approbation fonctionnent de la façon suivante :

- La demande n’a besoin d’être approuvée que par un seul des approbateurs sélectionnés ou approbateurs de secours. L’approbation de tous les approbateurs n’est pas nécessaire.
- La décision d’approbation repose sur le premier approbateur à consulter la demande.

Pour spécifier les paramètres d’approbation des utilisateurs que vous avez sélectionnés précédemment, procédez comme suit.

1. Pour exiger une approbation pour des demandes des utilisateurs sélectionnés, définissez **Exiger une approbation** sur **Oui**. Pour que les demandes soient approuvées automatiquement, définissez cette valeur sur **Non**.

1. Pour exiger des utilisateurs une justification pour demander le package d’accès, affectez la valeur **Oui** à **Exiger la justification du demandeur**.

    ![Package d’accès - Demandes - Paramètres d’approbation](./media/active-directory-entitlement-management-request-policy/approval.png)

### <a name="single-stage-approval"></a>Approbation en une seule étape

1. Pour les approbateurs, sélectionnez **Gestionnaire comme approbateur** ou **Choisir des approbateurs spécifiques**.

    ![Package d’accès – Demandes – Paramètres d’approbation en une seule étape](./media/active-directory-entitlement-management-request-policy/approval-single-stage.png)

1. Si vous avez sélectionné Gestionnaire en tant qu’approbateur, cliquez sur **Ajouter un secours** pour sélectionner un ou plusieurs utilisateurs ou groupes dans votre annuaire en tant qu’approbateur de secours au cas où la gestion des droits ne trouverait pas le gestionnaire.

    Le gestionnaire est déterminé par l’attribut **Gestionnaire** dans le profil Azure AD de l’utilisateur. Pour plus d’informations, consultez [Ajouter ou mettre à jour les informations du profil utilisateur avec Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Si vous avez sélectionné Choisir des approbateurs spécifiques, cliquez sur **Ajouter des approbateurs** pour sélectionner dans votre annuaire un ou plusieurs utilisateurs ou groupes devant assumer la fonction d’approbateurs.

1. Dans **La décision doit être prise dans combien de jours ?** , spécifiez le nombre de jours dont dispose un approbateur pour examiner une demande pour ce package d’accès.

    Si une demande n’est pas approuvée dans le délai imparti, elle est refusée automatiquement. L’utilisateur devra envoyer une autre demande pour le package d’accès.

1. Pour exiger des utilisateurs une justification pour demander le package d’accès, définissez **Exiger la justification de l’approbateur** sur **Oui**.

    Une justification est visible par les autres approbateurs et le demandeur.

### <a name="alternate-approvers"></a>Approbateurs de substitution

En plus de spécifier les approbateurs principaux autorisés à approuver des demandes, vous pouvez spécifier des approbateurs de substitution. Cela permet de s’assurer que les demandes sont approuvées ou refusées avant leur expiration (délai d’expiration).

En spécifiant des approbateurs de substitution, si les approbateurs principaux n’ont pas été en mesure d’approuver ou de refuser la demande, la demande en attente est transférée aux approbateurs de substitution, conformément à la planification de transfert que vous avez spécifiée lors de la configuration de la stratégie. Ils reçoivent un e-mail pour approuver ou refuser la demande en attente.

Une fois la demande transférée aux approbateurs de substitution, les approbateurs principaux peuvent toujours l’approuver ou la refuser. Pour approuver ou refuser la demande en attente, les approbateurs de substitution utilisent le même site Mon Accès que les approbateurs principaux.

Nous pouvons répertorier les personnes ou groupes de personnes qui doivent être les approbateurs principaux et les approbateurs de substitution. Veillez à répertorier des groupes de personnes différents comme approbateurs principaux et approbateurs de substitution.
Par exemple, si vous avez répertorié Alice et Bob comme approbateurs principaux, répertoriez Carol et Dave comme approbateurs de substitution. Pour ajouter des approbateurs de substitution à un package d’accès, procédez comme suit :

1. Cliquez sur **Afficher les paramètres de demande avancés**.

    ![Package d’accès – Stratégie – Afficher les paramètres de demande avancés](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Définissez l’option **En l’absence d’action, transférer à d’autres approbateurs ?** sur **Oui**.

1. Cliquez sur **Ajouter des approbateurs de substitution**, puis sélectionnez les approbateurs de substitution dans la liste.

    ![Package d’accès – Stratégie – Ajouter des approbateurs de substitution](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. Dans la zone **Passer aux autres approbateurs après combien de jours ?** , entrez le nombre de jours dont les approbateurs disposent pour approuver ou refuser une demande. Si aucun approbateur n’a approuvé ou refusé la demande pendant la durée de validité de celle-ci, la demande expire (délai d’expiration) et l’utilisateur doit soumettre une autre demande pour le package d’accès. 

    Les demandes ne peuvent être transférées aux approbateurs de substitution qu’un jour après que la durée de la demande a atteint sa demi-vie. Dans cet exemple, la durée de la demande est de 14 jours. Cela signifie que la durée de la demande atteint sa demi-vie le jour 7. La demande ne peut donc pas être transférée avant le jour 8. De même, les demandes ne peuvent pas être transférées à l’approbateur de substitution le dernier jour de leur durée. Ainsi, dans l’exemple, l’ultime limite pour le transfert de la demande est le jour 13.

## <a name="enable-requests"></a>Activer les demandes

1. Si vous souhaitez que le package d’accès soit immédiatement disponible afin d’être demandé par les utilisateurs dans la stratégie de demandes, cliquez sur **Oui** pour activer.

    Vous pouvez toujours l’activer plus tard, après avoir créé le package d’accès.

    Si vous avez sélectionné **Aucun (attributions directes d’administrateur uniquement)** et que vous affectez la valeur **No** à Activer, les administrateurs ne pourront pas attribuer directement ce package d’accès.

    ![Package d’accès - Stratégie - Paramètre Activer la stratégie](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Cliquez sur **Suivant**.
