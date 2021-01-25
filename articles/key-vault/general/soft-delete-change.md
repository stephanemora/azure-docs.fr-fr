---
title: Activer la suppression réversible sur tous les objets de coffre de clés - Azure Key Vault | Microsoft Docs
description: Utilisez ce document pour adopter la suppression réversible pour tous les coffres de clés et pour apporter des modifications à l’application et à l’administration afin d’éviter les erreurs conflictuelles.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572865"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>La suppression réversible sera activée sur tous les coffres de clés

> [!WARNING]
> Changement cassant : la possibilité de refuser la suppression réversible sera bientôt dépréciée. Il est recommandé aux utilisateurs et aux administrateurs d’Azure Key Vault d’activer immédiatement la suppression réversible sur leurs coffres de clés.
>
> Pour un HSM managé Azure Key Vault, la suppression réversible est activée par défaut et ne peut pas être désactivée.

Quand un secret est supprimé d’un coffre de clés sans la protection de la suppression réversible, le secret est définitivement supprimé. Les utilisateurs peuvent actuellement refuser la suppression réversible pendant la création du coffre de clés. Toutefois, Microsoft activera bientôt la protection par suppression réversible sur tous les coffres de clés afin de protéger les secrets contre toute suppression accidentelle ou malveillante par un utilisateur. Les utilisateurs ne seront plus en mesure de refuser ou désactiver la suppression réversible.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagramme montrant comment un coffre de clés est supprimé avec protection par suppression réversible et sans protection par suppression réversible.":::

Pour plus d’informations sur la fonctionnalité de suppression réversible, consultez [Vue d’ensemble de la suppression réversible d’Azure Key Vault](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Mon application peut-elle fonctionner avec la suppression réversible activée ?

> [!Important] 
> Vérifiez attentivement les informations suivantes avant d’activer la suppression réversible pour vos coffres de clés.

Les noms de coffres de clés sont globalement uniques. Les noms des secrets stockés dans un coffre de clés sont également uniques. Vous ne pourrez pas réutiliser le nom d’un coffre de clés ou d’un objet de coffre de clés qui existe à l’état de suppression réversible. 

Par exemple, si votre application crée programmatiquement un coffre de clés nommé « Coffre A » et supprime ensuite « Coffre A », le coffre de clés basculera à l’état de suppression réversible. Votre application ne pourra pas recréer un autre coffre de clés nommé « Coffre A » tant que le coffre de clés n’aura pas été définitivement supprimé. 

De plus, si votre application crée une clé nommée `test key` dans « Coffre A » et supprime ultérieurement cette clé, elle ne pourra pas créer de clé nommée `test key` dans « Coffre A » tant que l’objet `test key` n’aura pas été définitivement supprimé. 

Toute tentative de supprimer un objet de coffre de clés et de le recréer avec le même nom sans d’abord le sortir de l’état de suppression réversible peut entraîner des erreurs de conflit. Ces erreurs peuvent entraîner l’échec de vos applications ou de l’automatisation. Consultez votre équipe de développement avant d’apporter les modifications d’application et d’administration nécessaires suivantes. 

### <a name="application-changes"></a>Changements dans l’application

Si votre application part du principe que la suppression réversible n’est pas activée, et qu’elle s’attend à ce que les noms de secrets ou de coffres de clés supprimés soient disponibles pour une réutilisation immédiate, vous devrez apporter les modifications suivantes à votre logique d’application.

1. Supprimez le coffre de clés ou le secret d’origine.
1. Supprimez définitivement le coffre de clés ou le secret qui est dans l’état de suppression réversible.
1. Attendez la fin de la suppression définitive. Une nouvelle création immédiate peut entraîner un conflit.
1. Recréez le coffre de clés avec le même nom.
1. Si l’opération de création entraîne toujours une erreur de conflit de noms, essayez de recréer le coffre de clés. La mise à jour des enregistrements Azure DNS peut prendre jusqu’à 10 minutes dans le pire des cas.

### <a name="administration-changes"></a>Changements au niveau de l’administration

Les principaux de sécurité qui ont besoin d’un accès pour supprimer définitivement des secrets doivent disposer d’autorisations de stratégie d’accès supplémentaires pour supprimer définitivement ces secrets et le coffre de clés.

Désactivez toute stratégie Azure sur vos coffres de clés qui impose que la suppression réversible soit désactivée. Il peut être nécessaire de faire remonter ce problème à un administrateur qui contrôle les stratégies Azure appliquées à votre environnement. Si cette stratégie n’est pas désactivée, vous risquez de perdre la capacité à créer de nouveaux coffres de clés dans l’étendue de la stratégie appliquée.

Si votre organisation est soumise à des exigences de conformité légales et qu’elle ne peut pas autoriser la suppression des coffres de clés et des secrets de façon à rester dans un état récupérable pendant une période étendue, vous devrez ajuster la période de conservation de la suppression réversible pour répondre aux normes de votre organisation. Vous pouvez configurer une période de conservation comprise entre sept et 90 jours.

## <a name="procedures"></a>Procédures

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Auditer vos coffres de clés pour vérifier si la suppression réversible est activée

1. Connectez-vous au portail Azure.
1. Recherchez **Azure Policy**.
1. Sélectionnez **Définitions**.
1. Sous **Catégorie**, sélectionnez **Key Vault** dans le filtre.
1. Sélectionnez la stratégie **Le coffre de clés doit avoir la suppression réversible activée**.
1. Sélectionnez **Attribuer**.
1. Définissez l’étendue à votre abonnement.
1. Vérifiez que l’effet de la stratégie est défini sur **Audit**.
1. Sélectionnez **Vérifier + créer**. Une analyse complète de votre environnement peut prendre jusqu’à 24 heures.
1. Dans le volet **Azure Policy**, sélectionnez **Conformité**.
1. Sélectionnez la stratégie que vous avez appliquée.

Vous pouvez maintenant filtrer et voir les coffres de clés pour lesquels la suppression réversible est activée (ressources conformes) et les coffres de clés pour lesquels la suppression réversible n’est pas activée (ressources non conformes).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Activer la suppression réversible pour un coffre de clés existant

1. Connectez-vous au portail Azure.
1. Recherchez votre coffre de clés.
1. Sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Suppression réversible**, sélectionnez l’option **Activer la récupération de ce coffre et de ses objets**.
1. Définir la période de conservation pour la suppression réversible.
1. Sélectionnez **Enregistrer**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Accorder des autorisations de stratégie d’accès de suppression définitive à un principal de sécurité

1. Connectez-vous au portail Azure.
1. Recherchez votre coffre de clés.
1. Sélectionnez **Stratégies d’accès** sous **Paramètres**.
1. Sélectionnez le principal de service auquel vous voulez accorder l’accès.
1. Parcourez chaque menu déroulant sous **Clé**, **Secret** et **Autorisations de certificat** jusqu’à voir **Opérations privilégiées**. Sélectionnez l’autorisation **Vider**.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="does-this-change-affect-me"></a>Ce changement m’affecte-t-il ?

Si vous avez déjà activé la suppression réversible, ou si vous ne supprimez pas des objets de coffre de clés pour ensuite les recréer avec le même nom, vous ne remarquerez probablement aucun changement de comportement du coffre de clés.

Si vous avez une application qui supprime et recrée fréquemment des objets de coffre de clés avec les mêmes conventions de nommage, vous devrez apporter des modifications à la logique de votre application pour conserver le comportement attendu. Consultez la section [Changements dans l’application](#application-changes) de cet article.

### <a name="how-do-i-benefit-from-this-change"></a>Quel est le bénéfice de ce changement ?

La protection par suppression réversible offre à votre organisation une autre couche de protection contre la suppression accidentelle ou malveillante. En tant qu’administrateur d’un coffre de clés, vous pouvez restreindre l’accès aux autorisations de récupération et de vidage.

Si un utilisateur supprime accidentellement un coffre de clés ou un secret, vous pouvez lui accorder des autorisations d’accès pour récupérer le secret proprement dit sans risquer qu’il supprime définitivement le secret ou le coffre de clés. Ce processus de libre-service permet de réduire le temps d’arrêt de votre environnement et de garantir la disponibilité de vos secrets.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Comment savoir si je dois agir ?

Suivez les étapes fournies dans la section [Auditer vos coffres de clés pour vérifier si la suppression réversible est activée](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) de cet article. Cette modification affecte tout coffre de clés pour lequel la suppression réversible n’est pas activée.

### <a name="what-action-do-i-need-to-take"></a>Quelle action dois-je effectuer ?

Une fois que vous avez confirmé que vous n’avez pas besoin d’apporter des modifications à votre logique d’application, activez la suppression réversible sur tous vos coffres de clés.

### <a name="when-do-i-need-to-take-action"></a>Quand dois-je agir ?

Pour garantir que vos applications ne sont pas affectées, activez dès que possible la suppression réversible sur vos coffres de clés.

## <a name="next-steps"></a>Étapes suivantes

- Pour toute question concernant ce changement, contactez-nous à l’adresse [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com).
- Lisez [Vue d’ensemble de la suppression réversible](soft-delete-overview.md).
