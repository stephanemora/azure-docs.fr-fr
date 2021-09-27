---
title: Meilleures pratiques pour la récupération d’urgence avec Azure File Sync
description: Apprenez-en davantage sur les meilleures pratiques de récupération d’urgence avec Azure File Sync, plus particulièrement, la haute disponibilité, la protection des données et la redondance des données.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f5928815b6559c257319eb625587135cbf99df0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645737"
---
# <a name="best-practices-for-disaster-recovery-with-azure-file-sync"></a>Meilleures pratiques pour la récupération d’urgence avec Azure File Sync

Pour Azure File Sync, il existe trois domaines principaux à prendre en compte pour la récupération d’urgence : haute disponibilité, protection des données/sauvegarde et redondance des données. Cet article couvre chaque domaine et vous aide à choisir la configuration à utiliser pour votre propre solution de récupération d’urgence.

Dans un déploiement Azure File Sync, le point de terminaison cloud contient toujours une copie complète de vos données, tandis que le serveur local peut être affiché en tant que cache supprimable de vos données. En cas de sinistre côté serveur, vous pouvez effectuer une récupération en provisionnant un nouveau serveur, en y installant l’agent Azure File Sync et en le configurant comme nouveau point de terminaison de serveur.

En raison de leur nature hybride, certaines stratégies de récupération d’urgence et de sauvegarde de serveur traditionnelles ne fonctionnent pas avec Azure File Sync. Pour les serveurs inscrits, Azure File Sync ne prend pas en charge les opérations suivantes :

> [!WARNING]
> L’exécution de ces opérations peut entraîner des problèmes de synchronisation ou de fichiers hiérarchisés aboutissant à une perte de données éventuelle. Si vous avez effectué l’une de ces opérations, contactez le support Azure pour vous assurer que votre déploiement est sain.

- Transfert de lecteurs de disque d’un serveur à un autre
- Restauration à partir d’une sauvegarde du système d’exploitation
- Clonage du système d’exploitation d’un serveur vers un autre serveur
- Rétablissement d’un point de contrôle de machine virtuelle précédent
- Restauration de fichiers à partir d’une sauvegarde locale si la hiérarchisation cloud est activée

## <a name="high-availability"></a>Haute disponibilité

Vous pouvez utiliser deux stratégies différentes afin d’obtenir une haute disponibilité pour votre serveur local. Vous pouvez configurer un cluster de basculement ou un serveur de secours. Deux facteurs sont déterminants pour choisir entre les deux configurations : votre degré d’investissement dans votre système et si le fait de réduire au minimum la durée pendant laquelle votre système est défaillant en cas de sinistre en vaut la peine financièrement.

Pour un cluster de basculement, il n’est pas nécessaire d’effectuer des étapes spéciales pour utiliser Azure File Sync. Pour un serveur de secours, vous devez effectuer les configurations suivantes :

Avoir un serveur secondaire avec des points de terminaison de serveur différents qui se synchronisent avec le même groupe de synchronisation que votre serveur principal, mais n’autorisent pas l’accès de l’utilisateur final au serveur. Cela permet à tous les fichiers d’être synchronisés du serveur principal vers le serveur de secours. Vous pouvez envisager d’activer la hiérarchisation de l’espace de noms uniquement afin que seul l’espace de noms soit téléchargé initialement. En cas de défaillance de votre serveur principal, vous pouvez utiliser DFS-N pour reconfigurer rapidement l’accès de l’utilisateur final à votre serveur de secours.

## <a name="data-protectionbackup"></a>Protection des données/sauvegarde

La protection de vos données réelles est un composant clé d’une solution de récupération d’urgence. Il existe deux méthodes principales pour effectuer cette opération avec vos partages de fichiers Azure : vous pouvez sauvegarder vos données dans le cloud ou localement. Nous vous recommandons vivement de sauvegarder vos données dans le cloud, car votre point de terminaison cloud contiendra une copie complète de vos données, tandis que les points de terminaison de serveur ne peuvent contenir qu’un sous-ensemble de vos données.

### <a name="back-up-your-data-in-the-cloud"></a>Sauvegarder vos données dans le cloud

Vous pouvez utiliser [Sauvegarde Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json) comme solution de sauvegarde cloud. Sauvegarde Azure gère la planification, la conservation et les restaurations de sauvegarde, entre autres. Si vous préférez, vous pouvez prendre manuellement des [instantanés de partage](../files/storage-snapshots-files.md?toc=/azure/storage/file-sync/toc.json) et configurer votre propre solution de planification et de conservation, mais ce n’est pas idéal. Vous pouvez également utiliser des solutions tierces pour sauvegarder directement vos partages de fichiers Azure.

En cas d’incident, vous pouvez restaurer à partir d’un instantané de partage, qui est une copie en lecture seule à un point dans le temps de votre partage de fichiers. Étant donné que ces instantanés sont en lecture seule, ils ne sont pas affectés par les ransomware. Pour les jeux de données volumineux, dans lesquels les opérations de restauration de partage complet prennent beaucoup de temps, vous pouvez activer l’accès direct de l’utilisateur à l’instantané afin que les utilisateurs puissent copier les données dont ils ont besoin sur leur lecteur local, pendant que la restauration se termine.

Les instantanés sont stockés directement dans votre partage de fichiers Azure, que vous les déposiez manuellement ou que Sauvegarde Azure le fasse pour vous. Vous devez donc [activer la suppression réversible](../files/storage-files-prevent-file-share-deletion.md?toc=/azure/storage/file-sync/toc.json) pour protéger vos instantanés contre les suppressions accidentelles de votre partage de fichiers.

Pour plus d’informations, consultez [À propos de la sauvegarde des partages de fichiers Azure](../../backup/azure-file-share-backup-overview.md) ou contactez votre fournisseur de sauvegarde pour déterminer s’il prend en charge la sauvegarde des partages de fichiers Azure.

### <a name="back-up-your-data-on-premises"></a>Sauvegarder vos données localement

Si vous activez la hiérarchisation cloud, n’implémentez pas une solution de sauvegarde locale. Lorsque la hiérarchisation cloud est activée, seul un sous-ensemble de vos données est stocké localement sur votre serveur et le reste de vos données est stocké dans votre point de terminaison cloud. Selon la solution de sauvegarde que vous utilisez pour une sauvegarde locale, les fichiers hiérarchisés sont : ignorés et non sauvegardés (en raison de leur attribut FILE_ATTRIBUTE_RECALL_ONDATA_ACCESS), sont sauvegardés uniquement sous forme de fichier à plusieurs niveaux et peuvent ne pas être accessibles lors de la restauration en raison des modifications apportées au partage en direct, ou ils sont rappelés sur votre disque, ce qui entraîne des frais de sortie élevés.

Si vous préférez utiliser une solution de sauvegarde locale, les sauvegardes doivent être effectuées sur un serveur dans le groupe de synchronisation pour lequel la hiérarchisation cloud est désactivée. Lorsque vous effectuez une restauration, utilisez les options de restauration au niveau du volume ou au niveau du fichier. Les fichiers restaurés en utilisant l’option de restauration au niveau du fichier sont synchronisés avec tous les points de terminaison dans le groupe de synchronisation et les fichiers existants sont remplacés par la version restaurée à partir de la sauvegarde. Les restaurations au niveau du volume ne remplacent pas les versions plus récentes des fichiers dans le point de terminaison cloud ou d’autres points de terminaison de serveur.

Dans la version 9 et les versions ultérieures de l’agent Azure File Sync, les [instantanés VSS](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service) (notamment l’onglet **Versions précédentes**) sont à présent pris en charge sur les volumes pour lesquels la hiérarchisation cloud est activée. Cela vous permet d’effectuer des restaurations libre-service au lieu de demander à un administrateur d’effectuer les restaurations à votre place. Toutefois, vous devez activer la compatibilité des versions précédentes par le biais de PowerShell, ce qui a pour effet d’augmenter les coûts de stockage des instantanés. Les instantanés VSS ne protègent pas contre les sinistres sur le point de terminaison de serveur lui-même et ne doivent donc être utilisés qu’avec les sauvegardes côté cloud. Pour plus d’informations, consultez [Restauration libre-service via Versions précédentes et VSS](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-redundancy"></a>Redondance des données

Pour garantir la robustesse de la solution de récupération d’urgence, ajoutez une forme de redondance des données à votre infrastructure. Il existe 4 offres de redondance pour Azure Files : le [stockage localement redondant (LRS)](../common/storage-redundancy.md#locally-redundant-storage), le [stockage redondant interzone (ZRS)](../common/storage-redundancy.md#zone-redundant-storage), le [stockage géoredondant (GRS)](../common/storage-redundancy.md#geo-redundant-storage) et le [stockage géoredondant interzone (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage).

- [Stockage localement redondant (LRS)](../common/storage-redundancy.md#locally-redundant-storage) : avec LRS, chaque fichier est stocké trois fois au sein d’un cluster de stockage Azure. Cela le protège de la perte de données due à des défaillances matérielles, telles qu’un lecteur de disque défectueux. Toutefois, si un sinistre tel qu’un incendie ou une inondation se produit à l’intérieur du centre de données, tous les réplicas d’un compte de stockage utilisant un stockage localement redondant risquent d’être perdus ou irrécupérables.
- [Stockage redondant dans une zone (ZRS)](../common/storage-redundancy.md#zone-redundant-storage) : avec ZRS, trois copies de chaque fichier stocké, mais ces copies sont physiquement isolées dans trois clusters de stockage distincts dans différentes *zones de disponibilité* Azure. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d'un ou de plusieurs centres de données équipés d'une alimentation, d'un système de refroidissement et d'un réseau indépendants. Une écriture sur le stockage n’est pas acceptée tant qu’elle n’est pas écrite dans les clusters de stockage dans les trois zones de disponibilité.
- [Stockage géoredondant (GRS)](../common/storage-redundancy.md#geo-redundant-storage) : avec GRS, vous avez deux régions, une région primaire et une région secondaire. Les fichiers sont stockés trois fois au sein d’un cluster de stockage Azure dans la région primaire. Les écritures sont répliquées de façon asynchrone dans une région secondaire définie par Microsoft. Le stockage GRS fournit six copies de vos données réparties entre deux régions Azure.
- [Stockage géoredondant interzone (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage) : vous pouvez considérer GZRS comme s’il s’agissait de ZRS, mais avec la géoredondance. Avec GZRS, les fichiers sont stockés trois fois sur trois clusters de stockage distincts dans la région primaire. Toutes les écritures sont ensuite répliquées de façon asynchrone dans une région secondaire définie par Microsoft.

Pour garantir la robustesse de la solution de récupération d’urgence, la plupart des clients doivent envisager d’utiliser le stockage redondant interzone (ZRS). Le stockage ZRS ajoute un coût supplémentaire minimal si l’on tient compte des avantages de la redondance des données ajoutée et il est également le plus fluide en cas de panne. Si la stratégie ou les exigences réglementaires de votre organisation nécessitent la géoredondance de vos données, envisagez d’utiliser le stockage GRS ou GZRS.

### <a name="geo-redundancy"></a>Géo-redondance

Si vous activez GRS ou GZRS sur le compte de stockage contenant votre point de terminaison cloud, vous devez également l’activer sur votre service de synchronisation de stockage. Cela garantit que toutes les informations relatives à votre topologie Azure File Sync et les données contenues dans votre point de terminaison cloud sont copiées de façon asynchrone dans la région secondaire jumelée en cas de sinistre.

Pour les ressources qui sont configurées avec le stockage GRS ou GZRS, Microsoft lance le basculement de votre service si la région primaire est jugée irrémédiablement irrécupérable ou indisponible pendant une longue période. Le service Azure File Sync bascule automatiquement vers la région jumelée en cas de sinistre dans une région lorsque le service de synchronisation de stockage utilise le stockage GRS ou GZRS. Si vous utilisez Azure File Sync et que le service est configuré avec le stockage GRS ou GZRS, aucune action n’est requise de votre part en cas de sinistre.

Bien que vous puissiez demander manuellement un basculement de votre service de synchronisation de stockage vers votre région jumelée GRS ou GZRS, nous vous déconseillons de le faire en dehors des pannes régionales à grande échelle, car le processus n’est pas fluide et peut entraîner des frais supplémentaires. Pour initier le processus, ouvrez un ticket de support et demandez à ce que vos comptes de stockage Azure qui contiennent votre partage de fichiers Azure et votre service de synchronisation de stockage fassent l’objet d’un basculement.

> [!WARNING]
> Vous devez contacter le support pour demander le basculement de votre service de synchronisation de stockage si vous lancez ce processus manuellement. Si vous tentez de créer un service de synchronisation de stockage à l’aide des mêmes points de terminaison de serveur dans la région secondaire, vous risquez de conserver des données supplémentaires dans votre compte de stockage, car l’installation précédente d’Azure File Sync ne sera pas nettoyée.

Une fois le basculement effectué, les points de terminaison de serveur basculent pour se synchroniser automatiquement avec le point de terminaison cloud dans la région secondaire. Toutefois, les points de terminaison de serveur doivent être rapprochés des points de terminaison cloud. Cela peut entraîner des conflits de fichiers, car les données de la région secondaire peuvent ne pas être détectées dans la région primaire.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la sauvegarde des partages de fichiers Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json)
