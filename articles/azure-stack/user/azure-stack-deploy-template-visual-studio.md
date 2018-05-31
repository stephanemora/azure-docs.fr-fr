---
title: Déployer des modèles avec Visual Studio dans Azure Stack | Microsoft Docs
description: Découvrez comment déployer des modèles avec Visual Studio dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4101567eff68789fe5d46a01de26f6a873b519fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358181"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Déploiement de modèles dans Azure Stack à l’aide de Visual Studio

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser Visual Studio pour déployer des modèles Azure Resource Manager dans Azure Stack.

Pour déployer un modèle :

1. [Procédez à l’installation et à la connexion](azure-stack-install-visual-studio.md) d’Azure Stack avec Visual Studio.
2. Ouvrez Visual Studio.
3. Sélectionnez **Fichier**, puis **Nouveau**. Dans **Nouveau projet**, sélectionnez **Groupe de ressources Azure**.
4. Entrez le **Nom** du nouveau projet, puis sélectionnez **OK**.
5. Dans **Sélectionner un modèle Azure**, choisissez **Démarrage rapide Azure Stack** dans la liste déroulante.
6. Sélectionnez **101-create-storage-account**, puis **OK**.
7. Dans votre nouveau projet, développez le nœud **Modèles** dans **l’Explorateur de solutions** pour voir les modèles disponibles.
8. Dans **l’Explorateur de solutions**, choisissez le nom de votre projet, puis sélectionnez **Déployer**. Sélectionnez **Nouveau déploiement**.
9. Dans **Déployer vers le groupe de ressources**, utilisez la liste déroulante **Abonnement** pour sélectionner votre abonnement Microsoft Azure Stack.
10. Dans la liste **Groupe de ressources**, choisissez un groupe de ressources existant ou créez-en un.
11. Dans la liste **Emplacement du groupe de ressources**, choisissez un emplacement, puis sélectionnez **Déployer**.
12. Dans **Modifier les paramètres**, fournissez des valeurs pour les paramètres (qui varient selon le modèle), puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec la ligne de commande](azure-stack-deploy-template-command-line.md)
* [Développer des modèles pour Azure Stack](azure-stack-develop-templates.md)
