---
title: Qu’est-ce que la machine virtuelle Science des données Azure ?
titleSuffix: Azure Data Science Virtual Machine
description: Scénarios et composants d’analytique clés pour Azure Data Science Virtual Machine Windows et Linux.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a5fbcc1eef8717fdb1aa7f914c3e0ba6594fc27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281797"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Qu’est-ce qu’Azure Data Science Virtual Machine pour Linux et Windows ?

La Data Science Virtual Machine (DSVM) est une image de machine virtuelle personnalisée sur la plateforme cloud Azure spécialement conçue pour la science des données. Elle inclut de nombreux outils de science des données populaires qui sont préinstallés et préconfigurés afin d’accélérer la création d’applications intelligentes à des fins d’analytique avancée. 

La Data Science Virtual Machine est disponible sur :
+ **Windows Server 2019**
+ **Ubuntu 18.04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS et CentOS 7.4


> [!NOTE]
> Tous les outils de machine virtuelle pour le Deep Learning ont été intégrés à la DSVM. 


## <a name="why-choose-the-dsvm"></a>Pourquoi choisir une DSVM ?
L’objectif de la DSVM est de fournir aux experts en données, tous niveaux de compétence et rôles confondus, un environnement de science des données simplifié et préconfiguré. Au lieu de déployer un espace de travail comparable par vous-même, vous pouvez provisionner une DSVM. Ce choix peut vous faire gagner des jours, voire des _semaines_, sur les processus d’installation, de configuration et de gestion des packages. Une fois votre DSVM alloué, vous pouvez commencer immédiatement à travailler sur votre projet de science des données.

## <a name="sample-use-cases"></a>Exemples de cas d’utilisation

Dans la section qui suit, nous illustrons quelques cas d’utilisation courants pour les clients DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Déplacement des charges de travail de science des données vers le cloud

Une DSVM fournit une configuration de base pour les équipes de science des données qui souhaitent remplacer leurs postes de travail locaux par un bureau cloud managé. De cette façon, tous les scientifiques des données d’une équipe disposent d’une même configuration permettant de vérifier les expériences et de promouvoir la collaboration. Elle réduit également les coûts en allégeant la charge de travail de l’administrateur système. Cela permet de raccourcir le temps nécessaire à l’évaluation, à l’installation et à la gestion des packages logiciels pour l’analytique avancée.

### <a name="data-science-training-and-education"></a>Formation et éducation de la science des données
Les formateurs et instructeurs en entreprise qui enseignent la science des données fournissent généralement une image de machine virtuelle. Avec l’image, les étudiants disposent d’une installation cohérente et les exemples fonctionnent comme prévu. 

La DSVM crée un environnement à la demande avec une configuration cohérente qui simplifie le support et les soucis de compatibilité. Ceci est d’autant plus bénéfique dans les cas où ces environnements doivent être créés fréquemment, en particulier lors de formations plus courtes.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Flexibilité à la demande pour les projets d’envergure
Les concours de science des données ou la modélisation et l’exploration de données à grande échelle nécessitent une augmentation de la capacité matérielle, généralement sur une courte durée. La DSVM permet de répliquer rapidement et à la demande l’environnement de science des données sur des serveurs montés offrant ainsi des expériences d’exécution des ressources informatiques hautes performances.

### <a name="custom-compute-power-for-azure-notebooks"></a>Puissance de calcul personnalisée pour Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) est un service hébergé gratuit pour développer, exécuter et partager des blocs-notes Jupyter dans le cloud sans aucune installation. Le niveau de service gratuit est limité à 4 Go de mémoire et à 1 Go de données. 

Pour libérer toutes les limites, vous pouvez joindre un projet Notebooks à une DSVM ou à toute autre machine virtuelle s’exécutant sur un serveur Jupyter. Si vous vous connectez à Azure Notebooks avec un compte à l’aide d’Azure Active Directory (par exemple, un compte d’entreprise), Notebooks affiche automatiquement les DSVM dans tous les abonnements associés à ce compte. Vous pouvez [attacher une DSVM à Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) pour développer la puissance de calcul disponible.

### <a name="short-term-experimentation-and-evaluation"></a>Expérimentation et évaluation à court terme
Vous pouvez utiliser une DSVM pour évaluer et apprendre de nouveaux [outils](./tools-included.md) de science des données, notamment en consultant nos [exemples et procédures pas à pas](./dsvm-samples-and-walkthroughs.md).


### <a name="deep-learning-with-gpus"></a>Deep Learning avec les GPU
Dans la DSVM, vos modèles d’entraînement peuvent utiliser des algorithmes de deep learning sur du matériel basé sur des unités de traitement graphique (GPU). En tirant parti des fonctionnalités de mise à l’échelle des machines virtuelles de la plateforme Azure, la DSVM vous aide à utiliser le matériel basé sur GPU dans le cloud en fonction de vos besoins. Vous pouvez basculer vers une machine virtuelle basée sur GPU quand vous entraînez de grands modèles ou que vous avez besoin d’une grande rapidité de calcul, mais souhaitez garder le même disque de système d’exploitation. Vous pouvez choisir librement l’une des références SKU de machines virtuelles de série N avec activation du GPU dans DSVM. Notez que les comptes gratuits Azure ne prennent pas en charge les références SKU de machines virtuelles où le GPU est activé.

L’édition Windows Server 2016 de la DSVM est préinstallée avec les pilotes GPU, les frameworks et les versions GPU des frameworks de deep learning. Sur l’édition Linux, le deep learning sur GPU est activé sur les DSVM CentOS et Ubuntu. 

Vous pouvez également déployer l’édition Ubuntu, CentOS ou Windows 2016 de la DSVM sur une machine virtuelle Azure qui n’est pas basée sur des unités GPU. Dans ce cas, toutes les infrastructures d’apprentissage profond repasseront en mode UC.
 
[Découvrez-en plus sur les frameworks de deep learning et d’intelligence artificielle disponibles](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Ce qui est inclut dans la DSVM

Pour voir la liste complète des outils des DSVM Windows et Linux, [cliquez ici](tools-included.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

+ Windows :
  + [Configurer une DSVM Windows](provision-vm.md)
  + [Dix choses que vous pouvez effectuer sur une DSVM Windows](vm-do-ten-things.md)

+ Linux :
  + [Configurer une DSVM Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Configurer une DSVM Linux (CentOS)](linux-dsvm-intro.md)
  + [Science des données sur une DSVM Linux](linux-dsvm-walkthrough.md)
