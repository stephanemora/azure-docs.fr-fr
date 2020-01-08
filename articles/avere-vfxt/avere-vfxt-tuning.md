---
title: Paramétrage du cluster Avere vFXT - Azure
description: Vue d’ensemble des paramètres personnalisés pour optimiser les performances dans Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 8e25b3408482d9be9cb870df338ba0e53af52507
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414331"
---
# <a name="cluster-tuning"></a>Paramétrage du cluster

La plupart des clusters vFXT peuvent bénéficier de paramètres de performances personnalisés. Ces paramètres permettent au cluster de mieux fonctionner avec votre flux de travail particulier, le jeu de données et les outils.

Cette personnalisation doit être effectuée avec un technicien du support technique, car elle implique généralement la configuration de fonctionnalités qui ne sont pas disponibles à partir du Panneau de configuration Avere.

Cette section décrit certaines opérations de paramétrage personnalisé qui peuvent être effectuées.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Optimisations générales

Ces modifications peuvent être recommandées selon les qualités du jeu de données ou le style de flux de travail.

* Si la charge de travail est lourde en écriture, augmentez la taille par défaut du cache en écriture de 20 %.
* Si le jeu de données implique beaucoup de petits fichiers, augmentez la limite du nombre de fichiers du cache du cluster.
* Si le travail implique la copie ou le déplacement de données entre deux dépôts, ajustez le nombre de threads utilisés pour le déplacement des données :
  * Pour améliorer la vitesse, vous pouvez augmenter le nombre de threads parallèles utilisés.
  * Si le volume de stockage back-end est surchargé, vous devrez peut-être réduire le nombre de threads parallèles utilisés.
* Si le cluster met en cache des données pour un système de stockage principal (core filer) qui utilise des listes de contrôle d’accès (ACL) NFSv4, activez la mise en cache du mode d’accès afin de simplifier l’autorisation de fichier pour les clients particuliers.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Optimisations de serveur NAS ou de passerelle cloud

Pour tirer parti des vitesses de données plus élevées entre le cluster vFXT et le stockage cloud dans un scénario de serveur NAS ou de passerelle cloud (où le cluster vFXT fournit un accès de style NAS à un conteneur cloud), le technicien peut vous conseiller de modifier des paramètres semblables à ceux-ci pour effectuer de manière plus intense une transmission des données de type push vers le volume de stockage à partir du cache :

* Augmenter le nombre de connexions TCP entre le cluster et le conteneur de stockage

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Optimisations de réseau étendu hybride ou de cloud bursting

Dans un scénario de cloud bursting ou d’optimisation de réseau étendu de stockage hybride (où le cluster vFXT assure une intégration entre le cloud et le stockage matériel local), ces modifications peuvent être utiles :

* Augmenter le nombre de connexions TCP autorisées entre le cluster et le système de stockage principal
* Activer le paramètre d’optimisation de réseau étendu pour le système de stockage principal à distance (ce paramètre peut être utilisé pour un système de stockage local à distance ou un système de stockage principal cloud dans une autre région Azure).
* Augmenter la taille de mémoire tampon du socket TCP (en fonction de la charge de travail et des besoins en termes de performances)
* Activer le paramètre de transfert systématique afin de réduire les fichiers mis en cache de manière redondante (en fonction de la charge de travail et des besoins en termes de performances)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Optimisation de votre système Avere vFXT pour Azure

Utilisez la procédure décrite dans [Aide avec votre système](avere-vfxt-open-ticket.md) pour contacter le personnel du support technique au sujet de ces optimisations.
