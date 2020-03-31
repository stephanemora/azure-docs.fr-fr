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
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158344"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Connecter un appareil de passerelle Rigado Cascade 500 à votre application IoT Central


Cet article vous explique comment, en tant que concepteur de solutions, vous pouvez connecter un appareil de passerelle Rigado Cascade 500 à votre application Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>Qu’est-ce qu’un Cascade 500 ?

Une passerelle IoT Cascade 500 est une offre matérielle de Rigado incluse dans le cadre de sa solution Cascade EaaS. Il fournit aux équipes commerciales et de projets IoT une solution flexible de calcul de périphérie, un environnement d’application en conteneur robuste et un large éventail d’options de connectivité de périphériques sans fil, notamment Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 est précertifié pour Azure IoT Plug-and-Play (préversion), ce qui permet aux créateurs de solutions d’intégrer facilement l’appareil dans leurs solutions de bout en bout. La passerelle Cascade vous permet d’établir une connexion sans fil avec un large éventail de capteurs de surveillance de condition qui sont à proximité de l’appareil de passerelle. Ces capteurs peuvent être intégrés dans IoT Central via l’appareil de passerelle.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous devez disposer des ressources suivantes :

* Un appareil Rigado Cascade 500. Pour plus d’informations, veuillez visiter [Rigado](https://www.rigado.com/).
* Une application Azure IoT Central. Pour plus d'informations, consultez [Créer une application](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

Pour intégrer un appareil de passerelle Cascade 500 à votre instance d’application Azure IoT Central, vous devez configurer un modèle d’appareil correspondant dans votre application.

Pour ajouter un modèle d’appareil Cascade 500 : 

1. Accédez à l’onglet ***Modèles d’appareil*** dans le volet gauche, sélectionnez **+ Nouveau** : ![Créer un nouveau modèle d’appareil](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. La page vous donne la possibilité de ***créer un modèle personnalisé*** ou ***d’utiliser un modèle d’appareil préconfiguré***
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

![Clé primaire](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Vous êtes maintenant prêt à utiliser votre appareil C500 dans votre application IoT Central !

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un Rigado Cascade 500 à votre application Azure IoT Central, l’étape suivante suggérée est d’apprendre à [créer une application Analytique dans le magasin](../retail/tutorial-in-store-analytics-create-app-pnp.md) pour créer une solution de bout en bout. 