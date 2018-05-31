---
title: Installer Visual Studio et se connecter à Azure Stack | Microsoft Docs
description: Découvrez les étapes nécessaires pour installer Visual Studio et se connecter à Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 3eaefbe011c4d98fe9a76d4f277a76a2f167b191
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301891"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installer Visual Studio et se connecter à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utilisez Visual Studio pour créer et déployer des [modèles](azure-stack-arm-templates.md) Azure Resource Manager dans Azure Stack. Vous pouvez utiliser les étapes décrites dans cet article pour installer Visual Studio à partir du [Kit de développement Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes connecté par le biais d’un [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Les étapes mentionnées dans cet article effectuent une nouvelle installation de Visual Studio 2015 Community Edition. En savoir plus sur la [coexistence](https://msdn.microsoft.com/library/ms246609.aspx) avec d’autres versions de Visual Studio.

## <a name="install-visual-studio"></a>Installation de Visual Studio

1. Téléchargez et exécutez [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
2. Recherchez **Visual Studio Community 2015 avec Microsoft Azure SDK - 2.9.6**, sélectionnez **Ajouter**, puis **Installer**.

    ![Capture d’écran des étapes d’installation de WebPI](./media/azure-stack-install-visual-studio/image1.png)

3. Désinstallez la version de **Microsoft Azure PowerShell** installée dans le cadre du SDK Azure.

    ![Capture d’écran de l’interface Ajout/Suppression de programmes pour Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png)

4. [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md)

5. Redémarrez le système d’exploitation après l’installation.

## <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

1. Lancez Visual Studio.

2. Dans le menu **Affichage**, sélectionnez **Cloud Explorer**.

3. Dans le nouveau volet, sélectionnez **Ajouter un compte** et connectez-vous avec vos informations d’identification Azure Active Directory.
    ![Capture d’écran de Cloud Explorer après l’enregistrement et la connexion à Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

Une fois connecté, vous pouvez [déployer des modèles](azure-stack-deploy-template-visual-studio.md) ou parcourir les types de ressources et les groupes de ressources disponibles pour créer vos propres modèles.

## <a name="next-steps"></a>Étapes suivantes

* [Développer des modèles pour Azure Stack](azure-stack-develop-templates.md)
