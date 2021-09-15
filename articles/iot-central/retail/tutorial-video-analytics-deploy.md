---
title: 'Tutoriel : analytique vidéo Azure IoT - détection de mouvement et d’objet | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application Analytique vidéo - détection de mouvement et d’objet pour IoT Central.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 09/02/2021
ms.openlocfilehash: 026c1794f678c5d194b0e5174986f9f962508647
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473745"
---
# <a name="tutorial-deploy-and-walk-through-the-video-analytics---object-and-motion-detection-application-template"></a>Tutoriel : déployer et parcourir le modèle d’application Analytique vidéo - détection de mouvement et d’objet

Pour obtenir une vue d’ensemble de l’application clé *Analytique vidéo - détection de mouvement et d’objet*, le modèle d’application **Analytique vidéo - détection de mouvement et d’objet** vous permet de créer des solutions IoT, notamment des capacités d’analytique vidéo en direct.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagramme de la vue d’ensemble des composants d’analytique vidéo pour la détection d’objets et de mouvements.":::

Les principaux composants de la solution d’analytique vidéo sont les suivants :

### <a name="live-video-analytics-lva"></a>Analytique vidéo en direct (LVA)

LVA fournit une plateforme vous permettant de créer des applications vidéo intelligentes qui s’étendent à la périphérie et au cloud. La plateforme vous permet de créer des applications vidéo intelligentes qui s’étendent à la périphérie et au cloud. La plateforme offre la possibilité de capturer, d’enregistrer, d’analyser des vidéos en direct et de publier les résultats, tels qu’une analytique vidéo, dans les services Azure. Les services Azure peuvent s’exécuter dans le cloud ou à la périphérie. La plateforme peut être utilisée pour améliorer les solutions IoT avec analyse vidéo.

Pour plus d’informations, consultez [Live Video Analytics](https://github.com/Azure/live-video-analytics) sur GitHub.

### <a name="iot-edge-lva-gateway-module"></a>Module de passerelle LVA IoT Edge

Le module de passerelle LVA IoT Edge instancie les caméras en tant que nouveaux appareils et les connecte directement à IoT Central à l’aide du Kit de développement logiciel (SDK) du client d’appareil IoT.

Dans cette implémentation de référence, les appareils se connectent à la solution à l’aide de clés symétriques à partir de la périphérie. Pour plus d’informations sur la connectivité des appareils, consultez [Se connecter à Azure IoT Central](../core/concepts-get-connected.md).

### <a name="media-graph"></a>Graphe multimédia

Le graphe multimédia vous permet de définir l’emplacement à partir duquel capturer le média, comment le traiter et où livrer les résultats. Pour configurer le graphe multimédia, vous devez connecter des composants ou des nœuds de la manière souhaitée. Pour plus d’informations, consultez la [page relative au graphe multimédia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) sur GitHub.

La vidéo suivante fournit une procédure pas à pas sur l’utilisation du _modèle d’application d’analytique vidéo – détection d’objets et de mouvements_ pour déployer une solution IoT Central :

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

Dans cet ensemble de tutoriels, vous découvrez comment :

> [!div class="checklist"]
> * Déployer l’application
> * Déployer une instance IoT Edge qui se connecte à l’application
> * Superviser et gérer l’application

## <a name="prerequisites"></a>Prérequis

* Le déploiement de cette application ne nécessite aucun prérequis.
* Vous pouvez utiliser le plan tarifaire gratuit ou utiliser un abonnement Azure.

## <a name="deploy-the-application"></a>Déployer l’application

Procédez comme suit pour déployer une application IoT Central à l’aide du modèle d’application d’analytique vidéo :

1. Suivez le tutoriel [Créer une application d’analytique vidéo dans Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou [Créer une analytique vidéo dans Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) pour :
    - Créer un compte Azure Media Services.
    - Créer l’application IoT Central à partir du modèle d’application d’analytique vidéo pour la détection d’objets et de mouvements.
    - Configurer un appareil de passerelle dans l’application IoT Central. La passerelle permet aux caméras de se connecter à l’application.

1. Suivez le tutoriel [Créer une instance IoT Edge pour l’analytique vidéo (machine virtuelle Linux)](tutorial-video-analytics-iot-edge-vm.md) ou [Tutoriel : Créer une instance IoT Edge pour l’analytique vidéo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) pour :
    - Créer une machine virtuelle Azure sur laquelle est installé le runtime Azure IoT Edge. Préparer l’installation IoT Edge pour héberger le module d’analytique vidéo.
    - Connecter l’appareil IoT Edge à votre application IoT Central.

1. Effectuez le tutoriel [Superviser et gérer une application d’analytique vidéo](tutorial-video-analytics-manage.md) pour :
    - Ajouter des caméras de détection d’objet et de mouvement à la passerelle dans votre application IoT Central.
    - Démarrer le traitement de la caméra.
    - Installer un lecteur multimédia local pour afficher la vidéo capturée dans AMS.
    - Afficher la vidéo capturée qui montre les objets détectés.
    - Nettoyer.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous en avez terminé avec l’application, vous pouvez supprimer toutes les ressources que vous avez créées de la façon suivante :

1. Dans l’application IoT Central, dans la section **Administration**, accédez à la page **Votre application** . Puis sélectionnez **Supprimer**.
1. Dans le portail Azure, supprimez le groupe de ressources **lva-rg**.
1. Sur votre ordinateur local, arrêtez le conteneur Docker **amp-Viewer**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une vue d’ensemble des étapes de déploiement et de l’utilisation du modèle d’application d’analytique vidéo, consultez :

> [!div class="nextstepaction"]
> [Créer une application d’analytique vidéo dans Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou

> [!div class="nextstepaction"]
> [Créer une application d’analytique vidéo dans Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).