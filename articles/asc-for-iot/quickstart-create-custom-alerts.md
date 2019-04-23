---
title: Créer des alertes personnalisées destinées à Azure Security Center pour IoT (préversion) | Microsoft Docs
description: Créer et affecter des alertes d’appareil personnalisées destinées à Azure Security Center pour IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: d793b105e6d73c98739cd05d6e19a218413d7813
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861993"
---
# <a name="quickstart-create-custom-alerts"></a>Démarrage rapide : Créer des alertes personnalisées

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Avec les alertes et groupes de sécurité personnalisés, tirez pleinement parti des informations de sécurité de bout en bout et des connaissances catégorisées sur les appareils pour assurer une plus grande sécurité dans votre solution IoT. 

## <a name="why-use-custom-alerts"></a>Pourquoi utiliser des alertes personnalisées ? 

Vous connaissez vos appareils IoT mieux que personne.

Pour les clients qui connaissent très bien le comportement attendu de leurs appareils, Azure Security Center pour IoT permet de traduire cette connaissance en stratégie de comportement des appareils et de générer des alertes sur les écarts enregistrés par rapport au comportement normal, attendu.

## <a name="security-groups"></a>Groupes de sécurité

Les groupes de sécurité vous permettent de définir des groupes logiques d’appareils et de gérer leur état de sécurité de façon centralisée.

Ces groupes peuvent représenter des appareils équipés d’un matériel particulier, des appareils déployés dans un emplacement donné, en fait tout groupe répondant à des besoins spécifiques.

Les groupes de sécurité sont définis par une propriété d’étiquette de jumeau de module de sécurité nommée **SecurityGroup**. Modifiez la valeur de cette propriété pour changer le groupe de sécurité d’un appareil.  

Par défaut, chaque solution IoT sur IoT Hub dispose d’un groupe de sécurité nommé **par défaut**.

Utilisez des groupes de sécurité pour regrouper vos appareils par catégories logiques. Après avoir créé les groupes, affectez-les aux alertes personnalisées de votre choix, pour utiliser la solution de bout en bout la plus efficace. 

## <a name="customize-an-alert"></a>Personnaliser une alerte

1. Ouvrez votre hub IoT. 
2. Sélectionnez **Sécurité**, puis **Alertes personnalisées**. 
3. Choisissez les groupes de sécurité auxquels vous souhaitez appliquer la personnalisation. 
4. Cliquez sur **Ajouter une alerte personnalisée**
5. Entrez un nom d’alerte (notez que les noms d’alerte ne sont plus modifiables après leur création). 
6. Sélectionnez un comportement d’alerte personnalisée dans la liste déroulante. 
7. Modifiez les propriétés nécessaires, puis cliquez sur **OK**.
8. N’oubliez pas de cliquer sur **ENREGISTRER**. Si la nouvelle alerte n’est pas enregistrée, elle est supprimée la prochaine fois que vous fermez IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Alertes disponibles pour la personnalisation

Le tableau suivant récapitule les alertes disponibles pour la personnalisation.

| Severity | Nom                                                                                                    | source de données | Description                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Faible      | Alerte personnalisée - le nombre de messages cloud-à-appareil dans le protocole AMQP n’est pas dans la plage autorisée          | IoT Hub     | La quantité de messages cloud-à-appareil (protocole AMQP) dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                  |
| Faible      | Alerte personnalisée - le nombre de messages cloud-à-appareil rejetés dans le protocole AMQP n’est pas dans la plage autorisée | IoT Hub     | La quantité des messages cloud-à-appareil (protocole AMQP) qui ont été rejetés par l’appareil dans une fenêtre de temps n’est pas dans la plage autorisée configurée |
| Faible      | Alerte personnalisée - le nombre de messages appareil-à-cloud dans le protocole AMQP n’est pas dans la plage autorisée          | IoT Hub     | La quantité de messages appareil-à-cloud (protocole AMQP) dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                  |
| Faible      | Alerte personnalisée - le nombre d’appels de méthode directe n’est pas dans la plage autorisée                              | IoT Hub     | La quantité d’appels de méthode directe dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                                     |
| Faible      | Alerte personnalisée - le nombre de chargements de fichiers n’est pas dans la plage autorisée                                       | IoT Hub     | La quantité de chargements de fichiers dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                                              |
| Faible      | Alerte personnalisée - le nombre de messages cloud-à-appareil dans le protocole HTTP n’est pas dans la plage autorisée          | IoT Hub     | La quantité de messages cloud-à-appareil (protocole HTTP) dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                  |
| Faible      | Alerte personnalisée - le nombre de messages cloud-à-appareil rejetés dans le protocole HTTP n’est pas dans la plage autorisée | IoT Hub     | La quantité des messages cloud-à-appareil (protocole HTTP) qui ont été rejetés par l’appareil dans une fenêtre de temps n’est pas dans la plage autorisée configurée |
| Faible      | Alerte personnalisée - le nombre de messages appareil-à-cloud dans le protocole HTTP n’est pas dans la plage autorisée          | IoT Hub     | La quantité de messages appareil-à-cloud (protocole HTTP) dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                  |
| Faible      | Alerte personnalisée - le nombre de messages cloud-à-appareil dans le protocole MQTT n’est pas dans la plage autorisée          | IoT Hub     | La quantité de messages cloud-à-appareil (protocole MQTT) dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                  |
| Faible      | Alerte personnalisée - le nombre de messages cloud-à-appareil rejetés dans le protocole MQTT n’est pas dans la plage autorisée | IoT Hub     | La quantité des messages cloud-à-appareil (protocole MQTT) qui ont été rejetés par l’appareil dans une fenêtre de temps n’est pas dans la plage autorisée configurée |
| Faible      | Alerte personnalisée - le nombre de messages appareil-à-cloud dans le protocole MQTT n’est pas dans la plage autorisée          | IoT Hub     | La quantité de messages appareil-à-cloud (protocole MQTT) dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                  |
| Faible      | Alerte personnalisée - le nombre de vidanges de file d’attente de commandes n’est pas dans la plage autorisée                               | IoT Hub     | La quantité des vidanges de file d’attente de commandes dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                                      |
| Faible      | Alerte personnalisée - le nombre de mises à jour de jumeau n’est pas dans la plage autorisée                                       | IoT Hub     | La quantité des mises à jour de jumeau dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                                              |
| Faible      | Alerte personnalisée - le nombre d’opérations non autorisées n’est pas dans la plage autorisée                            | IoT Hub     | La quantité d’opérations non autorisées dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                                   |
| Faible      | Alerte personnalisée - le nombre de connexions actives n’est pas dans la plage autorisée                                        | Agent       | La quantité de connexions actives dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                                        |
| Faible      | Alerte personnalisée - une connexion sortante vers une adresse ip qui n’est pas autorisée a été créée                              | Agent       | Une connexion sortante vers une adresse ip qui n’est pas autorisée a été créée                                                                                  |
| Faible      | Alerte personnalisée - le nombre de connexions locales ayant échoué n’est pas dans la plage autorisée                                | Agent       | La quantité de connexions locales ayant échoué dans une fenêtre de temps n’est pas dans la plage autorisée configurée                                                       |
| Faible      | Alerte personnalisée - connexion d’un utilisateur qui n’est pas autorisé                                                      | Agent       | Un utilisateur local qui n’est pas autorisé s’est connecté à l’appareil                                                                                        |
| Faible      | Alerte personnalisée - exécution d’un processus qui n’est pas autorisé                                               | Agent       | Un processus qui n’est pas autorisé a été exécuté sur l’appareil |          |

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment déployer un agent de sécurité...

> [!div class="nextstepaction"]
> [Déployer un agent de sécurité](how-to-deploy-agent.md)
