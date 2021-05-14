---
title: Guide de migration Stockage Azure
description: Ce guide présente les principales étapes d’une migration de stockage
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 3e9baedafb436bc92f734bf39519918686cec58d
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124470"
---
# <a name="azure-storage-migration-overview"></a>Présentation de la migration de données Stockage Azure

Cet article se concentre sur les migrations de stockage vers Azure et fournit de l’aide pour ces scénarios de migration de stockage :

- Migration des données non structurées, telles que des fichiers ou des objets
- Migration des appareils basés sur des blocs, tels que des disques ou des réseaux de zone de stockage (SAN)

## <a name="migration-of-unstructured-data"></a>Migration des données non structurées

La migration des données non structurées comprend les scénarios suivants :

- Migration de fichiers à partir d’un périphérique de stockage NAS vers l’une des offres de fichiers Azure :
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [solutions de fournisseur de logiciel indépendant](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md).
- Migration d’objets à partir de solutions de stockage d’objets vers la plateforme de stockage d’objets Azure :
  - [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Phases de migration

L’intégralité d’une migration se compose de plusieurs phases : détection, évaluation et migration.

![Diagramme illustrant les phases de détection, d’évaluation et de migration](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Phase de détection

Dans la phase de détection, vous déterminez toutes les sources qui doivent être migrées. C’est le cas des partages SMB, des exportations NFS ou des espaces de noms d’objets. Vous pouvez effectuer cette phase manuellement ou utiliser des outils automatisés.

#### <a name="assessment-phase"></a>Phase d’évaluation

La phase d’évaluation est essentielle pour comprendre les options disponibles pour la migration. Pour réduire le risque lors de la migration, et pour éviter les problèmes courants, suivez ces trois étapes :

| Étapes de la phase d’évaluation                     | Options                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Choisir un service de stockage cible**            | - Stockage Blob Azure et Data Lake Storage<br>- Azure Files<br>- Azure NetApp Files<br>- Solutions pour fournisseur de logiciel indépendant |
| **Choisir une méthode de migration**                  | - En ligne<br>- Hors connexion<br> - Combinaison des deux                                  |
| **Choisir le meilleur outil de migration pour cette tâche** | - Outils disponibles à l’achat (auprès de Microsoft Azure ou d’un fournisseur de logiciel indépendant)<br> - Open source                             


Il existe plusieurs outils disponibles à l’achat, auprès d’un fournisseur de logiciel indépendant, capable de vous aider lors de la phase d’évaluation. Consultez notre [tableau comparatif](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Choisir un service de stockage cible

Le choix d’un service de stockage cible dépend de l’application ou des utilisateurs qui accèdent aux données. Le bon choix dépend des aspects techniques et financiers. Tout d’abord, effectuez une évaluation technique pour évaluer les cibles potentielles et déterminer les services qui répondent aux exigences. Ensuite, effectuez une évaluation financière pour déterminer le meilleur choix.

Pour sélectionner le service de stockage cible pour la migration, évaluez les aspects suivants de chaque service :

- Prise en charge de protocole
- Caractéristiques du niveau de performance
- Limites du service de stockage cible

Le diagramme suivant représente un arbre de décision simplifié, conçu pour vous guider vers le service Azure recommandé pour le traitement de vos fichiers. Si les services Azure natifs ne conviennent pas à vos besoins, vous pouvez vous tourner vers les [solutions d’un fournisseur de logiciel indépendant](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md).

Après l’évaluation technique et la sélection de la cible adéquate, évaluez les coûts pour déterminer l’option la plus rentable.

![Arbre de décision simplifié permettant de choisir un service de traitement de fichiers approprié](./media/storage-migration-overview/files-decision-tree.png)

Pour simplifier l’arbre de décision, les limites du service de stockage cible ne sont pas incorporées dans le diagramme. Pour en savoir plus sur les limites actuelles et déterminer si vous devez modifier vos choix en fonction de ces limites, consultez :

- [Limites de compte de stockage](../../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)
- [Limites de stockage Blob](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-blob-storage-limits)
- [Objectifs de performance et d’extensibilité d'Azure Files](../files/storage-files-scale-targets.md)
- [Limites des ressources pour Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-resource-limits.md)

Si l’une des limites constitue un obstacle à l’utilisation d’un service, Azure est compatible avec les solutions des fournisseurs de stockage disponibles sur le site Place de marché Azure. Pour en savoir plus sur les fournisseurs de logiciels indépendants partenaires que nous avons approuvés pour la prestation de services de fichiers, consultez l’article [Partenaires de stockage principal et secondaire du Stockage Azure](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md).

##### <a name="select-the-migration-method"></a>Sélectionner la méthode de migration

Il existe deux méthodes de migration de base pour les migrations de stockage.

- **En ligne**. La méthode dite « en ligne » utilise le réseau pour la migration des données. Vous pouvez utiliser le réseau Internet public ou [Azure ExpressRoute](../../expressroute/expressroute-introduction.md). Si le service n’a pas de point de terminaison public, vous devez utiliser un VPN avec le réseau Internet public.
- **Hors connexion.** La méthode dite « hors connexion » implique l’utilisation d’un appareil [Azure Data Box](https://azure.microsoft.com/services/databox/).

La décision d’utiliser une méthode « en ligne » par rapport à une méthode « hors connexion » dépend de la bande passante réseau disponible. La méthode « en ligne » est préférable dans les cas où la bande passante réseau est suffisante pour effectuer une migration dans le délai imparti.

Vous pouvez aussi utiliser les deux méthodes conjointement : la méthode « hors connexion » pour la migration en bloc initiale, puis une méthode « en ligne » pour effectuer une migration incrémentielle des modifications. L’utilisation conjointe des deux méthodes nécessite un niveau élevé de coordination. C’est pour cette raison que nous ne la recommandons pas. Si vous optez néanmoins pour ce choix, veillez à isoler les jeux de données migrés à l’aide de la méthode « en ligne » des jeux de données migrés à l’aide de la méthode « hors connexion ».

Pour en savoir plus sur les différentes méthodes et instructions de migration, consultez les articles [Choisir une solution Azure pour le transfert de données](./storage-choose-data-transfer-solution.md) et [Migrer vers des partages de fichiers Azure](../files/storage-files-migration-overview.md).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Choisir le meilleur outil de migration pour cette tâche

Il existe différents outils pour effectuer la migration. Certains sont disponibles en licence open source, comme AzCopy, robocopy, xcopy et rsync, tandis que d’autres requièrent l’achat d’une licence. N’hésitez pas à consulter notre [tableau comparatif](../solution-integration/validated-partners/data-management/migration-tools-comparison.md) pour connaître la liste des outils disponibles à l’achat.

Les outils open source sont bien adaptés aux migrations à petite échelle. Pour la migration des serveurs de fichiers Windows vers Azure Files, Microsoft recommande de commencer avec la fonctionnalité native du service Azure Files, puis d’utiliser [Azure File Sync](/windows-server/manage/windows-admin-center/azure/azure-file-sync). Quant aux outils disponibles à l’achat, ils conviennent mieux pour effectuer des migrations plus complexes qui se composent de sources différentes, nécessitent une capacité importante ou requièrent des exigences particulières (telles que la limitation de bande passante ou la création de rapports détaillés avec des fonctionnalités d’audit). Ces outils facilitent la migration et réduisent considérablement le risque. La plupart des outils disponibles à l’achat peuvent également effectuer des opérations de détection, et fournissent ainsi une aide précieuse pour la phase d’évaluation.

#### <a name="migration-phase"></a>Phase de migration

Durant la phase de migration, les données sont déplacées et migrées. Il s’agit de la dernière phase de la procédure. En règle générale, vous pouvez simplifier votre basculement en exécutant plusieurs fois la phase de migration. La phase de migration se compose des étapes suivantes :

1. **Migration initiale.** L’étape de migration initiale migre toutes les données de la source vers la cible. Cette étape permet de migrer la majeure partie des données qui doivent être migrées.
2. **Resynchronisation.** Une opération de resynchronisation migre toutes les données qui ont été modifiées après l’étape de migration initiale. Si de nombreuses modifications ont été apportées, vous pouvez répéter cette étape plusieurs fois. L’objectif de l’exécution de plusieurs opérations de resynchronisation est de réduire le temps nécessaire à la dernière étape. Si vos données sont inactives ou n’ont pas été modifiées (c’est notamment le cas des données de sauvegarde ou d’archivage), vous pouvez ignorer cette étape.
3. **Basculement final**. L’étape de basculement final bascule l’utilisation active des données de la source vers la cible et met la source hors service.

La durée de la migration des données non structurées dépend de plusieurs aspects. En dehors de la méthode choisie, les facteurs les plus importants sont la taille totale des données et la répartition des fichiers en fonction de leur taille. Plus le jeu de données total est volumineux, plus la migration sera longue. Plus la taille de fichier moyenne est petite, plus la migration sera longue. Si vous avez un grand nombre de petits fichiers, nous vous conseillons, si possible, de les archiver dans des fichiers plus volumineux (comme un fichier .tar ou .zip), afin de réduire la durée totale de la migration.

## <a name="migration-of-block-based-devices"></a>Migration des appareils basés sur des blocs

La migration des appareils basés sur des blocs s’effectue généralement dans le cadre d’une migration d’ordinateur virtuel ou d’hôte physique. C’est une erreur courante de repousser toute prise de décision concernant le stockage de bloc jusqu’à avoir effectué la migration. En effet, prendre ces décisions à l’avance en prenant en considération les exigences relatives à chaque charge de travail facilite la migration vers le cloud.

Pour savoir quelles sont les charges de travail à migrer et connaître l’approche à adopter pour leur migration, consultez [cet article de la documentation](../../virtual-machines/disks-types.md), ainsi que les ressources disponibles sur la [page du produit Stockage sur disque Azure](https://azure.microsoft.com/services/storage/disks/#resources). Vous pourrez ainsi savoir quels disques peuvent répondre à vos besoins et vous tenir informé sur les dernières fonctionnalités disponibles, comme le [bursting de disque](../../virtual-machines/disk-bursting.md). Pour plus d’informations sur la migration des machines virtuelles avec les appareils sous-jacents basés sur des blocs, consultez la documentation [Azure Migrate](../../migrate/index.yml) .

## <a name="see-also"></a>Voir aussi

- [Choisir une solution Azure pour transférer des données](./storage-choose-data-transfer-solution.md)
- [Comparaison des outils de migration disponibles à l’achat](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migrer vers des partages de fichiers Azure](../files/storage-files-migration-overview.md)
- [Migrer vers Data Lake Storage avec WANdisco LiveData Platform for Azure](../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [Copier ou déplacer des données vers le service Stockage Azure avec AzCopy](./storage-use-azcopy-v10.md)
- [Migrer des jeux de données volumineux vers le service Stockage Blob Azure avec AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)