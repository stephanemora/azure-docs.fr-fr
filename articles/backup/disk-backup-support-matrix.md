---
title: Matrice de prise en charge de sauvegarde de disques Azure
description: Fournit un résumé des paramètres de prise en charge et des limitations de la Sauvegarde de disque Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 7d6de863a45bcadd73e847cbf407c13363952151
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806102"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Matrice de prise en charge de la Sauvegarde de disque Azure (en préversion)

>[!IMPORTANT]
>La Sauvegarde de disque Azure est disponible en préversion sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>[Remplissez ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) pour vous inscrire à la préversion.

Vous pouvez utiliser [Sauvegarde Azure](./backup-overview.md) pour protéger les disques Azure. Cet article résume la disponibilité régionale, les scénarios pris en charge et les limitations.

## <a name="supported-regions"></a>Régions prises en charge

La Sauvegarde de disque Azure est disponible en préversion dans les régions suivantes : USA Ouest, USA Centre-Ouest, USA Est 2, Canada Centre, Royaume-Uni Ouest, Suisse Nord, Suisse Ouest, Australie Centre, Australie Centre 2, Corée Centre, Corée Sud, Japon Ouest, Asie Est, Émirats arabes unis Nord, Brésil Sud, Inde Centre. 

D’autres régions seront annoncées quand elles seront disponibles.

## <a name="limitations"></a>Limites

- La Sauvegarde de disque Azure est prise en charge pour les disques managés Azure, y compris les disques partagés (disques SSD Premium partagés). Les disques non managés ne sont pas pris en charge. Actuellement, cette solution ne prend pas en charge les disques ultra, dont les disques ultra partagés, en raison de l’absence de la fonctionnalité de capture instantanée.

- Le Sauvegarde de disque Azure prend en charge la sauvegarde de disque Accélérateur d’écriture. Toutefois, lors de la restauration, le disque est restauré en tant que disque normal. Le cache Accélérateur d’écriture peut être activé sur le disque après son montage sur une machine virtuelle.

- Le service Sauvegarde Azure fournit une sauvegarde de niveau opérationnel (instantané) des disques managés Azure avec une prise en charge de plusieurs sauvegardes par jour. Les sauvegardes ne sont pas copiées dans le coffre Sauvegarde.

- Actuellement, l’option Récupération à l’emplacement d’origine (OLR) pour restaurer en remplaçant les disques sources existants à partir desquels les sauvegardes ont été effectuées n’est pas prise en charge. Vous pouvez effectuer une restauration à partir d’un point de récupération pour créer un disque dans le même groupe de ressources que celui du disque source à partir duquel les sauvegardes ont été effectuées ou dans tout autre groupe de ressources. C’est ce qu’on appelle une Récupération à un autre emplacement (ALR).

- Le service Sauvegarde Azure pour disques managés utilise des instantanés incrémentiels qui sont limités à 200 par disque. Pour vous permettre d'effectuer des sauvegardes à la demande en dehors des sauvegardes planifiées, la stratégie de sauvegarde limite le nombre total de sauvegardes à 180. Apprenez-en davantage sur les [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md#restrictions) pour les disques managés.

- Les [limites d’abonnement et de service](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) Azure s’appliquent au nombre total de captures instantanées de disque par région et par abonnement.

- Les instantanés ponctuels de plusieurs disques attachés à une machine virtuelle ne sont pas pris en charge.

- Le coffre Sauvegarde et les disques à sauvegarder peuvent se trouver dans le même abonnement ou dans des abonnements différents. Toutefois, le disque de sauvegarde et le disque à sauvegarder doivent se trouver dans la même région.

- Les disques à sauvegarder et le groupe de ressources d’instantanés dans lequel les captures instantanées sont stockées localement doivent se trouver dans le même abonnement.

- La restauration d’un disque à partir d’une sauvegarde vers le même abonnement ou un abonnement différent est prise en charge. Cependant, le disque restauré sera créé dans la même région que celle de l’instantané.

- Les disques chiffrés ADE ne peuvent pas être protégés.

- Les disques chiffrés avec des clés gérées par la plateforme ou par le client sont prises en charge. Toutefois, la restauration échoue pour les points de restauration d’un disque chiffré à l’aide de clés gérées par le client (CMK) si la clé KeyVault du jeu de chiffrement de disque est désactivée ou supprimée.

- Actuellement, il n’est pas possible de modifier la stratégie de sauvegarde, et le groupe de ressources d’instantanés attribué à une instance de sauvegarde lorsque vous configurez la sauvegarde d’un disque ne peut pas être modifié.

- Actuellement, l’expérience du portail Azure pour configurer la sauvegarde des disques est limitée à un maximum de 20 disques d’un même abonnement.

- Actuellement (pendant la préversion), l’utilisation de PowerShell et Azure CLI pour configurer la sauvegarde et la restauration des disques n’est pas prise en charge.

- Lors de la configuration de la sauvegarde, le disque à sauvegarder sélectionné et le groupe de ressources d’instantanés dans lequel les captures instantanées doivent être stockées doivent faire partie du même abonnement. Vous ne pouvez pas créer d'instantané incrémentiel pour un disque particulier en dehors de l'abonnement associé à ce disque. Apprenez-en davantage sur les [instantanés incrémentiels](../virtual-machines/windows/disks-incremental-snapshots-portal.md#restrictions) pour les disques managés. Pour plus d’informations sur le choix d’un groupe de ressources d’instantanés, consultez [Configurer une sauvegarde](backup-managed-disks.md#configure-backup).

- Pour le succès des opérations de sauvegarde et de restauration, l’identité managée du coffre Sauvegarde requiert des attributions de rôles. Utilisez uniquement les définitions de rôles fournies dans la documentation. L’utilisation d’autres rôles tels que propriétaire, contributeur, etc., n’est pas prise en charge. Vous pouvez rencontrer des problèmes d’autorisation si vous commencez à configurer des opérations de sauvegarde ou de restauration peu de temps après avoir attribué des rôles. Cela est dû au fait que les attributions de rôles nécessitent quelques minutes pour prendre effet.

- Les disques managés permettent de modifier le niveau de performances au moment du déploiement ou ultérieurement sans modifier la taille du disque. La solution Sauvegarde de disque Azure prend en charge l’apport de modifications de niveau de performances au disque source en cours de sauvegarde. Pendant la restauration, le niveau de performances du disque restauré est identique à celui du disque source au moment de la sauvegarde. Suivez la documentation [ici](../virtual-machines/disks-performance-tiers-portal.md) pour modifier le niveau de performances de votre disque après l’opération de restauration.

- La prise en charge des [liaisons privées](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) pour des disques managés vous permet de limiter l’exportation et l’importation de disques managés afin qu’elles ne se produisent que dans votre réseau virtuel Azure. Le Sauvegarde de disque Azure prend en charge la sauvegarde de disques sur lesquels des points de terminaison privés sont activés. Cela n’inclut pas que les données de sauvegarde ou les captures instantanées soient accessibles via le point de terminaison privé.

- Pendant la préversion, vous ne pouvez pas désactiver la sauvegarde. Par conséquent, l’option **Arrêter la sauvegarde et conserver les données de sauvegarde** n’est pas prise en charge. Vous pouvez supprimer une instance de sauvegarde, ce qui entraînera non seulement l’arrêt de la sauvegarde, mais également la suppression de toutes les données de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarder des disques managés Azure](backup-managed-disks.md)
