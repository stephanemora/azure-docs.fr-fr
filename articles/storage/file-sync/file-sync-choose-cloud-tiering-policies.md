---
title: Choisir des stratégies de hiérarchisation Cloud Azure File Sync | Microsoft Docs
description: Découvrez ce qu’il faut savoir lors du choix des stratégies de hiérarchisation Cloud Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796195"
---
# <a name="choose-cloud-tiering-policies"></a>Choisir des stratégies de hiérarchisation Cloud

Cet article fournit des conseils pour les utilisateurs qui sélectionnent et ajustent leurs stratégies de hiérarchisation Cloud. Avant de lire, assurez-vous de bien comprendre le fonctionnement de la hiérarchisation Cloud. Pour des notions de base sur la hiérarchisation Cloud, consultez l’article[Comprendre la hiérarchisation Cloud Azure File Sync](file-sync-cloud-tiering-overview.md). Pour obtenir une explication détaillée des stratégies de hiérarchisation Cloud et des exemples, consultez [Stratégies de hiérarchisation Cloud Azure File Sync](file-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Limites
- La hiérarchisation cloud n’est pas prise en charge sur le volume système Windows.

- Vous pouvez toujours activer la hiérarchisation Cloud si vous disposez d’un quota FSRM au niveau du volume. Une fois qu’un quota FSRM est défini, les API de requête d’espace libre appelées signalent automatiquement l’espace libre sur le volume en fonction du quota paramétré. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Taille de fichier minimum pour un fichier à hiérarchiser

Pour les agents de version 9 et ultérieures, la taille minimale d’un fichier à hiérarchiser basée sur la taille de cluster du système. La taille de fichier minimale éligible pour la hiérarchisation cloud est calculée en multipliant par 2 la taille du cluster et utilise au minimum 8 ko. La table suivante illustre les tailles minimales des fichiers qui peuvent être hiérarchisés, sur la base de la taille du volume de cluster :

|Taille de volume de cluster (en octets) |Les fichiers de cette taille ou plus peuvent être hiérarchisés  |
|----------------------------|---------|
|4 ko ou moins (4096)      | 8 Ko    |
|8 Ko (8192)                 | 16 Ko   |
|16 Ko (16384)               | 32 Ko   |
|32 ko (32 768)               | 64 Ko   |
|64 ko (65 536)    | 128 Ko  |
|128 Ko (131072) | 256 KB |
|256 Ko (262144) | 512 Ko |
|512 Ko (524288) | 1 Mo |
|1 Mo (1048576) | 2 Mo |
|2 Mo (2097152) | 4 Mo |

Les tailles de cluster jusqu’à 2 Mo sont prises en charge avec l’agent Azure File Sync version 12 mais, pour des tailles supérieures, la hiérarchisation cloud ne fonctionne pas.

Tous les systèmes de fichiers utilisés par Windows organisent votre disque dur en fonction de la taille du cluster (également appelée taille d’unité d’allocation). La taille du cluster représente la plus petite quantité d’espace disque qui peut être utilisée pour contenir un fichier. Lorsque les tailles de fichiers ne correspondent pas à un multiple de la taille du cluster, plus d’espace est nécessaire pour conserver le fichier (jusqu’au multiple supérieur de la taille du cluster).

Azure File Sync est pris en charge sur les volumes NTFS avec Windows Server 2012 R2 et versions ultérieures. Le tableau suivant décrit les tailles de cluster par défaut quand vous créez un volume NTFS avec Windows Server 2019.

|Taille du volume    |Windows Server 2019             |
|---------------|--------------------------------|
|7 Mo – 16 To   | 4 Ko                |
|16 To – 32 To   | 8 Ko                |
|32 To – 64 To   | 16 Ko               |
|64 To – 128 To  | 32 Ko               |
|128 To – 256 To | 64 Ko (max précédent) |
|256 To - 512 To| 128 Ko              |
|512 To - 1 Po  | 256 KB              |
|1 Po - 2 Po    | 512 Ko              |
|2 To - 4 Po    | 1 024 Ko             |
|4 To - 8 To    | 2 048 Ko (taille max)  |
|> 8 To         | non pris en charge       |

Lors de la création du volume, il est possible que vous ayez mis en forme manuellement le volume avec une taille de cluster différente. Si votre volume provient d’une version antérieure de Windows, les tailles de cluster par défaut peuvent également être différentes. [Cet article contient des informations supplémentaires sur les tailles de cluster par défaut.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Même si vous choisissez une taille de cluster inférieure à 4 ko, la limite de 8 ko comme plus petite taille de fichier pouvant être hiérarchisée s’applique toujours. (Même si, techniquement, le double de la taille du cluster équivaut à moins de 8 ko.)

Ce minimum absolu est dû à la manière dont NTFS stocke les fichiers très petits, de 1 ko à 4 ko. En fonction d’autres paramètres du volume, il est possible que les petits fichiers ne soient pas du tout stockés dans un cluster sur le disque. Il est peut-être plus efficace de stocker ces fichiers directement dans la table de fichiers maîtres ou l’« enregistrement de la table MFT » du volume. Le point d’analyse de la hiérarchisation cloud est toujours stocké sur le disque et occupe exactement un cluster. Hiérarchiser de si petits fichiers pourrait ne pas représenter un gain de place. Les cas extrêmes peuvent même finir par utiliser plus d’espace lorsque la hiérarchisation cloud est activée. Pour éviter cela, la plus petite taille d’un fichier que la hiérarchisation cloud organisera est de 8 ko sur une taille de cluster de 4 ko ou moins. 

## <a name="selecting-your-initial-policies"></a>Sélection de vos stratégies initiales

En règle générale, lorsque vous activez la hiérarchisation Cloud sur un point de terminaison de serveur, vous devez créer un lecteur virtuel local pour chaque point de terminaison de serveur individuel. L’isolation du point de terminaison de serveur aide à mieux comprendre le fonctionnement de la hiérarchisation Cloud et l’ajustement des stratégies en conséquence. Toutefois, Azure File Sync fonctionne même si vous avez plusieurs points de terminaison de serveur sur le même lecteur. Pour plus d’informations, consultez la section [Plusieurs points de terminaison de serveur sur le volume local](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume). Nous vous recommandons également, lorsque vous activez pour la première fois la hiérarchisation Cloud, de conserver la stratégie de date désactivée et la stratégie d’espace libre du volume de 10 à 20 % environ. Pour la plupart des volumes de serveurs de fichiers, 20 % d’espace libre de volume est généralement la meilleure option.

Par souci de simplicité et pour comprendre la façon dont les éléments sont hiérarchisés, nous vous recommandons d’ajuster votre stratégie d’espace libre du volume et de désactiver votre stratégie de date (sauf s’il est nécessaire de l’activer). Nous vous recommandons de le faire, car la plupart des clients trouve que remplir le cache local avec autant de fichiers à chaud que possible et de hiérarchiser le reste sur le Cloud est important. Toutefois, la stratégie de date peut être avantageuse si vous souhaitez libérer de manière proactive l’espace disque local et que vous savez que les fichiers dans ce point de terminaison de serveur peuvent être consultés après le nombre de jours spécifié dans votre stratégie de date sans avoir besoin d’être conservés localement. Le paramétrage de la stratégie de date libère une capacité précieuse de disque local pour d’autres points de terminaison sur le même afin de mettre en cache plus de fichiers.

Après avoir défini vos stratégies, surveillez la sortie et ajustez les deux stratégies en conséquence. Nous vous recommandons de surveiller spécifiquement la **taille de rappel de hiérarchisation Cloud** et la **taille de rappel de hiérarchisation Cloud par métriques d’application** dans Azure Monitor. Pour savoir comment surveiller la sortie, consultez [Surveiller la hiérarchisation Cloud](file-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Ajustement de vos stratégies

Si la quantité de fichiers rappelés en continu à partir d’Azure est supérieure à celle que vous souhaitez, vous aurez peut-être plus de fichiers à chaud que d’espace pour les enregistrer sur le volume du serveur local. Augmentez la taille du volume local si possible, et/ou diminuez le pourcentage de votre stratégie d’espace libre du volume par petits incréments. Une diminution trop importante du pourcentage d’espace libre du volume peut avoir des conséquences négatives. Une plus grande attrition de votre jeu de données nécessite plus d’espace libre : pour les nouveaux fichiers et le rappel des fichiers « à froid ». La hiérarchisation commence par un délai pouvant atteindre une heure, puis nécessite du temps de traitement. c’est pourquoi vous devez toujours disposer de suffisamment d’espace libre sur votre volume.

La conservation de davantage de données localement implique une baisse des coûts de sortie car moins de fichiers sont rappelés à partir d’Azure, mais requiert davantage de stockage local, ce qui a un coût. 

Lorsque vous ajustez votre stratégie d’espace libre du volume, la quantité de données à conserver en local est déterminée par les facteurs suivants : votre bande passante, le modèle d’accès du jeu de données et le budget. Avec une faible connexion de bande passante, vous pourriez avoir besoin de plus de données locales afin réduire au maximum le décalage pour les utilisateurs. Autrement, vous pouvez baser la quantité d’espace libre du volume sur le taux de variation sur une période donnée. Par exemple, si vous savez que 10 % de votre jeu de données de 1 To changent ou sont activement utilisés chaque mois, vous pouvez conserver 100 Go localement de façon à ne pas rappeler fréquemment des fichiers. Si votre volume est de 2 To, vous pouvez conserver 5 % (soit 100 Go) localement, de sorte que les 95 % restants constituent votre pourcentage d’espace libre du volume. Toutefois, vous devriez ajouter une mémoire tampon pour les périodes de variations plus importantes, c’est-à-dire en commençant par un pourcentage d’espace libre du volume supérieur, puis en l’ajustant si nécessaire par la suite.

## <a name="standard-operating-procedures"></a>Procédures de fonctionnement standard

- Lors de la migration initiale vers Azure Files via Azure File Sync, la hiérarchisation Cloud dépend du chargement initial
- La hiérarchisation Cloud vérifie la conformité avec les stratégies d’espace libre du volume et de date toutes les soixante minutes
- L’utilisation du commutateur /LFSM sur Robocopy lors de la migration de fichiers permet de synchroniser les fichiers et la hiérarchisation Cloud pour libérer de l’espace pendant le chargement initial 
- Si la hiérarchisation se produit avant qu’une carte thermique ne soit formé, les fichiers sont hiérarchisés par timestamp de la dernière modification

## <a name="next-steps"></a>Étapes suivantes

* [Planification d’un déploiement de synchronisation de fichiers Azure](file-sync-planning.md)
