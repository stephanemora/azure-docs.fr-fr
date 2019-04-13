---
title: Planification d’un déploiement Azure Files | Microsoft Docs
description: Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d4361fc37d01b351d20a273aa39f558e9b00faa4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525923"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planification d’un déploiement Azure Files

[Azure Files](storage-files-introduction.md) offre des partages de fichiers managés dans le cloud qui sont accessibles via le protocole SMB standard. Comme Azure Files est entièrement managé, son déploiement dans des scénarios de production est beaucoup plus simple que le déploiement et la gestion d’un serveur de fichiers ou d’un appareil NAS. Cet article aborde les rubriques à prendre en compte lors du déploiement d’un partage de fichiers Azure pour une utilisation en production dans votre organisation.

## <a name="management-concepts"></a>Concepts de gestion

 Le diagramme suivant illustre la structure de gestion d’Azure Files :

![Structure de fichiers](./media/storage-files-introduction/files-concepts.png)

* **Compte de stockage** : Tous les accès à Azure Storage passent par un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Partage** : un partage Stockage Fichier est un partage de fichiers SMB dans Azure. Tous les répertoires et fichiers doivent être créés dans un partage parent. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans la limite de la capacité totale de 5 Tio du partage de fichiers.

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
    * Les clients qui prennent en charge le chiffrement SMB 3.0 envoient et recevoir des données via un canal chiffré.
    * Les clients qui ne prennent pas en charge SMB 3.0 avec chiffrement peuvent communiquer intra-centre de données sur SMB 2.1 ou SMB 3.0 sans chiffrement. Les clients SMB ne sont pas autorisés à communiquer entre centres de données sur SMB 2.1 ou SMB 3.0 sans chiffrement.
    * Les clients peuvent communiquer sur l’API REST de fichier avec HTTP ou HTTPS.
* Chiffrement au repos ([Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)) : Storage Service Encryption (SSE) est activé pour tous les comptes de stockage. Les données au repos sont chiffrées avec des clés entièrement gérées. Le chiffrement au repos n’augmente pas les coûts de stockage, ni ne réduit le niveau de performance. 
* Spécification facultative des données chiffrées en transit : quand Azure Files est sélectionné, il refuse l’accès aux données sur des canaux non chiffrés. Plus précisément, seules les connexions HTTPS et SMB 3.0 avec chiffrement sont autorisées.

    > [!Important]  
    > Si vous demandez un transfert sécurisé des données, les anciens clients SMB qui ne sont pas capables de communiquer avec SMB 3.0 échouent. Pour plus d’informations, voir [Montage sur Windows](storage-how-to-use-files-windows.md), [Montage sur Linux](storage-how-to-use-files-linux.md) et [Montage sur macOS](storage-how-to-use-files-mac.md).

Pour une sécurité maximale, nous vous recommandons vivement de toujours activer le chiffrement au repos et le chiffrement des données en transit chaque fois que vous utilisez des clients modernes pour accéder à vos données. Par exemple, si vous devez monter un partage sur une machine virtuelle Windows Server 2008 R2 qui prend uniquement en charge SMB 2.1, vous devez autoriser le trafic non chiffré sur votre compte de stockage, car SMB 2.1 ne prend pas en charge le chiffrement.

Si vous utilisez Azure File Sync pour accéder à votre partage de fichiers Azure, nous utilisons toujours HTTPS et SMB 3.0 avec chiffrement pour synchroniser vos données sur vos serveurs Windows Server, que vous exigiez ou non le chiffrement des données au repos.

## <a name="file-share-performance-tiers"></a>Niveaux de performances de partage de fichiers

Azure Files offre deux niveaux de performances : standard et premium.

* Les **partages de fichiers Standard** s’appuient sur des lecteurs de disque dur (HDD) rotatifs qui offrent des performances fiables pour les charges de travail d’E/S moins sensibles à la variabilité des performances, telles que les partages de fichiers à usage général et les environnements de Dev/Test. Les partages de fichiers standard sont disponibles uniquement dans le cadre d’un modèle de facturation avec paiement à l’utilisation.
* Les **partages de fichiers Premium (préversion)** s’appuient sur des disques SSD qui offrent de façon constante des performances élevées et une faible latence (à un chiffre en millisecondes pour la plupart des opérations d’E/S) pour les charges de travail les plus gourmandes en E/S. Ils sont ainsi adaptés à un vaste éventail de charges de travail telles que les bases de données, l’hébergement de site web, les environnements de développement, etc. Les partages de fichiers Premium sont disponibles uniquement dans le cadre d’un modèle de facturation avec approvisionnement. Partages de fichiers Premium utilisent un modèle de déploiement distinct à partir de partages de fichiers standard. Si vous souhaitez apprendre à créer un partage de fichiers premium, consultez notre article sur le sujet : [Comment créer un compte de stockage Azure premium fichier](storage-how-to-create-premium-fileshare.md).

> [!IMPORTANT]
> Fichier Premium partages sont toujours en version préliminaire, disponible uniquement pour le stockage LRS et sont uniquement disponibles dans un sous-ensemble de régions avec prise en charge de sauvegarde Azure soient disponibles dans Sélectionnez les régions :

|Région disponible  |Support Sauvegarde Azure  |
|---------|---------|
|Est des États-Unis 2      | Oui|
|USA Est       | Oui|
|USA Ouest       | Non  |
|Ouest des États-Unis 2      | Non  |
|USA Centre    | Non  |
|Europe Nord  | Non  |
|Europe Ouest   | Oui|
|Asie       | Oui|
|Japon Est    | Non  |
|Centre de la Corée | Non  |
|Australie Est| Non  |

### <a name="provisioned-shares"></a>Partages approvisionnés

Partages de fichiers Premium (version préliminaire) sont configurés selon un ratio Gio/IOPS/débit fixe. Pour chaque Gio approvisionné, le partage reçoit un débit d’une IOPS et de 0,1 Mio/s, dans les limites maximales autorisées par partage. L’approvisionnement minimal autorisé est de 100 Gio avec un minimum d’E/S par seconde/débit. Taille du partage peut être augmentée à toute heure et une réduction à tout moment, mais peut être réduite à une fois toutes les 24 heures depuis l’augmentation de la dernière.

Dans la mesure du possible, tous les partages peuvent atteindre en rafale jusqu’à trois IOPS par Gio de stockage approvisionné pendant 60 minutes ou plus, selon la taille du partage. Les nouveaux partages démarrent avec le crédit de rafale complète basé sur la capacité approvisionnée.

Tous les partages peuvent croître jusqu'à au moins 100 débit d’e/s et la cible de 100 Mio/s. Partages doivent être approvisionnés incréments de 1 Go. Taille minimale est de 100 Go, la taille suivante est GIO 101 et ainsi de suite.

> [!TIP]
> Ligne de base d’e/s = 100 + 1 * approvisionné Gio. (Jusqu'à un maximum de 100 000 e/s).
>
> Limite de rafale = 3 * ligne de base d’e/s. (Jusqu'à un maximum de 100 000 e/s).
>
> taux de sortie = 60 Mio/s + 0,06 * approvisionné Gio
>
> taux d’entrée = 40 Mio/s + 0.04 * approvisionné Gio

Taille du partage peut être augmentée à toute heure et une réduction à tout moment, mais peut être réduite à une fois toutes les 24 heures depuis l’augmentation de la dernière. Modifications de mise à l’échelle d’IOPS/débit entreront en vigueur dans les 24 heures après le changement de taille.

Le tableau suivant illustre quelques exemples de ces formules pour les tailles de partage configuré :

(Tailles dénoté par un * sont en version préliminaire publique limitée)

|Capacité (Go) | IOPS de base | Limite de rafale | Sortie (Mio/s) | Entrée (Mio/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Jusqu’à 300     | 66   | 44   |
|500         | 500     | Jusqu'à 1 500   | 90   | 60   |
|1 024       | 1 024   | Jusqu'à 3 072   | 122   | 81   |
|5 120       | 5 120   | Jusqu'à 15 360  | 368   | 245   |
|10,240 *     | 10,240  | Jusqu'à 30 720  | 675 | 450   |
|33,792 *     | 33,792  | Jusqu'à 100 000 | 2,088 | 1,392   |
|51,200 *     | 51,200  | Jusqu'à 100 000 | 3 132 | 2,088   |
|102,400 *    | 100 000 | Jusqu'à 100 000 | 6,204 | 4,136   |

Actuellement, taille de partage de fichier jusqu'à 5 To est en version préliminaire publique, tandis que les tailles jusqu'à 100 To sont en version préliminaire publique limitée, pour demander l’accès à la version préliminaire publique limitée complète [cette enquête.](https://aka.ms/azurefilesatscalesurvey)

### <a name="bursting"></a>Rupture

Partages de fichiers Premium peuvent croître leur IOPS jusqu'à un facteur de trois. Rupture est automatisé et fonctionne selon un système de crédit. Fonctionne sur une mesure du possible et la limite de croissance de rupture n’est pas une garantie, de partages de fichiers peuvent croître *jusqu'à* la limite.

Crédits s’accumulent dans un compartiment de rafale chaque fois que le trafic pour vos partages de fichiers est sous la ligne de base e/s. Par exemple, un partage de Gio 100 a planifié 100 e/s. Si le trafic réel sur le partage a été 40 e/s pour un intervalle de 1 seconde spécifique, les IOPS inutilisés 60 sont créditées dans un compartiment en rafale. Ces crédits seront ensuite être utilisées lors d’opérations dépasserait la ligne de base e/s.

> [!TIP]
> Taille du compartiment de limite de rafale = Baseline_IOPS * 2 * 3600.

Chaque fois qu’un partage dépasse la ligne de base e/s et a des crédits dans un compartiment de rafale, il sera expédié. Partages peuvent continuer à croître tant que restant crédits, bien que les partages inférieures à 50 TIO restera uniquement atteint la limite de croissance pour atteindre une heure. Partages supérieur à 50 TIO techniquement de dépasser cette limite d’une heure, les deux heures, mais cela repose sur le nombre de crédits de rafale à recevoir. Chaque e/s au-delà de la ligne de base e/s consomme un crédit et une fois que tous les crédits consommés le partage retourne à la ligne de base e/s.

Les crédits de partage ont trois états :

- Comptabiliser, lorsque le partage de fichiers est inférieure à la ligne de base e/s à l’aide.
- En baisse, lorsque le partage de fichiers est de rupture.
- E/s restant à zéro, lorsqu’il n’y a aucune ligne de base ou crédits sont en cours d’utilisation.

Nouveau début de partages de fichier avec le nombre total de crédits dans le compartiment de sa croissance.

## <a name="file-share-redundancy"></a>Redondance de partage de fichiers

Les partages standards de fichiers Azure prend en charge trois options de redondance de données : stockage localement redondant (LRS), stockage redondant (ZRS) et stockage géo-redondant (GRS).

Azure premium de fichiers partage uniquement prend en charge stockage localement redondant (LRS).

Les sections suivantes décrivent les différences entre les différentes options de redondance :

### <a name="locally-redundant-storage"></a>Stockage localement redondant

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Stockage redondant dans une zone

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Stockage géo-redondant

> [!Warning]  
> Si vous utilisez votre partage de fichiers Azure comme point de terminaison cloud sur un compte de stockage GRS, vous ne devez pas initier de basculement de compte de stockage. Cela provoquera en effet un arrêt de la synchronisation et pourra entraîner une perte inattendue de données dans le cas de fichiers nouvellement hiérarchisés. En cas de perte d’une région Azure, Microsoft déclenchera le basculement du compte de stockage en employant une méthode compatible avec Azure File Sync.

Le stockage géoredondant (GRS) est conçu pour fournir au moins 99,99999999999999 % de durabilité des objets sur une année données en répliquant vos données vers une région secondaire se situant à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

Si vous optez pour le stockage géo-redondant avec accès en lecture (RA-GRS), vous devez savoir que fichiers Azure ne prend pas en charge le stockage géo-redondant avec accès en lecture (RA-GRS) dans n’importe quelle région pour l’instant. Partages de fichiers dans le compte de stockage RA-GRS travailler comme ils le feraient dans les comptes GRS et sont facturés à prix GRS.

Le stockage géoredondant réplique les données vers un autre centre de données dans une région secondaire, mais elles ne peuvent être lues que si Microsoft initie un basculement de la région primaire vers la région secondaire.

Pour un compte de stockage avec GRS activé, toutes les données sont d’abord répliquées avec le stockage localement redondant (LRS). Une mise à jour est au préalable validée dans la région primaire et répliquée avec le stockage localement redondant. Elle est ensuite répliquée de manière asynchrone dans la région secondaire avec le stockage géoredondant. Lors de l’écriture des données dans l’emplacement secondaire, celles-ci sont également répliquées au sein de cet emplacement avec le stockage localement redondant.

Les régions primaire et secondaire gèrent les réplicas dans des domaines d’erreur et de mise à niveau distincts, au sein d’une unité d’échelle de stockage. Cette unité représente l’unité de réplication de base au sein du centre de données. Réplication à ce niveau est fournie par LRS ; Pour plus d’informations, consultez [stockage localement redondant (LRS) : redondance des données à faible coût pour le stockage Azure](../common/storage-redundancy-lrs.md).

Gardez ces points à l’esprit au moment de choisir une option de réplication :

* Stockage redondant dans une zone (ZRS) fournit haute disponibilité avec réplication synchrone et peut être un meilleur choix pour des scénarios que GRS. Pour plus d’informations sur le stockage redondant dans une zone, voir [ZRS](../common/storage-redundancy-zrs.md).
* Une réplication asynchrone implique un délai entre le moment où les données sont écrites dans la région primaire et celui où elles sont répliquées dans la région secondaire. En cas de sinistre régional, les modifications qui n’ont pas encore été répliquées vers la région secondaire risquent d’être perdues si ces données ne peuvent pas être récupérées à partir de la région primaire.
* Avec le stockage géoredondant, le réplica n’est disponible pour l’accès en lecture ou écriture que si Microsoft lance un basculement vers la région secondaire. En cas de basculement, vous aurez accès en lecture et écriture à ces données après le basculement. Pour plus d’informations, voir [Conseils sur la récupération d’urgence](../common/storage-disaster-recovery-guidance.md).

## <a name="data-growth-pattern"></a>Modèle de croissance des données

Aujourd'hui, la taille maximale d’un partage de fichiers Azure est 5 To (100 To pour le fichier de premium partagent une version préliminaire publique limitée). En raison de cette limitation actuelle, vous devez prendre en compte la croissance attendue des données quand vous déployez un partage de fichiers Azure.

Vous pouvez synchroniser plusieurs partages de fichiers Azure sur un même serveur de fichiers Windows avec Azure File Sync. Cela vous permet d’inclure dans Azure File Sync des partages de fichiers plus anciens et très volumineux que vous pouvez avoir localement. Pour plus d’informations, voir [Planification d’un déploiement Azure File Sync](storage-files-planning.md).

## <a name="data-transfer-method"></a>Méthode de transfert des données

Il existe de nombreuses options pour facilement transférer en bloc les données d’un partage de fichiers existant, par exemple, un partage de fichiers local, dans Azure Files. En voici quelques-unes connues (liste non exhaustive) :

* **Azure File Sync** : lors de la première synchronisation entre un partage de fichiers Azure (un « point de terminaison cloud ») et un espace de noms de répertoire Windows (un « point de terminaison de serveur »), Azure File Sync réplique toutes les données du partage de fichiers existant sur Azure Files.
* **[Azure Import Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : Le service Azure Import/Export permet de transférer en toute sécurité des volumes importants de données dans un partage de fichiers Azure en expédiant des disques durs vers un centre de données Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy est un outil de copie bien connu fourni avec Windows et Windows Server. Robocopy peut servir à transférer des données dans Azure Files en montant le partage de fichiers localement, puis en utilisant l’emplacement monté comme destination de la commande Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)** : AzCopy est un utilitaire de ligne de commande conçu pour copier des données à destination et à partir d’Azure Files, ou d’un stockage blob Azure, en utilisant des commandes simples avec des performances optimales. AzCopy est disponible pour Windows et Linux.

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md)
* [Déploiement d’Azure Files](storage-files-deployment-guide.md)
* [Déploiement d’Azure File Sync](storage-sync-files-deployment-guide.md)
