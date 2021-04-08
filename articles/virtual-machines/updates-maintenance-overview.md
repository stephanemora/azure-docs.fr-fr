---
title: Présentation des mises à jour et de la maintenance
description: En savoir plus sur les mises à jour et les options de maintenance disponibles avec les machines virtuelles dans Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103021956"
---
# <a name="updates-and-maintenance-overview"></a>Présentation des mises à jour et de la maintenance
Cet article fournit une vue d’ensemble des différentes options de mise à jour et de maintenance pour les machines virtuelles Azure.

## <a name="automatic-os-image-upgrade"></a>Mise à niveau automatique de l’image du système d’exploitation

L’activation des [mises à niveau automatiques des images de système d’exploitation](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) pour un groupe identique facilite la gestion des mises à jour en appliquant automatiquement et en toute sécurité chaque mise à niveau du disque du système d’exploitation à toutes les instances dans le groupe identique.

La fonctionnalité de [mise à niveau automatique du système d’exploitation](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) présente les caractéristiques suivantes :

- Une fois configurée, la dernière image du système d’exploitation publiée par les éditeurs de l’image est automatiquement appliquée au groupe identique, sans aucune intervention de l’utilisateur.
- Elle effectue une mise à niveau propagée de lots d’instances chaque fois qu’une nouvelle image est publiée par l’éditeur.
- Elle s’intègre avec les sondes d’intégrité d’application et l’[extension Intégrité de l’application](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Elle fonctionne pour toutes les tailles de machine virtuelle et pour les images tant Windows que Linux.
- Vous pouvez désactiver les mises à niveau automatiques à tout moment (les mises à niveau du système d’exploitation peuvent également être démarrées manuellement).
- Le disque du système d’exploitation d’une machine virtuelle est remplacé par le nouveau disque de système d’exploitation créé avec la dernière version de l’image. Les extensions configurées et les scripts de données personnalisés sont exécutés. Les disques de données persistantes sont conservés.
- Le [séquencement d’extensions](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) est pris en charge.
- La mise à niveau automatique de l’image du système d’exploitation peut être activée sur un groupe identique de n’importe quelle taille.


## <a name="automatic-vm-guest-patching-preview"></a>Mise à jour corrective automatique des machines virtuelles (préversion)

L’activation de la [mise à jour corrective automatique des systèmes invités](automatic-vm-guest-patching.md) des machines virtuelles Azure facilite la gestion des mises à jour en appliquant les patchs sur les machines virtuelles de façon automatique et sécurisée afin de maintenir la conformité en termes de sécurité.

La [mise à jour corrective automatique de l’invité de machine virtuelle](automatic-vm-guest-patching.md) présente les caractéristiques suivantes :
- Les patchs classés comme *Critique* ou *Sécurité* sont automatiquement téléchargés et appliqués sur la machine virtuelle.
- Les patchs sont appliqués pendant les heures creuses dans le fuseau horaire de la machine virtuelle.
- L’orchestration des patchs est gérée par Azure et les patchs sont appliqués selon les [principes de première disponibilité](automatic-vm-guest-patching.md#availability-first-patching).
- L’intégrité des machines virtuelles, telle que déterminée par les signaux d’intégrité de la plateforme, est surveillée pour détecter les échecs de mise à jour corrective.
- Elle fonctionne pour toutes les tailles de machine virtuelle.


## <a name="automatic-extension-upgrade-preview"></a>Mise à niveau automatique des extensions (préversion)

La [mise à niveau automatique des extensions](automatic-extension-upgrade.md) est disponible en préversion pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure. Lorsque la mise à niveau automatique des extensions est activée sur une machine virtuelle ou un groupe identique, l’extension est automatiquement mise à niveau chaque fois que l’éditeur de l’extension publie une nouvelle version pour cette extension.

 La mise à niveau automatique des extensions présente les fonctionnalités suivantes :
- Prise en charge pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure. Les groupes de machines virtuelles identiques de Service Fabric ne sont actuellement pas pris en charge.
- Les mises à niveau sont appliquées dans un modèle de déploiement selon le principe de première disponibilité.
- Pour un groupe de machines virtuelles identiques, au maximum 20 % des machines virtuelles du groupe identique seront mises à niveau par lot. La taille de lot minimale est d’une machine virtuelle.
- Fonctionne pour toutes les tailles de machine virtuelle et pour les extensions tant Windows que Linux.
- Vous pouvez désactiver les mises à jour automatiques à tout moment.
- La mise à niveau automatique des extensions peut être activée sur un groupe de machines virtuelles identiques de n’importe quelle taille.
- Chaque extension prise en charge est inscrite individuellement, et vous pouvez choisir les extensions à mettre à niveau automatiquement.
- Prise en charge dans toutes les régions du cloud public.

## <a name="hotpatch"></a>Mise à jour corrective à chaud 

La [mise à jour corrective à chaud](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) est une nouvelle façon d’installer les mises à jour sur les nouvelles machines virtuelles Windows Server Azure Edition, qui ne nécessite pas de redémarrage après l’installation. La mise à jour corrective à chaud pour les machines virtuelles Windows Server Azure Edition offre les avantages suivants :

- Impact plus faible sur la charge de travail avec moins de redémarrages
- Déploiement plus rapide des mises à jour, car les packages sont plus petits, s’installent plus rapidement et offrent une orchestration des patchs plus facile à mettre en œuvre avec Azure Update Manager
- Meilleure protection, car les packages de mise à jour Hotpatch se limitent à l’étendue des mises à jour de sécurité Windows, qui s’installent plus rapidement et sans redémarrage


## <a name="azure-update-management"></a>Azure Update Management

Vous pouvez utiliser [Update Management dans Azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) pour gérer les mises à jour du système d’exploitation de vos machines virtuelles Windows et Linux dans Azure, dans des environnements locaux et dans d’autres environnements cloud. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur toutes les machines d’agent et gérer le processus d’installation des mises à jour nécessaires pour les serveurs.

## <a name="maintenance-control"></a>Contrôle de la maintenance

Gérer les mises à jour de plateforme qui ne nécessitent pas de redémarrage à l’aide du [contrôle de maintenance](maintenance-control.md). Azure met régulièrement à jour son infrastructure pour améliorer la fiabilité, les performances et la sécurité, ou pour lancer de nouvelles fonctionnalités. La plupart des mises à jour sont transparentes pour les utilisateurs. Certaines charges de travail sensibles, comme le gaming, le streaming multimédia et les transactions financières, ne peuvent tolérer (même quelques secondes seulement) le blocage ou la déconnexion d’une machine virtuelle pour maintenance. Le contrôle de maintenance vous donne la possibilité d’attendre les mises à jour de plateforme et de les appliquer dans une fenêtre de 35 jours consécutifs. 

Le contrôle de maintenance vous permet de décider du moment où appliquer les mises à jour sur vos machines virtuelles isolées et vos hôtes dédiés Azure.

Avec le [contrôle de maintenance](maintenance-control.md), vous pouvez :
- Regrouper des mises à jour dans un package de mise à jour.
- Attendre jusqu’à 35 jours avant d’appliquer les mises à jour. 
- Automatiser les mises à jour de la plateforme en configurant un calendrier de maintenance ou en utilisant [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Les configurations de maintenance fonctionnent entre les abonnements et les groupes de ressources. 


## <a name="scheduled-events"></a>Événements planifiés

Événements planifiés est un service de métadonnées Azure qui permet à votre application de disposer de suffisamment de temps pour se préparer à la maintenance des machines virtuelles. Il fournit des informations sur les événements de maintenance à venir (par exemple, les redémarrages), afin que votre application puisse s’y préparer et limiter les interruptions de service. Il est disponible pour tous les types de machines virtuelles Azure, notamment PaaS et IaaS sur Windows et Linux. 

Pour plus d’informations sur les événements planifiés, consultez [Événements planifiés pour les machines virtuelles Windows](./windows/scheduled-events.md) et [Événements planifiés pour Linux](./linux/scheduled-events.md)

## <a name="next-steps"></a>Étapes suivantes

Passez en revue la documentation relative à la [disponibilité et à la mise à l’échelle](availability.md) pour plus d’informations sur l’augmentation de la durée de bon fonctionnement de vos applications et services. 