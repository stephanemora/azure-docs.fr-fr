---
title: Migration de la série StorSimple 8000 vers Azure File Sync
description: Découvrez comment effectuer une migration d’une appliance StorSimple 8100 ou 8600 vers Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6ad132513c2ec61dd5a290da1a88e50f0ad6eb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510349"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migration de StorSimple 8100 et 8600 vers Azure File Sync

La série StorSimple 8000 est représentée par les appliances physiques locales 8100 ou 8600 et leurs composants de service cloud. Il est possible de migrer les données de l’une ou l’autre de ces appliances vers un environnement Azure File Sync. Azure File Sync est le service Azure à long terme stratégique et par défaut vers lequel les appliances StorSimple peuvent être migrées.

StorSimple série 8000 atteindra sa [fin de vie](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) en décembre 2022. Il est important de commencer à planifier votre migration dès que possible. Cet article décrit les étapes à suivre pour effectuer correctement une migration vers Azure File Sync et fournit les connaissances générales nécessaires. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft s’engage à aider les clients lors de la migration. Envoyez un e-mail à l’adresse AzureFilesMigration@microsoft.com pour obtenir un plan de migration personnalisé ainsi qu’une assistance pendant la migration.

Azure File Sync est un service cloud Microsoft basé sur deux composants principaux :

* Synchronisation des fichiers et hiérarchisation cloud.
* Partages de fichiers comme stockage natif dans Azure, accessibles par le biais de différents protocoles comme SMB et File REST. Un partage de fichiers Azure est comparable à un partage de fichiers sur un serveur Windows Server, que vous pouvez monter comme lecteur réseau de façon native. Il prend en charge des aspects importants de fidélité des fichiers comme les attributs, les autorisations et les horodatages. Grâce aux partages de fichiers Azure, il n’est plus nécessaire qu’une application ou un service interprète les fichiers et dossiers stockés dans le cloud. Vous pouvez y accéder en mode natif via des protocoles et des clients familiers, tels que l’Explorateur de fichiers Windows. Cela fait des partages de fichiers Azure l’approche idéale et la plus flexible pour stocker des données de serveur de fichiers à usage général et certaines données d’application dans le cloud.

Cet article est consacré aux étapes de migration. Si vous souhaitez en savoir plus sur Azure File Sync avant d’effectuer la migration, consultez les articles suivants :

* [Azure File Sync - Vue d’ensemble](https://aka.ms/AFS "Vue d’ensemble")
* [Azure File Sync - Guide de déploiement](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Objectifs de la migration

L’objectif consiste à garantir l’intégrité des données de production et la disponibilité. Garantir la disponibilité implique de garder les temps d’arrêt à un niveau minimal de sorte qu’ils respectent les fenêtres de maintenance ou ne les dépassent que légèrement.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Chemin de migration de la série StorSimple 8000 vers Azure File Sync

L’exécution d’un agent Azure File Sync nécessite un serveur Windows Server local. Il doit s’agir au minimum d’un serveur 2012R2, la version idéale étant Windows Server 2019.

Il existe de nombreux chemins de migration possibles, et il serait trop long de tous les documenter dans un article et d’expliquer clairement les risques et inconvénients qu’ils présentent par rapport à celui que nous recommandons ici et qui, d’après nous, s’inscrit dans les meilleures pratiques.

![StorSimple 8000 series migration phases overview](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Vue d’ensemble des phases de migration de la série StorSimple 8000 plus loin dans cet article.")

L’image précédente illustre les phases correspondant aux sections de cet article.
Nous utilisons une migration côté cloud pour éviter les rappels inutiles de fichiers sur votre appliance StorSimple locale. Cette approche permet d’éviter tout impact sur le comportement de mise en cache local ou l’utilisation de la bande passante réseau, ce qui peut perturber vos charges de travail de production.
Une migration côté cloud fonctionne sur un instantané (un clone de volume) de vos données. Vos données de production sont donc isolées de ce processus, et ce jusqu’à la fin de la migration. Le fait de travailler à partir de ce qui est essentiellement une sauvegarde rend la migration sûre et facilement reproductible en cas de problème.

## <a name="considerations-around-existing-storsimple-backups"></a>Considérations relatives aux sauvegardes StorSimple existantes

StorSimple vous permet d’effectuer des sauvegardes sous la forme de clones de volume. Cet article utilise un nouveau clone de volume pour migrer vos fichiers dynamiques.
Si vous devez migrer des sauvegardes en plus de vos données actives, toutes les instructions de cet article continuent de s’appliquer. La seule différence est qu’au lieu de commencer avec un nouveau clone de volume, vous allez commencer par le plus ancien clone de volume de sauvegarde que vous devez migrer.

La séquence est la suivante :

* Déterminez l’ensemble minimal de clones de volume que vous devez migrer. Nous vous recommandons de garder cette liste la plus courte possible, car plus vous migrez des sauvegardes, plus l’ensemble du processus de migration sera long.
* Lorsque vous parcourez le processus de migration, commencez par le plus ancien clone de volume que vous souhaitez migrer et, à chaque migration suivante, utilisez le plus ancien clone suivant.
* Une fois la migration de chaque clone de volume terminée, vous devez prendre un instantané du partage de fichiers Azure. [Les instantanés de partage de fichiers Azure](storage-snapshots-files.md) vous permettent de conserver des sauvegardes jusqu’à une date et heure de la structure des fichiers et des dossiers pour vos partages de fichiers Azure. Vous aurez besoin de ces instantanés une fois la migration terminée, afin de vous assurer d’avoir conservé les versions de chacun de vos clones de volume au fur et à mesure de la migration.
* Veillez à prendre des instantanés de partage de fichiers Azure pour tous les partages de fichiers Azure, qui sont servis par le même volume StorSimple. Les clones de volume sont au niveau du volume, tandis que les instantanés de partage de fichiers Azure se trouvent au niveau du partage. Vous devez prendre un instantané du partage (sur chaque partage de fichiers Azure) une fois la migration d’un clone de volume terminée.
* Répétez le processus de migration pour chaque clone de volume et prenez des instantanés de partage après chaque migration jusqu’à ce que vous obteniez un instantané des données actives. Le processus de migration d’un clone de volume est décrit dans les phases ci-dessous. 

Si vous n’avez pas besoin de déplacer des sauvegardes et que vous pouvez démarrer une nouvelle chaîne de sauvegardes sur le partage de fichiers Azure après la migration des données actives uniquement, cela permet de réduire la complexité de la migration et le temps qu’elle prendra. Vous pouvez décider de déplacer ou non les sauvegardes et de leur nombre pour chaque volume (et non pour chaque partage) que vous avez dans StorSimple.

## <a name="phase-1-get-ready"></a>Phase 1 : Se préparer

:::row:::
    :::column:::
        ![Image illustrant une partie de l’image de vue d’ensemble précédente qui permet de cibler cette sous-section de l’article.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        La base de la migration est un clone de volume et une appliance cloud virtuelle, appelée StorSimple 8020.
Cette phase porte sur le déploiement de ces ressources dans Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Déployer une appliance virtuelle StorSimple 8020

Le déploiement d’une appliance cloud est un processus qui nécessite de prendre en considération la sécurité, la mise en réseau et quelques autres aspects.

> [!IMPORTANT]
> Le guide suivant contient des sections inutiles. Lisez et suivez l’article du début jusqu’à l’étape 3. Revenez ensuite à cet article. Pour l’instant, vous n’avez pas besoin d’effectuer l’étape 3 ni aucune autre partie ultérieure de ce guide.

[Déploiement d’une appliance virtuelle StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Déterminer un clone de volume à utiliser

Lorsque vous êtes prêt à commencer la migration, la première étape consiste à prendre un nouveau clone de volume, comme vous le feriez pour la sauvegarde, qui capture l’état actuel de votre stockage cloud StorSimple. Prenez un clone pour chacun des volumes StorSimple dont vous disposez.
Si vous avez besoin de déplacer des sauvegardes, le premier clone de volume que vous utilisez n’est pas un clone nouvellement créé, mais le plus ancien clone de volume (sauvegarde la plus ancienne) à migrer.
Pour obtenir des instructions détaillées, reportez-vous à la section [« Considérations relatives aux sauvegardes StorSimple existantes »](#considerations-around-existing-storsimple-backups).

> [!IMPORTANT]
> Le guide suivant contient des sections inutiles. Lisez et suivez uniquement les étapes décrites dans la section à laquelle le lien renvoie. Revenez ensuite à cet article. Vous n’avez pas besoin de suivre la section « Étapes suivantes ».

[Création du clone d’un volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Utiliser le clone de volume

La dernière étape de la phase 1 consiste à rendre disponible le clone de volume que vous avez choisi sur l’appliance virtuelle 8020 dans Azure.

> [!IMPORTANT]
> Le guide suivant contient les étapes nécessaires, mais également, à la fin, une instruction pour formater le volume. **NE FORMATEZ PAS LE VOLUME.** Suivez le lien ci-dessous renvoyant à la section 7 et lisez-la du début jusqu’à l’instruction : « 10. Pour formater un volume simple,… »  Arrêtez avant d’exécuter cette étape et revenez à cet article.

[Montage d’un clone de volume sur l’appliance virtuelle 8020 dans Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Récapitulatif de la phase 1

Maintenant que vous avez terminé la phase 1, vous aurez effectué les opérations suivantes :

* Déployé une appliance virtuelle StorSimple 8020 dans Azure.
* Déterminé le clone de volume avec lequel vous allez commencer la migration.
* Monté vos clones de volume (un pour chaque volume réel) sur l’appliance virtuelle StorSimple dans Azure, avec leurs données disponibles pour une utilisation ultérieure.

## <a name="phase-2-cloud-vm"></a>Phase 2 : Machine virtuelle cloud

:::row:::
    :::column:::
        ![Image illustrant une partie de l’image de vue d’ensemble précédente qui permet de cibler cette sous-section de l’article.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Une fois votre clone initial disponible sur l’appliance virtuelle StorSimple 8020 dans Azure, il est maintenant temps d’approvisionner une machine virtuelle et d’exposer le clone (ou plusieurs clones) de volume à cette machine virtuelle sur iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Déployer une machine virtuelle Azure

La machine virtuelle Windows Server dans Azure est similaire à l’appliance StorSimple 8020, un élément temporaire de l’infrastructure qui n’est nécessaire que pendant la migration.
La configuration de la machine virtuelle que vous déployez dépend principalement du nombre d’éléments (fichiers et dossiers) que vous allez synchroniser. Nous vous recommandons d’utiliser une configuration haute performance si vous rencontrez des problèmes.

Un seul serveur Windows Server peut synchroniser jusqu’à 30 partages de fichiers Azure.
Les spécifications que vous décidez doivent englober chaque partage/chemin d’accès ou la racine du volume StorSimple et compter les éléments (fichiers et dossiers).

La taille globale des données est moins un goulot d’étranglement que le nombre d’éléments dont vous avez besoin pour adapter les spécifications de la machine.

* [Découvrez comment dimensionner un serveur Windows Server en fonction du nombre d’éléments (fichiers et dossiers) que vous devez synchroniser.](storage-sync-files-planning.md#recommended-system-resources)

    **Remarque :** L’article du lien précédent présente un tableau avec une plage pour la mémoire du serveur (RAM). Penchez vers le chiffre le plus grand pour la machine virtuelle Azure. Vous pouvez pencher vers le chiffre le plus petit pour votre machine locale.

* [Découvrez comment déployer une machine virtuelle Windows Server.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Assurez-vous que la machine virtuelle est déployée dans la même région Azure que l’appliance virtuelle StorSimple 8020. Si, dans le cadre de cette migration, vous devez également modifier la région dans laquelle vos données cloud sont stockées aujourd’hui, vous pouvez effectuer cette opération ultérieurement, lorsque vous approvisionnez des partages de fichiers Azure.

> [!IMPORTANT]
> Souvent, une instance Windows Server locale est utilisée pour faire face à votre appareil StorSimple local. Dans ce type de configuration, il est possible d’activer la fonctionnalité « [Déduplication des données](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable) » sur cette instance Windows Server. **Si vous avez utilisé la déduplication des données avec vos données StorSimple, veillez également à activer la déduplication des données sur cette machine virtuelle Azure.** Ne confondez pas cette déduplication au niveau des fichiers avec la déduplication au niveau des blocs intégrée à StorSimple, pour laquelle aucune action n’est nécessaire.

> [!IMPORTANT]
> Pour optimiser les performances, déployez un **disque de système d’exploitation rapide** pour votre machine virtuelle cloud. Vous stockerez la base de données de synchronisation sur le disque du système d’exploitation pour tous vos volumes de données. Veillez également à créer un **grand disque de système d’exploitation**. En fonction du nombre d’éléments (fichiers et dossiers) sur vos volumes StorSimple, le disque du système d’exploitation peut nécessiter **plusieurs centaines de Gio** d’espace pour prendre en charge la base de données de synchronisation.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Exposer des volumes StorSimple 8020 à la machine virtuelle Azure

Au cours de cette phase, vous connectez un ou plusieurs volumes StorSimple de l’appliance virtuelle 8020 par iSCSI à la machine virtuelle Windows Server que vous avez approvisionnée.

> [!IMPORTANT]
> Pour les articles suivants, effectuez uniquement les sections **Obtenir une adresse IP privée pour l’appliance cloud** et **Se connecter via iSCSI** et revenez à cet article.

1. [Obtenir une adresse IP privée pour l’appliance cloud](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Se connecter via iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Récapitulatif de la phase 2

Maintenant que vous avez terminé la phase 2, vous avez : 

* Approvisionné une machine virtuelle Windows Server dans la même région que l’appliance StorSimple virtuelle 8020.
* Exposé tous les volumes applicables du 8020 à la machine virtuelle Windows Server via iSCSI.
* Vous devez à présent voir le contenu des fichiers et des dossiers lorsque vous utilisez l’Explorateur de fichiers sur la machine virtuelle Windows Server des volumes montés.

Passez à la phase 3 uniquement lorsque vous avez terminé ces étapes pour tous les volumes qui nécessitent une migration.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Phase 3 : Configurer des partages de fichiers Azure et se préparer pour Azure File Sync

:::row:::
    :::column:::
        ![Image illustrant une partie de l’image de vue d’ensemble précédente qui permet de cibler cette sous-section de l’article.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        Au cours de cette phase, vous allez déterminer et configurer un certain nombre de partages de fichiers Azure en créant un serveur Windows Server local en tant que solution de remplacement d’appliance StorSimple et en configurant ce serveur pour Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapper vos espaces de noms existants à des partages de fichiers Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Déployer des partages de fichiers Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Si vous devez modifier la région Azure dans laquelle vos données StorSimple résident actuellement, approvisionnez les partages de fichiers Azure dans la nouvelle région que vous souhaitez utiliser. Pour déterminer la région, sélectionnez-la lorsque vous approvisionnez les comptes de stockage qui contiennent vos partages de fichiers Azure. Assurez-vous également que la ressource Azure File Sync que vous allez configurer ci-dessous se trouve dans cette même nouvelle région.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Déployer la ressource cloud Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Si vous devez modifier la région Azure dans laquelle vos données StorSimple résident actuellement, vous avez approvisionné les comptes de stockage de vos partages de fichiers Azure dans la nouvelle région. Assurez-vous d’avoir sélectionné la même région lorsque vous déployez ce service de synchronisation de stockage.

### <a name="deploy-an-on-premises-windows-server"></a>Déployer un serveur Windows Server local

* Créez un serveur Windows Server 2019 (ou au minimum 2012R2) comme machine virtuelle ou serveur physique. Un cluster de basculement Windows Server est également pris en charge. Ne réutilisez pas le serveur que vous pourriez avoir devant le StorSimple 8100 ou 8600.
* Approvisionnez ou ajoutez un stockage DAS (Direct Attached Storage). Notez que les périphériques de stockage NAS ne sont pas pris en charge.

La meilleure pratique consiste à attribuer à votre nouveau serveur Windows Server une quantité de stockage égale ou supérieure à celle dont votre appliance StorSimple 8100 ou 8600 dispose localement pour la mise en cache. Vous utiliserez le serveur Windows Server de la même façon que vous avez utilisé l’appliance StorSimple : s’il possède la même quantité de stockage que l’appliance, alors l’expérience de mise en cache doit être similaire, si ce n’est identique.
Vous pouvez ajouter ou supprimer de l’espace de stockage sur votre serveur Windows Server à volonté. Cela vous permet de mettre à l’échelle la taille de votre volume local et la quantité de stockage local disponible pour la mise en cache.

### <a name="prepare-the-windows-server-for-file-sync"></a>Préparer le serveur Windows Server pour la synchronisation de fichiers

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configurer Azure File Sync sur Windows Server

Votre serveur Windows Server local inscrit doit être prêt et connecté à Internet pour ce processus.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Veillez à activer la hiérarchisation cloud.** La hiérarchisation cloud désigne la fonctionnalité AFS qui permet au serveur local d’avoir moins de capacité de stockage que ce qui est stocké dans le cloud, tout en ayant l’espace de noms complet disponible. Les données intéressantes localement sont également mises en cache localement pour des performances d’accès local rapides. Une autre raison d’activer la hiérarchisation cloud à cette étape est que nous ne souhaitons pas synchroniser le contenu des fichiers à ce stade, seul l’espace de noms doit être déplacé pour l’instant.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Phase 4 : Configurer la machine virtuelle Azure pour la synchronisation

:::row:::
    :::column:::
        ![Image illustrant une partie de l’image de vue d’ensemble précédente qui permet de cibler cette sous-section de l’article.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Cette phase concerne votre machine virtuelle Azure avec le premier clone de volume monté via iSCSI. Au cours de cette phase, vous connecterez la machine virtuelle via Azure File Sync et démarrerez un premier cycle de transfert de fichiers à partir de vos clones de volume StorSimple.
        
    :::column-end:::
:::row-end:::

Vous avez déjà configuré le serveur local qui remplacera votre appliance StorSimple 8100 ou 8600 pour Azure File Sync. 

La configuration de la machine virtuelle Azure est un processus presque identique, avec une étape supplémentaire. Les étapes suivantes vous guideront tout au long du processus.

> [!IMPORTANT]
> Il est important que la machine virtuelle Azure **ne soit pas configurée avec la hiérarchisation cloud activée !** Vous échangerez le volume de ce serveur avec des clones de volume plus récents tout au long de la migration. La hiérarchisation cloud n’offre aucun avantage et les frais d’utilisation de l’UC sont à éviter.

1. [Déployer l’agent AFS (voir la section précédente)](#prepare-the-windows-server-for-file-sync)
2. [Préparer la machine virtuelle pour Azure File Sync](#get-the-vm-ready-for-azure-file-sync)
3. [Configurer la synchronisation](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Préparer la machine virtuelle pour Azure File Sync

Azure File Sync est utilisé pour déplacer les fichiers des volumes StorSimple iSCSI montés vers les partages de fichiers Azure cibles.
Au cours de ce processus de migration, vous monterez plusieurs clones de volume sur votre machine virtuelle, sous la même lettre de lecteur. Azure File Sync doit être configuré pour voir le prochain clone de volume que vous avez monté en tant que version plus récente des fichiers et dossiers et pour mettre à jour les partages de fichiers Azure connectés via Azure File Sync. 

> [!IMPORTANT]
> Pour que cela fonctionne, une clé de Registre doit être définie sur le serveur avant de configurer Azure File Sync.

1. Créez un nouveau répertoire sur le lecteur système de la machine virtuelle. Les informations Azure File Sync devront être conservées à cet emplacement plutôt que sur les clones de volume montés. Par exemple : `"C:\syncmetadata"`
2. Ouvrez Regedit et recherchez la ruche de registre suivante : `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Créez une nouvelle clé de type chaîne, nommée : ***MetadataRootPath***
4. Définissez le chemin d’accès complet au répertoire que vous avez créé sur le volume système, par exemple : `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Configurer Azure File Sync sur la machine virtuelle Azure

Cette étape est similaire à la section précédente, qui explique comment configurer AFS sur le serveur local.

La différence est que vous ne devez pas activer la hiérarchisation cloud sur ce serveur et que vous devez vous assurer que les bons dossiers sont connectés aux partages de fichiers Azure appropriés. Dans le cas contraire, le nom des partages de fichiers Azure et du contenu des données ne correspondra pas et il n’existe aucun moyen de renommer les ressources cloud ou les dossiers locaux sans reconfigurer la synchronisation.

Reportez-vous à la [section précédente sur la configuration d’Azure File Sync sur un serveur Windows Server](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Récapitulatif de l’étape 4

À ce stade, vous aurez correctement configuré Azure File Sync sur la machine virtuelle Azure sur laquelle vous avez monté vos clones de volume StorSimple via iSCSI.
Les données de la machine virtuelle Azure sont désormais transmises vers les différents partages de fichiers Azure. De là, un espace de noms entièrement lié apparaît sur votre serveur Windows Server local.

> [!IMPORTANT]
> Assurez-vous qu’aucune modification n’est apportée ou qu’aucun accès au serveur Windows Server n’a été accordé aux utilisateurs pour le moment.

Le déplacement des données des clones de volume initiaux de la machine virtuelle Azure vers les partages de fichiers Azure peut prendre beaucoup de temps, voire des semaines. L’estimation du temps nécessaire à cette opération est délicate et dépend de nombreux facteurs. Plus particulièrement de la vitesse à laquelle la machine virtuelle Azure peut accéder aux fichiers sur les volumes StorSimple et de la rapidité avec laquelle Azure File Sync peut traiter les fichiers et dossiers nécessitant une synchronisation. 

Par expérience, nous pouvons supposer que la bande passante, et par conséquent la taille réelle des données, joue un rôle subordonné. La durée de cette opération de migration ou de toute opération ultérieure dépend principalement du nombre d’éléments qui peuvent être traités par seconde. Par exemple, 1 Tio avec un 100 000 fichiers et dossiers se terminera probablement moins vite que 1 Tio avec seulement 50 000 fichiers et dossiers.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Phase 5 : Itérer au sein de plusieurs clones de volume

:::row:::
    :::column:::
        ![Image illustrant une partie de l’image de vue d’ensemble précédente qui permet de cibler cette sous-section de l’article.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Comme nous l’avons vu dans la phase précédente, la synchronisation initiale peut prendre beaucoup de temps. Vos utilisateurs et applications continuent d’accéder à l’appliance StorSimple 8100 ou 8600 locale. Cela signifie que les modifications s’accumulent et que, chaque jour, un écart plus important se forme entre les données actives et le clone de volume initial que vous êtes en train de migrer. Dans cette section, vous allez apprendre à réduire le temps d’arrêt en utilisant plusieurs clones de volume et en indiquant à quel moment la synchronisation est terminée.
    :::column-end:::
:::row-end:::

Malheureusement, le processus de migration n’est pas instantané. Cela signifie que l’écart susmentionné avec les données actives est une conséquence inévitable. La bonne nouvelle est que vous pouvez répéter le processus de montage de nouveaux clones de volume. L’écart de chaque clone de volume sera progressivement plus petit. Ainsi, une synchronisation se terminera dans un délai que vous jugerez acceptable pour mettre les utilisateurs et les applications hors ligne afin de les transférer vers votre serveur Windows Server local.

Répétez les étapes suivantes jusqu’à ce que la synchronisation se termine assez rapidement pour que vous acceptiez de mettre les utilisateurs et les applications hors connexion :

1. [Déterminez si la synchronisation est terminée pour un clone de volume donné.](#determine-when-sync-is-done)
2. [Créez un nouveau clone de volume et montez-le sur l’appliance virtuelle 8020.](#the-next-volume-clones)
3. [Déterminez à quel moment la synchronisation est terminée.](#determine-when-sync-is-done)
4. [Stratégie de transfert.](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>Les clones de volume suivants

Nous avons traité de la création d’un ou plusieurs clones de volume plus haut dans cet article.
Cette phase comporte deux actions :

1. [Créer un clone de volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Monter ce clone de volume (voir ci-dessus)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Déterminer à quel moment la synchronisation est terminée

Une fois la synchronisation terminée, vous pouvez arrêter de mesurer le temps et déterminer si vous devez répéter le processus de création d’un clone de volume et son montage, ou si le temps pris par la synchronisation du dernier clone de volume est suffisamment court.

Pour déterminer si la synchronisation est terminée, procédez comme suit :

1. Ouvrez l’observateur d’événements et accédez à **Applications et services**.
2. Parcourez les fichiers et ouvrez **Microsoft\FileSync\Agent\Telemetry**.
3. Recherchez l’**événement 9102** le plus récent, qui correspond à une session de synchronisation terminée
4. Sélectionnez **Détails** et confirmez que la valeur **SyncDirection** est **Charger**.
5. Vérifiez le **HResult** et confirmez qu’il affiche **0**. Cela signifie que la session de synchronisation a réussi. Si HResult est une valeur différente de zéro, une erreur s’est produite lors de la synchronisation. Si le **PerItemErrorCount** est supérieur à 0, cela signifie que certains fichiers ou dossiers non pas été synchronisés correctement. Il est possible d’avoir une valeur HResult égale à 0, mais un PerItemErrorCount supérieur à 0. À ce stade, vous n’avez pas à vous soucier du PerItemErrorCount. Nous intercepterons ces fichiers plus tard. Si le nombre d’erreurs est important (des milliers d’éléments) contactez le service clientèle et demandez à être mis en relation avec le groupe de produits Azure File Sync pour obtenir des conseils directs sur la meilleure démarche à suivre.
6. Vérifiez si plusieurs événements 9102 avec HResult 0 apparaissent dans une ligne. Cela indique que la synchronisation est terminée pour ce clone de volume.

### <a name="cut-over-strategy"></a>Stratégie de transfert

1. Déterminez si la synchronisation à partir d’un clone de volume est suffisamment rapide. (L’écart est suffisamment petit.)
2. Mettez l’appliance StorSimple hors connexion.
3. Effectuez une instruction RoboCopy finale.

Mesurez le temps et déterminez si la synchronisation à partir d’un clone de volume récent peut se terminer dans une fenêtre de temps suffisamment petite pour vous permettre d’interrompre votre système.

Il est maintenant temps de désactiver l’accès des utilisateurs à l’appliance StorSimple. Aucune autre modification. Le temps d’arrêt a commencé.
Vous devez conserver l’appliance en ligne et connectée, mais vous devez maintenant empêcher qu’elle soit modifiée.

Dans la phase 6, vous comblerez tout écart avec les données actives depuis le dernier clone de volume.

## <a name="phase-6-a-final-robocopy"></a>Phase 6 : Une RoboCopy finale

À ce stade, il existe deux différences entre votre serveur Windows Server local et l’appliance StorSimple 8100 ou 8600 :

1. Des fichiers peuvent ne pas avoir été synchronisés (voir **PerItemErrors** dans le journal des événements ci-dessus).
2. L’appliance StorSimple dispose d’un cache rempli, alors que le serveur Windows Server n’a qu’un espace de noms sans contenu de fichier stocké localement pour l’instant.

![Image illustrant une partie de l’image de vue d’ensemble précédente qui permet de cibler cette sous-section de l’article.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Nous pouvons amener le cache de Windows Server à l’état de l’appliance et vérifier qu’aucun fichier n’est ignoré en utilisant une RoboCopy finale.

> [!CAUTION]
> Il est impératif que la commande RoboCopy que vous utilisez soit exactement comme décrit ci-dessous. Nous voulons uniquement copier les fichiers locaux et les fichiers qui n’ont pas été déplacés par l’approche de clonage de volume et de synchronisation auparavant. Nous pouvons résoudre les problèmes qui expliquent pourquoi ils ne se sont pas synchronisés plus tard, une fois la migration terminée. (Voir [Résolution des problèmes Azure File Sync](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Il s’agit probablement de caractères non imprimables dans des noms de fichiers qui ne vous manqueront pas lorsqu’ils seront supprimés.)

Commande RoboCopy :

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Arrière-plan :

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Permet à RoboCopy de s’exécuter en multithread. La valeur par défaut est 8, le maximum est 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Renvoie l’état au fichier LOG au format UNICODE (remplace le journal existant).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Génère les sorties dans la fenêtre de la console. Utilisé conjointement avec la sortie dans un fichier journal.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Exécute RoboCopy dans le même mode qu’une application de sauvegarde. Permet à RoboCopy de déplacer des fichiers pour lesquels l’utilisateur actuel n’a pas d’autorisations.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permet à RoboCopy de prendre uniquement en compte les écarts entre la source (appliance StorSimple) et la cible (répertoire Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidélité de la copie de fichier (la valeur par défaut est /COPY:DAT), indicateurs de copie : D=Données, A=Attributs, T=Horodatages, S=Sécurité=ACL NTFS, O=Informations propriétaire, U=Informations audit
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Copie de toutes les informations de fichier (équivalent à /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidélité de la copie des répertoires (la valeur par défaut est /DCOPY:DA), indicateurs de copie : D=Données, A=Attributs, T=Horodatages
   :::column-end:::
:::row-end:::

Vous devez exécuter cette commande RoboCopy pour chacun des répertoires du Windows Server en tant que cible, que vous avez configuré avec la synchronisation de fichiers vers un fichier Azure.

Vous pouvez exécuter plusieurs de ces commandes en parallèle.
Une fois cette étape RoboCopy terminée, vous pouvez autoriser vos utilisateurs et vos applications à accéder au serveur Windows Server comme ils le faisaient auparavant avec l’appliance StorSimple.

Consultez le ou les fichiers journaux robocopy pour voir si des fichiers ont été ignorés. Si des problèmes surviennent, vous pouvez, dans la plupart des cas, les résoudre une fois que la migration est terminée et que vos utilisateurs et applications ont été redéployés sur votre serveur Windows Server. Si vous devez résoudre des problèmes, faites-le avant la phase 7.

Il est probable que vous deviez créer les partages SMB sur le serveur Windows Server que vous aviez sur les données StorSimple. Vous pouvez charger cette étape en amont et le faire plus tôt pour ne pas perdre de temps ici, mais vous devez vous assurer qu’avant ce stade, aucune modification des fichiers ne se produira sur le serveur Windows.

Si vous avez un déploiement DFS-N, vous pouvez faire pointer les espaces de noms DFN vers les emplacements des nouveaux dossiers du serveur. Si vous ne disposez pas d’un déploiement DFS-N et que vous avez exposé votre appliance 8100 ou 8600 localement à un serveur Windows Server, vous pouvez retirer ce serveur du domaine et joindre votre nouveau serveur Windows Server avec AFS au domaine. Donnez-lui le même nom de serveur que l’ancien et les mêmes noms de partage, le transfert vers le nouveau serveur restera ainsi transparent pour vos utilisateurs, la stratégie de groupe ou les scripts.

## <a name="phase-7-deprovision"></a>Phase 7 : annulation du déploiement

Au cours de la dernière phase, vous avez itéré sur plusieurs clones de volume et avez finalement pu transférer l’accès utilisateur au nouveau serveur Windows Server après avoir mis votre appliance StorSimple hors connexion.

Vous pouvez maintenant commencer à déprovisionner les ressources inutiles.
Avant de commencer, il est recommandé d’observer quelque temps votre nouveau déploiement Azure File Sync en production. Cela vous donne la possibilité de corriger tout problème que vous pourriez rencontrer.

Une fois que vous êtes satisfait et que vous avez observé votre déploiement AFS pendant au moins quelques jours, vous pouvez commencer à déprovisionner les ressources dans cet ordre :

1. Désactivez la machine virtuelle Azure que nous avons utilisée pour déplacer des données des clones de volume vers les partages de fichiers Azure via la synchronisation de fichiers.
2. Accédez à votre ressource de service de synchronisation de stockage dans Azure et désinscrivez la machine virtuelle Azure. Cela la supprime de tous les groupes de synchronisation.

    > [!WARNING]
    > **SOYEZ SÛR de sélectionner la machine appropriée.** Vous avez désactivé la machine virtuelle cloud, ce qui signifie qu’elle doit s’afficher comme étant le seul serveur hors connexion dans la liste des serveurs inscrits. Vous ne devez pas sélectionner le serveur Windows Server local à cette étape, car cela aurait pour effet de le désinscrire.

3. Supprimez la machine virtuelle Azure et ses ressources.
4. Désactivez l’appliance virtuelle StorSimple 8020.
5. Désapprovisionnez toutes les ressources StorSimple dans Azure.
6. Débranchez l’appliance physique StorSimple de votre centre de données.

Votre migration est terminée.

## <a name="next-steps"></a>Étapes suivantes

Familiarisez-vous avec Azure File Sync, en particulier avec la flexibilité des stratégies de hiérarchisation cloud.

Si vous constatez dans le Portail Azure, ou à partir des événements précédents, que certains fichiers ne se synchronisent jamais, consultez le guide de résolution des problèmes pour connaître les mesures à prendre pour les résoudre.

* [Vue d’ensemble d’Azure File Sync : aka.ms/AFS](https://aka.ms/AFS)
* [Hiérarchisation cloud](storage-sync-cloud-tiering.md) 
* [Guide de résolution des problèmes Azure File Sync](storage-sync-files-troubleshoot.md)
