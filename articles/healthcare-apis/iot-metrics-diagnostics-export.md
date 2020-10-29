---
title: Exporter les métriques du connecteur Azure IoT pour FHIR (préversion) via les paramètres de diagnostic
description: Cet article explique comment exporter les métriques du connecteur Azure IoT pour FHIR (préversion) via les paramètres de diagnostic.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/16/2020
ms.author: jasteppe
ms.openlocfilehash: d7779c74a562e1237db863d7759b2adcffa2bddf
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558548"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Exporter les métriques du connecteur Azure IoT pour FHIR (préversion) via les paramètres de diagnostic

Dans cet article, vous allez apprendre à exporter les journaux de métriques du connecteur Azure IoT pour FHIR*. Ce sont les [**paramètres de diagnostic**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) du portail Azure qui permettent d’activer la journalisation des métriques. 

> [!TIP]
> Suivez les instructions décrites dans [Activer la journalisation des diagnostics dans l’API Azure pour FHIR et le connecteur Azure IoT pour FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) pour configurer la journalisation d’audit.

## <a name="enable-metric-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Activer la journalisation des métriques pour le connecteur Azure IoT pour FHIR (préversion)
1. Pour activer la journalisation des métriques pour le connecteur Azure IoT pour FHIR, sélectionnez votre service API Azure pour FHIR dans le portail Azure. 

2. Accédez à **Paramètres de diagnostic** . 

3. Sélectionnez **+ Ajouter un paramètre de diagnostic** .

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="Connecteur1 IoT" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Entrez un nom dans la boîte de dialogue **Nom du paramètre de diagnostic** .

5. Sélectionnez la méthode que vous souhaitez utiliser pour accéder à vos journaux de diagnostic :

    1. **Archivez les journaux dans un compte de stockage** pour les auditer ou les inspecter manuellement. Le compte de stockage que vous souhaitez utiliser doit déjà être créé.
    2. **Envoyez les journaux à Event Hub** pour ingestion par un service tiers ou une solution d’analytique personnalisée. Vous devez créer un espace de noms et une stratégie Event Hub pour pouvoir configurer cette étape.
    3. **Envoyez les journaux vers l’espace de travail Log Analytics** dans Azure Monitor. Vous devrez créer votre espace de travail Logs Analytics avant de pouvoir sélectionner cette option.

6. Sélectionnez **Erreurs, trafic et latence** pour le connecteur Azure IoT pour FHIR et toute autre catégorie métrique que vous souhaitez capturer pour l’API Azure pour FHIR.

7. Sélectionnez **Enregistrer** .

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="Connecteur1 IoT" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> L’affichage des premiers journaux de métriques dans le référentiel de votre choix peut prendre jusqu’à 15 minutes.  
 
Pour plus d’informations sur l’utilisation des journaux de diagnostic, consultez la [documentation relative aux journaux de ressources d’Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview).

## <a name="conclusion"></a>Conclusion 
L’accès aux journaux de métriques est essentiel pour l’analyse et la résolution des problèmes.  Le connecteur Azure IoT pour FHIR vous permet d’effectuer ces actions via les journaux de métriques. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les questions fréquemment posées sur le Connecteur Azure IoT pour FHIR.

>[!div class="nextstepaction"]
>[FAQ sur le Connecteur Azure IoT pour FHIR](fhir-faq.md)

*Dans le Portail Azure, le Connecteur Azure IoT pour FHIR est appelé Connecteur IoT (préversion).

FHIR est la marque déposée de HL7 et est utilisé avec l’autorisation de HL7.
