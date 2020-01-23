---
title: Générer des applications de stockage Azure hautement disponibles sur le stockage géoredondant interzone (GZRS) (préversion) | Microsoft Docs
description: Le stockage géoredondant interzone (GZRS) allie la haute disponibilité du stockage redondant interzone (ZRS) et une protection contre les pannes régionales, comme le prévoit le stockage géoredondant (GRS). Les données d’un compte de stockage GZRS sont répliquées entre les zones de disponibilité Azure dans la région principale et également répliquées vers une région géographique secondaire pour la protection contre les catastrophes régionales.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 2591f1846574994b878814f3b08df1de2a6e9fc1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973378"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Générer des applications de stockage Azure hautement disponibles sur le stockage géoredondant interzone (GZRS) (préversion)

Le stockage géoredondant interzone (GZRS) (préversion) allie la haute disponibilité du [stockage redondant interzone (ZRS)](storage-redundancy-zrs.md) et une protection contre les pannes régionales, comme le prévoit le [stockage géoredondant (GRS)](storage-redundancy-grs.md). Les données d’un compte de stockage GZRS sont répliquées entre trois [zones de disponibilité Azure](../../availability-zones/az-overview.md) dans la région principale et également répliquées vers une région géographique secondaire pour la protection contre les catastrophes régionales. Chaque région Azure est associée à une autre région au sein de la même région géographique, constituant ainsi des paires régionales. Pour plus d’informations et d’exceptions, consultez la [documentation](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Avec un compte de stockage GZRS, vous pouvez continuer à lire et écrire des données si une zone de disponibilité devient indisponible ou n’est pas récupérable. De plus, vos données sont également durables en cas de panne régionale totale ou d’incident empêchant la récupération de la région primaire. Le stockage GZRS est conçu pour fournir une durabilité des objets d’au moins 99,99999999999999 % (16 chiffres 9) sur une année donnée. GZRS offre également les mêmes objectifs de scalabilité que LRS, ZRS, GRS ou RA-GRS. Vous pouvez éventuellement activer l’accès en lecture aux données dans la région secondaire avec un stockage géoredondant interzone avec accès en lecture (RA-GZRS) si vos applications doivent être en mesure de lire les données en cas de sinistre dans la région principale.

Microsoft recommande l’utilisation de GZRS pour les applications ayant des besoins élevés en cohérence, durabilité, disponibilité, performances et résilience pour la reprise d’activité après sinistre. Pour renforcer la sécurité de l’accès en lecture à la région secondaire en cas de sinistre régional, activez RA-GZRS pour votre compte de stockage.

## <a name="about-the-preview"></a>À propos de la préversion

Seuls les comptes de stockage v2 à usage général prennent en charge GZRS et RA-GZRS. Pour plus d’informations sur les types de comptes de stockage, voir [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md). GZRS et RA-GZRS prennent en charge les objets blob de bloc, les objets blob de page (qui ne sont pas des disques VHD), les fichiers, les tables et les files d’attente.

GZRS et RA-GZRS sont actuellement disponibles en préversion dans les régions suivantes :

- Asie du Sud-Est
- Europe septentrionale
- Europe occidentale
- Sud du Royaume-Uni
- USA Est
- USA Est 2
- USA Centre

Microsoft continue d’activer le stockage GZRS et RA-GZRS dans d’autres régions Azure. Consultez la page [Mises à jour de service Azure](https://azure.microsoft.com/updates/) régulièrement pour plus d’informations sur les régions prises en charge.

Pour plus d’informations sur les prix de la préversion, consultez les prix de la préversion GZRS pour les [objets blob](https://azure.microsoft.com/pricing/details/storage/blobs), les [fichiers](https://azure.microsoft.com/pricing/details/storage/files/), les [files d’attente](https://azure.microsoft.com/pricing/details/storage/queues/) et les [tables](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft déconseille d’utiliser les fonctionnalités en préversion pour les charges de travail de production.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Fonctionnement de GZRS et RA-GZRS

Lorsque les données sont écrites dans un compte de stockage avec GZRS ou RA-GZRS activé, ces données sont d’abord répliquées de façon synchrone dans la région principale à travers trois zones de disponibilité. Les données sont ensuite répliquées de façon asynchrone dans une deuxième région qui est éloignée de plusieurs centaines de kilomètres. Lorsque les données sont écrites dans la région secondaire, elles sont répliquées de manière synchrone trois fois dans cette région à l’aide du [stockage localement redondant (LRS)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> Une réplication asynchrone implique un délai entre le moment où les données sont écrites dans la région primaire et celui où elles sont répliquées dans la région secondaire. En cas de sinistre régional, les modifications qui n’ont pas encore été répliquées vers la région secondaire risquent d’être perdues si ces données ne peuvent pas être récupérées à partir de la région primaire.

Lorsque vous créez un compte de stockage, vous spécifiez la façon dont les données de ce compte doivent être répliquées, ainsi que la région principale pour ce compte. La région secondaire couplée pour un compte de géoréplication est déterminée en fonction de la région primaire et n’est pas modifiable. Pour obtenir des informations actuelles sur les régions prises en charge par Azure, consultez [Continuité d’activité et reprise d’activité (BCDR) : régions couplées Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Pour plus d’informations sur la création d’un compte de stockage avec GZRS ou RA-GZRS, consultez la section [Créer un compte de stockage](storage-account-create.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Utiliser RA-GZRS pour la haute disponibilité

Lorsque vous activez RA-GZRS pour votre compte de stockage, vos données peuvent être lues à partir du point de terminaison secondaire, ainsi que depuis le point de terminaison principal de votre compte de stockage. Le point de terminaison secondaire ajoute le suffixe *– secondary* au nom du compte. Par exemple, si votre point de terminaison principal pour le service d’objets blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d’accès pour votre compte de stockage sont les mêmes pour le point de terminaison primaire et secondaire.

Pour tirer parti de RA-GZRS en cas de panne régionale, vous devez concevoir votre application à l’avance pour gérer ce scénario. Votre application doit lire et écrire sur le point de terminaison principal, mais passer à l’utilisation du point de terminaison secondaire dans le cas où la région prinicpale deviendrait indisponible. Pour obtenir des conseils de conception pour la haute disponibilité avec RA-GZRS, consultez [Concevoir des applications hautement disponibles avec RA-GZRS ou RA-GRS](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Étant donné que les données sont répliquées de façon asynchrone dans la région secondaire, la région secondaire est souvent en retard sur la région principale. Pour déterminer les opérations d’écriture qui ont été répliquées dans la région secondaire, votre application vérifie l’heure de la dernière synchronisation pour votre compte de stockage. Toutes les opérations d’écriture dans la région principale avant l’heure de la dernière synchronisation ont été répliquées avec succès dans la région secondaire, ce qui signifie qu’elles peuvent être lues à partir de la base de données secondaire. Toutes les opérations d’écriture dans la région principale après l’heure de la dernière synchronisation peuvent avoir été répliquées ou non dans la région secondaire, ce qui signifie qu’elles peuvent ne pas être disponibles pour les opérations de lecture.

Vous pouvez interroger la valeur de la propriété **Heure de la dernière synchronisation** à l’aide d’Azure PowerShell, Azure CLI ou une des bibliothèques clientes du stockage Azure. La propriété **Heure de la dernière synchronisation** correspond à une valeur de date/heure GMT.

Pour obtenir des conseils supplémentaires sur les performances et l’évolutivité avec RA-GZRS, consultez la [liste de contrôle des performances et de l’évolutivité du stockage Microsoft Azure](storage-performance-checklist.md).

### <a name="availability-zone-outages"></a>Pannes de zone de disponibilité

En cas de défaillance affectant une zone de disponibilité dans la région principale, vos applications peuvent continuer à lire et à écrire dans votre compte de stockage à l’aide des autres zones de disponibilité de cette région. Microsoft recommande de continuer à suivre les pratiques de gestion des erreurs temporaires lorsque vous utilisez GZRS ou ZRS. Ces pratiques incluent l’implémentation de stratégies de nouvelle tentative avec une interruption exponentielle.

Lorsqu’une zone de disponibilité devient indisponible, Azure procède à des mises à jour des réseaux, telles que le rejointoiement DNS. Ces mises à jour peuvent affecter votre application si vous accédez aux données avant qu’elles soient terminées.

### <a name="regional-outages"></a>Pannes régionales

Si une défaillance affecte l’ensemble de la région principale, Microsoft tente d’abord de restaurer la région principale. Si la restauration n’est pas possible, Microsoft effectue le basculement vers la région secondaire, afin que la région secondaire devienne la nouvelle région principale. Si RA-GZRS est activé pour le compte de stockage, les applications conçues pour ce scénario peuvent lire à partir de la région secondaire en attendant le basculement. Si RA-GZRS n’est pas activé pour le compte de stockage, les applications ne seront pas en mesure de lire sur la région secondaire tant que le basculement n’est pas terminé.

> [!NOTE]
> GZRS et RA-GZRS sont actuellement en préversion dans la région USA Est uniquement. Le basculement de compte géré par le client (préversion) n’est pas encore disponible dans USA Est 2, les clients ne peuvent donc pas gérer les événements de basculement de compte avec les comptes GZRS et RA-GZRS. Au cours de la préversion, Microsoft gérera les événements de basculement affectant les comptes GZRS et RA-GZRS.

Étant donné que les données sont répliquées de façon asynchrone dans la région secondaire, une défaillance qui affecte la région principale peut entraîner une perte de données si la région principale ne peut pas être récupérée. L’intervalle entre les écritures les plus récentes dans la région principale et la dernière écriture dans la région secondaire est appelé objectif de point de récupération (RPO). Le RPO indique le moment où les données peuvent être récupérées. Stockage Azure comporte généralement un RPO inférieur à 15 minutes, même s’il n’existe actuellement aucun contrat de niveau de service sur la durée de réplication des données sur la région secondaire.

L’objectif de délai de récupération (RTO) est une mesure de la durée nécessaire pour effectuer le basculement et replacer le compte de stockage en ligne. Cette mesure indique le temps nécessaire à Azure pour exécuter le basculement en modifiant les entrées DNS principales pour qu’elles pointent vers l’emplacement secondaire.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Migrer un compte de stockage vers GZRS ou RA-GZRS

Vous pouvez migrer n’importe quel compte de stockage existant vers GZRS ou RA-GZRS. La migration d’un compte ZRS existant vers GZRS ou RA-GZRS est simple, tandis que la migration d’un compte LRS, GRS ou RA-GRS est plus complexe. Les sections suivantes décrivent comment effectuer une migration dans les deux cas.

**Limitations connues**

- Actuellement, le niveau archive n’est pas pris en charge sur les comptes (RA-)GZRS. Pour plus d’informations, consultez [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
- Les disques managés ne prennent pas en charge (RA-)GZRS. Vous pouvez stocker des images et des instantanés de disques managés SSD Standard sur le stockage HDD Standard et [choisir entre les options LRS et ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="migrating-from-a-zrs-account"></a>Migration à partir d’un compte ZRS

Pour convertir un compte ZRS existant en RA-GZRS, utilisez l'applet de commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour modifier la référence SKU du compte. N’oubliez pas de remplacer les valeurs d’espace réservé par vos propres valeurs :

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Migration à partir d’un compte LRS, GRS ou RA-GRS

Il existe deux options pour la migration vers GZRS ou RA-GZRS à partir d’un compte LRS, GRS ou RA-GRS :

- Vous pouvez copier ou déplacer manuellement des données vers un nouveau compte de stockage GZRS ou RA-GZRS depuis un compte existant.
- Vous pouvez demander une migration dynamique.

#### <a name="perform-a-manual-migration"></a>Effectuer une migration manuelle

Si vous avez besoin que la migration se termine à une certaine date, envisagez d’effectuer une migration manuelle. Une migration manuelle offre plus de souplesse qu’une migration dynamique. Avec une migration manuelle, vous contrôlez le minutage.

Pour migrer manuellement les données d’un compte existant vers un compte GZRS ou RA-GZRS, utilisez un outil qui peut copier les données efficacement. Voici quelques exemples :

- Utilisez un utilitaire comme AzCopy ou un outil tiers fiable. Pour plus d’informations sur AzCopy, consultez [Prise en main d’AzCopy](storage-use-azcopy-v10.md).
- Si vous êtes familiarisé avec Hadoop ou HDInsight, vous pouvez attacher les comptes de stockage source et de destination à votre cluster. Ensuite, vous parallélisez le processus de copie de données avec un outil tel que DistCp.
- Vous pouvez créer vos propres outils en utilisant l’une des bibliothèques clientes de Stockage Azure.

#### <a name="perform-a-live-migration"></a>Effectuer une migration dynamique

Une migration manuelle peut entraîner un temps d’arrêt de l’application. Si votre application requiert une haute disponibilité, Microsoft offre également une option de migration dynamique. Une migration dynamique est une migration sur place sans temps d’arrêt.

Lors d’une migration dynamique, vous pouvez utiliser votre compte de stockage pendant le transfert de vos données entre les comptes de stockage source et de destination. Pendant le processus de migration dynamique, votre compte continue à respecter son contrat SLA en matière de durabilité et de disponibilité. Il n’y a aucun temps d’arrêt ni de perte de données avec la migration dynamique.

Seuls les comptes v2 à usage général prennent en charge GZRS/RA-GZRS. Par conséquent, avant d’envoyer une demande de migration dynamique vers GZRS/RA-GZRS, vous devez mettre à niveau votre compte vers le type usage général v2. Pour plus d’informations, compte [Vue d’ensemble des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) et [Mettre à niveau vers un compte de stockage v2 universel](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Une fois la migration terminée, le paramètre de réplication du compte de stockage est mis à jour sur **Stockage géoredondant (GZRS)** ou **Stockage géoredondant avec accès en lecture (RA-GZRS)** . Les points de terminaison de service, les clés d’accès, les signatures d’accès partagé (SAS) et autres options de configuration de compte restent inchangés.

Dans le cadre d’une migration dynamique, gardez à l’esprit les restrictions suivantes :

- Lorsque Microsoft gère votre demande de migration dynamique rapidement, il n’existe aucune garantie quant au moment où une migration dynamique s’achève. Si vos données doivent être migrées vers GZRS ou RA-GZRS avant une certaine date, Microsoft recommande d’effectuer plutôt une migration manuelle. En général, plus la quantité de données présentes dans votre compte est élevée, plus la migration des données est longue.
- Votre compte doit contenir des données.
- Vous ne pouvez migrer des données qu’au sein de la même région.
- Seuls les types de comptes de stockage standard prennent en charge la migration dynamique. Les comptes de stockage Premium doivent être migrés manuellement.
- La migration dynamique d’un compte GZRS ou RA-GZRS vers un compte LRS, GRS ou RA-GRS n’est pas prise en charge. Vous devez déplacer manuellement les données vers un compte de stockage (nouveau ou existant).
- Vous pouvez demander une migration dynamique entre RA-GRS et RA-GZRS. Toutefois, la migration de RA-GRS vers GZRS n’est pas prise en charge. Dans ce cas, vous devez demander une migration dynamique vers RA-GZRS, puis convertir manuellement le compte de stockage pour utiliser GZRS.
- Les disques managés prennent uniquement en charge LRS et ne peuvent pas être migrés vers GZRS ou RA-GZRS. Pour plus d’informations sur l’intégration avec des groupes à haute disponibilité, consultez [Introduction aux disques managés Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Vous pouvez stocker des images et des instantanés de disques managés SSD Standard sur le stockage HDD Standard et [choisir entre les options LRS, ZRS, GZRS et RA-GZRS](https://azure.microsoft.com/pricing/details/managed-disks/).
- Les comptes contenant des partages de fichiers volumineux ne sont pas pris en charge pour GZRS.

Pour demander une migration dynamique, utilisez le [portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). À partir du portail, sélectionnez le compte de stockage à migrer vers GZRS ou RA-GZRS, puis suivez ces instructions :

1. Sélectionnez **Nouvelle demande de support**.
2. Fournissez les informations **De base** de votre compte. Dans la section **Service**, sélectionnez **Gestion du compte de stockage** et spécifiez le compte à migrer.
3. Sélectionnez **Suivant**.
4. Dans la section **Problème**, spécifiez les valeurs suivantes :
    - **Gravité** : conservez la valeur par défaut.
    - **Type de problème** : sélectionnez **Migration des données**.
    - **Catégorie** : sélectionnez **Migrer vers (RA-)GZRS dans une région**.
    - **Titre** : tapez un titre descriptif tel que **Migration de compte (RA-)GZRS**.
    - **Détails** : tapez des détails supplémentaires dans la zone **Détails**, par exemple, « Je souhaite migrer vers GZRS à partir de [LRS, GRS] dans la région \_\_ ». ou « J’aimerais migrer vers un stockage RA-GZRS depuis un stockage [LRS, RA-GRS] dans la région \_\_. »
5. Sélectionnez **Suivant**.
6. Vérifiez que les informations de contact dans le panneau **Informations de contact** sont correctes.
7. Sélectionnez **Create** (Créer).

Un représentant du support technique vous contactera pour vous fournir de l’aide.

## <a name="see-also"></a>Voir aussi

- [Réplication Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Stockage localement redondant (LRS) : redondance des données à faible coût pour Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Stockage redondant interzone (ZRS) : applications Stockage Azure hautement disponibles](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
- [Objectifs de scalabilité et de performances pour les comptes de stockage standard](scalability-targets-standard-account.md)
