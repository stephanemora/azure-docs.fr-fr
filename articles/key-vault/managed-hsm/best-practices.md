---
title: Bonnes pratiques pour utiliser Azure Key Vault Managed HSM
description: Ce document décrit certaines des meilleures pratiques à employer pour utiliser Key Vault.
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: ambapat
ms.openlocfilehash: a7cccecc47973d372453ce86d0d5d12c8d773b3a
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413966"
---
# <a name="best-practices-when-using-managed-hsm"></a>Meilleures pratiques d’utilisation du HSM managé

## <a name="control-access-to-your-managed-hsm"></a>Contrôler l’accès à votre HSM managé

Le HSM managé est un service cloud qui protège les clés de chiffrement. Ces données étant sensibles et critiques, veillez à sécuriser l’accès à vos HSM managés en permettant seulement aux applications et utilisateurs autorisés d’y accéder. Cet [article](access-control.md) fournit une vue d’ensemble du modèle d’accès. Il décrit l’authentification et l’autorisation, ainsi que le contrôle d’accès en fonction du rôle.
- Créez un [groupe de sécurité Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md) pour les administrateurs HSM (plutôt que de leur attribuer le rôle d’administrateur). Cela permet d’éviter un « verrouillage administratif » en cas de suppression d’un compte individuel.
- Verrouillez l’accès à vos groupes d’administration, abonnements, groupes de ressources et HSM managés. Utilisez Azure RBAC pour contrôler l’accès à vos groupes d’administration, abonnements et groupes de ressources.
- Créez des attributions de rôle par clé à l’aide du [RBAC local HSM managé](access-control.md#data-plane-and-managed-hsm-local-rbac).
- Pour maintenir la séparation des tâches, évitez d’attribuer plusieurs rôles aux mêmes principaux. 
- Utilisez un principal à l’accès le moins privilégié pour attribuer des rôles.
- Créez une définition de rôle personnalisée avec un ensemble précis d’autorisations.

## <a name="choose-regions-that-support-availability-zones"></a>Choisir les régions prenant en charge les zones de disponibilité

- Pour garantir une haute disponibilité et une résilience de zone optimales, choisissez les régions Azure au sein desquelles les [zones de disponibilité](../../availability-zones/az-overview.md) sont prises en charge. Ces régions apparaissent en tant que « régions recommandées » dans le portail Azure.

## <a name="backup"></a>Backup

- Veillez à régulièrement sauvegarder votre HSM. Les sauvegardes peuvent être effectuées au niveau du HSM, ainsi que pour des clés spécifiques. 

## <a name="turn-on-logging"></a>Activation de la journalisation

- [Activez la journalisation](logging.md) pour votre HSM. Configurez également des alertes.

## <a name="turn-on-recovery-options"></a>Activer les options de récupération

- Le [suppression réversible](soft-delete-overview.md) est activée par défaut. Vous pouvez choisir une période de conservation comprise entre 7 et 90 jours.
- Activez la protection contre le vidage pour empêcher la suppression immédiate permanente de module HSM ou de clés. Lorsque la protection contre le vidage est activée, un module HSM ou des clés demeurent dans l’état Supprimé tant que les jours de conservation ne sont pas écoulés.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la sauvegarde/restauration HSM complète, consultez [Sauvegarde/Restauration complète](backup-restore.md).
- Pour savoir comment utiliser Azure Monitor pour configurer la journalisation, consultez [Journalisation du HSM managé](logging.md).
- Pour la gestion des clés, consultez [Gérer les clés du HSM managé](key-management.md).
- Pour gérer les attributions de rôles, consultez [Gestion du rôle HSM managé](role-management.md).
- Consultez [Vue d’ensemble de la suppression réversible d’un module HSM managé](soft-delete-overview.md) pour connaître les options de récupération.
