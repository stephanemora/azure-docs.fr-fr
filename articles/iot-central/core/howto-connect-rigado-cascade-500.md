---
title: Connecter un Rigado Cascade 500 dans Azure IoT Central | Microsoft Docs
description: Découvrez comment connecter un appareil de passerelle Rigado Cascade 500 à votre application IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: ee22c1e68f407eb2b508cd97e611ea83b82174b2
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109684124"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Connecter un appareil de passerelle Rigado Cascade 500 à votre application IoT Central

Cet article vous explique la procédure à suivre pour connecter un appareil de passerelle Rigado Cascade 500 à votre application Microsoft Azure IoT Central.

## <a name="what-is-cascade-500"></a>Qu’est-ce qu’un Cascade 500 ?

Une passerelle IoT Cascade 500 est une offre matérielle de Rigado incluse dans le cadre de sa solution Cascade EaaS. Il fournit aux équipes commerciales et de projets IoT une solution flexible de calcul de périphérie, un environnement d’application en conteneur robuste et un large éventail d’options de connectivité de périphériques sans fil, notamment Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 est certifié pour l’IoT Plug-and-Play Azure et vous permet d’intégrer facilement l’appareil dans vos solutions de bout en bout. La passerelle Cascade vous permet d’établir une connexion sans fil avec un large éventail de capteurs de surveillance de condition qui sont à proximité de l’appareil de passerelle. Ces capteurs peuvent être intégrés dans IoT Central via l’appareil de passerelle.

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans ce guide pratique, vous devez disposer des éléments suivants :

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- Un appareil Rigado Cascade 500. Pour plus d’informations, veuillez visiter [Rigado](https://www.rigado.com/).

## <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

Pour intégrer un appareil de passerelle Cascade 500 à votre instance d’application Azure IoT Central, vous devez configurer un modèle d’appareil correspondant dans votre application.

Pour ajouter un modèle d’appareil Cascade 500 : 

1. Accédez à l’onglet ***Modèles d’appareil** _ dans le volet gauche, sélectionnez _*+ Nouveau** : ![Créer un nouveau modèle d’appareil](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. La page vous donne la possibilité de ***Créer un modèle personnalisé** _ou _ *_Utiliser un modèle d’appareil préconfiguré_**
1. Sélectionnez le modèle d’appareil C500 dans la liste des modèles d’appareil préconfigurés, comme indiqué ci-dessous : ![Sélectionner un modèle d’appareil C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Sélectionnez ***Suivant : Personnaliser*** pour passer à l'étape suivante. 
1. Dans l'écran suivant, sélectionnez ***Créer*** pour intégrer le modèle d'appareil C500 à votre application IoT Central.

## <a name="retrieve-application-connection-details"></a>Récupérer les détails de connexion de l’application

Vous devez maintenant récupérer **l’ID d’étendue** et la **clé primaire** pour votre application Azure IoT Central afin de connecter l’appareil Cascade 500. 

1. Accédez à **Administration** dans le volet gauche, puis cliquez sur **Connexion de l’appareil**. 
2. Prenez note de l’**ID d’étendue** pour votre application IoT Central.
![ID d’étendue d’application](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Cliquez maintenant sur **Afficher les clés** et prenez note de la **Clé primaire**
![Clé primaire](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Contacter Rigado pour connecter la passerelle 

Pour connecter l’appareil Cascade 500 à votre application IoT Central, vous devez contacter Rigado et lui fournir les détails de connexion de l’application à partir des étapes ci-dessus. 

Une fois l’appareil connecté à Internet, Rigado est en mesure de transmettre une mise à jour de configuration à l’appareil de passerelle Cascade 500 via un canal sécurisé. 

Cette mise à jour appliquera les détails de connexion IoT Central sur l’appareil Cascade 500 et il apparaîtra dans la liste des appareils. 

![Liste d’appareils](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Vous êtes maintenant prêt à utiliser votre appareil C500 dans votre application IoT Central !

## <a name="next-steps"></a>Étapes suivantes

Voici quelques suggestions pour continuer :

- En savoir plus sur la [connectivité des appareils dans Azure IoT Central](./concepts-get-connected.md)
- Découvrez comment [superviser la connectivité des appareils à l’aide d’Azure CLI](./howto-monitor-devices-azure-cli.md)
