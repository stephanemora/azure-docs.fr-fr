---
title: Configurer un laboratoire pour enseigner la science des données avec Python et Jupyter Notebooks | Microsoft Docs
description: Découvrez comment configurer un laboratoire pour enseigner la science des données avec Python et Jupyter Notebooks.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444109"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Configurer un laboratoire pour enseigner la science des données avec Python et Jupyter Notebooks

Cet article explique comment configurer une machine modèle dans Lab Services avec les outils nécessaires pour enseigner aux élèves comment utiliser [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks est un projet open source qui vous permet de combiner facilement du texte enrichi et du code source [Python](https://www.python.org/) exécutable sur un seul canevas appelé notebook.  L’exécution d’un notebook produit un enregistrement linéaire des entrées et des sorties.  Ces sorties peuvent inclure du texte, des tables d’informations, des nuages de points, etc.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de laboratoire, consultez notre [Tutoriel pour configurer un compte Lab](tutorial-setup-lab-account.md).  Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres décrits dans le tableau ci-dessous pour le compte lab. Pour plus d’informations sur l’activation des images de la Place de Marché, consultez [Spécifier les images de la Place de Marché accessibles aux créateurs de labo](specify-marketplace-images.md).

| Paramètres du compte lab | Instructions |
| ------------------- | ------------ |
| Image de la Place de marché | Activez l’image [Data Science Virtual Machine - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) à utiliser au sein de votre compte lab. |

>[!TIP]
>Cet article se concentre sur la configuration d’une machine modèle qui utilise le système d’exploitation Windows Server.  Il est également possible de configurer une classe de science des données avec Python et Jupyter Notebooks à l’aide des images [Data Science Virtual Machine pour Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) ou [Data Science Virtual Machine pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) à partir de la Place de marché Azure.

### <a name="lab-settings"></a>Paramètres du labo

Utilisez les paramètres du tableau ci-dessous lors de la configuration d’un laboratoire de classe.  Pour plus d’informations sur la création d’un laboratoire de classe, consultez le [didacticiel Configurer un laboratoire de salle de classe](tutorial-setup-classroom-lab.md).

| Paramètres du labo | Valeur/instructions |
| ------------ | ------------------ |
|Taille de la machine virtuelle| GPU de petite taille (calcul). Cette taille est optimisée pour les applications nécessitant beaucoup de ressources système et de ressources réseau, notamment l’intelligence artificielle et le deep learning. |
|Image de machine virtuelle| Data Science Virtual Machine – Windows 2016|

## <a name="template-machine"></a>Machine modèle

L’image [Data Science Virtual Machine - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) fournit les outils et infrastructures de Deep Learning nécessaires pour ce type de cours.  L’image comprend Jupyter Notebooks et Visual Studio Code.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) est une application web qui permet aux scientifiques des données de prendre des données brutes, d’exécuter des calculs et de voir les résultats dans le même environnement.  Pour notre machine modèle, l’application web s’exécutera localement.  [Visual Studio Code](https://code.visualstudio.com/) est un IDE qui offre une expérience interactive riche lors de l’écriture et du test d’un notebook.  Pour plus d’informations, consultez [Utilisation de Jupyter Notebooks dans Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

La tâche restante pour configurer la classe consiste à fournir des notebooks locaux.  Pour obtenir des instructions sur l’utilisation des exemples Azure Machine Learning, consultez [Comment configurer un environnement avec Jupyter Notebooks](../../machine-learning/how-to-configure-environment.md#jupyter).  Vous pouvez également fournir vos propres notebooks sur la machine modèle.  Les notebooks seront copiés sur tous les ordinateurs des étudiants lors de la publication du modèle.

## <a name="cost-estimate"></a>Estimation du coût

Nous allons aborder une estimation de coût possible pour cette classe.  Nous allons utiliser une classe de 25 élèves.  Nous prévoyons 20 heures de temps de classe.  En outre, chaque étudiant obtient un quota de 10 heures pour les devoirs ou travaux en dehors des heures de cours planifiées.  La taille de machine virtuelle que nous avons choisie était avec un petit GPU (calcul), soit 139 unités Lab.

Voici un exemple d’estimation de coût possible pour cette classe :

25 élèves \* (20 heures planifiées + 10 heures de quota) \* 139 unités Lab \* 0,01 USD par heure = 1 042,5 USD

Pour plus d’informations sur les tarifs, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusion

Dans cet article, nous avons parcouru les étapes de création d’un laboratoire pour une classe Jupyter Notebooks. Vous pouvez utiliser une configuration similaire pour d’autres cours de Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Les étapes suivantes sont communes à la configuration de n’importe quel labo.

- [Créer et gérer un modèle](how-to-create-manage-template.md)
- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)
