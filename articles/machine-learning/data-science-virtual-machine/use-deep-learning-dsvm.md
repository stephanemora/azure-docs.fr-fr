---
title: Science des données avec Deep Learning Data Science Virtual Machine sur Azure | Microsoft Docs
description: Comment effectuer plusieurs tâches courantes de science des données avec Deep Learning Data Science Virtual Machine.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-deep-learning-virtual-machine"></a>Utilisation de Deep Learning Virtual Machine

Après avoir provisionné votre machine virtuelle DLVM (Deep Learning Virtual Machine), vous pouvez commencer à créer des modèles de réseau neuronal profonds pour générer des applications d’intelligence artificielle dans des domaines tels que la vision par ordinateur et la reconnaissance vocale. 

La machine virtuelle DLVM fournit une série d’outils pour l’intelligence artificielle. La [page sur les frameworks d’apprentissage profond et d’intelligence artificielle](dsvm-deep-learning-ai-frameworks.md) décrit l’utilisation de ces outils. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Didacticiels et procédures pas à pas de l’apprentissage profond

Outre les exemples basés sur les frameworks, un ensemble de procédures détaillées complètes qui ont été validées sur la machine virtuelle DLVM est également fourni. Ces procédures vous permettent de vous lancer dans le développement d’applications d’apprentissage profond dans des domaines tels que la reconnaissance vocale ou bien la compréhension d’image et de texte. Des didacticiels complets ayant trait à différents domaines et technologies sont continuellement ajoutés.   


- [Exécution de réseaux neuronaux sur différents frameworks](https://github.com/ilkarman/DeepLearningFrameworks) : procédure pas à pas complète qui montre comment migrer du code depuis un framework vers un autre. Elle montre également comment comparer les performances des modèles et d’exécution entre les frameworks. 

- [Guide pratique pour créer une solution de bout en bout afin de détecter des produits dans des images](https://github.com/Azure/cortana-intelligence-product-detection-from-images) : la détection d’image est une technique qui peut localiser des objets dans les images et les classer. Les retombées concrètes de cette technologie semblent très prometteuses dans de nombreux domaines professionnels. Par exemple, un détaillant peut utiliser cette technique pour déterminer quel produit un client a choisi dans un rayon. Il peut ensuite utiliser ces informations pour gérer le stock des produits. 

- [Extraction d’entités nommées à partir de résumés PubMed](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) : ce tutoriel montre comment extraire des entités nommées, telles que les noms de médicament ou de maladie, à partir de texte non structuré. Il entraîne une incorporation de mots personnalisée dans un corpus constitués de 18 millions de résumés PubMed, utilise ce modèle pour générer un modèle de réseau neuronal récurrent de mémoire à court et long terme pour l’extraction des entités, et montre que le modèle d’incorporation de mots propres à un domaine peut être plus performant qu’un modèle d’incorporation de mots génériques pour l’extraction d’entités.

- [Modèle d’apprentissage profond pour l’audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) : Ce tutoriel montre comment entraîner un modèle d’apprentissage profond pour la détection des événements audio dans le [jeu de données Urban Sounds](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html), et explique comment utiliser les données audio.

- [Classification de documents texte](https://github.com/anargyri/lstm_han) : cette procédure pas à pas montre comment créer et former deux architectures de réseau neuronal différentes : réseau d’attention hiérarchisée et réseau à mémoire à long ou court terme. Ces réseaux neuronaux utilisent l’API Keras de l’apprentissage profond pour classer les documents texte. Keras facilite l’utilisation de trois des frameworks d’apprentissage profond les plus populaires, à savoir Microsoft Cognitive Toolkit, TensorFlow et Theano.

## <a name="next-steps"></a>Étapes suivantes

La [page d’exemples](dsvm-samples-and-walkthroughs.md) fournit des pointeurs vers des exemples de code préchargés sur la machine virtuelle pour chacun des frameworks afin de vous aider à démarrer rapidement. 
