---
title: Démarrage rapide - Connecter des machines à Azure avec Azure Arc pour les serveurs - Portail
description: Dans ce guide de démarrage rapide, vous allez apprendre à connecter des machines à Azure avec Azure Arc pour les serveurs à partir du portail
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, Update Management, Change Tracking, inventaire, runbooks, Python, graphique, intégré
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 26c79db956b2703bf037fc6f7790d4ee13874410
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834247"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Démarrage rapide : Connecter des machines à Azure avec Azure Arc pour les serveurs - Portail

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Passez en revue les clients pris en charge et la configuration réseau requise dans la [Vue d’ensemble d’Azure Arc pour les serveurs](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Générer le script d’installation de l’agent à l’aide du portail Azure

1. Lancer [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)
1. Cliquez sur **+ Ajouter**
1. Suivez les instructions de l’assistant jusqu’à la fin
1. La dernière page comporte un script généré que vous pouvez copier (ou télécharger).

Le script doit être exécuté sur l’ordinateur cible auquel vous souhaitez vous connecter. Il télécharge l’agent, l’installe et connecte l’ordinateur en une seule opération.

Sur les serveurs non-Azure que vous souhaitez gérer :

1. Connectez-vous au serveur (à l’aide de SSH, RDP ou de l’accès distant PowerShell)
1. Démarrez un shell : bash sur Linux, PowerShell en tant qu’administrateur sur Windows
1. Collez le script dans le portail et exécutez-le sur le serveur à connecter à Azure.
1. L’authentification par défaut pour l’intégration d’un serveur individuel est *interactive* avec la « Connexion à l’appareil » d’Azure. Lorsque vous exécutez le script, un message semblable à celui-ci s’affiche :

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Ouvrez un navigateur et entrez le code à authentifier. Le navigateur n’a pas besoin d’être en cours d’exécution sur le serveur que vous intégrez ; il peut se trouver sur un autre ordinateur, comme votre ordinateur portable.

1. Si vous souhaitez vous authentifier de manière non interactive, suivez les étapes décrites dans [Créer un principal de service](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) et modifiez le script généré à partir du portail.

> [!NOTE]
> Si vous utilisez Internet Explorer sur le serveur pour la première fois pour ouvrir une session, cela génère une erreur. Vous pouvez simplement rouvrir le navigateur et le refaire.

## <a name="execute-the-script-on-target-nodes"></a>Exécuter le script sur les nœuds cibles

Connectez-vous à chaque nœud et exécutez le script que vous avez généré à partir du portail. Une fois le script terminé, accédez au portail Azure et vérifiez que le serveur a été correctement connecté.

![Réussite de l’intégration](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Nettoyer

Pour déconnecter un ordinateur d’Azure Arc pour les serveurs, vous devez effectuer deux étapes.

1. Sélectionnez l’ordinateur dans le [Portail](https://aka.ms/hybridmachineportal), cliquez sur les points de suspension (`...`) et sélectionnez **Supprimer**.
1. Désinstallez l’agent de l’ordinateur.

   Sur Windows, vous pouvez utiliser le Panneau de configuration « Applications et fonctionnalités » pour désinstaller l’agent.
  
  ![Applications et fonctionnalités](./media/quickstart-onboard/apps-and-features.png)

   Si vous voulez créer un script pour la désinstallation, vous pouvez utiliser l’exemple suivant, qui récupère le **PackageId** et désinstalle l’agent avec `msiexec /X`.

   Regardez sous la clé de Registre `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` et recherchez le **PackageId**. Vous pouvez alors désinstaller l’agent avec `msiexec`.

   L’exemple ci-dessous montre la désinstallation de l’agent.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Sur Linux, exécutez la commande suivante pour désinstaller l’agent.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Attribuer une stratégie à des ordinateurs connectés](../../governance/policy/assign-policy-portal.md)
