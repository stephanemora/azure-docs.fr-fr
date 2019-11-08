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
ms.openlocfilehash: b014f6015b3e13a603cf3893062bd0463eb110ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488195"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Démarrage rapide : Connecter des machines à Azure avec Azure Arc pour les serveurs - Portail

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Passez en revue les clients pris en charge et la configuration réseau requise dans la [Vue d’ensemble d’Azure Arc pour les serveurs](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Générer le script d’installation de l’agent à l’aide du portail Azure

1. Lancez [https://aka.ms/hybridmachineportal ][aka_hybridmachineportal]
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
1. Désinstallez l’agent de la machine.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Affecter une stratégie à des machines connectées](../../governance/policy/assign-policy-portal.md)