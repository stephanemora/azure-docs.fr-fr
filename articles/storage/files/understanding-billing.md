---
title: Comprendre la facturation d’Azure Files | Microsoft Docs
description: Découvrez comment interpréter les modèles de facturation provisionné et avec paiement à l’utilisation pour les partages de fichiers Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bb608492327baae958c32be05d8f2a1bb4dbfbf
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226639"
---
# <a name="understand-azure-files-billing"></a>Comprendre la facturation d’Azure Files
Azure Files propose deux modèles de facturation distincts : provisionné et paiement à l’utilisation. Le modèle provisionné est disponible uniquement pour les partages de fichiers Premium, qui sont déployés dans le type de compte de stockage **FileStorage**. Le modèle de paiement à l’utilisation est disponible uniquement pour les partages de fichiers standard, qui sont déployés dans le type de compte de stockage **Usage général version 2 (GPv2)** . Cet article explique comment fonctionnent les deux modèles pour vous aider à comprendre votre facture mensuelle Azure Files.

Pour plus d’informations sur la tarification d’Azure Files, consultez la [page de tarification d’Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Unités de stockage    
Azure Files utilise des unités de mesure binaires pour représenter la capacité de stockage : Kio, Mio, Gio et Tio. Votre système d’exploitation peut utiliser ou non la même unité de mesure ou le même système de comptage.

### <a name="windows"></a>Windows

Le système d’exploitation Windows et Azure Files mesurent la capacité de stockage à l’aide du système de comptage binaire, mais il existe une différence lors de l’étiquetage des unités. Azure Files étiquète sa capacité de stockage avec des unités de mesure binaires, tandis que Windows étiquète sa capacité de stockage en unités de mesure décimales. Quand Windows rapporte la capacité de stockage, il ne la convertit pas du système binaire au système décimal.

|Acronyme  |Définition  |Unité  |Windows affiche comme  |
|---------|---------|---------|---------|
|Kio     |1 024 octets         |kibioctet         |Ko (kilo-octet)         |
|MiB     |1 024 Kio (1 048 576 octets)         |mébioctet         |Mo (méga-octet)         |
|Gio     |1 024 Mio (1 073 741 824 octets)         |gibioctet         |Go (giga-octet)         |
|Tio     |1 024 Gio (1 099 511 627 776 octets)         |tébioctet         |To (téra-octet)         |

### <a name="macos"></a>macOS

Découvrez [comment iOS et macOS rapportent la capacité de stockage](https://support.apple.com/HT201402) sur le site web d’Apple pour déterminer le système de comptage utilisé.

### <a name="linux"></a>Linux

Un autre système de comptage peut être utilisé par chaque système d’exploitation ou chaque composant logiciel. Consultez leur documentation pour déterminer comment ils rapportent la capacité de stockage.

## <a name="provisioned-model"></a>Modèle provisionné
Azure Files utilise un modèle provisionné pour les partages de fichiers Premium. Dans un modèle provisionné, vous spécifiez de manière proactive au service Azure Files vos besoins en matière de stockage au lieu de payer en fonction de votre utilisation. Cela est comparable à un achat de matériel en local. En effet, lorsque vous provisionnez un partage de fichiers Azure avec une certaine quantité de stockage, vous payez ce stockage indépendamment de l’utilisation que vous en faites, à l’instar du support physique, que vous payez directement et pas uniquement lorsque vous commencez à l’utiliser. En revanche, contrairement à un support physique local, les partages de fichiers provisionnés peuvent être rapidement mis à l’échelle en fonction de vos caractéristiques de stockage et de performances d’e/s.

Lorsque vous provisionnez un partage de fichiers Premium, vous spécifiez le nombre de Gio requis par votre charge de travail. Chaque Gio que vous provisionnez vous donne droit à des IOPS et un débit supplémentaires à un taux fixe. En plus des IOPS de base qui vous sont garanties, chaque partage de fichiers Premium prend en charge le bursting sur la base du meilleur effort. Les formules pour les IOPS et le débit sont les suivantes :

- IOPS de base = 400 + 1 * par Gio provisionné. (Jusqu’à 100 000 IOPS maximum).
- Limite de rafale = MAX(4000, 3 * IOPS de base).
- Débit de sortie = 60 Mio/s + 0,06 * Gio provisionnés.
- Débit d’entrée = 40 Mio/s + 0,04 * Gio provisionnés.

La taille provisionnée du partage de fichiers peut être augmentée à tout moment, mais elle ne peut être réduite qu’au bout des 24 heures suivant la dernière augmentation. À l’issue des 24 heures sans augmentation de quota, vous pouvez diminuer le quota du partage autant de fois que vous le souhaitez, jusqu’à ce que vous l’augmentiez à nouveau. Les modifications de mise à l’échelle IOPS/débit prennent effet quelques minutes après le changement de taille provisionnée.

Il est possible de diminuer la taille de votre partage provisionné en dessous de votre Gio utilisé. Si vous faites cela, vous ne perdez pas les données, mais vous êtes toujours facturé pour la taille utilisée et recevez les performances (IOPS de base, débit et IOPS en rafale) du partage provisionné, pas de la taille utilisée.

Le tableau suivant illustre quelques exemples de ces formules pour les tailles de partage provisionné :

|Capacité (Gio) | IOPS de base | IOPS en rafale | Sortie (Mio/s) | Entrée (Mio/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Jusqu’à 4 000     | 66   | 44   |
|500         | 900     | Jusqu’à 4 000  | 90   | 60   |
|1 024       | 1 424   | Jusqu’à 4 000   | 122   | 81   |
|5 120       | 5 520   | Jusqu’à 15 360  | 368   | 245   |
|10 240      | 10 640  | Jusqu’à 30 720  | 675   | 450   |
|33 792      | 34 192  | Jusqu’à 100 000 | 2 088 | 1 392   |
|51 200      | 51 600  | Jusqu’à 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Jusqu’à 100 000 | 6 204 | 4 136   |

Les performances réelles des partages de fichiers sont soumises aux limites du réseau des machines, à la bande passante réseau disponible, aux tailles d’e/s, au parallélisme, entre autres nombreux facteurs. Par exemple, sur la base d’un test interne avec des tailles d’e/s en lecture/écriture de 8 Kio, une seule machine virtuelle Windows sans SMB Multichannel activé, *F16s_v2 standard*, connectée au partage de fichiers Premium sur SMB pourrait atteindre 20 000 e/s par seconde en écriture et 15 000 e/s par seconde. Avec les tailles d’e/s en lecture/écriture de 512 Mio, la même machine virtuelle peut atteindre 1,1 Gio/s en sortie et 370 Mio/s de débit d’entrée. Le même client peut atteindre des \~performances trois fois supérieures si SMB Multichannel est activé sur les partages Premium. Pour obtenir une mise à l’échelle des performances maximales, [activez SMB Multichannel](storage-files-enable-smb-multichannel.md) et répartissez la charge entre plusieurs machines virtuelles. Reportez-vous à [Performances de SMB Multichannel](storage-files-smb-multichannel-performance.md) et au [Guide de dépannage](storage-troubleshooting-files-performance.md) pour certains problèmes de performances courants et leurs solutions de contournement.

### <a name="bursting"></a>Mode en rafales
Si votre charge de travail a besoin de performances supplémentaires pour répondre aux pics de demande, votre partage peut utiliser des crédits de rafale pour atteindre la limite d’IOPS de la ligne de base du partage pour offrir les performances de partage dont il a besoin pour répondre à la demande. Les partages de fichiers Premium peuvent prévoir des rafales de leurs IOPS jusqu’à 4 000 ou jusqu’à multiplier leur nombre par trois, selon la valeur la plus élevée. Ce mode en rafales est automatisé et fonctionne selon un système de crédits. Il fonctionne dans la mesure des possibilités et la limite de rafale n’est pas une garantie : les partages de fichiers peuvent croître par rafales *jusqu’à* cette limite, pour une durée maximale de 60 minutes.

Des crédits s’accumulent dans un compartiment à rafales chaque fois que le trafic de votre partage de fichiers se trouve en dessous des IOPS de base. Par exemple, un partage de 100 Gio dispose de 500 IOPS de base. Si le trafic réel sur le partage est de 100 IOPS pour un intervalle spécifique de 1 seconde, les 400 IOPS inutilisées sont créditées dans un compartiment à rafales. De même, un partage inactif de 1 Tio accumule du crédit de rafale à 1 424 IOPS. Ces crédits sont ensuite utilisés lorsque des opérations dépassent les IOPS de base.

Chaque fois qu’un partage dépasse les IOPS de base et qu’il dispose de crédits dans un compartiment à rafales, il est augmenté par rafales pour atteindre le taux de rafales maximal autorisé. Les partages peuvent continuer de fonctionner en rafale tant qu’il reste des crédits, jusqu’à une durée maximale de 60 minutes, mais cela se base sur le nombre de crédits en rafale accumulés. Chaque e/s située au-delà des IOPS de base consomme un crédit ; une fois que tous les crédits sont consommés, le partage retourne aux IOPS de base.

Les crédits de partage présentent trois états :

- En hausse, lorsque le partage de fichiers utilise un nombre inférieur à celui des IOPS de base.
- En baisse, lorsque le partage de fichiers utilise plus que les IOPS de la ligne de base et en mode de rafale.
- Constant, lorsque le partage de fichiers utilise exactement les IOPS de la ligne de base, il n’y a aucun crédit accumulé ou utilisé.

Au départ, les nouveaux partages de fichiers se voient attribuer un nombre total de crédits dans leur compartiment à rafales. Les crédits de rafale ne seront pas augmentés si les IOPS du partage chutent en dessous des IOPS de base, en raison de la limitation par le serveur.

## <a name="pay-as-you-go-model"></a>Modèle avec paiement à l’utilisation
Azure Files utilise un modèle avec paiement à l’utilisation pour les partages de fichiers standard. Dans un modèle avec paiement à l’utilisation, le montant que vous payez est déterminé par la quantité que vous utilisez réellement, plutôt que par un montant provisionné. À un niveau élevé, vous payez un coût pour la quantité de données stockées sur le disque, puis un jeu de transactions supplémentaire en fonction de l’utilisation que vous faites de ces données. Un modèle de paiement à l’utilisation peut être intéressant financièrement, car vous n’avez pas besoin de surprovisionner pour prendre en compte de futures augmentations ou exigences en matière de performances, ni de déprovisionner si votre charge de travail ou empreinte de données change au fil du temps. En revanche, un modèle de paiement à l’utilisation peut être difficile à budgétiser, car il dépend de la consommation de l’utilisateur final.

### <a name="differences-in-standard-tiers"></a>Différences entre les niveaux standard
Lorsque vous créez un partage de fichiers standard, vous choisissez entre les niveaux Optimisé pour les transactions, Chaud et Froid. Tous trois sont stockés exactement sur le même matériel de stockage standard. La principale différence entre ces trois niveaux réside dans les tarifs de stockage des données au repos, qui sont inférieurs dans les niveaux les plus froids, et dans les tarifs des transactions, qui sont plus élevés dans les niveaux les plus froids. Cela signifie que :

- Le niveau Optimisé pour les transactions, comme son nom l’indique, optimise le tarif pour les charges riches en transactions. Si c’est à ce niveau que le tarif de stockage au repos des données est le plus élevé, le tarif des transactions y est le plus faible.
- Le niveau Chaud est destiné aux charges de travail actives qui ne portent pas sur un grand nombre de transactions. Si leur tarif de stockage au repos des données est légèrement inférieur, il est légèrement plus élevé qu’au niveau Optimisé pour les transactions en ce qui concerne les transactions. Il s’agit en quelque sorte d’un niveau intermédiaire entre les niveaux Optimisé pour les transactions et Froid.
- Le niveau Froid optimise le tarif pour les charges de travail dont l’activité est limitée, offrant ainsi le tarif de stockage au repos des données le plus bas, mais le tarif le plus élevée pour ce qui est des transactions.

Si vous placez une charge de travail rarement sollicitée dans le niveau Optimisé pour les transactions, vous ne payez presque rien pour les quelques fois par mois où vous effectuez des transactions sur votre partage, mais vous payez un montant élevé pour le stockage de données. Si vous déplacez ce partage de fichiers vers le niveau Froid, vos coûts de transaction restent très faibles, tout simplement parce que les transactions que vous effectuez sur cette charge de travail sont rares, mais le coût du stockage de données est bien moins élevé. Le choix du niveau approprié pour votre utilisation vous permet de réduire considérablement vos coûts. Le choix du niveau approprié pour votre utilisation vous permet de réduire considérablement vos coûts.

De même, si vous placez une charge de travail très sollicitée dans le niveau Froid, vous payez des coûts de transaction bien plus élevés mais des coûts de stockage de données plus faibles. Cela peut conduire à une situation dans laquelle l’augmentation des coûts due à l’augmentation des coûts de transaction dépasse les économies réalisées grâce au faible coût du stockage, et vous finissez par payer plus avec le niveau Froid que ce que vous auriez payé avec le niveau Optimisé pour les transactions. Pour certains niveaux d’utilisation, le niveau Froid peut se révéler plus coûteux que le niveau Optimisé pour les transactions, le niveau Chaud restant le plus économique.

C’est votre charge de travail et votre niveau d’activité qui déterminent le niveau le plus économique pour votre partage de fichiers standard. En pratique, la meilleure façon de choisir le niveau le plus intéressant consiste à examiner la consommation de ressources réelle du partage (données stockées, transactions d’écriture, etc.).

### <a name="what-are-transactions"></a>Qu’est-ce que les transactions ?
Les transactions sont des opérations ou des requêtes sur Azure Files pour charger, télécharger ou manipuler d’une autre manière le contenu du partage de fichiers. Chaque action effectuée sur un partage de fichiers se traduit par une ou plusieurs transactions. Sur les partages standard qui utilisent le modèle de facturation avec paiement à l’utilisation, cela se traduit par des coûts de transaction.

Il existe cinq catégories de transactions de base : écriture, liste , lecture, autre et suppression. Toutes les opérations effectuées par le biais de l’API REST ou de SMB sont classées dans l’une de ces quatre catégories comme suit :

| Type d'opération | Transactions d’écriture | Transactions de liste | Transactions de lecture | Autres transactions | Transactions de suppression |
|-|-|-|-|-|-|
| Opérations de gestion | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Opérations de données | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1 est disponible uniquement pour les partages de fichiers Premium, qui utilisent le modèle de facturation provisionné. Les transactions n’affectent pas la facturation pour les partages de fichiers Premium.

## <a name="see-also"></a>Voir aussi
- [Page des tarifs d’Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)
- [Planification d’un déploiement Azure Files](./storage-files-planning.md) et [Planification d’un déploiement Azure File Sync](./storage-sync-files-planning.md).
- [Créer un partage de fichiers](./storage-how-to-create-file-share.md) et [Déployer Azure File Sync](./storage-sync-files-deployment-guide.md).