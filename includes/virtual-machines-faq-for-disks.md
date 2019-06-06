---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b993b34f81298b40c8849084380b2d1770708351
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482276"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Forum aux questions sur les disques de machines virtuelles et les disques Premium gérés et non gérés Azure IaaS

Dans cet article, nous allons répondre à certaines questions fréquentes sur Azure Disques managés et les disques SSD Premium Azure.

## <a name="managed-disks"></a>Disques managés

**Qu’est-ce qu’Azure Managed Disks ?**

La fonctionnalité Disques managés est une fonctionnalité qui simplifie la gestion des disques associés aux machines virtuelles Azure IaaS en prenant en charge pour vous la gestion des comptes de stockage. Pour plus d’informations, consultez la section [Vue d’ensemble de la fonctionnalité Disques managés](../articles/virtual-machines/windows/managed-disks-overview.md).

**Si je crée un disque géré standard à partir d’un disque dur virtuel existant de 80 Go, combien cela me coûte-t-il ?**

Un disque géré standard créé à partir d’un disque dur virtuel de 80 Go est considéré comme un disque standard de la taille directement supérieure. Dans notre cas, il s’agit du disque S10. Vous êtes facturé en fonction de la tarification de disque S10. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/storage).

**Des frais de transaction s’appliquent-ils aux disques gérés Standard ?**

Oui. Vous êtes facturé pour chaque transaction. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/storage).

**Pour un disque standard géré, serai-je facturé pour la taille réelle des données sur le disque ou pour la capacité configurée du disque ?**

Vous êtes facturé en fonction de la capacité configurée du disque. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/storage).

**En quoi la tarification appliquée aux disques gérés Premium est-elle différente de celle associée aux disques non gérés ?**

La tarification de ces 2 types de disques est identique.

**Puis-je modifier le type de compte de stockage (Standard/Premium) de mes disques gérés ?**

Oui. Vous pouvez modifier le type de compte de stockage de vos disques managés en utilisant le portail Azure, PowerShell ou l’interface Azure CLI.

**Puis-je utiliser un fichier VHD dans un compte de stockage Azure pour créer un disque géré avec un autre abonnement ?**

Oui.

**Puis-je utiliser un fichier VHD dans un compte de stockage Azure pour créer un disque géré dans une région différente ?**

Non.

**Existe-t-il des restrictions de mise à l’échelle pour les clients utilisant des disques gérés ?**

La fonctionnalité Disques managés élimine les restrictions associées aux comptes de stockage. Toutefois, la limite maximale est de 50 000 disques managés par région et par type de disque pour un abonnement.

**Puis-je prendre une capture instantanée incrémentielle d’un disque géré ?**

Non. La fonctionnalité actuelle de capture instantanée crée une copie complète d’un disque géré.

**Les machines virtuelles d’un groupe à haute disponibilité peuvent-elles consister en une combinaison de disques gérés et non gérés ?**

Non. Les machines virtuelles d’un groupe à haute disponibilité doivent utiliser exclusivement des disques managés ou non managés. Lorsque vous créez un groupe à haute disponibilité, vous pouvez définir le type de disques à utiliser.

**Les disques gérés sont-ils l’option par défaut dans le portail Azure ?**

Oui.

**Est-il possible de créer un disque géré vide ?**

Oui. Vous pouvez tout à fait créer un disque vide. Un disque géré peut être créé indépendamment d’une machine virtuelle, par exemple, sans le joindre à une machine virtuelle.

**Par défaut, combien de domaines d’erreurs sont pris en charge pour les groupes à haute disponibilité utilisant Managed Disks ?**

En fonction de la région où se trouve le groupe à haute disponibilité qui utilise la fonctionnalité Disques managés, le nombre de domaines d’erreurs pris en charge peut être de 2 ou 3.

**Comment est configuré le compte de stockage Standard pour les diagnostics ?**

Vous configurez un compte de stockage privé pour les diagnostics de machine virtuelle.

**Quel type de prise en charge du contrôle d’accès en fonction du rôle est disponible pour Managed Disks ?**

La fonctionnalité Disques managés prend en charge trois rôles principaux par défaut :

* Propriétaire : Gérer tout, y compris l’accès
* Collaborateur : Gérer tout sauf les accès
* Lecteur : Afficher tout, mais ne pas apporter de modifications

**Existe-t-il un moyen de copier ou d’exporter un disque géré vers un compte de stockage privé ?**

Vous pouvez générer un URI de signature d’accès partagé (SAP) en lecture seule pour le disque managé et l’utiliser pour copier le contenu vers un compte de stockage privé ou un espace de stockage local. Vous pouvez utiliser l’URI SAS avec le Portail Azure, Azure PowerShell, l’interface de ligne de commande Azure ou [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Puis-je créer une copie de mon disque géré ?**

Les clients peuvent prendre une capture instantanée de leurs disques managés, qu’ils utilisent pour créer un autre disque managé.

**Les disques non gérés sont-ils encore pris en charge ?**

Oui, les disques managés et non managés sont pris en charge. Nous vous recommandons d’utiliser des disques managés pour les nouvelles charges de travail et de migrer vos charges de travail en cours vers des disques managés.

**Puis-je localiser conjointement des disques managés et non sur la même machine virtuelle ?**

Non.

**Si je créer un disque de 128 Go et augmente la taille à 130 gibioctets (Gio), sera être facturé pour la taille du disque suivante (256 Gio) ?**

Oui.

**Puis-je créer des disques gérés disposant du stockage localement redondant, géoredondant ou redondant interzone ?**

Actuellement, Azure Disques managés prend uniquement en charge les disques managés disposant du stockage localement redondant.

**Puis-je réduire la taille de mes disques gérés ?**

Non. Cette fonctionnalité n’est pas prise en charge pour l’instant.

**Puis-je résilier un bail sur mon disque ?**

Non. Cela n’est pas possible actuellement car un bail est présent pour empêcher une suppression accidentelle lorsque le disque est utilisé.

**Puis-je modifier la propriété de nom d’ordinateur lorsqu’un disque du système d’exploitation spécialisé (n’ayant pas été créé via l’outil de préparation système ou généralisé) est utilisé pour configurer une machine virtuelle ?**

Non. Vous ne pouvez pas mettre à jour la propriété de nom d’ordinateur. La nouvelle machine virtuelle a hérité cette particularité de la machine virtuelle parente qui a été utilisée pour créer le disque de système d’exploitation. 

**Où trouver des exemples de modèles Azure Resource Manager pour créer des machines virtuelles avec des disques gérés ?**
* [Liste de modèles utilisant des disques gérés](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Lorsque vous créez un disque à partir d’un objet blob, existe-t-il une relation continue avec cet objet blob source ?**

Non, quand le nouveau disque est créé, il constitue à ce moment-là une copie totalement indépendante de cet objet blob et aucune connexion n’existe entre les deux. Si vous voulez, une fois que vous avez créé le disque, vous pouvez supprimer l’objet blob source sans affecter le disque nouvellement créé en aucune façon.

**Puis-je renommer un disque managé ou non managé après l’avoir créé ?**

Vous ne pouvez pas renommer les disques managés. Par contre, vous pouvez renommer les disques non managés tant qu’ils ne sont pas attachés à une machine virtuelle ou un disque dur virtuel.

**Puis-je utiliser le partitionnement GPT sur un disque Azure ?**

Le partitionnement GPT peut être utilisé uniquement sur les disques de données, pas sur les disques de système d’exploitation. Les disques de système d’exploitation doivent utiliser le style de partition MBR.

**Quels types de disque prend en charge les captures instantanées ?**

Disque SSD Premium, disque SSD standard et le disque dur standard prennent en charge des captures instantanées. Pour ces types de trois disque, les instantanés sont pris en charge pour toutes les tailles de disque (y compris les disques jusqu'à 32 To de taille). Ultra SSDs ne gèrent pas les captures instantanées.

## <a name="standard-ssd-disks"></a>Disques SSD Standard

**Qu’est-ce qu’un disque SSD Standard Azure (préversion)**
Les disques SSD Standard sont des disques standard à semi-conducteur, optimisés en tant que stockage économique pour les charges de travail nécessitant des performances soutenues à des niveaux d’IOPS plus faibles.

<a id="standard-ssds-azure-regions"></a>**Quelles sont les régions actuellement prises en charge pour les disques SSD Standard ?**
Toutes les régions Azure prennent actuellement en charge les disques SSD Standard.

**La Sauvegarde Azure est-elle disponible pendant l’utilisation de disques SSD Standard ?**
Oui, le service Sauvegarde Azure est à présent disponible.

**Comment faire pour créer des disques SSD Standard ?**
Vous pouvez créer des disques SSD Standard à l’aide de modèles Azure Resource Manager, le Kit de développement logiciel, PowerShell ou CLI. Les paramètres suivants sont nécessaires dans le modèle Resource Manager pour créer des disques SSD Standard :

* *apiVersion* pour Microsoft.Compute doit être défini sur `2018-04-01` (ou version ultérieure)
* Pour *managedDisk.storageAccountType*, indiquez `StandardSSD_LRS`.

L’exemple suivant illustre la section *properties.storageProfile.osDisk* d’une machine virtuelle qui utilise des disques SSD Standard :

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Si vous souhaitez un exemple de modèle complet de création d’un disque SSD Standard, consultez [Créer une machine virtuelle à partir d’une image Windows avec des disques de données SSD Standard](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Puis-je convertir mes disques existants en disques SSD standard ?**
Oui, vous pouvez. Reportez-vous à [Convertir le stockage de disques managés Azure de standard en premium, et vice versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) pour des recommandations générales relatives à la conversion de la fonctionnalité Disques managés. Et utilisez la valeur suivante pour mettre à jour le type de disque vers un disque SSD standard.
-AccountType StandardSSD_LRS

**Quel est l’avantage d’utiliser des disques SSD Standard au lieu des disques HDD ?**
Les disques SSD standard offrent une meilleure latence, cohérence, disponibilité et fiabilité par rapport à des disques HDD. Pour cette raison, les charges de travail des applications s’exécutent beaucoup plus facilement sur les disques SSD Standard. Notez que les disques SSD Premium constituent la solution recommandée pour la plupart des charges de travail de production gourmandes en E/S.

**Puis-je utiliser des disques SSD Standard en tant que disques non managés ?**
Non, les disques SSD Standard sont disponibles seulement comme disques managés.

**Les disques SSD standard prennent-ils en charge les « SLA de machine virtuelle à instance unique » ?**
Non, les disques SSD standard n’ont pas de SLA de machine virtuelle à instance unique. Utiliser des disques SSD Premium pour une instance unique de contrat SLA de machine virtuelle.

## <a name="migrate-to-managed-disks"></a>Migrer vers la fonctionnalité Disques managés

**La migration a-t-elle un impact sur les performances des disques managés ?**

La migration implique le déplacement du disque d’un emplacement de stockage à un autre. Cela est orchestrée par la copie de sauvegarde de données, ce qui peuvent prendre plusieurs heures, généralement inférieure à 24 heures selon la quantité de données des disques. Pendant ce temps, votre application peut afficher une latence de lecture supérieure à la normale car certaines lectures sont redirigées vers l’emplacement d’origine et prennent donc plus de temps. Il n’y a aucun impact sur la latence d’écriture pendant cette période.  

**Quelles sont les modifications nécessaires dans une configuration de service Sauvegarde Azure préexistante avant/après la migration vers Managed Disks ?**

Aucune modification n’est nécessaire.

**Mes sauvegardes de machines virtuelles créées par le biais du service Sauvegarde Azure avant la migration continueront-elles à fonctionner ?**

Oui, les sauvegardes fonctionnent de manière transparente.

**Quelles sont les modifications nécessaires dans une configuration Azure Disk Encryption préexistante avant/après la migration vers Managed Disks ?**

Aucune modification n’est nécessaire.

**Migration automatisée d’une échelle de machine virtuelle existante est définie à partir de disques non gérés vers des disques gérés pris en charge ?**

Non. Vous pouvez créer un groupe de machines virtuelles identiques à l’aide de l’image de votre ancien groupe de machines virtuelles identiques avec des disques non managés.

**Puis-je créer un disque managé à partir d’un instantané d’objet blob de pages antérieur à la migration vers Managed Disks ?**

Non. Vous pouvez exporter un instantané d’objet blob de pages en tant qu’objet blob de pages, puis créer un disque managé à partir de l’objet blob de pages exporté.

**Puis-je basculer mes machines locales protégées par Azure Site Recovery vers une machine virtuelle avec Managed Disks ?**

Oui, vous pouvez choisir de basculer vers une machine virtuelle avec la fonctionnalité Disques managés.

**La migration impacte-t-elle les machines virtuelles Azure protégées par Azure Site Recovery par le biais de la réplication Azure vers Azure ?**

Oui. Actuellement, Azure Site Recovery Azure pour la protection Azure pour les machines virtuelles avec Managed Disks est disponible en tant qu’un service de disponibilité générale.

**Puis-je convertir des machines virtuelles avec des disques non managés résidant sur des comptes de stockage qui sont ou ont été chiffrés sur des disques managés ?**

Oui

## <a name="managed-disks-and-storage-service-encryption"></a>Disques managés et Storage Service Encryption

**Azure Storage Encryption est-il activé par défaut lors de la création d’un disque géré ?**

Oui.

**Qui gère les clés de chiffrement ?**

Microsoft gère les clés de chiffrement.

**Puis-je désactiver Storage Service Encryption pour mes disques gérés ?**

Non.

**Storage Service Encryption est-il disponible dans toutes les régions ?**

Non. Il est disponible dans toutes les régions où la fonctionnalité Disques managés est disponible. La fonctionnalité Disques managés est disponible dans toutes les zones publiques et en Allemagne. Il est également disponible en Chine, mais uniquement pour les clés gérées par Microsoft, pas pour les clés gérées par le client.

**Comment puis-je savoir si mon disque géré est chiffré ?**

Vous pouvez déterminer l’heure de création d’un disque géré depuis le portail Azure, Azure CLI et PowerShell. S’il a été créé après le 9 juin 2017, votre disque est chiffré.

**Comment puis-je chiffrer mes disques existants qui ont été créés avant le 10 juin 2017 ?**

À compter du 10 juin 2017, les nouvelles données écrites sur des disques managés existants sont chiffrées automatiquement. Nous avons également l’intention de chiffrer les données existantes, et le chiffrement aura lieu de manière asynchrone en arrière-plan. Si vous devez chiffrer des données existantes maintenant, créez une copie de votre disque. Les nouveaux disques seront chiffrés.

* [Copier les disques gérés à l’aide de l’interface Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copier les disques gérés à l’aide de PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Les instantanés et les images gérés sont-ils chiffrés ?**

Oui. Tous les instantanés et les images gérés créés après le 9 juin 2017 sont automatiquement chiffrés. 

**Puis-je convertir des machines virtuelles avec des disques non gérés situés sur des comptes de stockage qui sont ou ont été chiffrés sur des disques gérés ?**

Oui

**Un disque dur virtuel exporté à partir d’un disque géré ou un instantané seront-ils également chiffrés ?**

Non. Mais si vous exportez un disque dur virtuel vers un compte de stockage chiffré depuis un disque géré chiffré ou un instantané, il sera chiffré. 

## <a name="premium-disks-managed-and-unmanaged"></a>Disques Premium : gérés et non gérés

**Si une machine virtuelle utilise une taille qui prend en charge les disques SSD Premium, comme DSv2, puis-je attacher des disques de données Standard et Premium ?** 

Oui.

**Puis-je attacher des disques de données Standard et Premium à une taille qui ne prend pas en charge les disques SSD Premium, comme les séries D, Dv2, G ou F ?**

Non. Vous ne pouvez joindre que des disques de données Standard aux machines virtuelles n’utilisant pas une gamme de taille qui prend en charge les disques SSD Premium.

**Si je crée un disque de données Premium à partir d’un disque dur virtuel existant de 80 Go, combien cela me coûte-t-il ?**

Un disque de données Premium créé à partir d’un disque dur virtuel de 80 Go est considéré comme un disque standard de taille directement supérieure. Dans notre cas, il s’agit du disque P10. Vous êtes facturé en fonction de la tarification de disque P10.

**L’utilisation de disques SSD Premium occasionne-t-elle des coûts de transaction ?**

Il existe un coût fixe pour chaque taille de disque. Il s’ajoute aux limites spécifiques d’E/S par seconde et de débit. Les seuls autres coûts sont liés à la bande passante sortante et à la capacité de captures instantanées, le cas échéant. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/storage).

**Quelles sont les limites d’E/S par seconde et de débit associées à la mise en cache sur disque ?**

Les limites combinées pour le cache et le disque SSD local d’une machine de la série DS sont de 4 000 E/S par cœur et de 33 Mio par seconde par cœur. La série GS offre 5 000 E/S par seconde par cœur et 50 Mio par seconde par cœur.

**Le disque local SSD est-il pris en charge pour les machines virtuelles Managed Disks ?**

Le disque local SSD est un stockage temporaire inclus avec une machine virtuelle de la fonctionnalité Disques managés. Ce stockage temporaire n’occasionne aucun frais supplémentaire. Nous vous déconseillons d’utiliser ce disque SSD local pour stocker les données de vos applications, car il n’est pas rendu persistant dans le stockage d’objets Blob Azure.

**L’utilisation de la fonction TRIM sur les disques Premium a-t-elle des répercussions ?**

Il n’existe aucun inconvénient à l’utilisation de TRIM sur des disques Azure Premium ou Standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nouvelles tailles de disque : gérés et non gérés

**Quelle est la plus grande taille de disque managé prise en charge pour les disques de système d’exploitation et de données ?**

Le type de partition pris en charge par Azure pour un disque de système d’exploitation est l’enregistrement de démarrage principal (MBR). Le format MBR prend en charge un disque dont la taille peut atteindre 2 Tio. La plus grande taille prise en charge par Azure pour un disque de système d’exploitation est de 2 Tio. Azure prend en charge jusqu'à 32 To de disques de données gérés dans Azure global, 4 TIO dans les clouds souverains Azure.

**Quelle est la plus grande taille de disque non managé prise en charge pour les disques de système d’exploitation et de données ?**

Le type de partition pris en charge par Azure pour un disque de système d’exploitation est l’enregistrement de démarrage principal (MBR). Le format MBR prend en charge un disque dont la taille peut atteindre 2 Tio. La plus grande taille prise en charge par Azure pour un disque non managé de système d’exploitation est de 2 Tio. Azure prend en charge jusqu’à 4 Tio pour les disques de données non managés.

**Quelle est la plus grande taille d’objet blob de page prise en charge ?**

La plus grande taille d’objet blob de pages prise en charge par Azure est de 8 Tio (8 191 Gio). La taille blob de page maximale en cas d’attachement à une machine virtuelle en tant que disques de données ou de système d’exploitation est de 4 Tio (4 095 Gio).

**Dois-je utiliser une nouvelle version des outils Azure pour créer, joindre, redimensionner et charger des disques de taille supérieure à 1 Tio ?**

Vous n’avez pas besoin de mettre à niveau votre version des outils Azure pour créer, joindre ou redimensionner des disques de taille supérieure à 1 Tio. Pour charger votre fichier de disque dur virtuel en local directement vers Azure en tant qu’objet blob de pages ou disque non managé, vous devez utiliser l’ensemble d’outils le plus récent indiqué ci-dessous. Nous prenons en charge les chargements de disque dur virtuel uniquement jusqu’à 8 To.

|Outils Azure      | Versions prises en charge                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numéro de version 4.1.0 : Version de juin 2017 ou ultérieure|
|Azure CLI v1     | Numéro de version 0.10.13 : Version de mai 2017 ou ultérieure|
|Azure CLI v2     | Numéro de version 2.0.12 : Version de juillet 2017 ou ultérieure|
|AzCopy           | Numéro de version 6.1.0 : Version de juin 2017 ou ultérieure|

**Les tailles de disque P4 et P6 sont-elles prises en charge pour les disques non gérés ou les objets blob de pages ?**

Les tailles de disque P4 (32 Gio) et P6 (64 Gio) ne sont pas prises en charge en tant que niveaux de disque par défaut pour les disques non managés et les objets blob de pages. Vous devez explicitement [définir le niveau d’objets blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) sur P4 et P6 pour que votre disque soit mappé à ces niveaux. Si vous déployez un objet blob de pages ou un disque non managé avec une taille de disque ou une longueur de contenu inférieure à 32 Gio ou entre 32 Gio et 64 Gio sans définir le niveau de l’objet blob, vous continuez à utiliser P10 avec 500 IOPS et 100 Mio/s et le niveau tarifaire mappé.

**Si mon disque managé Premium de moins de 64 Gio a été créé avant l’activation du petit disque (aux environs du 15 juin 2017), comment est-il facturé ?**

Les disques Premium de petite taille de moins de 64 Gio continuent à être facturés selon le niveau de tarification P10.

**Comment puis-je changer la couche de disque de petits disques Premium inférieurs à 64 Gio en la faisant passer de P10 à P4 ou P6 ?**

Vous pouvez prendre un instantané de vos disques de petite taille et ensuite créer un disque pour changer automatiquement le niveau de tarification vers P4 ou P6 selon la taille fournie.

**Possible de redimensionner des disques gérés existants à partir de tailles de moins de 4 tebibytes (TIO) pour les nouvelles tailles de disque nouvellement introduites jusqu'à 32 To ?**

Oui.

**Quelles sont les plus grandes tailles de disque pris en charge par le service sauvegarde Azure et Azure Site Recovery ?**

La plus grande taille de disque prise en charge par Sauvegarde Azure et Azure Site Recovery est de 4 Tio. Prise en charge pour les disques plus volumineux jusqu'à 32 To n’est pas encore disponible.

**Quels sont la machine virtuelle recommandée tailles pour les tailles de disque plus volumineux (> 4 TIO) pour disque SSD Standard et des disques HDD Standard d’atteindre optimisée disque IOPS et bande passante ?**

Pour atteindre le débit de disque du disque SSD Standard et disques durs Standard de disques de grande taille (> 4 TIO) au-delà de 500 e/s et 60 Mio/s, nous vous recommandons de déployer une nouvelle machine virtuelle à partir d’une des tailles de machine virtuelle suivantes pour optimiser les performances : Série B, séries DSv2, Dsv3-séries, ESv3, série Fs, série Fsv2, série M, série GS, série NCv2, NCv3, série Ls machines virtuelles ou. Attachement de disques volumineux à des machines virtuelles ou des machines virtuelles qui n’utilisent pas les tailles recommandées ci-dessus existant constatez une baisse des performances.

**Comment puis-je mettre à niveau mes disques (> 4 TIO) qui ont été déployé pendant la préversion de tailles de disque plus volumineuse afin d’obtenir les IOPS et la bande passante plus élevée à la disponibilité générale ?**

Vous pouvez arrêter et démarrer la machine virtuelle que le disque est attaché à ou, détachez et rattachez votre disque. Les objectifs de performance de taille supérieure de disque ont été augmentées pour les disques SSD premium et standards SSDs à la disposition générale.

**Quelles régions sont de 8 To, TIO 16 et 32 TIO pris en charge dans les tailles de disque géré ?**

Du 8 TiB TIO 16, 32 TIO disque références SKU et est prises en charge dans toutes les régions sous 21Vianet globale de Azure, Microsoft Azure Government et Azure en Chine.

**Nous prennent en charge l’activation de la mise en cache de l’hôte sur toutes les tailles de disque ?**

Nous prenons en charge la mise en cache d’hôte de ReadOnly et en lecture/écriture sur les tailles de disque inférieure à 4 To. Pour les tailles de disque supérieures à 4 Tio, nous ne prenons en charge que la mise en cache définie sur None (Aucun). Nous vous recommandons de tirer parti de la mise en cache pour les plus petites tailles de disque, où vous pouvez vous attendre à observer une amélioration des performances avec les données mises en cache dans la machine virtuelle.

## <a name="what-if-my-question-isnt-answered-here"></a>Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?

Si votre question n’est pas répertoriée ici, faites-le-nous savoir et nous vous aiderons à trouver une réponse. Vous pouvez poser une question à la fin de cet article dans les commentaires. Pour prendre contact avec l’équipe de stockage Azure et d’autres membres de la Communauté sur cet article, consultez le [forum MSDN sur le stockage Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Pour soumettre une demande de fonctionnalité, transmettez vos questions et vos idées sur le [forum dédié aux commentaires sur le stockage Azure](https://feedback.azure.com/forums/217298-storage).
