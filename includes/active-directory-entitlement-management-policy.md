---
title: Fichier Include
description: Fichier Include
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 07/31/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: db16a2f122da1bf6c767e0a47c93c22f1882c406
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817214"
---
### <a name="policy-for-users-in-your-directory"></a>Stratégie : Pour les utilisateurs dans votre répertoire

Si vous souhaitez que votre stratégie s’applique aux utilisateurs figurant dans votre annuaire qui peuvent demander ce package d’accès, procédez comme suit.  Les **utilisateurs figurant dans votre annuaire** sont des utilisateurs internes et externes précédemment invités dans l’annuaire, soit parce qu’ils ont demandé une gestion des droits d’utilisation avec autre package d’accès, soit parce qu’ils sont invités avec Azure AD B2B. Lors de la définition de la stratégie, vous pouvez spécifier des utilisateurs individuels ou plus souvent des groupes d’utilisateurs. Par exemple, votre organisation a peut-être déjà un groupe tel que **Tous les employés**.  Si ce groupe est ajouté dans la stratégie pour des utilisateurs qui peuvent demander l’accès, tout membre de ce groupe peut ensuite demander l’accès.

1. Dans la section **Utilisateurs pouvant demander l’accès**, sélectionnez **Pour les utilisateurs dans votre annuaire**.

    Notez que le paramètre **For users in your directory (Pour les utilisateurs dans votre répertoire)** inclut les utilisateurs membres et les utilisateurs invités qui ont été ajoutés à votre répertoire. Si vous souhaitez inclure uniquement des utilisateurs membres et non des utilisateurs invités, sélectionnez **For users in your directory (Pour les utilisateurs dans votre répertoire)** , puis un groupe de vos utilisateurs membres. Si nécessaire, vous pouvez créer un groupe dynamique de vos utilisateurs membres (user.userType -eq "Member"). Pour plus d’informations, consultez [Règles d’appartenance de groupe dynamique dans Azure Active Directory](../articles/active-directory/users-groups-roles/groups-dynamic-membership.md).

1. Dans la section **Sélectionner des utilisateurs et des groupes**, cliquez sur **Ajouter des utilisateurs et des groupes**.

1. Dans le volet Sélectionnez des utilisateurs et des groupes, sélectionnez les utilisateurs et les groupes à ajouter.

    ![Package d’accès - Stratégie - Sélectionner des utilisateurs et des groupes](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Cliquez sur **Sélectionner** pour ajouter les utilisateurs et les groupes.

1. Passez à la section [Stratégie : Demander](#policy-request).

### <a name="policy-for-users-not-in-your-directory"></a>Stratégie : Pour les utilisateurs qui ne sont pas dans votre répertoire

Procédez comme suit si vous souhaitez que votre stratégie s’applique aux utilisateurs qui ne sont pas dans votre répertoire et qui peuvent demander ce package d’accès. Les **utilisateurs absents de votre annuaire** sont des utilisateurs qui se trouvent dans un autre annuaire Azure AD et n’ont peut-être pas encore été invités dans votre annuaire.  Les répertoires doivent être configurés pour être autorisés dans les paramètres de **Restrictions de collaboration des relations organisationnelles**.

> [!NOTE]
> Un compte d’utilisateur invité externe est créé pour un utilisateur qui n’est pas encore dans votre annuaire et dont la demande est approuvée ou approuvée automatiquement. L’invité est invité, mais ne recevra pas d’invitation par e-mail. Il recevra un e-mail une fois l’attribution au package d’accès fournie. Par défaut, quand cet utilisateur invité n’a plus aucune attribution de package d’accès (pour cause d’expiration ou d’annulation), son compte ne peut plus se connecter et est supprimé par la suite. Si vous voulez que des utilisateurs invités demeurent indéfiniment dans votre répertoire, même s’ils n’ont plus d’attribution de package d’accès, vous pouvez modifier les paramètres de votre configuration de gestion des droits d’utilisation.

1. Dans la section **Utilisateurs pouvant demander l’accès**, sélectionnez **Pour les utilisateurs qui ne sont pas dans votre répertoire**.

1. Dans la section **Sélectionner un répertoire Azure AD externe**, cliquez sur **Ajouter des répertoires**.

1. Entrez un nom de domaine et recherchez un annuaire Azure AD ayant ce nom de domaine.

1. Vérifiez qu’il s’agit du bon répertoire en fonction du nom de répertoire et du domaine initial.

    > [!NOTE]
    > Tous les utilisateurs du répertoire seront en mesure de demander ce package d’accès. Ceci inclut les utilisateurs de tous les sous-domaines associés au répertoire, pas seulement le domaine utilisé dans la recherche.

    ![Package d’accès - Stratégie - Sélectionner des répertoires](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Cliquez sur **Ajouter** pour ajouter le répertoire.

1. Répétez cette étape pour ajouter plus de répertoires.

1. Une fois que vous avez ajouté le compte de répertoires voulu à inclure dans la stratégie, cliquez sur **Sélectionner**.

1. Passez à la section [Stratégie : Demander](#policy-request).

### <a name="policy-none-administrator-direct-assignments-only"></a>Stratégie : Aucune (attributions direct administrateur uniquement)

Procédez comme suit si vous souhaitez que votre stratégie contourne les demandes d’accès et autorise les administrateurs à attribuer directement les utilisateurs spécifiques au package d’accès. Les utilisateurs n’auront pas à demander le package d’accès. Vous pouvez toujours définir des paramètres d’expiration, mais il n’y a aucun paramètre de demande.

1. Dans la section **Utilisateurs pouvant demander l’accès**, sélectionnez **Aucune (attributions direct administrateur uniquement)** .

    Après avoir créé le package d’accès, vous pouvez directement attribuer des utilisateurs internes et externes spécifiques à ce package d’accès. Si vous spécifiez un utilisateur externe, un compte d’utilisateur invité est créé dans votre répertoire.

1. Passez à la section [Stratégie : Expiration](#policy-expiration).

### <a name="policy-request"></a>Stratégie : Requête

Dans la section Demande, vous spécifiez les paramètres d’approbation lorsque des utilisateurs demandent le package d’accès.

1. Pour exiger une approbation pour des demandes des utilisateurs sélectionnés, définissez **Exiger une approbation** sur **Oui**. Pour que les demandes soient approuvées automatiquement, définissez cette valeur sur **Non**.

1. Si vous exigez une approbation, dans la section **Sélectionner des approbateurs** , cliquez sur **Ajouter des approbateurs**.

1. Dans le volet Sélectionner des approbateurs, sélectionnez un ou plusieurs utilisateurs et/ou groupes en tant qu’approbateurs.

    La demande n’a besoin d’être approuvée que par un seul des approbateurs sélectionnés. L’approbation de tous les approbateurs n’est pas nécessaire. La décision d’approbation repose sur le premier approbateur à consulter la demande.

    ![Package d’accès - Stratégie - Sélectionner des approbateurs](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Cliquez sur **Sélectionner** pour ajouter des approbateurs.

1. Cliquez sur **Afficher les paramètres avancés de demande** pour afficher plus de paramètres.

    ![Package d’accès - Stratégie - Sélectionner des répertoires](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Pour exiger des utilisateurs une justification pour demander le package d’accès, définissez **Demander une justification** sur **Oui**.

1. Pour demander à l’approbateur une justification pour approuver la demande du package d’accès, définissez **Demander une justification à l’approbateur** sur **Oui**.

1. Dans le champ **Délai d’expiration de l’approbation de la demande (jours)** , spécifiez le temps dont disposent les approbateurs pour consulter une demande. Si aucun approbateur ne la consulte durant cette période, la demande expire et l’utilisateur doit renvoyer une autre demande pour le package d’accès.

### <a name="policy-expiration"></a>Stratégie : Expiration

Dans la section Expiration, vous spécifiez l’expiration de l’attribution du package d’accès à un utilisateur.

1. Dans la section **Expiration**, définissez **Le package d’accès expire** sur **Date**, **Nombre de jours** ou **Jamais**.

    Pour **Date**, sélectionnez une date d’expiration.

    Pour **Nombre de jours**, spécifiez un nombre de jours compris entre 0 et 3 660.

    Selon votre sélection, l’attribution du package d’accès à un utilisateur expire à une date définie, un nombre de jours définis après approbation ou n’expire jamais.

1. Cliquez sur **Afficher les paramètres avancés d’expiration** pour afficher plus de paramètres.

1. Pour autoriser un utilisateur à étendre son attribution, définissez **Autoriser un utilisateur à étendre son accès** sur **Oui**.

    Si les extensions sont autorisées dans la stratégie, l’utilisateur recevra un e-mail 14 jours ainsi qu’un jour avant l’expiration de son attribution l’invitant à étendre l’attribution.

    ![Package d’accès - Stratégie - Paramètres d’expiration](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Stratégie : Activer la stratégie

1. Si vous souhaitez que le package d’accès soit immédiatement disponible aux utilisateurs dans la stratégie, cliquez sur **Oui** pour activer la stratégie.

    Vous pouvez toujours l’activer plus tard, après avoir créé le package d’accès.

    ![Package d’accès - Stratégie - Paramètre Activer la stratégie](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Cliquez sur **Suivant** ou **Créer**.
