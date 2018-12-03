---
title: Exemples Azure PowerShell - App Service | Microsoft Docs
description: Exemples Azure PowerShell - App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290737"
---
# <a name="azure-powershell-samples"></a>Exemples Azure PowerShell

Le tableau suivant contient des liens vers des scripts PowerShell créés à l’aide d’Azure PowerShell.

| | |
|-|-|
|**Créer une application**||
| [Créer une application avec un déploiement à partir de GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application web Azure qui extrait le code à partir de GitHub. |
| [Créer une application avec un déploiement continu à partir de GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application web Azure qui déploie du code en continu à partir de GitHub. |
| [Créer une application et déployer du code avec FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application web Azure et télécharge des fichiers à partir d’un répertoire local via FTP. |
| [Créer une application et déployer le code à partir d’un dépôt Git local](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application web Azure et configure la transmission de code de type push à partir d’un référentiel Git local. |
| [Créer une application et déployer le code dans un environnement de préproduction](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application web Azure avec un emplacement de déploiement pour les modifications de code intermédiaires. |
|**Configurer l’application**||
| [Mapper un domaine personnalisé à une application](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application web Azure et mappe un nom de domaine personnalisé. |
| [Lier un certificat SSL personnalisé à une application](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application web Azure et lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. |
|**Mettre à l’échelle une application**||
| [Mettre à l’échelle une application manuellement](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application web Azure et la met à l’échelle entre 2 instances. |
| [Mettre à l’échelle une application dans le monde entier avec une architecture haute disponibilité](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée deux applications web Azure dans deux régions géographiques différentes et les rend disponibles par le biais d’un point de terminaison unique à l’aide d’Azure Traffic Manager. |
|**Connecter l’application aux ressources**||
| [Connecter une application à une instance SQL Database](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application web Azure et une instance SQL Database, puis ajoute la chaîne de connexion de base de données aux paramètres d’application. |
| [Connecter une application à un compte de stockage](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application web Azure et un compte de stockage, puis ajoute la chaîne de connexion de stockage aux paramètres d’application. |
|**Sauvegarder et restaurer une app**||
| [Sauvegarder une application](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une app web Azure et une sauvegarde unique pour celle-ci. |
| [Créer une sauvegarde planifiée pour une application](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une app web Azure et une sauvegarde planifiée pour celle-ci. |
| [Supprimer une sauvegarde d’application](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Supprime une sauvegarde existante d’une application. |
| [Restaurer une application à partir d’une sauvegarde](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaure une application à partir d’une sauvegarde. |
| [Restaurer une sauvegarde dans plusieurs abonnements](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaure une application web à partir d’une sauvegarde dans un autre abonnement. |
|**Surveiller l’application**||
| [Superviser une application avec les journaux de serveur web](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application web Azure, active sa journalisation et télécharge les journaux sur votre ordinateur local. |
| | |
