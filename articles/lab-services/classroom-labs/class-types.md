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
ms.openlocfilehash: e1c5504b30c2784e8657ccc0dc4ec18689fe2a68
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806811"
---
# <a name="class-types-overview---azure-lab-services"></a>Vue d’ensemble des types de classes - Azure Lab Services

Azure Lab Services vous permet de configurer rapidement un environnement lab de salle de classe dans le cloud. Les articles de cette section fournissent des conseils sur la configuration de plusieurs types de laboratoires de classe à l’aide d’Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Deep Learning pour le traitement en langage naturel

Vous pouvez configurer un laboratoire axé sur le Deep Learning dans le cadre du traitement en langage naturel (NLP) à l’aide d’Azure Lab Services. Le traitement en langage naturel (NLP) est une forme d’intelligence artificielle qui fournit aux ordinateurs des fonctionnalités de traduction, de reconnaissance vocale et d’autres fonctionnalités de compréhension de la langue. Les étudiants qui suivent une classe de traitement en langage naturel vont entraîner une machine virtuelle Linux à appliquer des algorithmes de réseau neuronal afin de développer des modèles de Deep Learning utilisés pour l’analyse de la langue humaine écrite.

Pour plus d’informations sur la configuration de ce type de laboratoire, consultez [Configurer un laboratoire axé sur le Deep Learning pour le traitement en langage naturel à l’aide d’Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Scripts shell sur Linux

Vous pouvez configurer un laboratoire pour enseigner la création de scripts shell sur Linux. Dans le cadre de l’administration système, l’écriture de scripts permet aux administrateurs d’éviter les tâches répétitives. Dans cet exemple de scénario, les scripts bash traditionnels et les scripts améliorés sont abordés. Les scripts améliorés sont des scripts qui associent des commandes bash et Ruby. Cette approche permet à Ruby de passer des données et fournit des commandes bash pour interagir avec le shell.

Les étudiants qui suivent ces cours d’écriture de scripts disposent d’une machine virtuelle Linux pour apprendre les bases de Linux et également se familiariser avec les scripts de shell bash. La machine virtuelle Linux est fournie avec un accès au Bureau à distance. En outre, les éditeurs de texte [gedit](https://help.gnome.org/users/gedit/stable/) et [Visual Studio Code](https://code.visualstudio.com/) y sont installés.

Pour plus d’informations sur la configuration de ce type de laboratoire, consultez [Scripts shell sur Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Piratage éthique

Vous pouvez configurer un labo de classe qui se concentre sur l’analyse forensique du piratage éthique. Des tests d’intrusion, une pratique utilisée par la communauté de piratage éthique, sont effectués quand quelqu’un tente d’accéder au système ou au réseau pour détecter les vulnérabilités qu’un attaquant malveillant pourrait exploiter.

Dans une classe sur le piratage éthique, les étudiants apprennent les techniques modernes de défense face aux vulnérabilités. Chaque étudiant a accès à une machine virtuelle hôte Windows Server qui comporte deux machines virtuelles imbriquées : une avec une image [Metasploitable3](https://github.com/rapid7/metasploitable3) et une autre avec une image [Kali Linux](https://www.kali.org/). La machine virtuelle Metasploitable est utilisée à des fins d’exploitation.  La machine virtuelle Kali Linux permet d’accéder aux outils nécessaires pour exécuter des tâches d’investigation.

Pour plus d’informations sur la configuration de ce type de labo, consultez [Configurer un labo de classe sur le piratage éthique](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants :

- [Configurer un laboratoire axé sur le Deep Learning dans le cadre du traitement en langage naturel (NLP) à l’aide d’Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Scripts shell sur Linux](class-type-shell-scripting-linux.md)
- [Piratage éthique](class-type-ethical-hacking.md)
