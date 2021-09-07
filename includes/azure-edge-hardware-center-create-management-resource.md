---
author: v-dalc
ms.service: databox
ms.topic: include
ms.date: 08/17/2021
ms.author: alkohli
ms.openlocfilehash: 708ac746156582291cf071e730701013f1e005b0
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397928"
---
Afin de créer une ressource de gestion pour un appareil commandé sur Azure Edge Hardware Center, suivez ces étapes :

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter au portail Azure en suivant cette URL : [https://portal.azure.com](https://portal.azure.com).

1. Vous pouvez créer une ressource de gestion de deux façons :

    - Dans Azure Edge Hardware Center : recherchez et sélectionnez **Azure Edge Hardware Center**. Dans Hardware Center, affichez **Tous les articles commandés**. Cliquez sur le **Nom** de l’article. Dans la **Vue d’ensemble** de l’article, sélectionnez **Configurer le matériel**.
    
       L’option **Configurer le matériel** s’affiche quand un appareil a été expédié. 

       ![Illustration montrant 4 étapes pour démarrer la création d’une ressource de gestion à partir d’un article commandé dans Azure Edge Hardware Center.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-a.png#lightbox) 
    
    - Dans Azure Stack Edge : recherchez et sélectionnez **Azure Stack Edge**. Sélectionnez **+ Créer**. Sélectionnez ensuite **Créer une ressource de gestion**.
    
       ![Illustration montrant 3 étapes pour démarrer la création d’une ressource de gestion dans Azure Stack Edge.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-b.png#lightbox) 

    L’Assistant **Créer une ressource de gestion** s’ouvre.

1. Sous l’onglet **Informations de base**, entrez les paramètres suivants :

    |Paramètre                                  |Value                                                                                       |
    |-----------------------------------------|--------------------------------------------------------------------------------------------|
    |**Sélectionner un abonnement**<sup>1</sup>    |Sélectionnez l’abonnement à utiliser pour la ressource de gestion.                                 |
    |**Groupe de ressources**<sup>1</sup>           |Sélectionnez le groupe de ressources à utiliser pour la ressource de gestion. |
    |**Nom**                                 |Fournissez un nom pour la ressource de gestion.                                                 |
    |**Déployer la ressource Azure dans**             |Sélectionnez le pays ou la région où se trouvent les métadonnées de la ressource de gestion. Les métadonnées peuvent être stockées dans une autre localisation que celle de l’appareil physique. |
    |**Type d’appareil**<sup>2</sup>              |Sélectionnez le type d’appareil. Cette option correspond à la configuration sélectionnée pour le produit matériel dans une commande Azure Edge Hardware Center.<br>Par exemple, pour un appareil Azure Stack Edge Pro - GPU, le type d’appareil est **Azure Stack Edge Pro - 1 GPU** ou **Azure Stack Edge Pro - 1 GPU**.|       

    <sup>1</sup> Une organisation peut utiliser différents abonnements et groupes de ressources pour commander les appareils qu’elle utilise pour les gérer.

    <sup>2</sup> Quand vous utilisez **Configurer le matériel** à partir de l’article commandé, le type d’appareil est celui de l’article commandé et les **DÉTAILS DE L’APPAREIL** ne sont pas affichés. 

    ![Capture d’écran de l’onglet Informations de base pour créer une ressource de gestion. L’onglet Informations de base, les options et le bouton Vérifier + créer sont mis en évidence.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-02.png)

    Sélectionnez **Vérifier + créer** pour continuer.

5. Sous l’onglet **Vérifier + créer**, vérifiez les paramètres de la ressource de gestion et les conditions d’utilisation. Sélectionnez ensuite **Créer**.

    - Si vous avez démarré cette procédure en cliquant sur **Configurer le matériel** pour un article livré dans le cadre d’une commande Azure Edge Hardware Center, l’appareil, le nom de la ressource commandée et l’état de la commande sont listés en haut de l’écran. 

      ![Capture d’écran de l’onglet Vérifier + créer quand une ressource de gestion Azure Stack Edge est créée pour un article commandé dans Azure Edge Hardware Center. Les informations de commande de l’appareil sont mises en évidence.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-03.png)

    - Si vous avez commencé dans Azure Stack Edge, le type d’appareil est listé sous **Informations de base** et non dans les informations de commande de l’appareil. 

      ![Capture d’écran de l’onglet Vérifier + créer quand une ressource de gestion Azure Stack Edge est démarrée dans Azure Stack Edge. Le type d’appareil est mis en évidence sous Informations de base.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-04.png)  

    Le bouton **Créer** n’est pas disponible tant que toutes les vérifications de validation n’ont pas été effectuées.

6. Une fois le processus terminé, le volet Vue d’ensemble de la nouvelle ressource s’ouvre.

    ![Capture d’écran montrant une ressource de gestion terminée dans Azure Stack Edge.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-05\.png) 
