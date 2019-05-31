---
title: Utilisez des Machines virtuelles de science des données Azure
description: Se connecter à un serveur Azure Data Science Virtual Machine (DSVM) pour étendre la puissance de calcul disponible pour les blocs-notes Azure.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: getroyer
ms.openlocfilehash: ab3b742d50cc141420f9bffa1961a6e170b99d2a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234352"
---
# <a name="use-azure-data-science-virtual-machines"></a>Utilisez des Machines virtuelles de science des données Azure

Par défaut, les projets s’exécutent le **calcul gratuit** niveau, qui est limité à 4 Go de mémoire et 1 Go de données pour éviter les abus. Vous pouvez contourner ces limitations en utilisant une autre machine virtuelle configurée dans un abonnement Azure. À cet effet, le meilleur choix est une Machine virtuelle de science des données (DSVM) Azure à l’aide de la **Data Science Virtual Machine pour Linux (Ubuntu)** image. Une machine virtuelle DSVM est préconfigurée avec tout ce dont vous avez besoin pour les blocs-notes Azure et s’affiche automatiquement sur le **exécuter** liste déroulante dans Azure Notebooks.

> [!Note]
> Azure Notebooks est pris en charge uniquement sur les machines virtuelles Dsvm créée avec l’image Linux Ubuntu on. Ordinateurs portables ne sont pas pris en charge sur les images Windows 2012, Windows 2016 ou CentOS Linux.

## <a name="create-a-dsvm-instance"></a>Créer une instance de machine virtuelle DSVM

Pour créer une instance DSVM, suivez les instructions de la section [Créer une machine virtuelle Data Science Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Pour plus d’informations, y compris la tarification, consultez [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Se connecter à la machine DSVM

Une fois que vous avez créé la machine virtuelle DSVM, sélectionnez le **exécuter** liste déroulante sur les blocs-notes Azure tableau de bord de projet, sélectionnez l’instance appropriée de la science des données. La liste déroulante affiche les instances de machine virtuelle DSVM si les conditions suivantes sont remplies :

- Vous avez ouvert une session Azure Notebooks avec un compte qui utilise Azure Active Directory (AAD), par exemple un compte professionnel.
- Votre compte est connecté à un abonnement Azure.
- Vous disposez d’au moins un ou plusieurs machines virtuelles dans cet abonnement, avec accès en lecture, qui utilise la Machine virtuelle de science des données pour une image Linux (Ubuntu).)

![Instances Data Science Virtual Machine dans la liste déroulante sur le tableau de bord du projet](media/project-compute-tier-dsvm.png)

Lorsque vous sélectionnez une instance DSVM, Azure Notebooks peut vous demander les informations d’identification de l’ordinateur utilisé lors de la création de la machine virtuelle.

Si une des conditions ne sont pas remplie, vous pouvez toujours vous connecter à la machine virtuelle DSVM. Dans la liste déroulante, sélectionnez le **calcul Direct** option, qui vous invite à entrer un nom (pour l’afficher dans la liste), adresse IP de la machine virtuelle et port (généralement 8000, au port par défaut qui écoute JupyterHub) et les informations d’identification de la machine virtuelle :

![Invite à collecter des informations sur le serveur pour l’option Calcul direct](media/project-compute-tier-direct.png)

Vous obtenez ces valeurs à partir de la page de science des données dans le portail Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>L’accès aux fichiers de blocs-notes Azure à partir de la machine virtuelle DSVM

Conserver la parité des chemins de fichiers avec le **calcul gratuit** niveau, vous êtes en mesure d’ouvrir uniquement un projet à la fois sur une machine virtuelle DSVM. Pour ouvrir un nouveau projet, vous devez arrêter le projet ouvert tout d’abord.

![Bouton d’arrêt dans Azure Notebooks](media/shutdown.png)

Quand un projet est exécuté sur une machine virtuelle, les fichiers sont montés sur le répertoire racine du serveur Jupyter (le répertoire indiqué dans JupyterHub), en remplaçant les fichiers de blocs-notes Azure par défaut. Lorsque vous arrêtez la machine virtuelle en utilisant le **arrêt** bouton dans le bloc-notes, l’interface utilisateur, les blocs-notes Azure restaure les fichiers par défaut.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les machines virtuelles Dsvm sur [Introduction à Azure Data Science Virtual Machine](/azure/machine-learning/data-science-virtual-machine/overview).
