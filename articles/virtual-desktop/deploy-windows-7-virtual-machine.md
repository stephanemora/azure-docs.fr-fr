---
title: Déployer une machine virtuelle Windows 7 sur Windows Virtual Desktop - Azure
description: Découvrez comment configurer et déployer une machine virtuelle Windows 7 sur Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
ms.openlocfilehash: 3a6fb67ce531ed8cc028d2d0a8dfc3022544efe0
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947580"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Déployer une machine virtuelle Windows 7 sur Windows Virtual Desktop

Le processus de déploiement d’une machine virtuelle Windows 7 sur Windows Virtual Desktop diffère légèrement de celui des machines virtuelles exécutant des versions ultérieures de Windows. Ce guide vous explique comment déployer Windows 7.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, suivez les instructions de l'article [Créer un pool d’hôtes avec PowerShell](create-host-pools-powershell.md) pour créer un pool d'hôtes. Ensuite, suivez les instructions de l'article [Créer des pools d'hôtes dans la Place de marché Azure](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) pour attribuer un ou plusieurs utilisateurs au groupe d’applications de bureau.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurer une machine virtuelle Windows 7

Une fois les conditions préalables passées en revue, vous êtes prêt à configurer votre machine virtuelle Windows 7 à des fins de déploiement sur Windows Virtual Desktop.

Pour configurer une machine virtuelle Windows 7 sur Windows Virtual Desktop :

1. Connectez-vous au portail Azure et recherchez l’image Windows 7 Entreprise ou téléchargez votre propre image personnalisée Windows 7 Entreprise (x64).  
2. Déployez une ou plusieurs machines virtuelles avec Windows 7 Entreprise en tant que système d’exploitation hôte. Vérifiez que les machines virtuelles autorisent le protocole RDP (Remote Desktop Protocol) (port TCP/3389).
3. Connectez-vous à l’hôte Windows 7 Entreprise à l’aide du protocole RDP et authentifiez-vous avec les informations d’identification que vous avez définies lors de la configuration de votre déploiement. 
4. Ajoutez le compte que vous avez utilisé lors de la connexion à l’hôte avec RDP pour le groupe « utilisateur du Bureau à distance ». À défaut, vous risquez de ne pas pouvoir vous connecter à la machine virtuelle après l’avoir jointe à votre domaine Active Directory.
5. Accédez à Windows Update sur votre machine virtuelle.
6. Installez toutes les mises à jour Windows de la catégorie Important.
7. Installez toutes les mises à jour Windows de la catégorie Facultatif (à l’exception des modules linguistiques). Cette procédure installe la mise à jour protocole Remote Desktop Protocol 8.0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35393)) dont vous avez besoin pour suivre ces instructions.
8. Ouvrez l'Éditeur d'objets de stratégie de groupe et accédez à **Configuration de l’ordinateur** > **Modèles d’administration** > **Composants Windows** > **Services Bureau à distance** > **Hôte de session Bureau à distance** > **Environnement de session à distance**.
9. Activez la stratégie Remote Desktop Protocol 8.0.
10. Redémarrez la machine virtuelle en exécutant la commande suivante :
    
     ```cmd
     shutdown /r /t 0
     ```
    
11. Suivez les instructions [ici](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo) pour obtenir un jeton d’inscription.
12. [Téléchargez l’agent Windows Virtual Desktop pour Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
13. [Téléchargez le gestionnaire d’agent Windows Virtual Desktop pour Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
14. Ouvrez le programme d’installation de l’agent Windows Virtual Desktop et suivez les instructions. Quand vous y êtes invité, indiquez la clé d’inscription que vous avez créée à l’étape 11.
15. Ouvrez le programme d’installation de Windows Virtual Desktop et suivez les instructions.
16. Si vous le souhaitez, bloquez le port TCP/3389 pour supprimer l’accès direct à la machine virtuelle via le protocole RDP (Remote Desktop Protocol).

## <a name="next-steps"></a>Étapes suivantes

Votre déploiement Windows Virtual Desktop est maintenant prêt à l’emploi. [Téléchargez la dernière version du client Windows Virtual Desktop](https://aka.ms/wvd/clients/windows) pour commencer.

Pour obtenir la liste des problèmes connus et les instructions de dépannage pour Windows 7 sur Windows Virtual Desktop, consultez l'article [Résoudre les problèmes de machines virtuelles Windows 7 dans Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
