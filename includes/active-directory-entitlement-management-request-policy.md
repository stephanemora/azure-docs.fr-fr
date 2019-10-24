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
ms.openlocfilehash: aba3dd34383edbfb555adcc2063e4a2b76af1959
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389321"
---
## <a name="for-users-in-your-directory"></a>Pour les utilisateurs dans votre annuaire

Suivez ces étapes si vous souhaitez autoriser les utilisateurs de votre annuaire à demander ce package d’accès. Lors de la définition de la stratégie de demandes, vous pouvez spécifier des utilisateurs individuels ou plus souvent des groupes d’utilisateurs. Par exemple, votre organisation a peut-être déjà un groupe tel que **Tous les employés**.  Si ce groupe est ajouté dans la stratégie pour des utilisateurs qui peuvent demander l’accès, tout membre de ce groupe peut ensuite demander l’accès.

1. Dans la liste **Utilisateurs qui peuvent demander l’accès**, sélectionnez **Pour les utilisateurs dans votre annuaire**.

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

## <a name="for-users-not-in-your-directory"></a>Pour les utilisateurs qui ne sont pas dans votre annuaire

Suivez ces étapes si vous souhaitez autoriser les utilisateurs absents de votre annuaire à demander ce package d’accès. Les **utilisateurs absents de votre annuaire** sont des utilisateurs qui se trouvent dans un autre annuaire ou domaine Azure AD et n’ont peut-être pas encore été invités dans votre annuaire. Les annuaires Azure AD doivent être configurés pour autoriser les invitations dans **Restrictions de collaboration**. Pour plus d’informations, consultez [Permettre une collaboration B2B externe et gérer les utilisateurs autorisés à en inviter d’autres](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Un compte d’utilisateur invité sera créé pour un utilisateur qui n’est pas encore dans votre annuaire et dont la demande est approuvée ou approuvée automatiquement. L’invité est invité, mais ne recevra pas d’invitation par e-mail. Il recevra un e-mail une fois l’attribution au package d’accès fournie. Par défaut, quand cet utilisateur invité n’a plus aucune attribution de package d’accès (pour cause d’expiration ou d’annulation), son compte ne peut plus se connecter et est supprimé par la suite. Si vous voulez que des utilisateurs invités demeurent indéfiniment dans votre annuaire, même s’ils n’ont plus d’attribution de package d’accès, vous pouvez modifier les paramètres de votre configuration de gestion des droits d’utilisation. Pour plus d’informations sur l’objet utilisateur invité, consultez [Propriétés d’un utilisateur Azure Active Directory B2B Collaboration](../articles/active-directory/b2b/user-properties.md).

1. Dans la liste **Utilisateurs qui peuvent demander l’accès**, sélectionnez **Pour les utilisateurs qui ne sont pas dans votre annuaire**.

    Quand vous sélectionnez cette option, de nouvelles options s’affichent.

    ![Package d’accès - Demandes - Pour les utilisateurs qui ne sont pas dans votre annuaire](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Sélectionnez l’une des options suivantes :

    |  |  |
    | --- | --- |
    | **Organisations connectées spécifiques** | Choisissez cette option si vous souhaitez sélectionner parmi une liste d’organisations que votre administrateur a ajoutées précédemment. Tous les utilisateurs des organisations sélectionnées pourront demander ce package d’accès. |
    | **Toutes les organisations connectées** | Choisissez cette option si vous souhaitez que tous les utilisateurs de toutes les organisations connectées puissent demander ce package d’accès. |

    Une organisation connectée est un domaine ou un annuaire Azure AD externe avec lequel vous collaborez fréquemment.

1. Si vous avez sélectionné **Organisations connectées spécifiques**, cliquez sur **Ajouter des annuaires** pour effectuer une sélection parmi une liste d’organisations connectées que votre administrateur a ajoutées précédemment.

1. Entrez un nom de domaine pour rechercher une organisation connectée avec ce nom de domaine.

    ![Package d’accès - Demandes - Sélectionner des annuaires](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Si l’organisation avec laquelle vous souhaitez collaborer ne figure pas dans la liste, vous pouvez demander à votre administrateur de l’ajouter en tant qu’organisation connectée. 

1. Une fois que vous avez sélectionné toutes les organisations connectées, cliquez sur **Sélectionner**.

    > [!NOTE]
    > Tous les utilisateurs des organisations connectées sélectionnées pourront demander ce package d’accès. Cela comprend les utilisateurs de tous les sous-domaines associés aux organisations connectées.

1. Passez à la section [Approbation](#approval) plus bas.

## <a name="none-administrator-direct-assignments-only"></a>Aucune (attributions direct administrateur uniquement)

Effectuez ces étapes si vous souhaitez contourner les demandes d’accès et autoriser les administrateurs à attribuer directement des utilisateurs spécifiques à ce package d’accès. Les utilisateurs n’auront pas à demander le package d’accès. Vous pouvez toujours définir des paramètres de cycle de vie, mais il n’y a aucun paramètre de demande.

1. Dans la liste **Utilisateurs qui peuvent demander l’accès**, sélectionnez **Aucun (attributions directes d’administrateur uniquement)** .

    ![Package d’accès - Demandes - Aucun (attributions directes d’administrateur uniquement)](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Après avoir créé le package d’accès, vous pouvez directement attribuer des utilisateurs internes et externes spécifiques à ce package d’accès. Si vous spécifiez un utilisateur externe, un compte d’utilisateur invité est créé dans votre répertoire. Pour plus d’informations sur l’attribution directe d’un utilisateur, consultez [Afficher et modifier les attributions pour un package d’accès](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Passez à la section [Activer les demandes](#enable-requests) plus bas.

## <a name="approval"></a>Approbation

Dans la section Approbation, vous spécifiez si une approbation est nécessaire quand les utilisateurs demandent ce package d’accès. Les paramètres d’approbation fonctionnent de la façon suivante :

- La demande n’a besoin d’être approuvée que par un seul des approbateurs sélectionnés ou approbateurs de secours. L’approbation de tous les approbateurs n’est pas nécessaire.
- La décision d’approbation repose sur le premier approbateur à consulter la demande.

1. Pour exiger une approbation pour des demandes des utilisateurs sélectionnés, définissez **Exiger une approbation** sur **Oui**. Pour que les demandes soient approuvées automatiquement, définissez cette valeur sur **Non**.

    ![Package d’accès - Demandes - Paramètres d’approbation](./media/active-directory-entitlement-management-request-policy/approval.png)

1. Pour exiger des utilisateurs une justification pour demander le package d’accès, affectez la valeur **Oui** à **Exiger la justification du demandeur**.

1. Déterminez si la demande doit être approuvée en une ou plusieurs étapes. Si une seule étape est nécessaire, affectez la valeur **1** à **Combien de phases**.

1. Pour les approbateurs, sélectionnez **Gestionnaire comme approbateur** ou **Choisir des approbateurs spécifiques**.

    Le gestionnaire est déterminé par l’attribut **Gestionnaire** dans le profil Azure AD de l’utilisateur. Pour plus d’informations, consultez [Ajouter ou mettre à jour les informations du profil utilisateur avec Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

    ![Profil utilisateur Azure Active Directory - Attribut Gestionnaire](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. Si vous avez sélectionné Gestionnaire en tant qu’approbateur, cliquez sur **Ajouter un secours** pour sélectionner un ou plusieurs utilisateurs ou groupes dans votre annuaire en tant qu’approbateur de secours au cas où la gestion des droits ne trouverait pas le gestionnaire.

1. Si vous avez sélectionné Choisir des approbateurs spécifiques, cliquez sur **Ajouter des approbateurs** pour sélectionner dans votre annuaire un ou plusieurs utilisateurs ou groupes devant assumer la fonction d’approbateurs.

1. Dans **La décision doit être prise dans combien de jours ?** , spécifiez le nombre de jours dont dispose un approbateur pour examiner une demande pour ce package d’accès.

    Si une demande n’est pas approuvée dans le délai imparti, elle est refusée automatiquement. L’utilisateur devra envoyer une autre demande pour le package d’accès.

1. Pour exiger des utilisateurs une justification pour demander le package d’accès, définissez **Demander une justification** sur **Oui**.

    Une justification est visible par les autres approbateurs et le demandeur.

## <a name="enable-requests"></a>Activer les demandes

1. Si vous souhaitez que le package d’accès soit immédiatement disponible afin d’être demandé par les utilisateurs dans la stratégie de demandes, cliquez sur **Oui** pour activer.

    Vous pouvez toujours l’activer plus tard, après avoir créé le package d’accès.

    Si vous avez sélectionné **Aucun (attributions directes d’administrateur uniquement)** et que vous affectez la valeur **No** à Activer, les administrateurs ne pourront pas attribuer directement ce package d’accès.

    ![Package d’accès - Stratégie - Paramètre Activer la stratégie](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Cliquez sur **Suivant**.
