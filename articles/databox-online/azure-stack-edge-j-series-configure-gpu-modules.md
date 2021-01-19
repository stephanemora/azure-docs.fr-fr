---
title: Exécuter un module GPU sur un appareil GPU Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Décrit comment configurer et exécuter un module GPU sur un appareil Azure Stack Edge Pro via le portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: d172ce98ba93360c621a91fb0e2a55d022470943
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935554"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Configurer et exécuter un module GPU sur un appareil Azure Stack Edge Pro

Votre appareil Azure Stack Edge Pro contient une ou plusieurs unités de traitement graphique (GPU, Graphics Processing Unit). Les GPU sont souvent choisis pour les calculs IA, car ils offrent des capacités de traitement parallèle et sont plus rapides au rendu d’image que les processeurs (ou CPU). Pour plus d’informations sur le GPU de votre appareil Azure Stack Edge Pro, accédez aux [spécifications techniques des appareils Azure Stack Edge Pro](azure-stack-edge-gpu-technical-specifications-compliance.md).

Cet article explique comment configurer et exécuter un module sur le GPU de votre appareil Azure Stack Edge Pro. Dans cet article, vous allez utiliser un module de conteneur disponible publiquement, **Digits**, écrit pour les GPU Nvidia T4. Cette procédure peut être utilisée pour configurer tout autre module publié par Nvidia pour ces GPU.


## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez accès à un appareil Azure Stack Edge Pro à 1 nœud compatible avec GPU. Cet appareil est activé avec une ressource dans Azure.  

## <a name="configure-module-to-use-gpu"></a>Configurer le module pour utiliser le GPU

Pour configurer un module afin qu’il utilise le GPU sur votre appareil Azure Stack Edge Pro pour exécuter un module,<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> procédez comme suit.

1. Dans le portail Azure, accédez à la ressource associée à votre appareil.

2. Dans **Vue d’ensemble**, sélectionnez **IoT Edge**.

    ![Configurer le module pour utiliser le GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. Dans **Activer le service IoT Edge**, sélectionnez **Ajouter**.

   ![Configurer le module pour utiliser le GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Dans **Créer un service IoT Edge**, entrez les paramètres de votre ressource IoT Hub :

   |Champ   |Valeur    |
   |--------|---------|
   |Abonnement      | Abonnement utilisé par la ressource Azure Stack Edge. |
   |Resource group    | Groupe de ressources utilisé par la ressource Azure Stack Edge. |
   |IoT Hub           | Choisissez entre **Créer un nouveau** ou **Utiliser un existant**. <br> Par défaut, un niveau Standard (S1) est utilisé pour créer une ressource IoT. Pour utiliser une ressource IoT de niveau gratuit, créez-en une, puis sélectionnez-la. <br> Dans chaque cas, la ressource IoT Hub utilise les mêmes abonnement et groupe de ressources que la ressource Azure Stack Edge.     |
   |Nom              | Si vous ne voulez pas utiliser le nom par défaut fourni pour une nouvelle ressource IoT Hub, entrez un autre nom. |

   Lorsque vous avez terminé de configurer les paramètres, sélectionnez **Vérifier + créer**. Vérifiez les paramètres de votre ressource IoT Hub, puis sélectionnez **Créer**.

   ![Bien démarrer avec le calcul 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

   La création d’une ressource IoT Hub prend plusieurs minutes. Une fois la ressource créée, la **Vue d’ensemble**  indique que le service IoT Edge est en cours d’exécution.

   ![Bien démarrer avec le calcul 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Pour vérifier que le rôle de calcul Edge a été configuré, sélectionnez **Propriétés**.

   ![Bien démarrer avec le calcul 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

6. Dans **Propriétés**, sélectionnez le lien pour **Appareil IoT Edge**.

   ![Configurer le module pour utiliser le GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

   Dans le volet droit, vous voyez l’appareil IoT Edge associé à votre appareil Azure Stack Edge Pro. Cet appareil correspond à l’appareil IoT Edge que vous avez créé lors de la création de la ressource IoT Hub.
 
7. Sélectionnez cet appareil IoT Edge.

   ![Configurer le module pour utiliser le GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

8. Sélectionnez **Définir des modules**.

   ![Configurer le module pour utiliser le GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

9. Sélectionnez **+ Ajouter**, puis **+ Module IoT Edge**. 

    ![Configurer le module pour utiliser le GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

10. Sous l’onglet **Ajouter un module IoT Edge** :

    1. Fournissez l’**URI de l’image**. Vous allez utiliser le module Nvidia publiquement disponible **Digits** ici. 
    
    2. Définissez **Stratégie de redémarrage** sur **toujours**.
    
    3. Définissez **État souhaité** sur **en cours d’exécution**.
    
    ![Configurer le module pour utiliser le GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

11. Sous l’onglet **Variables d’environnement**, indiquez le nom de la variable et la valeur correspondante. 

    1. Pour que le module actuel utilise un seul GPU sur cet appareil, utilisez NVIDIA_VISIBLE_DEVICES. 

    2. Définissez cette condition sur la valeur 0 ou 1. Une valeur de 0 ou 1 garantit qu’au moins un GPU est utilisé par l’appareil pour ce module. Quand vous définissez la valeur sur 0, 1, cela signifie que ces deux GPU sur votre appareil sont utilisés par ce module.

       ![Configurer le module pour utiliser le GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

       Pour plus d’informations sur les variables d’environnement que vous pouvez utiliser avec le GPU Nvidia, accédez au [runtime de conteneur nVidia](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Un GPU ne peut être mappé qu’à un seul module. Un module peut toutefois utiliser un seul GPU, deux GPU, voire aucun.

12. Entrez un nom pour votre module. À ce stade, vous pouvez choisir de fournir une option de création de conteneur et de modifier les paramètres de jumeau de module ou, si vous avez terminé, sélectionnez **Ajouter**. 

    ![Configurer le module pour utiliser le GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

13. Vérifiez que le module est en cours d’exécution et sélectionnez **Vérifier + créer**.

    ![Configurer le module pour utiliser le GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

14. Sous l’onglet **Vérifier + créer**, les options de déploiement que vous avez sélectionnées s’affichent. Vérifiez les options et sélectionnez **Créer**.
    
    ![Configurer le module pour utiliser le GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

15. Prenez note de l’**état du runtime** du module.
    
    ![Configurer le module pour utiliser le GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Le déploiement du module prend quelques minutes. Sélectionnez **Actualiser** pour voir l’**état du runtime** passer à **en cours d’exécution**.

    ![Configurer le module pour utiliser le GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail les [variables d’environnement](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec) que vous pouvez utiliser avec le GPU Nvidia.
