---
title: Suppression réversible de HSM managé dans Azure Key Vault | Microsoft Docs
description: La suppression réversible d’un HSM managé vous permet de récupérer des clés et instances de HSM supprimées. Cet article fournit une vue d’ensemble de la fonctionnalité.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: b832aa066e1d31bfc064f988c722d2503ff96507
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122525847"
---
# <a name="managed-hsm-soft-delete-overview"></a>Vue d’ensemble de la suppression réversible de HSM managé

> [!IMPORTANT]
> La suppression réversible ne peut pas être désactivée pour les ressources module HSM managé.

> [!IMPORTANT]
> Les ressources de module HSM managé supprimées de manière réversible continueront d’être facturées à plein tarif horaire jusqu’à leur suppression définitive.

La fonctionnalité de suppression réversible de module HSM managé permet la récupération de modules HSM et de clés supprimés. Plus précisément, cette fonctionnalité fournit les dispositifs de protection suivants :

- Après sa suppression, un module HSM ou une clé reste récupérable pendant une durée configurable de 7 à 90 jours calendaires. Vous pouvez définir la durée de conservation quand vous créez un module HSM. Si vous ne spécifiez pas de valeur, la durée de conservation par défaut de 90 jours sera utilisée. Cette durée donne aux utilisateurs suffisamment de temps pour remarquer la suppression accidentelle d’une clé ou d’un module HSM et d’y remédier.
- Pour supprimer définitivement une clé, les utilisateurs doivent effectuer deux actions. Tout d’abord, ils doivent supprimer la clé, ce qui a pour effet de la placer dans l’état de suppression réversible. Ensuite, ils doivent supprimer définitivement la clé se trouvant dans l’état de suppression réversible. L’opération de suppression définitive nécessite le rôle de responsable du chiffrement du module HSM managé. Ces dispositifs de protection supplémentaires réduisent le risque qu’un utilisateur supprime accidentellement ou par malveillance une clé ou un module HSM.


## <a name="soft-delete-behavior"></a>Comportement de la suppression réversible

La suppression réversible ne peut pas être désactivée pour les ressources module HSM managé.

Les ressources marquées comme supprimées sont conservées pendant une durée spécifiée. Il existe également un mécanisme de récupération des modules HSM ou des clés supprimés, ce qui vous permet d’annuler les suppressions.

La durée de conservation par défaut est de 90 jours. Quand vous créez une ressource module HSM, vous pouvez définir l’intervalle de la stratégie de conservation sur une valeur comprise entre 7 et 90 jours. La stratégie de rétention pour la protection contre le vidage utilise le même intervalle. Une fois la stratégie de conservation définie, vous ne pouvez plus la changer.

Vous ne pouvez pas réutiliser le nom d’une ressource module HSM supprimée de manière réversible tant que la période de conservation n’est pas terminée et que la ressource HSM n’a pas été purgée (supprimée définitivement).

## <a name="purge-protection"></a>Protection contre le vidage

La protection contre la suppression définitive est un comportement facultatif. Elle n’est pas activée par défaut. Vous pouvez l’activer en utilisant [Azure CLI](./recovery.md?tabs=azure-cli) ou [PowerShell](./recovery.md?tabs=azure-powershell).

Quand la protection contre la suppression définitive est activée, il n’est pas possible de supprimer définitivement un module HSM ou une clé se trouvant dans l’état de suppression réversible tant que la période de conservation n’est pas terminée. Après une suppression réversible, les HSM et les clés peuvent encore être récupérés, ce qui garantit que la stratégie de conservation sera appliquée.

La durée de conservation par défaut est de 90 jours. Vous pouvez définir l’intervalle de la stratégie de conservation sur une valeur comprise entre 7 et 90 jours quand vous créez un module HSM. L’intervalle de la stratégie de conservation peut être défini uniquement quand vous créez un module HSM. Il ne peut pas être changé ultérieurement.

Découvrez [Comment utiliser la suppression réversible de HSM managé avec l’interface CLI](./recovery.md?tabs=azure-cli#managed-hsms-cli) ou [Comment utiliser la suppression réversible de HSM managé avec PowerShell](./recovery.md?tabs=azure-powershell#managed-hsms-powershell).

## <a name="managed-hsm-recovery"></a>Récupération de HSM managé

Quand vous supprimez un module HSM, le service crée une ressource proxy dans l’abonnement, en ajoutant suffisamment de métadonnées pour permettre la récupération. La ressource proxy est un objet stocké. Elle est disponible dans le même emplacement que le module HSM supprimé. 

## <a name="key-recovery"></a>Récupération de clé

Quand vous supprimez une clé, le service la place dans l’état supprimé, ce qui la rend inaccessible à toute opération. Dans cet état, les clés peuvent être listées, récupérées ou supprimées définitivement. Pour voir les objets, utilisez la commande Azure CLI `az keyvault key list-deleted` (décrit dans [Suppression réversible et de protection contre la suppression définitive d’un module HSM managé avec l’interface CLI](./recovery.md?tabs=azure-cli#keys-cli)) ou le paramètre Azure PowerShell `-InRemovedState` (décrit dans [Suppression réversible et de protection contre la suppression définitive d’un module HSM managé avec PowerShell](./recovery.md?tabs=azure-powershell#keys-powershell)).  

Quand vous supprimez une clé, le module HSM managé planifie la suppression des données sous-jacentes qui correspondent au HSM ou à la clé supprimés pour qu’elle se produise après un intervalle de conservation prédéterminé. L’enregistrement DNS qui correspond au module HSM est également conservé pendant la période de conservation.

## <a name="soft-delete-retention-period"></a>Période de rétention de la suppression réversible

Les ressources ayant fait l’objet d’une suppression réversible sont conservées pendant une durée définie sur 90 jours. Au cours de la période de conservation de la suppression réversible :

- Vous pouvez afficher la liste de la totalité des modules HSM et clés se trouvant dans l’état de suppression réversible pour votre abonnement. Vous pouvez également accéder à des informations concernant leur suppression et leur récupération.
- Seuls les utilisateurs disposant du rôle Contributeur du module HSM managé peuvent afficher la liste des modules HSM supprimés. Nous vous recommandons de créer un rôle personnalisé avec ces autorisations pour le traitement des coffres supprimés.
- Les noms de HSM managés doivent être uniques dans un emplacement donné. Quand vous créez une clé, vous ne pouvez pas utiliser un nom si le module HSM contient une clé portant ce nom et étant dans l’état supprimé.
- Seuls les utilisateurs disposant du rôle Contributeur du module HSM managé peuvent lister, consulter, récupérer et supprimer de manière définitive des modules HSM managés.
- Seuls les utilisateurs titulaires du rôle « Responsable du chiffrement du HSM managé » peuvent répertorier, consulter, récupérer et supprimer définitivement des clés.
  
À la fin de la période de conservation suivant la suppression réversible d’un module HSM managé ou d’une clé, si ceux-ci n’ont pas été récupérés, le service les supprime définitivement. Vous ne pouvez pas replanifier la suppression de ressources.

### <a name="billing-implications"></a>Implications de facturation

Une HSM managé est un service à monolocataire. Quand vous créez un module HSM managé, le service réserve des ressources sous-jacentes qui lui sont allouées. Ces ressources restent allouées même quand le module HSM est dans l’état supprimé. Vous êtes facturé pour le module HSM pendant qu’il est dans l’état supprimé.

## <a name="next-steps"></a>Étapes suivantes

Ces articles décrivent les principaux scénarios d’utilisation de la suppression réversible :

- [Comment utiliser une suppression réversible de HSM managé avec PowerShell](./recovery.md?tabs=azure-powershell) 
- [Guide pratique pour utiliser une suppression réversible de module HSM managé avec l’interface Azure CLI](./recovery.md?tabs=azure-cli)
