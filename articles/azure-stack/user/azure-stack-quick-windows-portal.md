---
title: Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle Windows
description: Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle Windows avec le portail
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/19/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: ec9005e5efbf26f8969c87c17d2bf7ae7708e7d6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Démarrage rapide : créer une machine virtuelle Windows avec le portail Azure Stack

Vous pouvez créer une machine virtuelle Windows dans le portail Azure Stack. Le portail est une interface utilisateur basé sur le navigateur grâce à laquelle vous pouvez créer, configurer et gérer les ressources.

## <a name="sign-in-to-the-azure-stack-portal"></a>Se connecter au portail Azure Stack

Connectez-vous au portail Azure Stack. L’adresse du portail Azure Stack varie en fonction du produit Azure Stack auquel vous vous connectez :

* Pour le Kit de développement Azure Stack (ASDK), accédez à : https://portal.local.azurestack.external.
* Pour un système intégré Azure Stack, accédez à l’URL fournie par votre opérateur Azure Stack.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Dans le portail, cliquez sur **Nouveau** > **Calcul** > **Windows Server 2016 Datacenter Eval** > **Créer**. Si vous ne voyez pas l’entrée **Windows Server 2016 Datacenter Eval**, contactez votre opérateur Azure Stack. Demandez-lui de l’ajouter à la Place de Marché comme expliqué dans l’article [Ajouter l’image de machine virtuelle Windows Server 2016 à la Place de Marché Azure Stack](../azure-stack-add-default-image.md).
    ![Procédure de création d’une machine virtuelle Windows dans le portail](media/azure-stack-quick-windows-portal/image01.png)
2. Sous **De base**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**. Choisissez un **Abonnement**. Créez un **Groupe de ressources** ou sélectionnez un groupe existant, sélectionnez un **Emplacement**, puis cliquez sur **OK**.

    ![Configurer les paramètres de base](media/azure-stack-quick-windows-portal/image02.png)
3. Sous **Choisir une taille**, cliquez sur **D1 Standard** > **Sélectionner**.
    ![Choisir la taille de la machine virtuelle](media/azure-stack-quick-windows-portal/image03.png)
4. Sous **Paramètres**, acceptez les valeurs par défaut et cliquez sur **OK**.
    ![Configurer les paramètres des machines virtuelles](media/azure-stack-quick-windows-portal/image04.png)
5. Sous **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.
    ![Afficher le résumé et créer la machine virtuelle](media/azure-stack-quick-windows-portal/image05.png)
6. Pour voir votre nouvelle machine virtuelle, cliquez sur **Toutes les ressources**, puis recherchez la machine virtuelle et cliquez sur son nom.
    ![Voir la machine virtuelle](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin de la machine virtuelle, supprimez la machine virtuelle et les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir de la page de la machine virtuelle, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows simple. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
