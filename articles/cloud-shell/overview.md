---
title: Vue d’ensemble d’Azure Cloud Shell | Microsoft Docs
description: Vue d’ensemble d’Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832460"
---
# <a name="overview-of-azure-cloud-shell"></a>Vue d’ensemble d’Azure Cloud Shell
Azure Cloud Shell est un interpréteur de commandes interactif, authentifié et accessible par navigateur qui permet de gérer les ressources Azure.
Il vous donne la possibilité de choisir l'expérience d'interpréteur de commandes la plus adaptée à votre façon de travailler, qu'il s'agisse de Bash ou de PowerShell.

Essayez à partir de shell.azure.com en cliquant ci-dessous.

[![Lancement de l’incorporation](https://shell.azure.com/images/launchcloudshell.png "Lancement d’Azure Cloud Shell")](https://shell.azure.com)

Essayez à partir du Portail Azure en cliquant sur l’icône Cloud Shell.

![Lancement du portail](media/overview/portal-launch-icon.png)

## <a name="features"></a>Fonctionnalités

### <a name="browser-based-shell-experience"></a>Expérience shell basée sur navigateur
Cloud Shell permet d’accéder à une expérience de ligne de commande basée sur navigateur avec les tâches de gestion Azure à l’esprit.
Exploitez Cloud Shell pour travailler librement à partir d’une machine locale d’une façon que seul le cloud peut fournir.

### <a name="choice-of-preferred-shell-experience"></a>Choix de votre expérience d’interpréteur de commandes préféré
Les utilisateurs peuvent choisir entre Bash ou PowerShell.
1. Sélectionnez **Cloud Shell**.

    ![Icône Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Sélectionnez **Bash** ou **PowerShell**.

    ![Choisir Bash ou PowerShell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Station de travail Azure configurée et authentifiée
Service managé par Microsoft, Cloud Shell est livré avec des outils en ligne de commande usuels et une prise en charge de langages. De plus, il s’authentifie automatiquement de façon sécurisée pour garantir un accès immédiat aux ressources par l’intermédiaire d’Azure CLI ou des cmdlets Azure PowerShell.

Consultez la [liste complète des outils installés dans Cloud Shell](features.md#tools).

### <a name="integrated-cloud-shell-editor"></a>Éditeur Cloud Shell intégré
Cloud Shell propose un éditeur de texte graphique intégré s’appuyant sur l’éditeur open source Monaco. Créez et modifiez facilement des fichiers de configuration en exécutant `code .` pour un déploiement fluide via Azure CLI ou Azure PowerShell.

[En savoir plus sur l’éditeur Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Intégré avec docs.microsoft.com

Vous pouvez utiliser Cloud Shell directement depuis la documentation hébergée sur [docs.microsoft.com](https://docs.microsoft.com). Il est intégré dans [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)et la [documentation d’Azure CLI](https://docs.microsoft.com/cli/azure) - cliquez sur le bouton « Essayer » dans un extrait de code pour ouvrir l'expérience immersive du shell. 

### <a name="multiple-access-points"></a>Plusieurs points d’accès
Cloud Shell est un outil flexible qui s’utilise à partir de :
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentation d’Azure CLI](https://docs.microsoft.com/cli/azure)
* [Documentation d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Application mobile Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extension Azure Account pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Connecter votre stockage Microsoft Azure Files
Les machines Cloud Shell sont temporaires, mais vos fichiers sont conservés de deux manières : via une image disque et via un partage de fichiers monté nommé `clouddrive`.  Lors du premier lancement, Cloud Shell vous invite à créer un groupe de ressources, un compte de stockage et un partage Azure Files en votre nom. Il s’agit d’une étape unique, et ces ressources sont automatiquement jointes pour toutes les sessions. Un partage de fichier unique peut être mappé et sera utilisé par Bash et PowerShell dans Cloud Shell.

Apprenez à monter un [compte de stockage nouveau ou existant](persisting-shell-storage.md) ou découvrez-en plus sur les [mécanismes de persistance utilisés dans Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Le pare-feu de stockage Azure n’est pas pris en charge pour les comptes de stockage Cloud Shell.

## <a name="concepts"></a>Concepts
* Cloud Shell s’exécute sur un hôte temporaire fourni par session et par utilisateur
* Cloud Shell expire après 20 minutes sans activité interactive
* Cloud Shell requiert qu’un partage de fichiers Azure soit monté
* Cloud Shell utilise le même partage de fichiers Azure pour Bash et PowerShell
* Cloud Shell est affecté à une machine par compte d’utilisateur
* Cloud Shell rend $HOME persistant en conservant une image de 5 Go dans votre partage de fichiers
* Les autorisations sont définies en tant qu’utilisateur Linux standard dans Bash

En savoir plus sur les fonctionnalités de [Bash dans Cloud Shell](features.md) et [PowerShell dans Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Tarifs
La machine qui héberge Cloud Shell est gratuite, avec comme condition préalable le montage d’un partage Azure Files. Les coûts de stockage standard s’appliquent.

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Bash dans Cloud Shell](quickstart.md) <br>
[Démarrage rapide de PowerShell dans Cloud Shell](quickstart-powershell.md)
