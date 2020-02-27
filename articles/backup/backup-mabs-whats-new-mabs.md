---
title: Nouveautés du serveur de sauvegarde Microsoft Azure
description: Le serveur de sauvegarde Microsoft Azure offre des capacités de sauvegarde améliorées pour protéger les machines virtuelles, les fichiers et dossiers, les charges de travail et plus encore. Découvrez comment installer le serveur de sauvegarde Azure v3 ou effectuer une mise à niveau vers ce dernier.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582804"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Nouveautés du serveur de sauvegarde Microsoft Azure

Le serveur de sauvegarde Microsoft Azure version 3 (MABS v3), dernière mise à niveau en date, comporte des correctifs de bogues critiques, la prise en charge de Windows Server 2019, la prise en charge de SQL 2017 et d’autres fonctionnalités et améliorations. Pour connaître la liste des bogues corrigés et les instructions d’installation de MABS v3, voir l’article de la Base de connaissances [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

Les fonctionnalités suivantes sont incluses dans MABS v3 :

## <a name="volume-to-volume-migration"></a>Migration de volume à volume

Avec le stockage de sauvegarde moderne (MBS) de MABS v2, nous avons annoncé la mise en place du stockage adapté à la charge de travail : avec cette fonctionnalité, vous configurez certaines charges de travail à sauvegarder dans un stockage spécifique, selon les propriétés de stockage. Toutefois, après la configuration, vous pourriez avoir besoin de déplacer des sauvegardes de certaines sources de données vers un autre stockage pour optimiser l’utilisation des ressources. MABS v3 offre la possibilité de migrer des sauvegardes et de les configurer de façon à les stocker sur un autre volume en [trois étapes](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Empêcher les pertes de données imprévues

Dans les entreprises, MABS est géré par une équipe d’administrateurs. Malgré les instructions sur le stockage à utiliser pour les sauvegardes, un mauvais choix de volume comme stockage de sauvegarde pour MABS peut entraîner une perte de données critiques. Avec MABS v3, vous pouvez éviter ce type de situation en configurant ces volumes comme non disponibles pour le stockage avec [ces cmdlets PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Allocation de taille personnalisée

Le stockage de sauvegarde moderne (MBS) consomme peu de stockage, selon les besoins. Pour cela, MABS calcule la taille des données à sauvegarder lorsqu’il est configuré dans une optique de protection. Toutefois, si cela représente de nombreux fichiers et dossiers, comme dans le cas d’un serveur de fichiers, le calcul de la taille risque de prendre beaucoup de temps. Avec MABS v3, vous pouvez configurer MABS de façon à ce que, au lieu de calculer la taille de chaque fichier, il accepte la taille du volume comme valeur par défaut, ce qui fait gagner le temps.

## <a name="optimized-cc-for-rct-vms"></a>CC optimisé pour les machines virtuelles RCT

MABS utilise la fonctionnalité RCT (suivi des modifications natif d’Hyper-V), qui limite le nombre de longues vérifications de cohérence nécessaires, dans des scénarios comme des incidents sur une machine virtuelle. RCT offre une meilleure résilience que le suivi des modifications proposé par les sauvegardes sur instantané VSS. MABS v3 optimise encore davantage la consommation de réseau et de stockage en transférant seulement les données modifiées pendant les vérifications de cohérence.

## <a name="support-to-tls-12"></a>Prise en charge de TLS 1.2

TLS 1.2 est le moyen de communication sécurisé proposé par Microsoft avec le meilleur chiffrement qui soit. MABS prend maintenant en charge la communication TLS 1.2 entre MABS et les serveurs protégés, pour l’authentification par certificat et les sauvegardes cloud.

## <a name="vmware-vm-protection-support"></a>Prise en charge de la protection des machines virtuelles VMware

La sauvegarde de machines virtuelles VMware est maintenant prise en charge pour les déploiements en production. MABS v3 offre les fonctionnalités suivantes pour la protection des machines virtuelles VMware :

- Prise en charge de vCenter et d’ESXi 6.5, ainsi que de 5.5 et 6.0.
- Protection automatique des machines virtuelles VMware dans le cloud. Les machines virtuelles VMware ajoutées à un dossier protégé sont automatiquement protégées sur disque et dans le cloud.
- Amélioration de l’efficacité de la récupération à un autre emplacement VMware.

## <a name="sql-2017-support"></a>Prise en charge de SQL 2017

MABS v3 peut être installé avec SQL 2017 comme base de données MABS. Vous pouvez mettre à niveau le serveur SQL de SQL 2016 à SQL 2017, ou tout simplement l’installer. Il est également possible de sauvegarder la charge de travail SQL 2017 à la fois dans un environnement en cluster et dans un environnement non cluster avec MABS v3.

## <a name="windows-server-2019-support"></a>Prise en charge de Windows Server 2019

MABS v3 peut être installé sur Windows Server 2019. Pour utiliser MABS v3 avec Windows Server 2019, vous pouvez mettre à niveau votre système d’exploitation vers Windows Server 2019 soit avant l’installation/la mise à niveau de MABS v3, soit après l’installation/la mise à niveau de MABS v3 sur Windows Server 2016.

MABS v3 est une version complète qui peut faire l’objet d’une installation directe sur Windows Server 2016 ou Windows Server 2019, ou d’une mise à niveau à partir de MABS v2. Avant de mettre à niveau ou d’installer le serveur de sauvegarde v3, lisez les prérequis d’installation.
Pour plus d’informations sur les étapes d’installation/de mise à niveau de MABS, cliquez [ici](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS présente le même codebase que System Center Data Protection Manager. MABS v3 est équivalent à Data Protection Manager 1807.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment préparer votre serveur ou commencer à protéger une charge de travail :

- [Problèmes connus dans MABS V3](backup-mabs-release-notes-v3.md)
- [Préparer les charges de travail du serveur de sauvegarde](backup-azure-microsoft-azure-backup.md)
- [Utiliser le Serveur de sauvegarde pour sauvegarder un serveur VMware](backup-azure-backup-server-vmware.md)
- [Utiliser le serveur de sauvegarde pour sauvegarder SQL Server](backup-azure-sql-mabs.md)
- [Utiliser Modern Backup Storage avec le serveur de sauvegarde](backup-mabs-add-storage.md)
