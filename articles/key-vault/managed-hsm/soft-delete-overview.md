---
title: Suppression réversible de HSM managé dans Azure Key Vault | Microsoft Docs
description: La suppression réversible d’un HSM managé vous permet de récupérer des clés et instances de HSM supprimées.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 06/01/2021
ms.openlocfilehash: 673260fb8b78cb08ccd8581862238dc2e0341362
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414911"
---
# <a name="managed-hsm-soft-delete-overview"></a>Vue d’ensemble de la suppression réversible de HSM managé

> [!IMPORTANT]
> La suppression réversible est activée par défaut pour les ressources de HSM managé. Il n’est pas possible de la désactiver.

> [!IMPORTANT]
> Les ressources de HSM managés supprimés de manière réversible continueront d’être facturés à plein tarif horaire jusqu’à leur suppression définitive.

La fonctionnalité de suppression réversible de HSM managé permet la récupération des HSM et clés supprimés. Plus précisément, cette fonctionnalité offre les protections suivantes :

- Après suppression, un HSM ou une clé restent récupérables pendant une période configurable de 7 à 90 jours calendaires. La période de rétention peut être définie lors de la création du HSM. Si aucune valeur n’est spécifiée, la période de rétention par défaut est de 90 jours. Les utilisateurs disposent ainsi de suffisamment de temps pour remarquer la suppression accidentelle d’une clé ou d’un HSM.
- Deux opérations sont nécessaires pour supprimer définitivement une clé. Tout d’abord, un utilisateur doit supprimer la clé, ce qui a pour effet de celle-ci dans l’état de suppression réversible. Ensuite, l’utilisateur doit supprimer définitivement la clé à l’état de suppression réversible. L’opération de suppression définitive nécessite que le rôle « Responsable du chiffrement du HSM managé » soit attribué à l’utilisateur. Ces protections supplémentaires réduisent le risque qu’un utilisateur supprime accidentellement ou par malveillance une clé ou un HSM.


## <a name="soft-delete-behavior"></a>Comportement de la suppression réversible

Pour un HSM manégé, la suppression réversible est activée par défaut. Vous ne pouvez pas créer de ressource de HSM managé quand la suppression réversible est désactivée.

Quand la suppression réversible est activée, les ressources marquées comme supprimées sont conservées pendant une période déterminée (par défaut, 90 jours). Le service fournit également un mécanisme de récupération des HSM ou clés supprimés, qui permet d’annuler leur suppression.

La période de rétention par défaut est de 90 jours mais, pendant la création d’une ressource de HSM, il est possible de définir l’intervalle de la stratégie de rétention sur une valeur comprise entre 7 et 90 jours. La stratégie de rétention pour la protection contre le vidage utilise le même intervalle. Une fois défini, l'intervalle de la stratégie de rétention ne peut plus être modifié.

Vous ne pouvez pas réutiliser le nom d’une ressource de HSM supprimée de manière réversible tant que la période de rétention n’est pas écoulée et que le HSM n’a pas été supprimé définitivement.

## <a name="purge-protection"></a>Protection contre le vidage

La protection contre le vidage ou la suppression définitive est un comportement facultatif et n’est **pas activé par défaut**. Elle peut être activée via [CLI](./recovery.md?tabs=azure-cli) ou [PowerShell](./recovery.md?tabs=azure-powershell).

Quand la protection contre le vidage est activée, il n’est pas possible de supprimer définitivement un HSM ou une clé à l’état supprimé avant la fin de la période de rétention. Après une suppression réversible, les HSM et les clés restent récupérables pour garantir le respect de la stratégie de rétention.

La période de rétention par défaut est de 90 jours, mais il est possible de définir l’intervalle de la stratégie de rétention sur une valeur comprise entre 7 et 90 jours lors de la création d’un HSM. L’intervalle de la stratégie de rétention ne peut être défini qu’au moment de la création d’un HSM. Cette valeur n’est pas modifiable par la suite.

Découvrez [Comment utiliser la suppression réversible de HSM managé avec l’interface CLI](./recovery.md?tabs=azure-cli#managed-hsm-cli) ou [Comment utiliser la suppression réversible de HSM managé avec PowerShell](./recovery.md?tabs=azure-powershell#managed-hsm-powershell).

## <a name="managed-hsm-recovery"></a>Récupération de HSM managé

En cas de suppression d’un HSM, le service crée une ressource de proxy sous l’abonnement, en y ajoutant suffisamment de métadonnées pour permettre la récupération. La ressource de proxy est un objet stocké, disponible dans le même emplacement que le HSM supprimé. 

## <a name="key-recovery"></a>Récupération de clé

Lors de la suppression d’une clé, le service la met dans l’état supprimé qui la rend inaccessible à toute opération. Dans cet état, une clé peut être répertoriée, récupérée ou vidée (supprimée définitivement). Pour afficher les objets, utilisez la commande `az keyvault key list-deleted` de l’interface de ligne de commande Azure (comme décrit dans [Suppression réversible de HSM managé et protection contre la suppression définitive avec l’interface CLI](./recovery.md?tabs=azure-cli#keys-cli)) ou le paramètre Azure PowerShell `-InRemovedState` (comme décrit dans [Suppression réversible de HSM managé et protection contre la suppression définitive avec PowerShell](./recovery.md?tabs=azure-powershell#keys-powershell)).  

Dans le même temps, le HSM managé planifie la suppression des données sous-jacentes correspondant au HSM ou à la clé supprimés en vue de leur exécution après un intervalle de rétention prédéterminé. L’enregistrement DNS correspondant au HSM est également conservé pendant la l’intervalle de rétention.

## <a name="soft-delete-retention-period"></a>Période de rétention de la suppression réversible

Les ressources ayant fait l’objet d’une suppression réversible sont conservées pendant une durée fixée à 90 jours. Au cours de la période de rétention de la suppression réversible :

- Vous pouvez répertorier la totalité des HSM et des clés à l’état de suppression réversible pour votre abonnement, ainsi qu’accéder à des informations concernant leur suppression et leur récupération.
  - Seuls des utilisateurs titulaires du rôle Contributeur du HSM managé peuvent répertorier les HSM supprimés. Nous recommandons à nos utilisateurs de créer un rôle personnalisé avec ces autorisations spéciales pour le traitement des coffres supprimés.
- Il est impossible de créer un HSM managé du même nom au même emplacement. Parallèlement, il n’est pas possible de créer une clé dans un HSM donné si celui-ci contient une clé du même nom à l’état supprimé.
- Seuls des utilisateurs titulaires du rôle « Contributeur du HSM managé » peuvent répertorier, consulter, récupérer et suppression définitive des HSM managés.
- Seuls des utilisateurs titulaires du rôle « Responsable du chiffrement du HSM managé » peuvent répertorier, consulter, récupérer et supprimer définitivement des clés.
  
À la fin de la période de rétention suivant la suppression réversible d’un HSM managé ou d’une clé, si ceux-ci n’ont pas été récupérés, ils sont supprimés définitivement. La suppression de la ressource ne peut pas être replanifiée.

### <a name="billing-implications"></a>Implications de facturation

Une HSM managé est un service à monolocataire. Lorsque vous créez un HSM managé, le service réserve des ressources sous-jacentes qui y sont allouées. Ces ressources restent allouées même quand le HSM est à l’état supprimé. Par conséquent, vous serez facturé pour le HSM à l’état supprimé.

## <a name="next-steps"></a>Étapes suivantes

Les deux guides ci-après présentent les principaux scénarios d’usage de la suppression réversible.

- [Comment utiliser une suppression réversible de HSM managé avec PowerShell](./recovery.md?tabs=azure-powershell) 
- [Comment utiliser une suppression réversible de HSM managé avec l’interface CLI](./recovery.md?tabs=azure-cli)
