---
title: 'Démarrage rapide : configurer un pare-feu pour un serveur Analysis Services dans Azure | Microsoft Docs'
description: Découvrez comment configurer un pare-feu pour une instance de serveur Analysis Services dans Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9af0c0a32706c6745412401c9136e032e2e3f9ff
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268916"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Démarrage rapide : configurer un pare-feu de serveur - Portail

Ce démarrage rapide vous aide à configurer un pare-feu pour votre serveur Azure Analysis Services. L’activation d’un pare-feu et la configuration de plages d’adresses PI pour les ordinateurs accédant à votre serveur uniquement constituent une partie importante de la protection de votre serveur et de vos données.

## <a name="prerequisites"></a>Prérequis

- Un serveur Analysis Services dans votre abonnement. Pour en savoir plus, consultez [Quickstart: Create a server - Portal](analysis-services-create-server.md) (Démarrage rapide : créer un serveur - Portail) ou [Quickstart: Create a server - PowerShell](analysis-services-create-powershell.md) (Démarrage rapide : créer un serveur - PowerShell)
- Une ou plusieurs plages d’adresses IP pour les ordinateurs clients (si nécessaire).

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure. 

[Se connecter au portail](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Configurer un pare-feu

1. Cliquez sur votre serveur pour ouvrir la page Vue d’ensemble. 
2. Dans **Paramètres** > **Pare-feu** > **Activer le pare-feu**, cliquez sur **Activé**.
3. Pour autoriser l’accès DirectQuery à partir du service Power BI, dans **Allow access from Power BI** (Autoriser l’accès à partir de Power BI), cliquez sur **Activé**.  
4. (Facultatif) Spécifiez une ou plusieurs plages d’adresses IP. Entrez un nom et une adresse IP de début et de fin pour chaque plage. 
5. Cliquez sur **Enregistrer**.

     ![Paramètres du pare-feu](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez les plages d’adresses IP ou désactivez le pare-feu.

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris comment configurer un pare-feu pour votre serveur. Maintenant que vous disposez d’un serveur et que vous l’avez protégé avec un pare-feu, vous pouvez ajouter un exemple de modèle de données de base à celui-ci à partir du portail. Un exemple de modèle permet d’en savoir plus sur la configuration des rôles de base de données de modèle et le test des connexions client. Pour en savoir plus, passez au didacticiel sur l’ajout d’un exemple de modèle.

> [!div class="nextstepaction"]
> [Didacticiel : ajouter un exemple de modèle à votre serveur](analysis-services-create-sample-model.md)
