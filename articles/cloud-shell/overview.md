---
title: Vue d’ensemble d’Azure Cloud Shell | Microsoft Docs
description: Vue d’ensemble d’Azure Cloud Shell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: juluk
ms.openlocfilehash: 85d054be6b72c736ec66384b9caec71208f36802
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869361"
---
# <a name="overview-of-azure-cloud-shell"></a>Vue d’ensemble d’Azure Cloud Shell
Azure Cloud Shell est un shell interactif, accessible par navigateur pour la gestion des ressources Azure.
Il vous donne la possibilité de choisir l’expérience d’interpréteur de commandes la plus adaptée à votre façon de travailler.
Les utilisateurs Linux peuvent choisir une expérience Bash, et les utilisateurs Windows l’option PowerShell.

Essayez à partir de shell.azure.com en cliquant ci-dessous.

[![Lancement incorporé](https://shell.azure.com/images/launchcloudshell.png "Lancer Azure Cloud Shell")](https://shell.azure.com)

Essayez à partir du Portail Azure en cliquant sur l’icône Cloud Shell.

![Lancement du portail](media/overview/portal-launch-icon.png)

## <a name="features"></a>Caractéristiques

### <a name="browser-based-shell-experience"></a>Expérience shell basée sur navigateur
Cloud Shell permet d’accéder à une expérience de ligne de commande basée sur navigateur avec les tâches de gestion Azure à l’esprit.
Exploitez Cloud Shell pour travailler librement à partir d’une machine locale d’une façon que seul le cloud peut fournir.

### <a name="choice-of-preferred-shell-experience"></a>Choix de votre expérience d’interpréteur de commandes préféré
Les utilisateurs peuvent choisir entre Bash ou PowerShell dans la liste déroulante des interpréteurs de commandes.

![Bash dans Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell dans Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Station de travail Azure configurée et authentifiée
Service managé par Microsoft, Cloud Shell est livré avec des outils en ligne de commande usuels et une prise en charge de langages. De plus, il s’authentifie automatiquement de façon sécurisée pour garantir un accès immédiat aux ressources par l’intermédiaire d’Azure CLI ou des cmdlets Azure PowerShell.

Consultez la [liste complète des outils installés dans Cloud Shell](features.md#tools).

### <a name="integrated-cloud-shell-editor"></a>Éditeur Cloud Shell intégré
Cloud Shell propose un éditeur de texte graphique intégré s’appuyant sur l’éditeur open source Monaco. Créez et modifiez des fichiers de configuration facilement en exécutant `code .` pour un déploiement fluide via Azure CLI 2.0 ou Azure PowerShell.

[En savoir plus sur l’éditeur Cloud Shell](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Plusieurs points d’accès
Cloud Shell est un outil flexible qui s’utilise à partir de :
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentation « Essayez-le » d’Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Application mobile Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extension de compte Azure VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Connecter votre stockage Microsoft Azure Files
Les machines Cloud Shell, temporaires, ont besoin qu’un partage Azure Files (nouveau ou existant) soit monté en tant que `clouddrive` pour pouvoir conserver les fichiers.

Lors du premier lancement, Cloud Shell vous invite à créer un groupe de ressources, un compte de stockage et un partage Azure Files en votre nom. Il s’agit d’une étape unique, et ces ressources sont automatiquement jointes pour toutes les sessions. Un partage de fichier unique peut être mappé et sera utilisé par Bash et PowerShell dans Cloud Shell.

En savoir plus pour la façon de monter un [compte de stockage nouveau ou existant](persisting-shell-storage.md).

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
