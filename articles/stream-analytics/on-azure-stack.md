---
title: Exécuter Azure Stream Analytics sur Azure Stack
description: Créez un travail de périphérie Azure Stream Analytics et déployez-le sur Azure Stack Hub par le biais du runtime Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 9cfb98e4d221ee0e8aae7f96ce241269bd56616d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562247"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Exécuter Azure Stream Analytics sur Azure Stack

Vous pouvez exécuter Azure Stream Analytics sur Azure Stack Hub en tant que module Azure IoT Edge. Des configurations ajoutées au module IoT Edge permettent d’interagir avec le Stockage Blob Azure, Azure Event Hubs et Azure IoT Hub s’exécutant dans un abonnement Azure Stack Hub en autorisant les URL personnalisées trouvées dans chaque emploi d’Azure Stack Hub.

Avec Stream Analytics sur Azure Stack, vous pouvez créer des architectures véritablement hybrides pour le traitement des flux dans votre propre cloud autonome privé. Votre cloud peut être connecté avec des applications natives cloud en utilisant des services Azure cohérents en local, ou bien déconnecté de ces applications. 

Cet article vous montre comment envoyer en streaming des données à partir d’IoT Hub ou d’Event Hubs vers un autre hub d’événements ou stockage d’objets blob dans un abonnement Azure Stack Hub et traiter ces données avec Stream Analytics.

## <a name="set-up-environments"></a>Configurer des environnements

Stream Analytics est un service hybride sur Azure Stack Hub. Il s’agit d’un module IoT Edge qui est configuré dans Azure, mais qui peut être exécuté sur Azure Stack Hub.

Si vous débutez avec Azure Stack Hub ou IoT Edge, suivez les instructions fournies ici pour configurer des environnements.

### <a name="prepare-the-azure-stack-hub-environment"></a>Préparer l’environnement Azure Stack Hub

Créez un abonnement Azure Stack Hub. Pour plus d’informations, consultez le [tutoriel sur la création d’un abonnement Azure Stack Hub](/azure-stack/user/azure-stack-subscribe-services/).

Si vous voulez évaluer Azure Stack Hub sur votre propre serveur, vous pouvez utiliser le Kit de développement Azure Stack (ASDK). Pour plus d’informations sur le Kit ASDK, consultez la [présentation du Kit ASDK](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Pour exécuter Stream Analytics sur Azure Stack Hub, votre appareil doit avoir le runtime IoT Edge et doit disposer d’une connectivité réseau au hub Azure Stack ou être une machine virtuelle s’exécutant sur ce hub. Le runtime IoT Edge permet aux travaux de périphérie Stream Analytics de s’intégrer au Stockage Azure et à Azure Event Hubs qui s’exécutent sur votre hub Azure Stack. Pour plus d’informations, consultez [Azure Stream Analytics sur IoT Edge](stream-analytics-edge.md). 

En plus de disposer d’un accès réseau aux ressources Azure Stack Hub, l’appareil IoT Edge ou la machine virtuelle doit accéder à IoT Hub dans le cloud public Azure pour configurer le module Stream Analytics. 

Les articles suivants montrent comment configurer le runtime IoT Edge sur votre appareil ou machine virtuelle :

* [Installer le runtime Azure IoT Edge sur Windows](../iot-edge/how-to-install-iot-edge.md)
* [Installer le runtime Azure IoT Edge sur des systèmes Linux Debian](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Créer un travail de périphérie Azure Stream Analytics

Les travaux de périphérie Stream Analytics s’exécutent dans des conteneurs déployés sur des appareils IoT Edge. Ils sont composés de deux parties :
* Une partie cloud qui est chargée de la définition des travaux : les utilisateurs définissent les paramètres d’entrée, de sortie, de requête et d’autres paramètres, comme des événements dans le désordre, dans le cloud.
* Un module en cours d’exécution sur vos appareils IoT. Il contient le moteur Stream Analytics et reçoit la définition du travail à partir du cloud.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Quand vous créez un travail Stream Analytics pour qu’il s’exécute sur un appareil IoT Edge, il doit être stocké de manière à pouvoir être appelé à partir de cet appareil. Vous pouvez utiliser un compte de stockage Azure existant ou en créer un.
1. Dans le portail Azure, accédez à **Créer une ressource** > **Stockage** > **Compte de stockage - blob, fichier, table, file d’attente**.
1. Indiquez les valeurs suivantes pour créer votre compte de stockage.

   | Champ | Valeur |
   | --- | --- |
   | Nom | Fournissez un nom unique pour votre compte de stockage. |
   | Emplacement | Choisissez un emplacement proche de vous.|
   | Abonnement | Choisissez le même abonnement que votre IoT Hub.|
   | Groupe de ressources | Utilisez le même groupe de ressources pour toutes les ressources de test que vous créez dans le cadre des tutoriels et [guides de démarrage rapide IoT Edge](../iot-edge/quickstart.md). Par exemple, utilisez **IoTEdgeResources**. |

1. Conservez les valeurs par défaut pour les autres champs, puis sélectionnez **Créer**.


### <a name="create-a-new-job"></a>Créer une nouvelle tâche

1. Dans le portail Azure, accédez à **Créer une ressource** > **Internet des objets** > **Travail Stream Analytics**.
1. Indiquez les valeurs suivantes pour créer votre compte de stockage.

   | Champ | Valeur |
   | --- | --- |
   | Nom du travail | Fournissez un nom pour votre travail. Par exemple, **IoTEdgeJob**. |
   | Abonnement | Choisissez le même abonnement que votre IoT Hub.|
   | Groupe de ressources | Utilisez le même groupe de ressources pour toutes les ressources de test que vous créez dans le cadre des tutoriels et [guides de démarrage rapide IoT Edge](../iot-edge/quickstart.md). Par exemple, utilisez **IoTEdgeResources**. |
   | Emplacement | Choisissez un emplacement proche de vous. |
   | Environnement d'hébergement | Sélectionnez **Edge**. |

1. Sélectionnez **Create** (Créer).

### <a name="configure-your-job"></a>Configurer votre travail

Après avoir créé votre travail Stream Analytics dans le portail Azure, configurez-le avec une entrée, une sortie et une requête à exécuter sur les données qui y transitent. Vous pouvez spécifier manuellement des entrées à partir d’un hub IoT ou d’un hub d’événements dans un abonnement Azure Stack Hub.

1. Accédez à votre travail Stream Analytics dans le portail Azure.
1. Sous **Configurer**, sélectionnez **Paramètres du compte de stockage**, puis choisissez le compte de stockage que vous avez créé à l’étape précédente.
   > [!div class="mx-imgBorder"]
   > [ ![Capture d’écran montrant le paramétrage du compte de stockage de travail](media/on-azure-stack/storage-account-settings.png) ](media/on-azure-stack/storage-account-settings.png#lightbox)
1. Sous **Topologie de la tâche**, sélectionnez **Entrées** > **Ajouter une entrée de flux**.
1. Dans la liste déroulante, sélectionnez **Hub IoT**, **Hub d’événements** ou **Hub Edge**. 
1. Si l’entrée est un hub d’événements ou un hub IoT dans un abonnement Azure Stack Hub, fournissez les informations manuellement, comme indiqué ici.

   #### <a name="event-hub"></a>Event Hub

   | Champ | Valeur |
   | --- | --- |
   | Alias d’entrée | Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
   | Espace de noms Service Bus | Conteneur pour un ensemble d’entités de messagerie. Lorsque vous créez un concentrateur Event Hub, vous créez également l’espace de noms. Par exemple, *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com*. |
   | Nom du hub d’événements | Nom du concentrateur Event Hub à utiliser comme entrée. |
   | Nom de la stratégie du hub d’événements | Stratégie d’accès partagé qui fournit l’accès au concentrateur Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option permettant d’indiquer manuellement les paramètres de hub d’événements. |
   | Clé de stratégie Event Hub | Clé d’accès partagé utilisée pour autoriser l’accès au hub d’événements. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option permettant d’indiquer manuellement les paramètres de hub d’événements. Vous pouvez la trouver dans les paramètres du hub d’événements. |
   | Groupe de consommateurs du hub d’événements (facultatif) | Utilisez un groupe de consommateurs différent pour chaque travail Stream Analytics. Cette chaîne identifie le groupe de consommateurs à utiliser pour ingérer les données du concentrateur Event Hub. Si aucun groupe de consommateurs n’est spécifié, le travail Stream Analytics utilise le groupe de consommateurs $Default. |
   | Nombre de partitions | Nombre de partitions dans un hub d’événements. |

   > [!div class="mx-imgBorder"]
   > [ ![Capture d’écran montrant les entrées du hub d’événements.](media/on-azure-stack/event-hub-input.png) ](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>hub IOT

   | Champ | Valeur |
   | --- | --- |
   | Alias d’entrée | Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
   | IoT Hub | Nom du concentrateur IoT Hub à utiliser comme entrée. Par exemple, *<IoT Hub Name>.shanghai.azurestack.corp.microsoft.com*. |
   | Nom de la stratégie d’accès partagé | Stratégie d’accès partagé qui fournit l’accès au concentrateur IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
   | Clé de la stratégie d’accès partagé | Clé d’accès partagé utilisée pour autoriser l’accès au concentrateur IoT Hub. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option permettant d’indiquer manuellement les paramètres du hub IoT. |
   | Groupe de consommateurs (facultatif) | Utilisez un groupe de consommateurs différent pour chaque travail Stream Analytics. Le groupe de consommateurs est utilisé pour ingérer les données du hub IoT. Stream Analytics utilise le groupe de consommateurs $Default, sauf si vous spécifiez autre chose. |
   | Nombre de partitions | Nombre de partitions dans un hub d’événements. |

   > [!div class="mx-imgBorder"]
   > [ ![Capture d’écran montrant les entrées du hub IoT.](media/on-azure-stack/iot-hub-input.png) ](media/on-azure-stack/iot-hub-input.png#lightbox)

1. Conservez les valeurs par défaut pour les autres champs, puis sélectionnez **Enregistrer**.
1. Sous **Topologie de la tâche**, ouvrez **Sorties**, puis sélectionnez **Ajouter**.
1. Dans la liste déroulante, sélectionnez **Stockage Blob**, **Hub d’événements** ou **Hub Edge**.
1. Si la sortie est un hub d’événements ou un stockage d’objets blob dans un abonnement Azure Stack Hub, fournissez les informations manuellement, comme indiqué ici.

   #### <a name="event-hub"></a>Event Hub

   | Champ | Valeur |
   | --- | --- |
   | Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cet Event Hub. |
   | Espace de noms Service Bus | Conteneur pour un ensemble d’entités de messagerie. En créant un hub d’événements, vous avez également créé un espace de noms Service Bus. Par exemple, *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com*. |
   | Nom du hub d’événements | Nom de votre sortie Event Hub. |
   | Nom de la stratégie du hub d’événements | Stratégie d’accès partagé que vous pouvez créer dans l’onglet **Configurer** de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
   | Clé de stratégie Event Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Event Hub. |

   > [!div class="mx-imgBorder"]
   > [ ![Capture d’écran montrant les sorties du hub d’événements.](media/on-azure-stack/event-hub-output.png) ](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Stockage d’objets blob 

   | Champ | Valeur |
   | --- | --- |
   | Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage d’objets blob. |
   | Compte de stockage | Nom du compte de stockage où vous envoyez votre sortie. Par exemple, *<Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com*. |
   | Clé du compte de stockage | Clé secrète associée au compte de stockage. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option permettant d’indiquer manuellement les paramètres du stockage d’objets blob. |

> [!NOTE]
> Le format Parquet n’est pas pris en charge pour les travaux de périphérie sur Azure Stack Hub. Pour le **Nombre minimal** de lignes et la **Durée maximale**, utilisez **0** ou laissez-les vides.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Déployer Stream Analytics sur une machine virtuelle ou un appareil connecté à Azure Stack

1. Dans le portail Azure, ouvrez IoT Hub. Accédez à **IoT Edge**, puis sélectionnez l’appareil ou la machine virtuelle que vous voulez cibler pour ce déploiement.
1. Sélectionnez **Définir des modules** >  **+ Ajouter**, puis sélectionnez **Module Azure Stream Analytics**. 
1. Sélectionnez l’abonnement et le travail de périphérie Stream Analytics que vous avez créé. Sélectionnez **Enregistrer**, puis sélectionnez **Suivant : Routes**.

   > [!div class="mx-imgBorder"]
   > [ ![Capture d’écran montrant l’ajout de modules.](media/on-azure-stack/edge-modules.png) ](media/on-azure-stack/edge-modules.png#lightbox)

1. Sélectionnez **Vérifier + créer**.
1. À l’étape **Vérifier + créer**, sélectionnez **Créer**. 
   > [!div class="mx-imgBorder"]
   > [ ![Capture d’écran montrant le manifeste.](media/on-azure-stack/module-content.png) ](media/on-azure-stack/module-content.png#lightbox)
1. Vérifiez que le module est ajouté à la liste.
   > [!div class="mx-imgBorder"]
   > [ ![Capture d’écran montrant la page de déploiement.](media/on-azure-stack/edge-deployment.png) ](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes
- [Azure Stream Analytics sur IoT Edge](./stream-analytics-edge.md)
- [Développer des travaux de périphérie Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
