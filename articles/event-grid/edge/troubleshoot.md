---
title: Détection des problèmes – Azure Event Grid sur IoT Edge | Microsoft Docs
description: Détection des problèmes dans Event Grid sur IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 9b8e5b95b0d1853d81de5a4ec603a3a59563da9d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379797"
---
# <a name="common-issues"></a>Problèmes courants

Si vous rencontrez des problèmes lors de l’utilisation d’Azure Event Grid dans votre environnement, consultez cet article pour vous guider dans la détection et la résolution des problèmes.

## <a name="view-event-grid-module-logs"></a>Afficher les journaux du module Event Grid

Pour détecter des problèmes, il se peut que vous deviez accéder aux journaux du module Event Grid. Sur la machine virtuelle où le module est déployé, exécutez la commande suivante :

Sur Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Sur Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Impossible d’effectuer des requêtes HTTPs

* Tout d’abord, assurez-vous que le module Event Grid a **inbound:serverAuth:tlsPolicy** défini sur **strict** ou **activé**.

* Dans le cas de communications de module à module, vérifiez que vous effectuez l’appel sur le port **4438** et que le nom du module correspond à ce qui est déployé. 

  Par exemple, si le module Event Grid a été déployé avec le nom **eventgridmodule**, votre URL doit être **https://eventgridmodule:4438** . Assurez-vous que la casse et le numéro de port sont corrects.
    
* S’il s’agit d’un module non-IoT, assurez-vous que le port Event Grid est mappé à l’ordinateur hôte pendant le déploiement, par exemple,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Impossible d’effectuer des requêtes HTTP

* Commencez par vous assurer que le module Event Grid a **inbound:serverAuth:tlsPolicy** défini sur **activé** ou **désactivé**.

* Dans le cas de communications de module à module, vérifiez que vous effectuez l’appel sur le port **5888** et que le nom du module correspond à ce qui est déployé. 

  Par exemple, si le module Event Grid a été déployé avec le nom **eventgridmodule**, votre URL doit être **http://eventgridmodule:5888** . Assurez-vous que la casse et le numéro de port sont corrects.
    
* S’il s’agit d’un module non-IoT, assurez-vous que le port Event Grid est mappé à l’ordinateur hôte pendant le déploiement, par exemple,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>La chaîne d’approbation a été émise par une autorité qui n’est pas approuvée

Par défaut, le module Event Grid module est configuré pour authentifier des clients avec un certificat émis par le démon de sécurité IoT Edge. Assurez-vous que le client présente un certificat enraciné dans cette chaîne.

La classe **IoTSecurity** dans [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) montre comment récupérer des certificats à partir du démon de sécurité IoT Edge et les utiliser pour configurer des appels sortants.

S’il ne s’agit pas d’un environnement de production, vous avez la possibilité de désactiver l’authentification du client. Pour en savoir plus, consultez la page [Sécurité et authentification](security-authentication.md).

## <a name="debug-events-not-received-by-subscriber"></a>Événements de débogage non reçus par l’abonné

Les raisons typiques sont les suivantes :

* L’événement n’a jamais été publié avec succès. Le client doit avoir reçu un StatusCode HTTP « 200 (OK) » lors de la publication d’un événement sur le module Event Grid.

* Vérifiez dans l’abonnement aux événements les points suivants :
    * L’URL de point de terminaison est valide
    * Aucun filtre dans l’abonnement n’entraîne d’abandon de l’événement.

* Vérifiez si le module d’abonné est en cours d’exécution

* Connectez-vous à la machine virtuelle sur laquelle le module Event Grid est déployé et consultez ses journaux.

* Activez la journalisation par livraison en définissant **broker:logDeliverySuccess=true** et en redéployant le module Event Grid, puis en renouvelant la demande. L’activation de la journalisation par livraison pouvant avoir un impact sur le débit et la latence, une fois le débogage terminé, nous vous recommandons de reconvertir en **broker:logDeliverySuccess=false** et de redéployer le module Event Grid.

* Activez les métriques en définissant **metrics:reportertype=console**, puis redéployez le module Event Grid. Toutes les opérations effectuées après cela entraînent la journalisation des métriques sur la console du module Event Grid, qui peut être utilisée pour poursuivre le débogage. Nous vous recommandons d’activer les métriques uniquement pour le débogage, et de les désactiver en définissant **metrics:reportertype=none**, puis en redéployant le module Event Grid.

## <a name="next-steps"></a>Étapes suivantes

Signalez les problèmes et suggestions liés à l’utilisation d’Event Grid sur IoT Edge via [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues).