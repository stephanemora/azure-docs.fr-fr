---
title: Historique de Version de Azure Site Recovery déploiement Planner
description: Différents correctifs de Versions de planificateur de déploiement de récupération Site connus et limitations connues, ainsi que leurs dates de lancement.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927371"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Historique de Version de Azure Site Recovery déploiement Planner

Cet article fournit un historique de toutes les versions d’Azure Site Recovery Deployment Planner, ainsi que les correctifs, connus limitations dans chaque et leurs dates de lancement.

## <a name="version-24"></a>Version 2.4

**Date de publication : 17 avril 2019**

**Correctifs :**

- Amélioration de compatibilité du système d’exploitation, en particulier lors de la gestion des erreurs en fonction de la localisation.
- Ajout de machines virtuelles avec jusqu'à 20 Mbits/s de données change tarif (évolution) à la liste de vérification de compatibilité.
- Messages d’erreur améliorés
  - Prise en charge pour vCenter 6.7.
  - Prise en charge ajoutée pour station de travail Windows Server 2019 et Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Version 2.3

**Date de publication : 3 décembre 2018.**

**Correctifs :**

- Correction d’un problème qui empêchait le Planificateur de déploiement à partir de la génération d’un rapport avec l’emplacement cible fourni et d’un abonnement.

## <a name="version-22"></a>Version 2.2 

**Date de publication : 25 avril 2018**

**Correctifs :**

- Opérations de GetVMList :
  - Correction d’un problème qui provoquait GetVMList échoue si le dossier spécifié n’existe pas. Il désormais crée le répertoire par défaut, ou crée le répertoire spécifié dans le paramètre outputfile.
  - Ajouté que plus détaillée des raisons des échecs pour GetVMList.
- Informations de type de machine virtuelle ajoutées en tant que colonne dans la feuille de machines virtuelles compatible de l’état du Planificateur de déploiement.
- Hyper-V pour la récupération d’urgence Azure :
  - Exclus de machines virtuelles avec partagé des disques durs virtuels et des relais disques à partir de profilage. L’opération Startprofiling affiche la liste des machines virtuelles exclus dans la console.
  - Ajout des machines virtuelles avec des disques de plus de 64 à la liste des machines virtuelles incompatibles.
  - Mise à jour de la réplication initiale (IR) et le facteur de compression delta replication (DR).
  - Limité prise en charge pour le stockage SMB.

## <a name="version-21"></a>Version 2.1

**Date de publication : 3 janvier 2018**

**Correctifs :**

- Mise à jour le rapport Excel.
- Correction des bogues dans l’opération GetThroughput.
- Ajout de l’option pour limiter le nombre de machines virtuelles à profiler ou générer le rapport. La limite par défaut est 1 000 machines virtuelles.
- VMware pour la récupération d’urgence Azure :
  - Correction d’un problème de Windows Server 2016 machine virtuelle dans la table incompatible. 
  - Mise à jour des messages de compatibilité pour les machines virtuelles Windows de Extensible Firmware Interface (EFI).
- Mise à jour de VMware vers Azure et Hyper-V vers Azure, l’activité des données de la machine virtuelle limite par machine virtuelle. 
- Plus grande fiabilité de l’analyse de fichier de liste de machine virtuelle.

## <a name="version-201"></a>Version 2.0.1

**Date de publication : 7 décembre 2017**

**Correctifs :**

- Ajout d’une recommandation pour optimiser la bande passante réseau.

## <a name="version-20"></a>Version 2.0

**Date de publication : 28 novembre 2017**

**Correctifs :**

- Prise en charge pour Hyper-V pour la récupération d’urgence Azure.
- Calculatrice du coût supplémentaire.
- Ajout du système d’exploitation vérification de la version de VMware pour la récupération d’urgence Azure pour déterminer si la machine virtuelle est compatible ou incompatible pour la protection. L’outil utilise la chaîne de version du système d’exploitation qui est retournée par le serveur vCenter pour cette machine virtuelle. C’est la version de système d’exploitation invité que l’utilisateur a sélectionné lors de la création de la machine virtuelle dans VMware.

**Limitations connues :**

- Pour Hyper-V pour la récupération d’urgence Azure, la machine virtuelle avec le nom qui contient les caractères tels que : `,`, `"`, `[`, `]`, et ``` ` ``` ne sont pas pris en charge. Si le profilage, génération de rapports échoue ou aura un résultat incorrect.
- VMware pour la récupération d’urgence Azure, machine virtuelle avec un nom contenant des virgules n’est pas pris en charge. Si profilé, Échec de la génération de rapports ou prévoyez d’avoir un résultat incorrect.

## <a name="version-131"></a>Version 1.3.1

**Date de publication : 19 juillet 2017** 

**Correctifs :**

- Prise en charge des disques volumineux (> 1To) dans la génération de rapports. Vous pouvez désormais utiliser le Planificateur de déploiement pour planifier la réplication pour les machines virtuelles qui ont des tailles de disque supérieures à 1 To (jusqu'à 4 095 Go).
En savoir plus sur la [Prise en charge des disques volumineux sur Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Version 1.3

**Date de publication : 9 mai 2017**

**Correctifs :**

- Prise en charge ajoutée pour un disque géré dans la génération de rapports. Le nombre de machines virtuelles qui peuvent être placés à un seul compte de stockage est calculé en fonction de si le disque géré est sélectionné pour le basculement/Test de basculement.

## <a name="version-12"></a>version 1.2

**Date de publication : 7 avril 2017**

**Correctifs :**

- Ajout de démarrage (BIOS ou EFI) vérifications de type pour chaque machine virtuelle déterminer si la machine virtuelle est compatible ou incompatible pour la protection.
- Ajout du système d’exploitation des informations pour chaque machine virtuelle de type dans les machines virtuelles compatibles et les feuilles de calcul de machines virtuelles incompatibles.
- Prise en charge pour l’opération GetThroughput pour les régions US Government et Chine Microsoft Azure.
- Ajout de quelques autres vérifications préalables pour les serveurs vCenter et ESXi.
- Correction d’un problème de rapport incorrect générée lorsque les paramètres régionaux sont définis sur en langue anglaise.

## <a name="version-11"></a>Version 1.1

**Date de publication : 9 mars 2017**

**Correctifs :**

- Correction d’un problème qui empêchait le profilage de machines virtuelles lorsqu’il y a au moins deux machines virtuelles ayant le même nom ou adresse IP sur plusieurs hôtes ESXi de vCenter.
- Correction d’un problème qui a provoqué la copie et la recherche doit être désactivée pour les machines virtuelles compatibles et les feuilles de calcul de machines virtuelles incompatibles.

## <a name="version-10"></a>Version 1.0

**Date de publication : 23 février 2017**

**Limitations connues :**

- Prend en charge uniquement pour VMware vers les scénarios de récupération d’urgence Azure. Pour Hyper-V pour les scénarios de récupération d’urgence Azure, utilisez le [outil de planification de capacité Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Ne prend pas en charge l’opération GetThroughput pour les régions US Government et Chine Microsoft Azure.
- Le profil ne peut pas outil machines virtuelles si le serveur vCenter possède au moins deux machines virtuelles ayant le même nom ou adresse IP sur plusieurs hôtes ESXi.
Dans cette version, l’outil ignore le profilage des noms ou adresses IP des machines virtuelles en double dans le fichier VMListFile. La solution de contournement consiste à profiler des machines virtuelles à l’aide d’un hôte ESXi au lieu du serveur vCenter. Veillez à exécuter une instance par hôte ESXi.
