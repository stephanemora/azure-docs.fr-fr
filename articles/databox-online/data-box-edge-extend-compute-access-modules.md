---
title: Gérer le trafic de calcul sur la zone de données Azure Edge aux modules d’accès | Microsoft Docs
description: Décrit comment étendre le réseau de calcul sur votre périmètre de la boîte de données pour accéder aux modules via une adresse IP externe.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917234"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Activer le réseau de calcul sur votre zone de données Azure Edge

Cet article décrit comment les modules en cours d’exécution sur votre zone de données Azure Edge accès au réseau de calcul activé sur l’appareil.

Pour configurer le réseau, vous devez procédez comme suit :

- Activer une interface réseau sur votre appareil Edge de zone de données pour le calcul
- Ajouter un module de calcul de l’accès réseau sur votre périmètre de la boîte de données
- Vérifier le module peut accéder à l’interface réseau est activée

Dans ce didacticiel, vous allez utiliser un module d’application de serveur Web pour illustrer le scénario.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, vous devez :

- Un périphérique de périmètre de zone de données avec le programme d’installation de périphérique terminée.
- Vous avez terminé **configurer le calcul** étape en tant que par le [didacticiel : Transformer des données avec la zone de données Azure Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) sur votre appareil. Votre appareil doit avoir une ressource IoT Hub associée, un appareil IoT et un appareil IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Activer l’interface réseau pour le calcul

Pour accéder aux modules en cours d’exécution sur votre appareil via un réseau externe, vous devez affecter une adresse IP à une interface réseau sur votre appareil. Vous pouvez gérer ces paramètres de computing à partir de votre interface utilisateur web locale.

Effectuez les étapes suivantes sur votre interface utilisateur web locale pour configurer les paramètres de computing.

1. Dans l’interface utilisateur web locale, accédez à **Configuration > Compute settings** (Paramètres de computing).  

2. **Activer** l’interface réseau que vous souhaitez utiliser pour se connecter à un module de calcul que vous allez exécuter sur l’appareil.

    - Si vous utilisez des adresses IP statiques, entrez une adresse IP pour l’interface réseau.
    - Si vous utilisez DHCP, les adresses IP sont automatiquement attribuées. Cet exemple utilise le protocole DHCP.

    ![Activer les paramètres de calcul 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Sélectionnez **Apply** pour appliquer les paramètres. Prenez note de l’adresse IP affectée à l’interface réseau si vous utilisez DHCP.

    ![Activer les paramètres de computing](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Ajouter le module d’application de serveur Web

Procédez comme suit pour ajouter un module d’application de serveur Web sur votre appareil Edge de zone de données.

1. Accédez à la ressource IoT Hub associée à votre appareil Edge de zone de données, puis sélectionnez **appareil IoT Edge**.
2. Sélectionnez l’appareil IoT Edge associé à votre appareil Edge de zone de données. Sur le **détails de l’appareil**, sélectionnez **définir des modules**. Sur **ajouter des modules**, sélectionnez **+ ajouter** , puis sélectionnez **Module IoT Edge**.
3. Dans le **des modules IoT Edge personnalisés** panneau :

    1. Spécifiez un **nom** pour votre module d’application de serveur Web que vous souhaitez déployer.
    2. Fournir un **URI de l’Image** pour votre image de module. Un module de mise en correspondance les balises et le nom fourni est récupéré. Dans ce cas, `nginx:stable` extraira une image nginx stable (marquée comme stable) du public [référentiel Docker](https://hub.docker.com/_/nginx/).
    3. Dans le **Options de création de conteneur**, collez l’exemple de code suivant :  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Cette configuration vous permet d’accéder au module à l’aide de l’adresse IP réseau de calcul sur *http* sur le port TCP 8080 (avec le port de serveur Web par défaut est 80).

        ![Spécifiez les informations de port dans le panneau de module personnalisé IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Sélectionnez **Enregistrer**.

## <a name="verify-module-access"></a>Vérifier l’accès de module

1. Vérifiez que le module est correctement déployé et qu’il est en cours d’exécution. Sur le **détails de l’appareil** page, sur le **Modules** onglet, l’état d’exécution du module doit être **en cours d’exécution**.  
2. Se connecter pour le module d’application de serveur web. Ouvrez une fenêtre de navigateur et tapez :

    `http://<compute-network-IP-address>:8080`

    Vous devez voir que l’application de serveur Web est en cours d’exécution.

    ![Vérifier la connexion au module sur le port spécifié](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer des utilisateurs via le Portail Azure](data-box-edge-manage-users.md).
