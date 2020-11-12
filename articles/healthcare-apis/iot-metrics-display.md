---
title: Afficher et configurer les métriques du Connecteur Azure IoT pour FHIR (préversion)
description: Cet article explique comment afficher et configurer les métriques du connecteur Azure IoT pour FHIR (préversion).
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133384"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Afficher et configurer les métriques du Connecteur Azure IoT pour FHIR (préversion) 

Dans cet article, vous allez apprendre à afficher et à configurer le connecteur IoT Azure pour les métriques FHIR*. 

> [!TIP]
> Suivez les instructions dans [Exporter les métriques du connecteur Azure IoT pour FHIR (préversion) via les paramètres de diagnostic](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) pour savoir comment configurer l’exportation de données de métriques.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Afficher les métriques pour le connecteur Azure IoT pour FHIR (préversion)
1. Pour afficher les métriques des connecteurs IoT, sélectionnez votre service API Azure pour FHIR dans le portail Azure. 

2. Accédez à **Métriques**. 

3. Sélectionnez l’onglet **Connecteur IoT**.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Connecteur1 IoT" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Sélectionnez un connecteur IoT pour en afficher les métriques (par exemple : il y a [4] connecteurs IoT associés à ce service API Azure pour FHIR).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Connecteur2 IoT" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> L’onglet **Personnalisé** permet de créer des combinaisons heure/date spécifiques pour afficher les métriques de connecteur IoT.

5. Sélectionnez la période des métriques de connecteur IoT que vous souhaitez afficher (par exemple : 1 heure, 24 heures, 7 jours ou Personnalisé).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Connecteur3 IoT" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Types de métriques pour le connecteur Azure IoT pour FHIR (préversion) 
Les métriques de connecteur IoT affichées sont les suivantes :

|Type de métriques|Objectif des métriques| 
|-----------|--------------|
|Nombre de messages entrants|Nombre de messages entrants bruts reçus (par exemple : événements de l’appareil).|
|Nombre de messages normalisés|Nombre de messages normalisés.|
|Nombre de groupes de messages|Nombre de groupes dont les messages sont agrégés dans la fenêtre temporelle désignée.|
|Latence moyenne de la phase normalisée|Latence moyenne de la phase de normalisation. La phase de normalisation consiste à normaliser les messages entrants bruts.|
|Latence moyenne de la phase de regroupement|Latence moyenne de la phase de regroupement. La phase de regroupement consiste à effectuer la mise en mémoire tampon, l’agrégation et le regroupement de messages normalisés.| 
|Nombre total d'erreurs|Nombre total d’erreurs.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Zoom sur les métriques du connecteur Azure IoT pour FHIR (préversion) et configuration
Dans cet exemple, nous allons nous concentrer sur la métrique **Nombre de messages entrants**.

1. Sélectionnez un point dans le temps sur lequel vous souhaitez vous concentrer.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Connecteur4 IoT" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. À partir de cet écran, vous pouvez **ajouter une métrique** , **ajouter un filtre** et **appliquer un fractionnement** pour personnaliser davantage. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT Connector5" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusion 
L’accès aux métriques du plan de données est essentiel pour la supervision et la résolution des problèmes.  Le connecteur Azure IoT pour FHIR vous aide à réaliser ces actions grâce à Métriques. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les questions fréquemment posées sur le Connecteur Azure IoT pour FHIR.

>[!div class="nextstepaction"]
>[FAQ sur le Connecteur Azure IoT pour FHIR](fhir-faq.md)

*Dans le Portail Azure, le Connecteur Azure IoT pour FHIR est appelé Connecteur IoT (préversion).

FHIR est la marque déposée de HL7 et est utilisé avec l’autorisation de HL7.