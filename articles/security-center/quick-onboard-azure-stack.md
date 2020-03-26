---
title: Intégrer des machines virtuelles Azure Stack dans Azure Security Center
description: Ce guide de démarrage rapide vous montre comment provisionner l’extension de machine virtuelle Azure Monitor, Update and Configuration Management sur une machine virtuelle Azure Stack.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73686528"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Démarrage rapide : Intégrer vos machines virtuelles Azure Stack à Security Center
Après avoir intégré votre abonnement Azure, vous pouvez activer Security Center pour protéger vos machines virtuelles en cours d’exécution sur Azure Stack en ajoutant l’extension de machine virtuelle **Azure Monitor, Update and Configuration Management** à partir de la Place de marché Azure Stack.

Ce guide de démarrage rapide vous montre comment ajouter l’extension de machine virtuelle **Azure Monitor, Update and Configuration Management** sur une machine virtuelle (Linux et Windows sont tous deux pris en charge) en cours d’exécution sur Azure Stack.

## <a name="prerequisites"></a>Prérequis
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Vous devez avoir un abonnement Azure sur le niveau Standard de Security Center pour commencer ce guide de démarrage rapide. Vous trouverez des instructions sur la mise à niveau sur la page [Intégrer un abonnement Azure à Security Center Standard](security-center-get-started.md). Vous pouvez essayer le niveau Security Center Standard gratuitement pendant 30 jours. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Sélectionner votre espace de travail dans Azure Security Center

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).
2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre. 

   ![Vue d’ensemble de Security Center][2]

3. Dans le menu principal de Security Center, sélectionnez **Prise en main**.
4. Sélectionnez l’onglet **Prise en main**.

   ![Bien démarrer][3]

5. Cliquez sur **Configurer** sous **Ajouter de nouveaux ordinateurs non Azure**. Une liste de vos espaces de travail Log Analytics apparaît. Elle comprend, le cas échéant, l’espace de travail par défaut créé pour vous par Security Center à l’activation de l’approvisionnement automatique. Sélectionnez cet espace de travail ou un autre espace de travail sur lequel vous souhaitez que la machine virtuelle Azure Stack signale les données de sécurité.

    ![Ajouter un ordinateur autre qu’Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Le panneau **Agent direct** s’ouvre et affiche un lien permettant de télécharger l’agent et les clés pour votre ID d’espace de travail, qui serviront à la configuration de l’agent.

   >[!NOTE]
   > Vous n’avez pas besoin de télécharger l’agent manuellement. L’agent sera installé en tant qu’extension de machine virtuelle au cours des étapes ci-dessous.

6. À droite du champ **ID de l’espace de travail**, sélectionnez l’icône de copie et collez l’ID dans le Bloc-notes.

7. À droite du champ **Clé primaire**, sélectionnez l’icône de copie et collez la clé dans le Bloc-notes.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Ajouter l’extension de machine virtuelle à vos machines virtuelles Azure Stack existantes
Vous devez maintenant ajouter l’extension de machine virtuelle **Azure Monitor, Update and Configuration Management** aux machines virtuelles en cours d’exécution sur votre système Azure Stack.

1. Dans un nouvel onglet de navigateur, connectez-vous à votre portail **Azure Stack**.
2. Accédez à la page **Machines virtuelles**, puis sélectionnez la machine virtuelle que vous souhaitez protéger avec Security Center. Pour plus d’informations sur la façon de créer une machine virtuelle sur Azure Stack, consultez [ce guide de démarrage rapide pour les machines virtuelles Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [ce guide de démarrage rapide pour les machines virtuelles Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Sélectionnez **Extensions**. La liste des extensions de machine virtuelle installées sur cette machine virtuelle s’affiche.
4. Cliquez sur l’onglet **Ajouter**. Le panneau de menus **Nouvelle ressource** s’ouvre et affiche la liste des extensions de machine virtuelle disponibles. 
5. Sélectionnez l’extension **Azure Monitor, Update and Configuration Management** et cliquez sur **Créer**. Le panneau de configuration **Installer l’extension** s’ouvre.

>[!NOTE]
> Si l’extension **Azure Monitor, Update and Configuration Management** n’est pas listée dans votre place de marché, contactez votre opérateur Azure Stack pour qu’elle soit disponible.

6. Dans la page de configuration **Installer l’extension**, collez l’**ID de l’espace de travail** et la **Clé de l’espace de travail (clé primaire)** que vous avez copiés dans le Bloc-notes au cours de la procédure précédente.
7. Une fois que vous avez fourni les paramètres de configuration nécessaires, cliquez sur **OK**.
8. Une fois l’installation de l’extension terminée, son état s’affiche en tant qu’**Approvisionnement réussi**. Il peut s’écouler une heure avant que la machine virtuelle n’apparaisse dans le portail Security Center.

Pour plus d’informations sur l’installation et la configuration de l’agent pour Windows, consultez la page [Connecter des ordinateurs Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Pour plus d’informations sur la résolution des problèmes lié à l’agent Linux, consultez [Résoudre les problèmes liés à l’agent Linux Azure Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md).

Vous pouvez maintenant effectuer le monitoring de vos machines virtuelles Azure et de vos ordinateurs extérieurs à Azure en un seul et même endroit. Dans le portail Security Center sur Azure, la fenêtre **Calcul** affiche une vue d’ensemble de toutes les machines virtuelles et de tous les ordinateurs ainsi que des recommandations correspondantes. Security Center met également en évidence les éventuelles détections d’alertes de sécurité pour ces ordinateurs.

  ![Panneau Calcul][6]

Deux types d’icônes sont représentés sur le panneau **Calcul** :

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Ordinateur extérieur à Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Machine virtuelle Azure (les machines virtuelles Azure Stack s’affichent dans ce groupe)

## <a name="clean-up-resources"></a>Nettoyer les ressources
Quand vous n’en avez plus besoin, vous pouvez supprimer l’extension de la machine virtuelle par le biais du portail Azure Stack.

Pour supprimer l’extension :

1. Ouvrez le **portail Azure Stack**.
2. Accédez à **Machines virtuelles**, sélectionnez la machine virtuelle dont vous souhaitez supprimer l’extension.
3. Sélectionnez **Extensions**, puis sélectionnez l’extension **Microsoft.EnterpriseCloud.Monitoring**.
4. Cliquez sur **Désinstaller**, puis confirmez votre choix en cliquant sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez provisionné l’extension Azure Monitor, Update and Configuration Management sur une machine virtuelle en cours d’exécution sur Azure Stack. Pour en savoir plus sur Security Center, enchaînez avec le didacticiel sur la configuration d’une stratégie de sécurité et l’évaluation de la sécurité des ressources.

> [!div class="nextstepaction"]
> [Tutoriel : Définir et évaluer les stratégies de sécurité](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
