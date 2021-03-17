---
title: Tutoriel expliquant comment filtrer et analyser des données avec le rôle de calcul sur Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Apprenez à configurer le rôle de calcul sur Azure Stack Edge Pro avec GPU et à l'utiliser pour transformer des données avant de les envoyer à Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 750b7a8367d46434f48626268a0eb37c9edddfb1
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633532"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Tutoriel : Configurer le calcul sur un appareil Azure Stack Edge Pro avec GPU

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ce tutoriel vous explique comment configurer un rôle de calcul et créer un cluster Kubernetes sur votre appareil Azure Stack Edge Pro. 

Cette procédure peut prendre environ 20 à 30 minutes.


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer le calcul
> * Obtenir les points de terminaison Kubernetes

 
## <a name="prerequisites"></a>Prérequis

Avant de configurer un rôle de calcul sur votre appareil Azure Stack Edge Pro, vérifiez que :

- Vous avez activé votre appareil Azure Stack Edge Pro, comme décrit dans [Activer Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Assurez-vous que vous avez suivi les instructions dans [Activer le réseau de calcul](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) et :
    - Vous avez activé une interface réseau pour le calcul.
    - Affecté des adresses IP de nœud Kubernetes et adresses IP de service externe Kubernetes.

## <a name="configure-compute"></a>Configurer le calcul

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Obtenir les points de terminaison Kubernetes

Pour configurer un client pour accéder au cluster Kubernetes, vous avez besoin du point de terminaison Kubernetes. Procédez comme suit pour récupérer le point de terminaison de l'API Kubernetes à partir de l'interface utilisateur locale de votre appareil Azure Stack Edge Pro.

1. Dans l’interface utilisateur web locale de votre appareil, accédez à la page **Appareils**.
2. Sous **Points de terminaison de l’appareil**, copiez le point de terminaison du **service d’API Kubernetes**. Ce point de terminaison est une chaîne au format suivant : `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Page Appareil dans l’interface utilisateur locale](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Enregistrez la chaîne de point de terminaison. Vous allez utiliser cette chaîne de point de terminaison ultérieurement lors de la configuration d’un client pour accéder au cluster Kubernetes par le biais de kubectl.

4. Lorsque vous êtes dans l’interface utilisateur web locale, vous pouvez :

    - Accéder à l’API Kubernetes, sélectionner **Paramètres avancés** et télécharger un fichier de configuration avancée pour Kubernetes. 

        ![Page Appareil dans l’interface utilisateur locale 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Si vous avez reçu une clé de Microsoft (certains utilisateurs peuvent en avoir une), vous pouvez utiliser ce fichier de configuration.

        ![Page Appareil dans l’interface utilisateur locale 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Vous pouvez également accéder au point de terminaison **Tableau de bord Kubernetes** et télécharger un fichier de configuration `aseuser`. 
    
        ![Page Appareil dans l’interface utilisateur locale 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Vous pouvez utiliser ce fichier de configuration pour vous connecter au tableau de bord Kubernetes ou pour déboguer les problèmes dans votre cluster Kubernetes. Pour plus d’informations, consultez [Accéder au tableau de bord Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le calcul
> * Obtenir les points de terminaison Kubernetes


Pour savoir comment administrer votre appareil Azure Stack Edge Pro, consultez :

> [!div class="nextstepaction"]
> [Administrer un appareil Azure Stack Edge Pro via l'interface utilisateur web locale](azure-stack-edge-manage-access-power-connectivity-mode.md)
