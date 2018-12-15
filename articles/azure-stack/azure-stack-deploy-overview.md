---
title: Évaluer le Kit de développement Azure Stack | Microsoft Docs
description: En savoir plus sur le déploiement du Kit de développement Azure Stack à des fins d’évaluation.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: jeffgilb
ms.custom: mvc
ms.reviewer: misainat
ms.openlocfilehash: 44fed3311234e1a64cb46c3403f39a9e269d189b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956923"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Démarrage rapide : évaluer le Kit de développement Azure Stack

Le [Kit de développement Azure Stack (ASDK)](./asdk/asdk-what-is.md) est un environnement de développement et de test que vous pouvez déployer pour évaluer et présenter les fonctionnalités et services Azure Stack. Pour prendre en main le kit de développement Azure Stack, vous devez préparer le matériel informatique hôte et exécuter quelques scripts (l’installation prend plusieurs heures). Une fois que vous aurez effectué ces étapes préalables, vous pourrez vous connecter aux portails de l’administrateur ou de l’utilisateur pour commencer à utiliser Azure Stack.

## <a name="prerequisites"></a>Prérequis

### <a name="asdk-host-computer-requirements"></a>Exigences de l’ordinateur hôte ASDK

Avant d’installer le Kit de développement Azure Stack, vous devez préparer l’ordinateur qui hébergera le Kit de développement. L’ordinateur hôte du Kit de développement doit répondre aux exigences relatives aux matériel, aux logiciels et à la configuration réseau requise décrite dans **[Considérations liées à la planification du déploiement d’Azure Stack](./asdk/asdk-deploy-considerations.md)**.

> [!TIP]
> Vous pouvez utiliser l’[outil de vérification des exigences de déploiement Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) après l’installation du système d’exploitation sur l’ordinateur hôte du kit de développement pour confirmer que votre matériel réponde à toutes les exigences.

### <a name="account-requirements"></a>Exigences pour les comptes

Vous devez aussi choisir entre utiliser Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS) en tant que solution d’identité de votre déploiement. Passez en revue la configuration requise du compte dans **[Considérations liées à la planification du déploiement](./asdk/asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Téléchargez et extrayez le package de déploiement

Après avoir préparé l’ordinateur hôte de votre Kit de développement, téléchargez et extrayez le package de déploiement ASDK. Le package de déploiement contient le fichier Cloudbuilder.vhdx, un disque dur virtuel (VHD) qui contient un système d’exploitation démarrable et les fichiers d’installation d’Azure Stack.

Vous pouvez télécharger le package de déploiement sur l’hôte du Kit de développement ou sur un autre ordinateur. Une fois extraits, les fichiers de déploiement occupent jusqu’à 60 Go d’espace disque. L’utilisation d’un autre ordinateur peut vous permettre de limiter les exigence de stockage sur l’hôte du Kit de développement.

**[Télécharger et extraire le Kit de développement Azure Stack (ASDK)](./asdk/asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Préparer l’ordinateur hôte

Avant de pouvoir installer le Kit de développement Azure Stack (ASDK), l’environnement hôte doit être préparé et le système doit être configuré pour démarrer à partir du disque dur virtuel du Kit de développement. Lorsque vous redémarrez l’hôte, il démarre à partir de CloudBuilder.vhdx et vous pouvez commencer à déployer le Kit de développement ASDK.

**[Préparer l’ordinateur hôte du kit de développement Azure Stack](./asdk/asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installer le kit de développement Azure Stack sur l’ordinateur hôte

Une fois que l’ordinateur hôte démarre sur le disque dur virtuel, vous pouvez déployer le Kit de développement sur l’environnement virtuel Cloudbuilder. Vous pouvez déployer le Kit de développement Azure Stack à l’aide d’une interface graphique utilisateur (GUI) fournie en exécutant le script PowerShell asdk-installer.ps1, ou depuis [la ligne de commande PowerShell](./asdk/asdk-deploy-powershell.md).

> [!NOTE]
> Après le démarrage de l’ordinateur hôte à partir de l’image CloudBuilder.vdhx, vous avez la possibilité de configurer les [paramètres de télémétrie Azure Stack](./asdk/asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *avant* d’installer le Kit de développement Azure Stack.

**[Installer le Kit de développement Azure Stack (ASDK)](./asdk/asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Effectuer des configurations post-déploiement

Après avoir installé le Kit de développement Azure Stack, nous vous recommandons d’effectuer quelques vérifications post-installation et des modifications de configuration.

**outils**

Installez les outils Azure Stack PowerShell et GitHub, et vérifiez le succès de votre installation à l’aide de la cmdlet test-AzureStack.

**Solution d’identité**

Pour un déploiement utilisant Azure AD, vous devez activer les portails d’administrateur et de locataire Azure Stack. Cette activation accepte de donner au portail Azure Stack et à Azure Resource Manager les autorisations appropriées (répertoriées sur la page de consentement) pour tous les utilisateurs du répertoire.

**Expiration du mot de passe**

Vous devez réinitialiser la stratégie d’expiration du mot de passe pour faire en sorte que le mot de passe de l’hôte du Kit de développement n’expire pas avant la fin de la période d’expiration.

> [!NOTE]
> Vous pouvez aussi configurer les [Paramètres de télémétries Azure Stack](./asdk/asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *après* avoir installé le Kit de développement Azure Stack.

**[Tâches post-déploiement du kit de développement Azure Stack](./asdk/asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Inscription auprès d’Azure

Vous devez inscrire Azure Stack auprès d’Azure pour pouvoir ensuite [télécharger des éléments marketplace Azure](./asdk/asdk-marketplace-item.md) dans Azure Stack.

**[Inscrire Azure Stack auprès d’Azure](./asdk/asdk-register.md)**

## <a name="next-steps"></a>Étapes suivantes

Félicitations ! En effectuant les étapes décrites dans ce démarrage rapide, vous obtenez un environnement ASDK avec un portail d’[administrateur](https://adminportal.local.azurestack.external) et un portail d’[utilisateur](https://portal.local.azurestack.external).
