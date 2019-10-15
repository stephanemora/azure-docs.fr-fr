---
title: Exemples de types de classes dans Azure Lab Services | Microsoft Docs
description: Cite certains des types de classes pour lesquels il est possible de configurer des laboratoires à l’aide d’Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0e8b203240b4b2d6b67534ab52a7cd4ccf5df571
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976595"
---
# <a name="class-types-overview---azure-lab-services"></a>Vue d’ensemble des types de classes - Azure Lab Services
Azure Lab Services vous permet de configurer rapidement un environnement lab de salle de classe dans le cloud. Les articles de cette section fournissent des conseils sur la configuration de plusieurs types de laboratoires de classe à l’aide d’Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Deep Learning pour le traitement en langage naturel
Vous pouvez configurer un laboratoire axé sur le Deep Learning dans le cadre du traitement en langage naturel (NLP) à l’aide d’Azure Lab Services. Le traitement en langage naturel (NLP) est une forme d’intelligence artificielle qui fournit aux ordinateurs des fonctionnalités de traduction, de reconnaissance vocale et d’autres fonctionnalités de compréhension de la langue. Les étudiants qui suivent une classe de traitement en langage naturel vont entraîner une machine virtuelle Linux à appliquer des algorithmes de réseau neuronal afin de développer des modèles de Deep Learning utilisés pour l’analyse de la langue humaine écrite. 

Pour plus d’informations sur la configuration de ce type de laboratoire, consultez [Configurer un laboratoire axé sur le Deep Learning pour le traitement en langage naturel à l’aide d’Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Scripts shell sur Linux
Vous pouvez configurer un laboratoire pour enseigner la création de scripts shell sur Linux. Dans le cadre de l’administration système, l’écriture de scripts permet aux administrateurs d’éviter les tâches répétitives. Dans cet exemple de scénario, les scripts bash traditionnels et les scripts améliorés sont abordés. Les scripts améliorés sont des scripts qui associent des commandes bash et Ruby. Cette approche permet à Ruby de passer des données et fournit des commandes bash pour interagir avec l’interpréteur de commandes. 

Les étudiants qui suivent ces classes d’écriture de script entraînent une machine virtuelle Linux à apprendre les bases de Linux, et se familiarisent avec les scripts d’interpréteur de commandes bash. La machine virtuelle Linux est fournie avec un accès au Bureau à distance. En outre, les éditeurs de texte [gedit](https://help.gnome.org/users/gedit/stable/) et [Visual Studio Code](https://code.visualstudio.com/) y sont installés.

Pour plus d’informations sur la configuration de ce type de laboratoire, consultez [Scripts shell sur Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Piratage éthique 
Vous pouvez configurer un labo de classe qui se concentre sur l’analyse forensique du piratage éthique. Des tests d’intrusion, une pratique utilisée par la communauté de piratage éthique, sont effectués quand quelqu’un tente d’accéder au système ou au réseau pour détecter les vulnérabilités qu’un attaquant malveillant pourrait exploiter. 

Dans une classe sur le piratage éthique, les étudiants apprennent les techniques modernes de défense face aux vulnérabilités. Chaque étudiant a accès à une machine virtuelle hôte Windows Server qui a deux machines virtuelles imbriquées : une machine virtuelle avec une image **Metaspoiltable** et une autre machine avec une image [Kali Linux](https://www.kali.org/). La machine virtuelle Metasploitable est utilisée pour les besoins de fonctionnement, et la machine virtuelle Kali sert à accéder aux outils nécessaires pour exécuter les tâches forensiques.

Pour plus d’informations sur la configuration de ce type de labo, consultez [Configurer un labo de classe sur le piratage éthique](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- [Configurer un laboratoire axé sur le Deep Learning dans le cadre du traitement en langage naturel (NLP) à l’aide d’Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Scripts shell sur Linux](class-type-shell-scripting-linux.md)