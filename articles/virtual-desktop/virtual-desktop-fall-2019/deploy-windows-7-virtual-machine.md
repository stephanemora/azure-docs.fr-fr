---
title: Déployer une machine virtuelle Windows 7 sur Azure Virtual Desktop (classique) – Azure
description: Découvrez comment configurer et déployer une machine virtuelle Windows 7 sur Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1b7c78ab9a518649d205bb9dc92c86d7bb71bcea
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744626"
---
# <a name="deploy-a-windows-7-virtual-machine-on-azure-virtual-desktop-classic"></a>Déployer une machine virtuelle Windows 7 sur Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer les objets Azure Resource Manager Azure Virtual Desktop introduits dans la version actuelle d’Azure Virtual Desktop, consultez [cet article](../deploy-windows-7-virtual-machine.md).

Le processus de déploiement d’une machine virtuelle Windows 7 sur Azure Virtual Desktop diffère légèrement de celui des machines virtuelles exécutant des versions ultérieures de Windows. Ce guide vous explique comment déployer Windows 7.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, suivez les instructions de l'article [Créer un pool d’hôtes avec PowerShell](create-host-pools-powershell-2019.md) pour créer un pool d'hôtes. Ensuite, suivez les instructions de l'article [Créer des pools d'hôtes dans la Place de marché Azure](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) pour attribuer un ou plusieurs utilisateurs au groupe d’applications de bureau.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurer une machine virtuelle Windows 7

Après avoir répondu aux prérequis, vous êtes prêt à configurer votre machine virtuelle Windows 7 pour la déployer sur Azure Virtual Desktop.

Pour configurer une machine virtuelle Windows 7 sur Azure Virtual Desktop :

1. Connectez-vous au portail Azure et recherchez l’image Windows 7 Entreprise ou téléchargez votre propre image personnalisée Windows 7 Entreprise (x64).
2. Déployez une ou plusieurs machines virtuelles avec Windows 7 Entreprise en tant que système d’exploitation hôte. Vérifiez que les machines virtuelles autorisent le protocole RDP (Remote Desktop Protocol) (port TCP/3389).
3. Connectez-vous à l’hôte Windows 7 Entreprise à l’aide du protocole RDP et authentifiez-vous avec les informations d’identification que vous avez définies lors de la configuration de votre déploiement.
4. Ajoutez le compte que vous avez utilisé lors de la connexion à l’hôte avec RDP pour le groupe « Utilisateur du Bureau à distance ». À défaut, vous risquez de ne pas pouvoir vous connecter à la machine virtuelle après l’avoir jointe à votre domaine Active Directory.
5. Accédez à Windows Update sur votre machine virtuelle.
6. Installez toutes les mises à jour Windows de la catégorie Important.
7. Installez toutes les mises à jour Windows de la catégorie Facultatif (à l’exception des modules linguistiques). Cette procédure installe la mise à jour protocole Remote Desktop Protocol 8.0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) dont vous avez besoin pour suivre ces instructions.
8. Ouvrez l'Éditeur d'objets de stratégie de groupe et accédez à **Configuration de l’ordinateur** > **Modèles d’administration** > **Composants Windows** > **Services Bureau à distance** > **Hôte de session Bureau à distance** > **Environnement de session à distance**.
9. Activez la stratégie Remote Desktop Protocol 8.0.
10. Joignez cette machine virtuelle à votre domaine Active Directory.
11. Redémarrez la machine virtuelle en exécutant la commande suivante :

     ```cmd
     shutdown /r /t 0
     ```

12. Suivez les instructions [ici](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) pour obtenir un jeton d’inscription.
13. [Téléchargez l’agent Azure Virtual Desktop pour Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Téléchargez Azure Virtual Desktop Agent Manager pour Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Ouvrez le programme d’installation de l’agent Azure Virtual Desktop et suivez les instructions. Quand vous y êtes invité, spécifiez la clé d’inscription que vous avez créée à l’étape 12.
16. Ouvrez Azure Virtual Desktop Agent Manager et suivez les instructions.
17. Si vous le souhaitez, bloquez le port TCP/3389 pour supprimer l’accès direct à la machine virtuelle via le protocole RDP (Remote Desktop Protocol).
18. Confirmez éventuellement que votre .NET Framework est au moins à la version 4.7.2. Cela est particulièrement important si vous créez une image personnalisée.

## <a name="next-steps"></a>Étapes suivantes

Votre déploiement Azure Virtual Desktop est maintenant prêt à l’emploi. [Téléchargez la dernière version du client Azure Virtual Desktop](https://aka.ms/wvd/clients/windows) pour commencer.

Pour obtenir la liste des problèmes connus et les instructions de dépannage pour Windows 7 sur Azure Virtual Desktop, consultez notre article [Résoudre les problèmes des machines virtuelles Windows 7 dans Azure Virtual Desktop](troubleshoot-windows-7-vm.md).
