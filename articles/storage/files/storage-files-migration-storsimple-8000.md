---
title: Migration de la série StorSimple 8000 vers Azure File Sync
description: Découvrez comment effectuer une migration d’une appliance StorSimple 8100 ou 8600 vers Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331086"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migration de StorSimple 8100 et 8600 vers Azure File Sync

La série StorSimple 8000 est représentée par les appliances physiques locales 8100 ou 8600 et leurs composants de service cloud. Il est possible de migrer les données de l’une ou l’autre de ces appliances vers un environnement Azure File Sync. Azure File Sync est le service Azure à long terme stratégique et par défaut vers lequel les appliances StorSimple peuvent être migrées.

StorSimple série 8000 atteindra sa [fin de vie](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) en décembre 2022. Il est important de commencer à planifier votre migration dès que possible. Cet article décrit les étapes à suivre pour effectuer correctement une migration vers Azure File Sync et fournit les connaissances générales nécessaires. 

## <a name="phase-1-prepare-for-migration"></a>Phase 1 : Préparation de la migration

Cette section contient les étapes à suivre au début de la migration des volumes StorSimple vers des partages de fichiers Azure.

### <a name="inventory"></a>Inventaire
Lorsque vous commencez à planifier votre migration, identifiez tout d’abord toutes les appliances et tous les volumes StorSimple à migrer. Une fois que vous avez terminé, vous pouvez choisir le chemin de migration qui vous convient le mieux.

* Pour les appliances physiques StorSimple (série 8000) : utilisez ce guide de migration. 
* Pour les appliances virtuelles, [série StorSimple 1200 : utilisez un autre guide de migration](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Résumé des coûts de migration
Les migrations vers des partages de fichiers Azure à partir de volumes StorSimple et via des tâches de transformation de données dans une ressource StorSimple Data Manager sont gratuites. Toutefois, il existe d’autres coûts qui peuvent être encourus pendant et après une migration :

* **Sortie réseau :** Vos fichiers StorSimple résident dans un compte de stockage au sein d’une région Azure spécifique. Si vous configurez les partages de fichiers Azure vers lesquels vous migrez dans un compte de stockage qui se trouve dans la même région Azure, il n’y a aucun coût de sortie. Dans le cadre de cette migration, vous pouvez déplacer vos fichiers vers un compte de stockage situé dans une autre région. Dans ce cas, des coûts de sortie vous seront facturés.
* **Transactions de partage de fichiers Azure :** Lorsque des fichiers sont copiés dans un partage de fichiers Azure (dans le cadre d’une migration ou en dehors d’une migration), des coûts de transaction s’appliquent à l’écriture des fichiers et des métadonnées. La meilleure pratique consiste à démarrer votre partage de fichiers Azure sur le niveau Transaction optimisée pendant la migration. Basculez sur le niveau souhaité une fois la migration terminée. Les phases ci-dessous vous le signaleront au moment opportun.
* **Modification d’un niveau de partage de fichiers Azure :** La modification du niveau d’un partage de fichiers Azure entraîne des coûts de transaction. Dans la plupart des cas, il est plus rentable de suivre les conseils du point précédent.
* **Coût de stockage :** Lorsque cette migration commence à copier des fichiers dans un partage de fichiers Azure, le stockage Azure Files est consommé et facturé. 
* **StorSimple :** Tant que vous n’avez pas déprovisionné les appareils StorSimple et les comptes de stockage, les coûts de StorSimple pour le stockage, les sauvegardes et les appliances continueront à être facturés.

### <a name="direct-share-access-vs-azure-file-sync"></a>Accès direct au partage par rapport à Azure File Sync
Les partages de fichiers Azure ouvrent un tout nouveau monde de possibilités pour structurer le déploiement de vos services de fichiers. Un partage de fichiers Azure désigne simplement un partage SMB dans le cloud, que vous pouvez configurer pour que les utilisateurs y accèdent directement via le protocole SMB avec l’authentification Kerberos familière et les autorisations NTFS existantes (listes de contrôle d’accès de fichiers et de dossiers) fonctionnant en mode natif. [En savoir plus sur l’accès aux partages de fichiers Azure en fonction de l’identité](storage-files-active-directory-overview.md).

Une alternative à l’accès direct est [Azure File Sync](https://aka.ms/AFS). Azure File Sync est un équivalent direct de la capacité de StorSimple à mettre en cache localement les fichiers fréquemment utilisés. 

Azure File Sync est un service cloud Microsoft basé sur deux composants principaux :

* Synchronisation des fichiers et hiérarchisation cloud.
* Partages de fichiers comme stockage natif dans Azure, accessibles par le biais de différents protocoles comme SMB et File REST. 

Les partages de fichiers Azure conservent d’importants aspects de fidélité sur les fichiers stockés comme les attributs, les autorisations et les horodatages. Grâce aux partages de fichiers Azure, il n’est plus nécessaire qu’une application ou un service interprète les fichiers et dossiers stockés dans le cloud. Vous pouvez y accéder en mode natif via des protocoles et des clients familiers, tels que l’Explorateur de fichiers Windows. Les partages de fichiers Azure vous permettent de stocker dans le cloud des données d’applications et des données de serveurs de fichiers à usage général. La sauvegarde d’un partage de fichiers Azure est une fonctionnalité intégrée qui peut être améliorée par Sauvegarde Azure.

Cet article est consacré aux étapes de migration. Si vous souhaitez en savoir plus sur Azure File Sync avant d’effectuer la migration, consultez les articles suivants :

* [Azure File Sync - Vue d’ensemble](https://aka.ms/AFS "Vue d’ensemble")
* [Azure File Sync - Guide de déploiement](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Clé de chiffrement des données du service StorSimple
Lorsque vous avez configuré votre appliance StorSimple pour la première fois, elle a généré une «  *clé de chiffrement des données de service*  » et vous a demandé de stocker cette clé en toute sécurité.
Cette clé est utilisée pour chiffrer toutes les données du compte de stockage Azure associé dans lequel l’appliance StorSimple stocke vos fichiers.

La « clé de chiffrement des données de service » est nécessaire pour une migration réussie. C’est le bon moment pour récupérer cette clé dans vos dossiers, pour chacune des appliances de votre inventaire.

Si vous ne trouvez pas les clés dans vos dossiers, vous pouvez les récupérer à partir de l’appliance. Chaque appliance a une clé de chiffrement unique.

* Soumettez une demande de support auprès de Microsoft Azure par le biais du portail Azure. Le contenu de la demande doit mentionner les numéros de série des appareils StorSimple et demander à récupérer la « clé de chiffrement des données de service ». 
* Un ingénieur du support technique de StorSimple vous contactera en vous demandant de partager votre écran.
* Avant le début de la réunion, assurez-vous de vous connecter à votre appliance StorSimple [via une console série](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) ou via une [session PowerShell à distance](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple). 

> [!CAUTION]
> Lorsque vous décidez de la façon de vous connecter à votre appliance StorSimple, prenez en compte les points suivants :
> * La connexion via une session HTTPS est l’option la plus sûre et celle qui est recommandée.
> * Une connexion directe à la console série de l’appareil est sécurisée, mais une connexion à la console via des commutateurs réseau ne l’est pas. 
> * Les connexions par session HTTP sont une possibilité, mais elles **ne sont pas chiffrées** et ne sont donc pas non plus recommandées, sauf si elles sont utilisées dans un réseau fermé et approuvé.

### <a name="storsimple-volume-backups"></a>Sauvegardes de volume StorSimple
StorSimple propose des sauvegardes différentielles au niveau du volume. Les partages de fichiers Azure ont également cette capacité, appelée « instantanés de partage ».

Déterminez si, dans le cadre de votre migration, vous avez également l’obligation de déplacer les sauvegardes.

> [!CAUTION]
> Arrêtez-vous ici si vous devez migrer des sauvegardes de volumes StorSimple.
>
> Actuellement, vous pouvez uniquement migrer la sauvegarde de volume la plus récente. La prise en charge de la migration des sauvegardes arrivera fin 2020.
> Si vous commencez maintenant, vous ne pourrez pas « verrouiller » vos sauvegardes plus tard.
> Dans la version à venir, les sauvegardes doivent être « lues » sur les partages de fichiers Azure de la plus ancienne à la plus récente, avec des instantanés de partage de fichiers Azure pris entre chaque lecture.

Si vous souhaitez uniquement migrer les données actives sans avoir besoin de sauvegardes, vous pouvez continuer à suivre ce guide.
Si vous n’avez besoin de conserver vos sauvegardes qu’à très court terme, par exemple un mois ou deux, vous pouvez décider de poursuivre votre migration maintenant et ne déprovisionner vos ressources StorSimple qu’après cette période. Cette approche vous permettrait de créer autant d’historique des sauvegardes sur le partage de fichiers Azure que nécessaire. Pendant la période de fonctionnement des deux systèmes, un coût supplémentaire s’applique, ce qui fait que cette approche n’est pas à envisager si vous avez besoin de plus qu’une conservation des sauvegardes à très court terme.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mapper vos volumes StorSimple existants à des partages de fichiers Azure
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Nombre de comptes de stockage
Votre migration est susceptible de tirer parti d’un déploiement de plusieurs comptes de stockage, qui contiennent chacun un plus petit nombre de partages de fichiers Azure.

Si vos partages de fichiers sont très actifs (utilisés par de nombreux utilisateurs et/ou applications), seuls deux partages de fichiers Azure peuvent atteindre la limite de performances de votre compte de stockage. Pour cette raison, la meilleure pratique consiste à migrer vers plusieurs comptes de stockage, chacun ayant ses propres partages de fichiers individuels et généralement pas plus de deux ou trois partages par compte de stockage.

Une meilleure pratique consiste à déployer les comptes de stockage avec un partage de fichiers pour chaque. Vous pouvez regrouper plusieurs partages de fichiers Azure dans le même compte de stockage s’ils sont destinés à l’archivage.

Ces considérations s’appliquent davantage à l’[accès direct au cloud](#direct-share-access-vs-azure-file-sync) (par le biais d’une machine virtuelle ou d’un service Azure) qu’à Azure File Sync. Si vous envisagez d’utiliser Azure File Sync sur ces partages uniquement, le regroupement de plusieurs partages dans le même compte de stockage Azure est une bonne idée. Vous pouvez également envisager de lift-and-shift une application dans le cloud, qui accéderait alors directement à un partage de fichiers. Ou vous pourriez commencer à utiliser un service dans Azure qui bénéficierait également d’un nombre d’IOPS et de débits plus élevés. 

Si vous avez établi la liste de vos partages, vous devez mapper chaque partage au compte de stockage dans lequel il résidera.

> [!IMPORTANT]
> Veillez à choisir une région Azure, puis assurez-vous que chaque compte de stockage et chaque ressource Azure File Sync correspondent à la région que vous avez sélectionnée.

### <a name="phase-1-summary"></a>Récapitulatif de la phase 1
À la fin de la phase 1 :
* Vous avez une bonne vue d’ensemble de vos appareils et volumes StorSimple.
* Le service de transformation des données est prêt à accéder à vos volumes StorSimple dans le cloud, car vous avez récupéré votre clé de chiffrement des données de service pour chaque appareil StorSimple.
* Vous avez un plan non seulement pour les volumes à migrer, mais aussi pour le mappage de vos volumes au nombre approprié de partages de fichiers et de comptes de stockage Azure.

> [!CAUTION]
> Si vous devez migrer des sauvegardes de volumes StorSimple, **ARRÊTEZ-VOUS ICI** .
>
> Cette approche de migration repose sur de nouvelles capacités du service de transformation des données qui ne peuvent pas migrer les sauvegardes actuellement. La prise en charge de la migration des sauvegardes arrivera fin 2020.
> Actuellement, vous ne pouvez migrer que vos données actives. Si vous commencez maintenant, vous ne pourrez pas « verrouiller » vos sauvegardes plus tard.
> Les sauvegardes doivent être « lues » sur les partages de fichiers Azure, des données actives les plus anciennes aux plus récentes, avec des instantanés de partage de fichiers Azure entre chaque lecture.

Si vous souhaitez uniquement migrer les données actives sans avoir besoin de sauvegardes, vous pouvez continuer à suivre ce guide.

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>Phase 2 : Déploiement des ressources de stockage et de migration Azure

Cette section décrit les considérations relatives au déploiement des différents types de ressources nécessaires dans Azure. Certaines conserveront vos données après la migration et d’autres sont nécessaires uniquement pour la migration. Ne commencez pas à déployer les ressources avant d’avoir finalisé votre plan de déploiement. Il est difficile, parfois impossible, de modifier certains aspects de vos ressources Azure une fois qu’elles ont été déployées.

### <a name="deploy-storage-accounts"></a>Déployer des comptes de stockage
Vous aurez probablement besoin de déployer plusieurs comptes de stockage Azure. Chacun d’eux contiendra un plus petit nombre de partages de fichiers Azure, conformément au plan de déploiement vous avez terminé dans la section précédente de cet article. Rendez-vous sur le portail Azure pour [déployer les comptes de stockage que vous avez planifiés](../common/storage-account-create.md#create-a-storage-account). Envisagez d’utiliser les paramètres de base suivants pour tout nouveau compte de stockage :

##### <a name="subscription"></a>Abonnement
Vous pouvez utiliser le même abonnement que celui utilisé pour votre déploiement StorSimple ou en utiliser un autre. La seule limitation est que votre abonnement doit se trouver dans le même locataire Azure AD que l’abonnement StorSimple. Envisagez de déplacer l’abonnement StorSimple vers le locataire approprié avant de commencer la migration. Vous ne pouvez déplacer que l’intégralité de l’abonnement ; les ressources StorSimple individuelles ne peuvent pas être déplacées vers un autre locataire ou abonnement.

##### <a name="resource-group"></a>Resource group
Les groupes de ressources aident à l’organisation des ressources et à la gestion administrative des autorisations. En savoir plus sur les [groupes de ressources dans Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

##### <a name="storage-account-name"></a>Nom du compte de stockage
Le nom de votre compte de stockage devient partie intégrante d’une URL et présente certaines limitations de caractère. Dans votre convention d’affectation de noms, considérez que les noms de compte de stockage doivent être uniques au monde, n’autorisez que des lettres minuscules et les chiffres, exigez entre 3 et 24 caractères et interdisez les caractères spéciaux tels que les tirets ou les traits de soulignement. Voir aussi : [Règles d’affectation de noms des ressources de stockage Azure](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

##### <a name="location"></a>Location
L’emplacement ou la région Azure d’un compte de stockage est très important. Si vous utilisez Azure File Sync, tous vos comptes de stockage doivent se trouver dans la même région que votre ressource de service de synchronisation de stockage. La région Azure que vous sélectionnez doit être proche ou centrale par rapport à vos serveurs/utilisateurs locaux. Une fois que votre ressource a été déployée, vous ne pouvez pas modifier sa région.

Vous pouvez choisir une région différente de celle où résident actuellement vos données StorSimple (compte de stockage).

> [!IMPORTANT]
> Si vous choisissez une région différente de celle où se trouve votre compte de stockage StorSimple, des [frais de sortie s’appliqueront](https://azure.microsoft.com/pricing/details/bandwidth) pendant la migration. Les données quitteront la région StorSimple et entreront dans la région de votre compte de stockage. Aucuns frais de bande passante ne s’appliquent si vous restez dans la même région Azure.

##### <a name="performance"></a>Performances
Vous avez la possibilité de choisir entre le stockage Premium (SSD) et le stockage Standard pour les partages de fichiers Azure. Le stockage Standard comprend [plusieurs niveaux pour un partage de fichiers](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Le stockage Standard est l’option qui convient à la plupart des clients qui migrent depuis StorSimple.

Vous ne savez toujours pas ?
* Choisissez le stockage Premium si vous avez besoin des [performances d’un partage de fichiers Azure Premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
* Choisissez le stockage Standard pour les charges de travail du serveur de fichiers à usage général, y compris les données à chaud et les données d’archivage. Choisissez également le stockage Standard si la seule charge de travail sur le partage dans le cloud sera Azure File Sync.

##### <a name="account-kind"></a>Type de compte
* Pour le stockage Standard, choisissez : *StorageV2 (usage général v2)*
* Pour les partages de fichiers Premium, choisissez : *FileStorage*

##### <a name="replication"></a>Réplication
Plusieurs paramètres de réplication sont disponibles. En savoir plus sur les différents types de réplication.

Choisissez l’une des deux options suivantes :
* *Stockage localement redondant (LRS)*
* *Stockage redondant interzone (ZRS)* , qui n’est pas disponible dans toutes les régions Azure.

> [!NOTE]
> Seuls les types de redondance LRS et ZRS sont compatibles avec les grands partages de fichiers Azure d’une capacité de 100 Tio.

Le stockage globalement redondant (toutes les variantes) n’est pas pris en charge pour le moment. Vous pouvez changer de type de redondance plus tard et passer à GRS lorsqu’Azure le prendra en charge.

##### <a name="enable-100tib-capacity-file-shares"></a>Activer les partages de fichiers d’une capacité de 100 Tio

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Image illustrant l’onglet Avancé dans le portail Azure pour la création d’un compte de stockage.":::
    :::column-end:::
    :::column:::
        Dans la section *Avancé* de l’Assistant Nouveau compte de stockage du portail Azure, vous pouvez activer la prise en charge des *partages de fichiers volumineux* dans ce compte de stockage. Si cette option n’est pas disponible, vous avez probablement sélectionné le mauvais type de redondance. Veillez à sélectionner LRS ou ZRS pour que cette option soit disponible.
    :::column-end:::
:::row-end:::

Opter pour les grands partages de fichiers d’une capacité de 100 Tio présente plusieurs avantages :
* Vos performances sont considérablement accrues par rapport aux partages de fichiers plus petits d’une capacité de 5 Tio (par exemple : IOPS multiplié par 10).
* La migration se terminera beaucoup plus rapidement.
* Vous vous assurez qu’un partage de fichiers aura une capacité suffisante pour contenir toutes les données que vous y migrerez.
* La croissance future est couverte.

### <a name="azure-file-shares"></a>Partages de fichiers Azure
Une fois les comptes de stockage créés, vous pouvez accéder à la section *« Partage de fichiers »* du compte de stockage et déployer le nombre approprié de partages de fichiers Azure selon votre plan de migration (voir phase 1). Envisagez d’utiliser les paramètres de base suivants pour tout nouveau partage de fichiers dans Azure :

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Image illustrant l’onglet Avancé dans le portail Azure pour la création d’un compte de stockage.":::
    :::column-end:::
    :::column:::
        </br>**Nom**</br>Les lettres minuscules, les chiffres et les traits d’union sont pris en charge.</br></br>**Quota**</br>Le quota ici est comparable au quota inconditionnel SMB sur un serveur Windows. La meilleure pratique consiste à ne pas définir de quota ici, car votre migration et vos autres services échoueront lorsque le quota sera atteint.</br></br>**Niveau**</br>Sélectionnez *Transaction optimisée* pour votre nouveau partage de fichiers. Pendant la migration, de nombreuses transactions se produiront et il est plus rentable de changer de niveau plus tard pour le niveau le plus adapté à votre charge de travail.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager
La ressource Azure qui contiendra vos tâches de migration s’appelle *« StorSimple Data Manager »* . Cliquez sur Nouvelle ressource et recherchez-la, puis cliquez sur *Créer* .

Il s’agit d’une ressource temporaire utilisée pour l’orchestration. Vous la déprovisionnerez une fois votre migration terminée. Elle doit être déployée dans le même abonnement, le même groupe de ressources et la même région que votre compte de stockage StorSimple.

### <a name="azure-file-sync"></a>Azure File Sync
Azure File Sync (AFS) vous permet d’ajouter la mise en cache locale des fichiers les plus fréquemment consultés. À l’instar des capacités de mise en cache de StorSimple, la fonctionnalité de hiérarchisation cloud d’AFS offre une latence d’accès local combinée à un meilleur contrôle de la capacité de cache disponible sur le serveur Windows et à une synchronisation sur plusieurs sites. Si votre objectif est de disposer d’un cache local, préparez dans votre réseau local une machine virtuelle Windows Server (les serveurs physiques et les clusters de basculement sont également pris en charge) avec une capacité suffisante de « stockage attaché directement (DAS) ». 

> [!IMPORTANT]
> Ne configurez pas encore Azure File Sync : il est préférable de configurer Azure File Sync une fois la migration de votre partage terminée. Le déploiement d’AFS ne doit pas démarrer avant la phase 4 d’une migration.

### <a name="phase-2-summary"></a>Récapitulatif de la phase 2
À la fin de la phase 2, vous aurez déployé vos comptes de stockage, tous les partages de fichiers Azure sur lesdits comptes, ainsi qu’une ressource StorSimple Data Manager. Vous utiliserez cette dernière dans la phase 3, lorsque vous configurerez vos tâches de migration.

## <a name="phase-3-create-and-run-a-migration-job"></a>Phase 3 : Création et exécution d’une tâche de migration
Cette section décrit comment configurer une tâche de migration et mapper avec soin les répertoires sur un volume StorSimple qui doit être copié dans le partage de fichiers Azure cible que vous sélectionnez. Pour commencer, accédez à votre ressource StorSimple Data Manager, recherchez **Définitions de tâches** dans le menu et sélectionnez **+ Définition de tâche** . Le type de stockage cible est le type par défaut : *Partage de fichiers Azure* .

![Types de tâches de migration de StorSimple série 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Capture d’écran de Définitions de tâches dans le portail Azure avec une nouvelle boîte de dialogue Définition de tâche ouverte qui demande le type de tâche : Copier vers un partage de fichiers ou un conteneur de blobs.")

> [!IMPORTANT]
> Avant d’exécuter une tâche de migration, arrêtez les sauvegardes planifiées automatiquement de vos volumes StorSimple.

:::row:::
    :::column:::
        ![Tâche de migration de StorSimple série 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Capture d’écran du nouveau formulaire de création de tâche pour une tâche de transformation de données.")
    :::column-end:::
    :::column:::
        **Nom de la définition de travail**</br>Ce nom doit indiquer l’ensemble des fichiers que vous déplacez. Donnez-lui un nom similaire à celui de votre partage de fichiers Azure. </br></br>**Emplacement d’exécution de la tâche**</br>Lorsque vous sélectionnez une région, vous devez sélectionner la même région que votre compte de stockage StorSimple ou, si cela n’est pas possible, une région proche. </br></br><h3>Source</h3>**Abonnement source**</br>Choisissez l’abonnement dans lequel vous stockez votre ressource StorSimple Device Manager. </br></br>**Ressource StorSimple**</br>Sélectionnez la ressource StorSimple Device Manager auprès de laquelle votre appliance est inscrite. </br></br>**Clé de chiffrement des données du service**</br>Consultez la [section précédente de cet article](#storsimple-service-data-encryption-key) si vous ne trouvez pas la clé dans vos dossiers. </br></br>**Appareil**</br>Sélectionnez l’appareil StorSimple contenant le volume sur lequel vous souhaitez migrer. </br></br>**Volume**</br>Sélectionnez le volume source. Plus tard, vous déciderez si vous souhaitez migrer l’ensemble du volume ou des sous-répertoires vers le partage de fichiers Azure cible. </br></br><h3>Cible</h3>Choisissez l’abonnement, le compte de stockage et le partage de fichiers Azure comme cible de cette tâche de migration.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> La sauvegarde de volume la plus récente sera utilisée pour effectuer la migration. Assurez-vous qu’au moins une sauvegarde de volume est présente, sinon la tâche échouera. Assurez-vous également que la dernière sauvegarde que vous avez est assez récente, afin de réduire au maximum le delta sur le partage actif. Il peut être utile de déclencher manuellement et d’effectuer une autre sauvegarde de volume **avant** l’exécution de la tâche que vous venez de créer.

### <a name="directory-mapping"></a>Mappage de répertoires
Le mappage est facultatif pour votre tâche de migration. Si vous le laissez vide, **tous** les fichiers et dossiers à la racine de votre volume StorSimple seront déplacés vers la racine de votre partage de fichiers Azure cible. Dans la plupart des cas, le stockage du contenu d’un volume entier dans un partage de fichiers Azure n’est pas la meilleure approche. Il est souvent préférable de fractionner le contenu d’un volume sur plusieurs partages de fichiers dans Azure. Si vous n’avez pas encore créé de plan, consultez d’abord cette section : [Mapper votre volume StorSimple à des partages de fichiers Azure](#map-your-existing-storsimple-volumes-to-azure-file-shares).

Dans le cadre de votre plan de migration, vous avez peut-être décidé que les dossiers sur un volume StorSimple doivent être fractionnés entre plusieurs partages de fichiers Azure. Si tel est le cas, vous pouvez effectuer ce fractionnement en :
1. définissant plusieurs tâches pour migrer les dossiers sur un volume. Chacune aura la même source de volume StorSimple, mais un partage de fichiers Azure différent comme cible ;
1. spécifiant précisément les dossiers du volume StorSimple qui doivent être migrés dans le partage de fichiers spécifié, à l’aide de la section *Directory-mapping* du formulaire de création de tâche et en respectant la [sémantique de mappage](#semantic-elements) spécifique.

> [!IMPORTANT]
> Les chemins d’accès et les expressions de mappage de ce formulaire ne peuvent pas être validés lorsque le formulaire est envoyé. Si les mappages ne sont pas spécifiés correctement, une tâche peut échouer complètement ou produire un résultat indésirable. Dans ce cas, il est généralement préférable de supprimer le partage de fichiers Azure, de le recréer, puis de corriger les instructions de mappage dans une nouvelle tâche de migration pour le partage. L’exécution d’une nouvelle tâche avec des instructions de mappage corrigées peut résoudre les dossiers omis et les placer dans le partage existant. Toutefois, seuls les dossiers qui ont été omis en raison d’un chemin d’accès mal orthographié peuvent être résolus de cette façon.

#### <a name="semantic-elements"></a>Éléments sémantiques
Un mappage est exprimé de gauche à droite : [\source path] \> [\target path].

|Caractère sémantique          | Signification  |
|:---------------------------|:---------|
| **\\**                     | indicateur de niveau racine        |
| **\>**                     | opérateur de mappage [source] et [cible]        |
|**\|** ou RETOUR (nouvelle ligne) | séparateur de deux instructions de mappage de dossiers. </br>Vous pouvez également omettre ce caractère et appuyer sur Entrée pour afficher l’expression de mappage suivante sur sa propre ligne        |

### <a name="examples"></a>Exemples
Déplace le contenu du dossier « Données utilisateur » à la racine du partage de fichiers cible :
``` console
\User data > \\
```
Déplace l’intégralité du contenu du volume vers un nouveau chemin d’accès sur le partage de fichiers cible :
``` console
\ \> \Apps\HR tracker
```
Déplace le contenu du dossier source vers un nouveau chemin d’accès sur le partage de fichiers cible :
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
Trie plusieurs emplacements sources dans une nouvelle structure de répertoires :
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Règles sémantiques
* Spécifiez toujours des chemins de dossier relatifs au niveau racine. 
* Commencez chaque chemin d’accès au dossier par un indicateur de niveau racine « \" ». 
* N’incluez pas de lettres de lecteur. 
* Lorsque vous spécifiez plusieurs chemins d’accès, les chemins source ou cible ne peuvent pas se chevaucher.</br>
   Exemple de chevauchement du chemin d’accès source non valide :</br>
    *\\dossier\1 > \\dossier*</br>
    *\\dossier\\1\\2 > \\dossier2*</br>
   Exemple de chevauchement du chemin d’accès cible non valide :</br>
   *\\dossier > \\*</br>
   *\\dossier2 > \\*</br>
* Les dossiers sources qui n’existent pas seront ignorés. 
* Les structures de dossiers qui n’existent pas sur la cible seront créées. 
* Comme pour Windows : les noms de dossiers ne respectent pas la casse, mais la conservent.

> [!NOTE]
> Le contenu du dossier « *\System Volume Information* » et de « *$Recycle.Bin* » sur votre volume StorSimple ne sera pas copié par la tâche de transformation.

### <a name="phase-3-summary"></a>Récapitulatif de la phase 3
À la fin de la phase 3, vous aurez exécuté vos tâches de transformation de données des volumes StorSimple vers les partages de fichiers Azure. Vous pouvez maintenant vous concentrer sur la configuration d’Azure File Sync pour le partage (une fois que les tâches de migration pour un partage sont terminées) ou de l’accès direct au partage de fichiers Azure de vos applications et de vos travailleurs de l’information.

## <a name="phase-4-accessing-your-azure-file-shares"></a>Phase 4 : Accès à vos partages de fichiers Azure

Il existe deux stratégies principales pour accéder à vos partages de fichiers Azure :

* **Azure File Sync :** [Comment déployer Azure File Sync](#deploy-azure-file-sync) sur un serveur Windows local. AFS offre tous les avantages d’un cache local, tout comme StorSimple.
* **Accès direct au partage :** [Comment déployer un accès direct au partage](#deploy-direct-share-access). Utilisez cette stratégie si votre scénario d’accès pour un partage de fichiers Azure donné ne tire pas parti de la mise en cache locale ou si vous n’avez plus la possibilité d’héberger un serveur Windows local. Ici, vos utilisateurs et applications continuent d’accéder aux partages SMB via le protocole SMB, mais ces partages ne sont plus sur un serveur local, mais directement dans le cloud.

Vous devez déjà avoir choisi l’option qui vous convient le mieux, dans la [phase 1](#phase-1-prepare-for-migration) de ce guide.

Le reste de cette section se concentre sur les instructions de déploiement.

### <a name="deploy-azure-file-sync"></a>Déployer Azure File Sync

Il est temps de déployer une partie d’Azure File Sync :
1. Créez la ressource cloud Azure File Sync.
1. Déployez l’agent Azure File Sync sur votre serveur local.
1. Inscrivez le serveur auprès de la ressource cloud.

Ne créez pas encore de groupes de synchronisation. La configuration de la synchronisation avec un partage de fichiers Azure doit se produire uniquement lorsque vos tâches de migration vers un partage de fichiers Azure sont terminées. Si vous commencez à utiliser File Sync avant la fin de votre migration, cela rendra votre migration inutilement difficile, car vous ne pourrez pas facilement dire quand il sera temps de lancer une opération de basculement.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Déployer la ressource cloud Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Si vous souhaitez modifier la région Azure dans laquelle vos données résident une fois que la migration est terminée, déployez le service de synchronisation de stockage dans la même région que les comptes de stockage cibles pour cette migration.

#### <a name="deploy-an-on-premises-windows-server"></a>Déployer un serveur Windows Server local

* Créez un serveur Windows Server 2019 (ou au minimum 2012R2) comme machine virtuelle ou serveur physique. Un cluster de basculement Windows Server est également pris en charge. Ne réutilisez pas le serveur qui précède l’appliance StorSimple 8100 ou 8600.
* Approvisionnez ou ajoutez un stockage DAS (Direct Attached Storage). Notez que les périphériques de stockage NAS ne sont pas pris en charge.

La meilleure pratique consiste à attribuer à votre nouveau serveur Windows Server une quantité de stockage égale ou supérieure à celle dont votre appliance StorSimple 8100 ou 8600 dispose localement pour la mise en cache. Vous utiliserez le serveur Windows Server de la même façon que vous avez utilisé l’appliance StorSimple : s’il possède la même quantité de stockage que l’appliance, alors l’expérience de mise en cache doit être similaire, si ce n’est identique.
Vous pouvez ajouter ou supprimer de l’espace de stockage sur votre serveur Windows Server à volonté. Cela vous permet de mettre à l’échelle la taille de votre volume local et la quantité de stockage local disponible pour la mise en cache.

#### <a name="prepare-the-windows-server-for-file-sync"></a>Préparer le serveur Windows Server pour la synchronisation de fichiers

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configurer Azure File Sync sur Windows Server

Votre serveur Windows Server local inscrit doit être prêt et connecté à Internet pour ce processus.

> [!IMPORTANT]
> Votre migration StorSimple de fichiers et de dossiers vers le partage de fichiers Azure doit être terminée avant de continuer. Assurez-vous qu’aucune autre modification n’est effectuée sur le partage de fichiers.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Veillez à activer la hiérarchisation cloud. La hiérarchisation cloud désigne la fonctionnalité Azure File Sync qui permet au serveur local d’avoir moins de capacité de stockage que ce qui est stocké dans le cloud, tout en ayant l’espace de noms complet disponible. Les données intéressantes localement sont également mises en cache localement pour des performances d’accès local rapides. Une autre raison d’activer la hiérarchisation cloud à cette étape est que nous ne souhaitons pas synchroniser le contenu des fichiers à ce stade, seul l’espace de noms doit être déplacé pour l’instant.

### <a name="deploy-direct-share-access"></a>Déployer l’accès direct au partage

:::row:::
    :::column:::
        [![Guide pas à pas et démonstration sur la façon d’exposer en toute sécurité des partages de fichiers Azure directement aux travailleurs de l’information et aux applications : cliquez pour lire la vidéo !](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Cette vidéo est un guide et une démonstration de la façon d’exposer en toute sécurité des partages de fichiers Azure directement aux travailleurs de l’information et aux applications en cinq étapes simples.</br>
        La vidéo fait référence à une documentation dédiée pour certains sujets :
* [Vue d’ensemble de l’identité](storage-files-active-directory-overview.md)
* [Comment joindre un domaine à un compte de stockage](storage-files-identity-auth-active-directory-enable.md)
* [Présentation de la mise en réseau pour les partages de fichiers Azure](storage-files-networking-overview.md)
* [Comment configurer des points de terminaison publics et privés](storage-files-networking-endpoints.md)
* [Comment configurer un VPN site à site](storage-files-configure-s2s-vpn.md)
* [Comment configurer un VPN point à site Windows](storage-files-configure-p2s-vpn-windows.md)
* [Comment configurer un VPN point à site Linux](storage-files-configure-p2s-vpn-linux.md)
* [Procédure de configuration du transfert DNS](storage-files-networking-dns.md)
* [Configurer DFS-N](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Récapitulatif de la phase 4
Au cours de cette phase, vous avez créé et exécuté plusieurs tâches de *service de transformation des données* (DTS) dans votre *ressource StorSimple Data Manager* . Ces tâches ont migré vos fichiers et dossiers vers des partages de fichiers Azure. En outre, vous avez déployé Azure File Sync ou préparé votre réseau et vos comptes de stockage pour un accès direct au partage.

## <a name="phase-5-user-cut-over"></a>Phase 5 : Transfert de l’utilisateur
Cette phase est consacrée à la finalisation de votre migration :
* Planifiez votre temps d’arrêt.
* Récupérez les modifications apportées par vos utilisateurs et applications côté StorSimple pendant que les tâches de transformation des données de la phase 3 étaient en cours d’exécution. 
* Faites basculer vos utilisateurs vers le nouveau serveur Windows avec Azure File Sync ou vers les partages de fichiers Azure via un accès direct au partage.

### <a name="plan-your-downtime"></a>Planifier votre temps d’arrêt
Cette approche de migration nécessite un temps d’arrêt pour vos utilisateurs et vos applications. L’objectif est de réduire au minimum le temps d’arrêt et les considérations suivantes peuvent vous y aider :

* Gardez vos volumes StorSimple disponibles pendant l’exécution de vos tâches de transformation de données.
* Lorsque vous avez terminé d’exécuter vos tâches de migration des données pour un partage, il est temps de supprimer l’accès utilisateur (au moins l’accès en écriture) des volumes/partages StorSimple. Une RoboCopy finale récupèrera les modifications de votre partage de fichiers Azure, et vous pourrez alors transférer vos utilisateurs. L’emplacement où vous exécutez RoboCopy varie selon que vous avez choisi d’utiliser Azure File Sync ou l’accès direct au partage. La prochaine section sur RoboCopy couvre ce sujet.
* Une fois que vous avez terminé la récupération des modifications à l’aide de RoboCopy, vous êtes prêt à exposer le nouvel emplacement à vos utilisateurs : soit le partage de fichiers Azure directement, soit un partage SMB sur un serveur Windows avec AFS. Souvent, un déploiement DFS-N permet d’effectuer une opération de migration rapide et efficace. Il conserve la cohérence de vos adresses de partage existantes et les redirigent vers un nouvel emplacement qui contient vos fichiers et dossiers migrés.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Déterminer le moment où votre espace de noms est entièrement synchronisé à votre serveur

Lorsque vous utilisez Azure File Sync pour un partage de fichiers Azure, il est important que vous déterminiez que l’espace de noms entier est entièrement téléchargé sur le serveur AVANT de démarrer une RoboCopy locale. Le temps nécessaire au téléchargement de votre espace de noms dépend du nombre d’éléments dans votre partage de fichiers Azure. Il existe deux méthodes permettant de déterminer si votre espace de noms est entièrement arrivé sur le serveur :

#### <a name="azure-portal"></a>Portail Azure
Vous pouvez utiliser le portail Azure pour voir à quel moment votre espace de noms est entièrement arrivé.
* Connectez-vous au portail Azure, accédez à votre groupe de synchronisation et vérifiez l’état de synchronisation de votre groupe de synchronisation et de votre point de terminaison de serveur. 
* Le sens intéressant est le suivant : Si le point de terminaison de serveur vient d’être approvisionné, il affiche **Synchronisation initiale** indiquant que l’espace de noms est toujours en cours de chargement.
Une fois qu’il n’affiche plus *Synchronisation initiale* , votre espace de noms est entièrement copié sur le serveur et vous pouvez continuer avec une RoboCopy locale.

#### <a name="windows-server-event-viewer"></a>Observateur d’événements Windows Server
Vous pouvez également utiliser l’observateur d’événements sur votre serveur Windows pour connaître le moment où l’espace de noms est entièrement arrivé.

1. Ouvrez l’ **observateur d’événements** et accédez à **Applications et services** .
1. Parcourez les fichiers et ouvrez **Microsoft\FileSync\Agent\Telemetry** .
1. Recherchez l’ **événement 9102** le plus récent, qui correspond à une session de synchronisation terminée.
1. Sélectionnez **Détails** et confirmez que vous examinez un événement dans lequel la valeur **SyncDirection** est **Télécharger** .
1. Lorsque votre espace de noms aura été téléchargé sur le serveur, il y aura un événement unique avec **Scenario** , la valeur **FullGhostedSync** et **HResult** = **0** .
1. S’il vous manque cet événement, vous pouvez également rechercher d’autres **événements 9102** où **SyncDirection** = **Télécharger** et **Scenario** =  **« RegularSync »** . Le fait de trouver un de ces événements indique aussi que l’espace de noms a terminé son téléchargement et que la synchronisation est passée à des sessions de synchronisation régulières, qu’il y ait quelque chose à synchroniser ou non, pour le moment.

### <a name="a-final-robocopy"></a>Une RoboCopy finale
À ce stade, il existe des différences entre votre serveur Windows Server local et l’appliance StorSimple 8100 ou 8600 :

1. Vous devez récupérer les modifications apportées par les utilisateurs/applications côté StorSimple pendant la migration.
1. Pour les cas où vous utilisez Azure File Sync : L’appliance StorSimple dispose d’un cache rempli, alors que le serveur Windows Server n’a qu’un espace de noms sans contenu de fichier stocké localement pour l’instant. Par conséquent, la RoboCopy finale peut vous aider à démarrer votre cache AFS local en récupérant le contenu du fichier mis en cache localement dans la mesure où il est disponible et peut tenir sur le serveur AFS.
1. Certains fichiers peuvent avoir été ignorés par la tâche de transformation des données en raison de caractères non valides. Si c’est le cas, copiez-les sur le serveur Windows Azure File Sync. Plus tard, vous pourrez les ajuster pour qu’ils se synchronisent. Si vous n’utilisez pas Azure File Sync pour un partage particulier, il est préférable de renommer les fichiers avec des caractères non valides sur le volume StorSimple, puis d’exécuter la RoboCopy directement sur le partage de fichiers Azure. 

> [!WARNING]
> Vous ne devez pas démarrer la RoboCopy avant que le serveur n’ait téléchargé complètement l’espace de noms pour un partage de fichiers Azure !
> Voir la section « [Déterminer le moment où votre espace de noms est entièrement synchronisé à votre serveur](#determine-when-your-namespace-has-fully-synced-to-your-server) ».

 Vous souhaitez uniquement copier les fichiers qui ont été modifiés après la dernière exécution de la tâche de migration et les fichiers qui n’ont pas été déplacés par lesdites tâches auparavant. Vous pouvez résoudre les problèmes qui expliquent pourquoi ils n’ont pas été déplacés plus tard sur le serveur, une fois la migration terminée. Voir [Résolution des problèmes Azure File Sync](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy a plusieurs paramètres. L’exemple suivant illustre une commande terminée et une liste des raisons pour lesquelles vous choisissez ces paramètres :

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
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

Quand vous configurez les emplacements source et cible de la commande RoboCopy, veillez à examiner la structure de la source et de la cible pour vous assurer qu’elles correspondent. Si vous avez utilisé la fonctionnalité de mappage de répertoires de la tâche de migration, la structure de votre répertoire racine peut être différente de celle de votre volume StorSimple. Si c’est le cas, vous pouvez avoir besoin de plusieurs tâches RoboCopy, une pour chaque sous-répertoire. Puisque cette commande RoboCopy utilise /MIR, elle ne déplacera pas les fichiers qui sont identiques (fichiers hiérarchisés pour l’instance), mais si le chemin source et le chemin cible sont incorrects, /MIR vide également les structures de répertoires sur votre serveur Windows ou votre partage de fichiers Azure qui ne sont pas présentes sous le chemin source StorSimple. Elles doivent donc correspondre exactement pour que la tâche RoboCopy atteigne son objectif, à savoir la mise à jour de votre contenu migré avec les dernières modifications apportées pendant la migration. 

Consultez le fichier journal RoboCopy pour voir si des fichiers ont été ignorés. Si des problèmes existent, corrigez-les, puis réexécutez la commande RoboCopy. Ne déprovisionnez pas les ressources StorSimple avant d’avoir corrigé les problèmes en suspens pour les fichiers ou dossiers qui vous intéressent.

Si vous n’utilisez pas Azure File Sync pour mettre en cache le partage de fichiers Azure en question, mais que vous avez plutôt opté pour un accès direct au partage :
1. [Montez votre partage de fichiers Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) en tant que lecteur réseau sur un ordinateur Windows local.
1. Exécutez RoboCopy entre votre volume StorSimple et le partage de fichiers Azure monté. Si les fichiers ne sont pas copiés, corrigez leurs noms côté StorSimple pour supprimer les caractères non valides et réessayez d’exécuter RoboCopy. La commande RoboCopy mentionnée précédemment peut être exécutée plusieurs fois sans provoquer de rappel inutile vers StorSimple.

### <a name="user-cut-over"></a>Transfert de l’utilisateur

Si vous utilisez Azure File Sync, vous devrez probablement créer les partages SMB sur ce serveur Windows AFS qui correspond aux partages que vous aviez sur les volumes StorSimple. Vous pouvez charger cette étape en amont et le faire plus tôt pour ne pas perdre de temps ici, mais vous devez vous assurer qu’avant ce stade, personne n’a accès ni ne peut apporter des modifications au serveur Windows.

Si vous avez un déploiement DFS-N, vous pouvez faire pointer les espaces de noms DFN vers les emplacements des nouveaux dossiers du serveur. Si vous ne disposez pas d’un déploiement DFS-N et que vous avez déjà relié votre appliance 8100/8600 localement à un serveur Windows, vous pouvez retirer ce serveur du domaine. Ensuite, joignez le domaine à votre nouveau serveur Windows Azure File Sync. Au cours de ce processus, donnez au serveur le même nom de serveur et les mêmes noms de partage que l’ancien serveur, afin que le basculement reste transparent pour vos utilisateurs, votre stratégie de groupe et vos scripts.

[En savoir plus sur DFS-N](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>annulation du déploiement
Lorsque vous déprovisionnez une ressource, vous perdez l’accès à la configuration de cette ressource, ainsi qu’à ses données. Le déprovisionnement ne peut pas être annulé. Ne continuez donc pas avant d’avoir confirmé que votre migration est terminée et qu’il n’existe aucune dépendance sur les fichiers, dossiers ou sauvegardes de volume StorSimple que vous allez déprovisionner.

Avant de commencer, il est recommandé d’observer quelque temps votre nouveau déploiement Azure File Sync en production. Cela vous donne la possibilité de corriger tout problème que vous pourriez rencontrer.
Une fois que vous avez observé votre déploiement Azure File Sync pendant au moins quelques jours, vous pouvez commencer à déprovisionner les ressources dans cet ordre :

1. Déprovisionnez votre ressource StorSimple Data Manager via le portail Azure.
   Toutes vos tâches DTS seront supprimées avec elle. Vous ne pourrez pas récupérer facilement les journaux de copie. S’ils sont importants pour vos dossiers, récupérez-les avant de déprovisionner la ressource.
1. Assurez-vous que vos appliances physiques StorSimple ont été migrées, puis désinscrivez-les.
   Si vous n’êtes pas entièrement sûr qu’elles ont été migrées, ne poursuivez pas. Si vous déprovisionnez ces ressources alors qu’elles sont encore nécessaires, vous ne serez pas en mesure de récupérer les données ou leur configuration.
1. S’il ne reste plus d’appareils inscrits dans une ressource StorSimple Device Manager, vous pouvez supprimer cette dernière.
1. Il est maintenant temps de supprimer le compte de stockage StorSimple dans Azure. Là encore, arrêtez-vous et confirmez que la migration est terminée et que rien ni personne ne dépend de ces données avant de continuer.
1. Débranchez l’appliance physique StorSimple de votre centre de données.
1. Si vous êtes propriétaire de l’appliance StorSimple, vous êtes libre de la recycler. 
   Si votre appareil est loué, informez le bailleur et retournez l’appareil comme il se doit.

Votre migration est terminée.

> [!NOTE]
> Vous avez des questions ou rencontrez des problèmes ?</br>
> Nous sommes là pour vous aider : AzureFilesMigration@microsoft.com


## <a name="next-steps"></a>Étapes suivantes

* Familiarisez-vous avec [Azure File Sync](https://aka.ms/AFS).
* Comprenez la flexibilité des stratégies de [hiérarchisation cloud](storage-sync-cloud-tiering.md).
* [Activez Sauvegarde Azure](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) sur vos partages de fichiers Azure pour planifier des instantanés et définir des planifications de conservation de sauvegardes.
* Si vous constatez dans le portail Azure que certains fichiers ne se synchronisent jamais, consultez le [guide de résolution des problèmes](storage-sync-files-troubleshoot.md) pour connaître les mesures à prendre pour les résoudre.
