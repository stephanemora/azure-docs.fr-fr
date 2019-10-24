---
title: Régir l’accès des utilisateurs externes à la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez plus en détail les paramètres à spécifier pour régir l’accès des utilisateurs externes à la gestion des droits d'utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcf4a0272e21a1fba3cf9adbd9158492e4318578
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72452907"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>Régir l’accès des utilisateurs externes à la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La gestion des droits d'utilisation Azure AD utilise [Azure AD B2B (Business-to-Business)](../b2b/what-is-b2b.md) pour collaborer avec des personnes extérieures à votre organisation dans un autre annuaire. Avec Azure AD B2B, les utilisateurs externes s’authentifient auprès de leur annuaire de base, mais disposent d’une représentation dans votre annuaire. La représentation dans votre annuaire permet à l’utilisateur d’avoir accès à vos ressources.

Cet article décrit les paramètres que vous pouvez spécifier pour régir l’accès des utilisateurs externes.

## <a name="how-entitlement-management-can-help"></a>De quelle façon la gestion des droits d’utilisation peut aider

Lorsque vous utilisez l'expérience d'invitation [Azure AD B2B](../b2b/what-is-b2b.md), vous devez déjà connaître les adresses e-mail des utilisateurs invités externes que vous souhaitez importer dans votre annuaire de ressources, et avec lesquels vous voulez collaborer. Cela fonctionne très bien lorsque vous travaillez sur un projet plus petit ou à court terme et que vous connaissez déjà tous les participants, mais s'avère plus complexe si vous souhaitez collaborer avec un grand nombre d'utilisateurs ou si les participants changent au fil du temps.  Par exemple, vous pouvez travailler avec une autre organisation et avoir un point de contact avec cette organisation, mais au fil du temps, d'autres utilisateurs de cette organisation auront également besoin d'un accès.

Avec la gestion des droits d'utilisation, vous pouvez définir une stratégie autorisant les utilisateurs des organisations que vous spécifiez à pouvoir demander eux-mêmes un package d'accès. Vous pouvez spécifier si une approbation est requise, ainsi qu'une date d’expiration de l’accès. Si une approbation est exigée, vous pouvez également inviter un ou plusieurs utilisateurs de l'organisation externe dans votre annuaire, et le ou les désigner comme approbateurs, car ils sont susceptibles de savoir quels utilisateurs externes de leur organisation ont besoin d'un accès. Une fois le package d'accès configuré, vous pouvez envoyer le lien de ce package à la personne à contacter (sponsor) au sein de l'organisation externe. Cette personne peut le partager avec d'autres utilisateurs de l'organisation externe et ceux-ci peuvent à leur tour utiliser ce lien pour demander le package d'accès. Les utilisateurs de cette organisation déjà invités dans votre répertoire peuvent également utiliser ce lien.

Lorsqu'une demande est approuvée, la gestion des droits d'utilisation accorde à l'utilisateur l'accès nécessaire, notamment une invitation si cet utilisateur n'est pas encore présent dans votre répertoire. Azure AD crée automatiquement un compte invité B2B pour cet utilisateur. Notez qu’un administrateur peut avoir précédemment restreint les organisations autorisées à collaborer, en définissant une [liste d'autorisations ou de refus B2B](../b2b/allow-deny-list.md) pour autoriser ou refuser les invitations liées à d’autres organisations.  Si l’utilisateur ne figure pas sur la liste d'autorisations ou de refus, il ne sera pas invité.

Pour limiter dans le temps l'accès de l'utilisateur externe, vous spécifiez une date d'expiration dans la stratégie, comme 180 jours. Une fois ces 180 jours écoulés et en l'absence d’extension, la gestion des droits d'utilisateur supprime tous les accès associés à ce package d'accès. Par défaut, si l'utilisateur invité par le biais de la gestion des droits d'utilisation ne dispose d'aucune autre attribution de package d'accès, lorsqu'il perd sa dernière attribution, son compte invité est bloqué pendant 30 jours, puis supprimé. Et ce, afin d'éviter la prolifération de comptes inutiles. Comme décrit dans les sections suivantes, ces paramètres sont configurables.

## <a name="how-access-works-for-external-users"></a>Comment l’accès fonctionne pour les utilisateurs externes

Le diagramme et les étapes ci-dessous offrent une vue d’ensemble de la façon dont les utilisateurs externes sont autorisés à accéder à un package d’accès.

![Diagramme montrant le cycle de vie des utilisateurs externes](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Dans votre annuaire, vous créez un package d’accès qui comprend une stratégie [Pour les utilisateurs qui ne sont pas dans votre annuaire](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Vous envoyez un [lien du portail Mon accès](entitlement-management-access-package-settings.md) à votre contact, à l’organisation externe, afin qu’il puisse le partager avec ses utilisateurs qui veulent demander le package d’accès.

1. Un utilisateur externe (**Demandeur A** dans cet exemple) utilise le lien vers le portail Mon accès pour [demander l’accès](entitlement-management-request-access.md) au package d’accès.

1. Un approbateur [approuve la demande](entitlement-management-request-approve.md) (ou la demande est approuvée automatiquement).

1. La demande passe à l’[état de remise en cours](entitlement-management-process.md).

1. À l’aide du processus d’invitation B2B, un compte d’utilisateur invité est créé dans votre annuaire : **Demandeur A (Invité)**  dans cet exemple. Si une [liste verte ou une liste d’exclusion](../b2b/allow-deny-list.md) est définie, le paramètre de liste est appliqué.

1. L’utilisateur invité a reçu l’accès à toutes les ressources du package d’accès. Les modifications apportées à Azure AD et à d’autres services Microsoft Online Services, ou à des applications SaaS connectées, peuvent prendre un certain temps. Pour plus d’informations, consultez [Lorsque les modifications sont appliquées](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. L’utilisateur externe reçoit un e-mail l’informant que son accès a été [autorisé](entitlement-management-process.md).

1. Pour accéder aux ressources, l’utilisateur externe peut cliquer sur le lien dans l’e-mail, ou tenter d’accéder directement à une des ressources de l’annuaire pour terminer le processus d’invitation.

1. Selon les paramètres de stratégie, au fil du temps, l’attribution du package d’accès à l’utilisateur externe expire, et l’accès de l’utilisateur externe est supprimé.

1. En fonction du cycle de vie des paramètres des utilisateurs externes, lorsque l’utilisateur externe n’a plus d’attribution de package d’accès, il ne peut plus se connecter et le compte d’utilisateur invité est supprimé de votre annuaire.

## <a name="manage-the-lifecycle-of-external-users"></a>Gérer le cycle de vie des utilisateurs externes

Vous pouvez sélectionner ce qui se passe lorsqu’un utilisateur externe, qui a été invité à accéder à votre annuaire par le biais d’une demande de package d’accès en cours d’approbation, ne dispose plus d’attributions de package d’accès. Cela peut se produire si l’utilisateur abandonne toutes les attributions de package d’accès, ou si l’attribution de son dernier package d’accès arrive à expiration. Par défaut, lorsqu’un utilisateur externe n’a plus d’attributions de package d’accès, il ne peut pas se connecter à votre annuaire. Au bout de 30 jours, son compte d’utilisateur invité est supprimé de votre annuaire.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, dans la section **Gestion des droits d’utilisation**, cliquez sur **Paramètres**.

1. Cliquez sur **Modifier**.

    ![Paramètres de la gestion du cycle de vie des utilisateurs externes](./media/entitlement-management-external-users/settings-external-users.png)

1. Dans la section **Gérer le cycle de vie des utilisateurs externes**, sélectionnez les différents paramètres pour les utilisateurs externes.

1. Si, lorsqu’un utilisateur externe perd sa dernière attribution aux packages d’accès, vous souhaitez l’empêcher de se connecter à cet annuaire, définissez **Empêcher l'utilisateur externe de se connecter à cet annuaire** sur **Oui**.

1. Si, lorsqu’un utilisateur externe perd sa dernière attribution aux packages d’accès, vous souhaitez supprimer son compte d’utilisateur invité dans votre annuaire, définissez **Supprimer l’utilisateur externe**  sur **Oui**.

    > [!NOTE]
    > La gestion des droits d'utilisation supprime uniquement les comptes qui ont été invités par l’intermédiaire de la gestion des droits d'utilisation. Notez également qu’un utilisateur ne pourra pas se connecter et sera supprimé de votre annuaire, même s’il a été ajouté aux ressources de votre annuaire qui n’étaient pas des attribution de packages d’accès. Si l’invité était présent dans votre annuaire avant de recevoir des attributions de package d’accès, il sera conservé. Toutefois, s’il a été invité par le biais d’une attribution de package d’accès, et qu’après avoir été invité il a également été affecté à un site OneDrive Entreprise ou SharePoint Online, il sera toujours supprimé.

1. Si vous souhaitez supprimer le compte d’utilisateur invité de votre annuaire, vous pouvez définir le nombre de jours avant sa suppression. Si vous souhaitez supprimer le compte d’utilisateur invité dès qu’il perd la dernière attribution à un package d’accès, définissez **Nombre de jours avant la suppression de l’utilisateur externe de cet annuaire**  sur **0**.

1. Cliquez sur **Enregistrer**.

## <a name="enable-a-catalog-for-external-users"></a>Activer un catalogue pour les utilisateurs externes

Lorsque vous créez un [catalogue](entitlement-management-catalog-create.md), il existe un paramètre permettant aux utilisateurs qui proviennent d’annuaires externes de demander des packages d’accès dans le catalogue. Si vous ne souhaitez pas que les utilisateurs externes soient autorisés à demander des packages d’accès dans le catalogue, définissez **Activé pour les utilisateurs externes**  sur **Non**.

**Rôle prérequis :** administrateur général, administrateur d’utilisateurs ou propriétaire de catalogue.

![Volet Nouveau catalogue](./media/entitlement-management-shared/new-catalog.png)

Vous pouvez également modifier ce paramètre après avoir créé le catalogue.

![Modifier les paramètres du catalogue](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>Étapes suivantes

- [Pour les utilisateurs qui ne sont pas dans votre annuaire](entitlement-management-access-package-create.md#for-users-not-in-your-directory)
- [Créer et gérer un catalogue de ressources](entitlement-management-catalog-create.md)
- [Délégation et rôles](entitlement-management-delegate.md)
