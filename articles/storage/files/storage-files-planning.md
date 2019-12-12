---
title: Planification d’un déploiement Azure Files | Microsoft Docs
description: Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fdfa01a45c0dd35da65b2ad7ce8b0d291148af1a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931102"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planification d’un déploiement Azure Files

[Azure Files](storage-files-introduction.md) offre des partages de fichiers managés dans le cloud qui sont accessibles via le protocole SMB standard. Comme Azure Files est entièrement managé, son déploiement dans des scénarios de production est beaucoup plus simple que le déploiement et la gestion d’un serveur de fichiers ou d’un appareil NAS. Cet article aborde les rubriques à prendre en compte lors du déploiement d’un partage de fichiers Azure pour une utilisation en production dans votre organisation.

## <a name="management-concepts"></a>Concepts de gestion

 Le diagramme suivant illustre la structure de gestion d’Azure Files :

![Structure de fichiers](./media/storage-files-introduction/files-concepts.png)

* **Compte de stockage** : Tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Partage** : un partage Stockage Fichier est un partage de fichiers SMB dans Azure. Tous les répertoires et fichiers doivent être créés dans un partage parent. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans la limite de la capacité totale du partage de fichiers. La capacité totale pour les partages de fichiers Premium et Standard est de 100 Tio.

* **Répertoire** : hiérarchie facultative de répertoires.

* **Fichier** : fichier du partage. Un fichier peut avoir une taille de 1 Tio maximum.

* **Format d’URL** : pour les demandes adressées à un partage de fichiers Azure avec le protocole File REST, les fichiers sont accessibles via le format d’URL suivant :

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Méthode d’accès aux données

Azure Files offre deux méthodes intégrées et pratiques d’accès aux données, que vous pouvez utiliser séparément ou conjointement pour accéder à vos données :

1. **Accès direct au cloud** : tout partage de fichiers Azure peut être monté sur [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) et/ou [Linux](storage-how-to-use-files-linux.md) avec le protocole SMB (Server Message Block) standard ou via l’API File REST. Avec SMB, les lectures et les écritures dans les fichiers du partage sont effectuées directement sur le partage de fichiers dans Azure. Pour un montage sur une machine virtuelle dans Azure, le client SMB dans le système d’exploitation doit prendre en charge au moins le protocole SMB 2.1. Pour un montage local, par exemple, sur une station de travail d’utilisateur, le client SMB pris en charge par la station de travail doit prendre en charge au moins le protocole SMB 3.0 (avec chiffrement). En plus du protocole SMB, les nouvelles applications ou les nouveaux services peuvent accéder directement au partage de fichiers via l’API REST de fichier, qui fournit une interface de programmation d’applications simple et scalable pour le développement de logiciels.
2. **Azure File Sync** : avec Azure File Sync, les partages peuvent être répliqués sur des serveurs Windows Server locaux ou dans Azure. Vos utilisateurs accèdent au partage de fichiers via Windows Server, tout comme avec un partage SMB ou NFS. Cela est utile dans les scénarios où les données sont consultées et modifiées loin d’un centre de données Azure, par exemple, dans une succursale. Les données peuvent être répliquées entre plusieurs points de terminaison Windows Server, c’est-à-dire entre plusieurs succursales. Enfin, les données peuvent être hiérarchisées dans Azure Files, de sorte que toutes les données sont toujours accessibles via le serveur, mais le serveur ne dispose pas d’une copie complète des données. Les données sont alors rappelées sans discontinuité quand elles sont ouvertes par l’utilisateur.

Le tableau suivant illustre la façon dont vos utilisateurs et les applications peuvent accéder à votre partage de fichiers Azure :

| | Accès direct au cloud | Azure File Sync |
|------------------------|------------|-----------------|
| Quels protocoles devez-vous utiliser ? | Azure Files prend en charge les protocoles SMB 2.1, SMB 3.0 et l’API REST de fichier. | Accédez à votre partage de fichiers Azure via un protocole pris en charge sur Windows Server (SMB, NFS, FTPS, etc.) |  
| Où exécutez-vous votre charge de travail ? | **Dans Azure** : Azure Files offre un accès direct à vos données. | **Localement avec un réseau lent** : les clients Windows, Linux et macOS peuvent monter un partage de fichiers Windows local comme cache rapide de votre partage de fichiers Azure. |
| Quel est le niveau d’ACL dont vous avez besoin ? | Au niveau du partage et des fichiers. | Au niveau du partage, des fichiers et de l’utilisateur. |

## <a name="data-security"></a>Sécurité des données

Azure Files propose plusieurs options intégrées pour garantir la sécurité des données :

* Prise en charge du chiffrement dans les deux protocoles réseau : chiffrement SMB 3.0 et File REST via le protocole HTTPS. Par défaut : 
    * Les clients qui prennent en charge le chiffrement SMB 3.0 envoient et reçoivent des données sur un canal chiffré.
    * Les clients qui ne prennent pas en charge SMB 3.0 avec chiffrement peuvent communiquer au sein du centre de données sur SMB 2.1 ou SMB 3.0 sans chiffrement. Les clients SMB ne sont pas autorisés à communiquer entre centres de données sur SMB 2.1 ou SMB 3.0 sans chiffrement.
    * Les clients peuvent communiquer sur l’API REST de fichier avec HTTP ou HTTPS.
* Chiffrement au repos ([Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)) : Storage Service Encryption (SSE) est activé pour tous les comptes de stockage. Les données au repos sont chiffrées avec des clés entièrement gérées. Le chiffrement au repos n’augmente pas les coûts de stockage, ni ne réduit le niveau de performance. 
* Spécification facultative des données chiffrées en transit : quand Azure Files est sélectionné, il refuse l’accès aux données sur des canaux non chiffrés. Plus précisément, seules les connexions HTTPS et SMB 3.0 avec chiffrement sont autorisées.

    > [!Important]  
    > Si vous demandez un transfert sécurisé des données, les anciens clients SMB qui ne sont pas capables de communiquer avec SMB 3.0 échouent. Pour plus d’informations, voir [Montage sur Windows](storage-how-to-use-files-windows.md), [Montage sur Linux](storage-how-to-use-files-linux.md) et [Montage sur macOS](storage-how-to-use-files-mac.md).

Pour une sécurité maximale, nous vous recommandons vivement de toujours activer le chiffrement au repos et le chiffrement des données en transit chaque fois que vous utilisez des clients modernes pour accéder à vos données. Par exemple, si vous devez monter un partage sur une machine virtuelle Windows Server 2008 R2 qui prend uniquement en charge SMB 2.1, vous devez autoriser le trafic non chiffré sur votre compte de stockage, car SMB 2.1 ne prend pas en charge le chiffrement.

Si vous utilisez Azure File Sync pour accéder à votre partage de fichiers Azure, nous utilisons toujours HTTPS et SMB 3.0 avec chiffrement pour synchroniser vos données sur vos serveurs Windows Server, que vous exigiez ou non le chiffrement des données au repos.

## <a name="file-share-performance-tiers"></a>Niveaux de performances de partage de fichiers

Azure Files offre deux niveaux de performances : Standard et Premium.

### <a name="standard-file-shares"></a>Partages de fichiers Standard

Les partages de fichiers Standard s’appuient sur des disques durs (HDD). Les partages de fichiers Standard offrent des performances fiables pour les charges de travail d’E/S moins sensibles à la variabilité des performances, telles que les partages de fichiers à usage général et les environnements de Dev/Test. Les partages de fichiers standard sont disponibles uniquement dans le cadre d’un modèle de facturation avec paiement à l’utilisation.

> [!IMPORTANT]
> Si vous souhaitez utiliser des partages de fichiers supérieurs à 5 Tio, consultez la section [Intégrer à des partages de fichiers plus grands (niveau Standard)](#onboard-to-larger-file-shares-standard-tier) pour connaître les étapes d’intégration, ainsi que les restrictions et la disponibilité régionale.

### <a name="premium-file-shares"></a>Partages de fichiers Premium

Les partages de fichiers Premium s’appuient sur des disques SSD. Les partages de fichiers Premium offrent de façon constante des performances élevées et une faible latence (à un chiffre en millisecondes pour la plupart des opérations d’E/S) pour les charges de travail gourmandes en E/S. Ils sont ainsi adaptés à un vaste éventail de charges de travail, telles que les bases de données, l’hébergement de site web et les environnements de développement. Les partages de fichiers Premium sont disponibles uniquement dans le cadre d’un modèle de facturation avec approvisionnement. Les partages de fichiers Premium utilisent un modèle de déploiement distinct des partages de fichiers Standard.

La sauvegarde Azure est disponible pour les partages de fichiers Premium, sachant qu’Azure Kubernetes Service prend en charge les partages de fichiers Premium dans la version 1.13 et versions ultérieures.

Si vous souhaitez savoir comment créer un partage de fichiers Premium, consultez notre article sur le sujet : [Guide pratique pour créer un compte de stockage de fichiers Premium Azure](storage-how-to-create-premium-fileshare.md).

Actuellement, vous ne pouvez pas convertir directement un partage de fichiers Standard en partage de fichiers Premium, et vice-versa. Si vous souhaitez basculer vers l’un ou l’autre de ces niveaux, vous devez créer un nouveau partage de fichiers dans le niveau voulu, puis copier manuellement les données depuis votre partage d’origine sur le partage que vous avez créé. Vous pouvez effectuer cette opération en utilisant un des outils de copie pris en charge par Azure Files, tel que Robocopy ou AzCopy.

> [!IMPORTANT]
> Les partages de fichiers Premium sont disponibles avec le stockage LRS dans la plupart des régions qui offrent des comptes de stockage et avec le stockage ZRS dans moins de régions. Pour savoir si les partages de fichiers Premium sont actuellement disponibles dans votre région, consultez la page des [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pour Azure. Pour connaître les régions qui prennent en charge ZRS, consultez [Couverture du support et disponibilité régionale](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> Pour nous aider à hiérarchiser les nouvelles régions et les fonctionnalités du niveau Premium, répondez à ce [sondage](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>Partages approvisionnés

Les partages de fichiers Premium sont approvisionnés selon un ratio Gio/IOPS/débit fixe. Pour chaque Gio approvisionné, le partage reçoit un débit d’une IOPS et de 0,1 Mio/s, dans les limites maximales autorisées par partage. L’approvisionnement minimal autorisé est de 100 Gio avec un minimum d’E/S par seconde/débit.

Dans la mesure du possible, tous les partages peuvent atteindre en rafale jusqu’à trois IOPS par Gio de stockage approvisionné pendant 60 minutes ou plus, selon la taille du partage. Les nouveaux partages démarrent avec le crédit de rafale complète basé sur la capacité approvisionnée.

Les partages doivent être provisionnés par incréments de 1 Gio. La taille minimale est de 100 Gio, la taille suivante de 101 Gio, et ainsi de suite.

> [!TIP]
> IOPS de base = 1 * par Gio provisionné. (Jusqu’à 100 000 IOPS maximum).
>
> Limite de rafale = 3 * IOPS de base. (Jusqu’à 100 000 IOPS maximum).
>
> Débit de sortie = 60 Mio/s + 0,06 * Gio provisionnés
>
> Débit d’entrée = 40 Mio/s + 0,04 * Gio provisionnés

La taille de partage approvisionnée est spécifiée par le quota de partage. Le quota du partage peut être augmenté à tout moment, mais il ne peut être réduit qu’au bout des 24 heures suivant la dernière augmentation. À l’issue des 24 heures sans augmentation de quota, vous pouvez diminuer le quota du partage autant de fois que vous le souhaitez, jusqu’à ce que vous l’augmentiez à nouveau. Les modifications de mise à l’échelle IOPS/débit prennent effet quelques minutes après le changement de taille.

Il est possible de diminuer la taille de votre partage provisionné en dessous de votre Gio utilisé. Si vous faites cela, vous ne perdez pas les données, mais vous êtes toujours facturé pour la taille utilisée et recevez les performances (IOPS de base, débit et IOPS en rafale) du partage provisionné, pas de la taille utilisée.

Le tableau suivant illustre quelques exemples de ces formules pour les tailles de partage provisionné :

|Capacité (Gio) | IOPS de base | IOPS en rafale | Sortie (Mio/s) | Entrée (Mio/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Jusqu’à 300     | 66   | 44   |
|500         | 500     | Jusqu’à 1 500   | 90   | 60   |
|1 024       | 1 024   | Jusqu’à 3 072   | 122   | 81   |
|5 120       | 5 120   | Jusqu’à 15 360  | 368   | 245   |
|10 240      | 10 240  | Jusqu’à 30 720  | 675 | 450   |
|33 792      | 33 792  | Jusqu’à 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Jusqu’à 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Jusqu’à 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Les performances des partages de fichiers sont soumises aux limites du réseau des machines, à la bande passante réseau disponible, aux tailles d’e/s, au parallélisme, entre autres nombreux facteurs. Par exemple, sur la base d’un test interne avec des tailles d’e/s en lecture/écriture de 8 Kio, une seule machine virtuelle Windows, *F16s_v2 standard*, connectée au partage de fichiers Premium sur SMB pourrait atteindre 20 000 e/s par seconde en écriture et 15 000 e/s par seconde. Avec les tailles d’e/s en lecture/écriture de 512 Mio, la même machine virtuelle peut atteindre 1,1 Gio/s en sortie et 370 Mio/s de débit d’entrée. Pour obtenir une mise à l’échelle des performances maximales, répartissez la charge entre plusieurs machines virtuelles. Reportez-vous au [guide de résolution des problèmes](storage-troubleshooting-files-performance.md) pour certains problèmes de performances courants et leurs solutions de contournement.

#### <a name="bursting"></a>Mode en rafales

Les partages de fichiers Premium peuvent rapidement accroître leur IOPS jusqu’à multiplier leur nombre par trois. Ce mode en rafales est automatisé et fonctionne selon un système de crédits. Il fonctionne dans la mesure des possibilités et la limite de rafale n’est pas une garantie : les partages de fichiers peuvent croître par rafales *jusqu’à* cette limite.

Des crédits s’accumulent dans un compartiment à rafales chaque fois que le trafic de votre partage de fichiers se trouve en dessous des IOPS de base. Par exemple, un partage de 100 Gio dispose de 100 IOPS de base. Si le trafic réel sur le partage est de 40 IOPS pour un intervalle spécifique de 1 seconde, les 60 IOPS inutilisées sont créditées dans un compartiment à rafales. Ces crédits sont ensuite utilisés lorsque des opérations dépassent les IOPS de base.

> [!TIP]
> Taille du compartiment à rafales = IOPS de base * 2 * 3600.

Chaque fois qu’un partage dépasse les IOPS de base et qu’il dispose de crédits dans un compartiment à rafales, il est augmenté par rafales. Les partages peuvent continuer à croître tant qu’il reste des crédits, même si les partages inférieurs à 50 Tio ne demeureront à la limite de la rafale de croissance que sur une durée pouvant atteindre une heure. Les partages supérieurs à 50 Tio peuvent techniquement dépasser cette limite d’une heure, pour atteindre deux heures, mais cela dépend du nombre de crédits de rafale accumulés. Chaque e/s située au-delà des IOPS de base consomme un crédit ; une fois que tous les crédits sont consommés, le partage retourne aux IOPS de base.

Les crédits de partage présentent trois états :

- En hausse, lorsque le partage de fichiers utilise un nombre inférieur à celui des IOPS de base.
- En baisse, lorsque le partage de fichiers s’accroît.
- Constant, lorsqu’il n’y a aucun crédit ou IOPS de base en cours d’utilisation.

Au départ, les nouveaux partages de fichiers se voient attribuer un nombre total de crédits dans leur compartiment à rafales. Les crédits de rafale ne seront pas augmentés si les IOPS du partage chutent en dessous des IOPS de base, en raison de la limitation par le serveur.

## <a name="file-share-redundancy"></a>Redondance de partage de fichiers

Les partages Azure Files prennent en charge quatre options de redondance des données : le stockage localement redondant (LRS), le stockage redondant interzone (ZRS), le stockage géoredondant (GRS) et le stockage géoredondant interzone (GZRS) (préversion).

Les partages Premium Azure Files prennent en charge les stockages LRS et ZRS, ZRS étant actuellement disponible dans moins de régions.

Les sections suivantes décrivent les différences entre les différentes options de redondance :

### <a name="locally-redundant-storage"></a>Stockage localement redondant

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Stockage redondant dans une zone

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Stockage géo-redondant

> [!Warning]  
> Si vous utilisez votre partage de fichiers Azure comme point de terminaison cloud sur un compte de stockage GRS, vous ne devez pas initier de basculement de compte de stockage. Cela provoquera en effet un arrêt de la synchronisation et pourra entraîner une perte inattendue de données dans le cas de fichiers nouvellement hiérarchisés. En cas de perte d’une région Azure, Microsoft déclenchera le basculement du compte de stockage en employant une méthode compatible avec Azure File Sync.

Le stockage géoredondant (GRS) est conçu pour fournir au moins 99,99999999999999 % de durabilité des objets sur une année données en répliquant vos données vers une région secondaire se situant à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

Si vous optez pour le stockage géoredondant avec accès en lecture (RA-GRS), vous devez savoir qu’Azure Files ne prend en charge ce type de stockage dans aucune région pour l’instant. Les partages de fichiers dans le compte de stockage RA-GRS fonctionnent comme ils le feraient dans des comptes GRS, et ils sont facturés au tarif GRS.

Le stockage géoredondant réplique les données vers un autre centre de données dans une région secondaire, mais elles ne peuvent être lues que si Microsoft initie un basculement de la région primaire vers la région secondaire.

Dans le cas d’un compte de stockage GRS, toutes les données sont d’abord répliquées avec le stockage localement redondant (LRS). Une mise à jour est au préalable validée dans la région primaire et répliquée avec le stockage localement redondant. Elle est ensuite répliquée de manière asynchrone dans la région secondaire avec le stockage géoredondant. Lors de l’écriture des données dans l’emplacement secondaire, celles-ci sont également répliquées au sein de cet emplacement avec le stockage localement redondant.

Les régions primaire et secondaire gèrent les réplicas dans des domaines d’erreur et de mise à niveau distincts, au sein d’une unité d’échelle de stockage. Cette unité représente l’unité de réplication de base au sein du centre de données. La réplication à ce niveau est assurée par le stockage localement redondant ; pour en savoir plus, voir [Stockage localement redondant (LRS) : redondance des données à faible coût pour le stockage Azure](../common/storage-redundancy-lrs.md).

Gardez ces points à l’esprit au moment de choisir une option de réplication :

* Le stockage géoredondant interzone (GZRS) (préversion) offre une haute disponibilité et une durabilité maximale en répliquant les données de manière synchrone sur trois zones de disponibilité Azure, puis en répliquant les données de manière asynchrone dans la région secondaire. Vous pouvez également activer l’accès en lecture à la région secondaire. Le stockage GZRS est conçu pour fournir une durabilité des objets d’au moins 99,99999999999999 % (16 chiffres 9) sur une année donnée. Pour plus d’informations sur le stockage GZRS, consultez l’article [Stockage géo-redondant dans une zone pour la haute disponibilité et la durabilité maximale (préversion)](../common/storage-redundancy-gzrs.md).
* Le stockage redondant interzone (ZRS), qui assure la haute disponibilité avec réplication synchrone, peut représenter dans certains scénarios un meilleur choix que le stockage géoredondant. Pour plus d’informations sur le stockage redondant dans une zone, voir [ZRS](../common/storage-redundancy-zrs.md).
* Une réplication asynchrone implique un délai entre le moment où les données sont écrites dans la région primaire et celui où elles sont répliquées dans la région secondaire. En cas de sinistre régional, les modifications qui n’ont pas encore été répliquées vers la région secondaire risquent d’être perdues si ces données ne peuvent pas être récupérées à partir de la région primaire.
* Avec le stockage géoredondant, le réplica n’est disponible pour l’accès en lecture ou écriture que si Microsoft lance un basculement vers la région secondaire. En cas de basculement, vous aurez accès en lecture et écriture à ces données après le basculement. Pour plus d’informations, voir [Conseils sur la récupération d’urgence](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Intégrer à des partages de fichiers plus grands (niveau Standard)

Cette section s’applique uniquement aux partages de fichiers Standard. Tous les partages de fichiers Premium sont disponibles avec une capacité de 100 Tio.

### <a name="restrictions"></a>Restrictions

- La conversion de compte LRS/ZRS en GRS/GZRS n’est pas possible pour les comptes de stockage avec des partages de fichiers volumineux activés.

### <a name="regional-availability"></a>Disponibilité régionale

Le partage de fichiers Standard est disponible dans toutes les régions, jusqu’à 5 Tio. Dans certaines régions, il est disponible avec une limite de 100 Tio ; ces régions sont listées dans le tableau suivant :

|Région |Redondance prise en charge |
|-------|---------|
|Australie Est |LRS     |
|Sud-Australie Est|LRS |
|Centre du Canada  |LRS     |
|Est du Canada     |LRS     |
|Inde centrale  |LRS     |
|USA Centre*   |LRS     |
|Asie Est      |LRS     |
|USA Est*        |LRS     |
|USA Est 2*      |LRS     |
|France Centre |LRS, ZRS|
|France Sud   |LRS     |
|Centre-Nord des États-Unis |LRS     |
|Europe Nord   |LRS     |
|Inde Sud    |LRS     |
|Asie Sud-Est |LRS, ZRS|
|Centre-USA Ouest|LRS     |
|Europe Ouest*    |LRS, ZRS|
|USA Ouest*        |LRS     |
|USA Ouest 2      |LRS, ZRS|

\* Pris en charge pour les nouveaux comptes ; les comptes existants n’ont pas tous terminé le processus de mise à niveau. Vous pouvez vérifier si vos comptes de stockage existants ont terminé le processus de mise à niveau en tentant d’[activer les partages de fichiers volumineux](storage-files-how-to-create-large-file-share.md).

Pour nous aider à hiérarchiser les nouvelles régions et les nouvelles fonctionnalités, veuillez répondre à ce [sondage](https://aka.ms/azurefilesatscalesurvey).

### <a name="enable-and-create-larger-file-shares"></a>Activer et créer des partages de fichiers plus volumineux

Pour commencer à utiliser des partages de fichiers plus volumineux, consultez notre article [Guide pratique pour activer et créer des partages de fichiers volumineux](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>Modèle de croissance des données

Aujourd’hui, la taille maximale d’un partage de fichiers Azure est de 100 Tio. En raison de cette limitation actuelle, vous devez prendre en compte la croissance attendue des données quand vous déployez un partage de fichiers Azure.

Vous pouvez synchroniser plusieurs partages de fichiers Azure sur un même serveur de fichiers Windows avec Azure File Sync. Cela vous permet d’inclure dans Azure File Sync des partages de fichiers plus anciens et très volumineux que vous pouvez avoir localement. Pour plus d’informations, voir [Planification d’un déploiement Azure File Sync](storage-files-planning.md).

## <a name="data-transfer-method"></a>Méthode de transfert des données

Il existe de nombreuses options pour facilement transférer en bloc les données d’un partage de fichiers existant, par exemple, un partage de fichiers local, dans Azure Files. En voici quelques-unes connues (liste non exhaustive) :

* **Azure File Sync** : lors de la première synchronisation entre un partage de fichiers Azure (un « point de terminaison cloud ») et un espace de noms de répertoire Windows (un « point de terminaison de serveur »), Azure File Sync réplique toutes les données du partage de fichiers existant sur Azure Files.
* **[Azure Import Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : Le service Azure Import/Export permet de transférer en toute sécurité des volumes importants de données dans un partage de fichiers Azure en expédiant des disques durs vers un centre de données Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy est un outil de copie bien connu fourni avec Windows et Windows Server. Robocopy peut servir à transférer des données dans Azure Files en montant le partage de fichiers localement, puis en utilisant l’emplacement monté comme destination de la commande Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy est un utilitaire de ligne de commande conçu pour copier des données à destination et à partir d’Azure Files, ou d’un stockage blob Azure, en utilisant des commandes simples avec des performances optimales.

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md)
* [Déploiement d’Azure Files](storage-files-deployment-guide.md)
* [Déploiement d’Azure File Sync](storage-sync-files-deployment-guide.md)
