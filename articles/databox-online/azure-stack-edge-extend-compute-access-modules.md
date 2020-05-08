---
title: Gérer le réseau de calcul sur Azure Stack Edge pour accéder aux modules | Microsoft Docs
description: Décrit comment étendre le réseau de calcul sur Azure Stack Edge pour accéder aux modules au moyen d’une adresse IP externe.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: b352458b768c997f295596a7cbd5e8c02f843a9f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82568754"
---
# <a name="enable-compute-network-on-your-azure-stack-edge"></a>Activer le réseau de calcul sur Azure Stack Edge

Cet article décrit comment les modules en cours d’exécution sur Azure Stack Edge peuvent accéder au réseau de calcul activé sur l’appareil.

Pour configurer le réseau, vous devez procédez comme suit :

- Activer une interface réseau sur votre appareil Azure Stack Edge pour le calcul
- Ajouter un module pour accéder au réseau de calcul sur Azure Stack Edge
- Vérifier que le module peut accéder à l’interface réseau activée

Dans ce tutoriel, vous allez utiliser un module d’application de serveur Web pour illustrer le scénario.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous avez besoin des éléments suivants :

- Un appareil Azure Stack Edge configuré.
- Vous avez terminé l’étape **Configurer le calcul** du [Tutoriel : Transformer des données avec Azure Stack Edge](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) sur votre appareil. Votre appareil doit être associé à une ressource IoT Hub, un appareil IoT et un appareil IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Activer une interface réseau pour le calcul

Pour accéder aux modules s’exécutant sur votre appareil par le biais d’un réseau externe, vous devez affecter une adresse IP à une interface réseau sur votre appareil. Vous pouvez gérer ces paramètres de computing à partir de votre interface utilisateur web locale.

Effectuez les étapes suivantes sur votre interface utilisateur web locale pour configurer les paramètres de computing.

1. Dans l’interface utilisateur web locale, accédez à **Configuration > Compute settings** (Paramètres de computing).  

2. **Activez** l’interface réseau que vous souhaitez utiliser pour la connexion à un module de calcul que vous exécuterez sur l’appareil.

    - Si vous utilisez des adresses IP statiques, entrez une adresse IP pour l’interface réseau.
    - Si vous utilisez DHCP, les adresses IP sont attribuées automatiquement. Cet exemple utilise DHCP.

    ![Activer les paramètres de calcul 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Sélectionnez **Apply** pour appliquer les paramètres. Notez l’adresse IP affectée à l’interface réseau si vous utilisez DHCP.

    ![Activer les paramètres de computing](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Ajouter le module d’application de serveur web

Procédez comme suit pour ajouter un module d’application de serveur web sur votre appareil Azure Stack Edge.

1. Accédez à la ressource IoT Hub associée à votre appareil Azure Stack Edge, puis sélectionnez **Appareil IoT Edge**.
2. Sélectionnez l’appareil IoT Edge associé à votre appareil Azure Stack Edge. Dans **Détails sur l’appareil**, sélectionnez **Définir des modules**. Dans **Ajouter des modules**, sélectionnez **+ Ajouter** , puis sélectionnez **Module IoT Edge**.
3. Dans le panneau **Modules personnalisés IoT Edge** :

    1. Spécifiez un **nom** pour le module d’application de serveur web que vous souhaitez déployer.
    2. Indiquez un **URI d’image** pour votre image de module. Un module de mise en correspondance du nom et des balises est récupéré. Dans ce cas, `nginx:stable` extrait une image nginx stable (marquée comme stable) à partir du [référentiel Docker](https://hub.docker.com/_/nginx/) public.
    3. Dans **Options de création de conteneur**, collez l’exemple de code suivant :  

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

        Cette configuration vous permet d’accéder au module à l’aide de l’adresse IP réseau de calcul sur *http* sur le port TCP 8080 (avec le port de serveur web par défaut 80).

        ![Spécifier les informations de port dans le panneau de module personnalisé IoT Edge](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Sélectionnez **Enregistrer**.

## <a name="verify-module-access"></a>Vérifier l’accès au module

1. Vérifiez que le module est correctement déployé et qu’il est en cours d’exécution. Sur la page **Détails de l’appareil**, dans l’onglet **Modules**, l’état runtime du module doit être **en cours d’exécution**.  
2. Connectez-vous au module d’application de serveur web. Ouvrez une fenêtre de navigateur et entrez :

    `http://<compute-network-IP-address>:8080`

    Vous devez voir que l’application de serveur web est en cours d’exécution.

    ![Vérifier la connexion au module sur le port spécifié](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer des utilisateurs via le Portail Azure](azure-stack-edge-manage-users.md).
