---
title: Afficher et activer les paramètres de diagnostic dans le service FHIR-API Azure Healthcare
description: Cet article explique comment activer les paramètres de diagnostic dans le service FHIR et examiner des exemples de requêtes pour les journaux d’audit.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: how-to
ms.date: 10/12/2021
ms.author: zxue
ms.openlocfilehash: 7574d4abf20e07019016796e937cf80c7d915128
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130187572"
---
# <a name="view-and-enable-diagnostic-settings-in-the-fhir-service"></a>Afficher et activer les paramètres de diagnostic dans le service FHIR

L’accès aux journaux de diagnostic est essentiel pour tous les services de santé. La conformité aux exigences réglementaires comme Health Insurance Portability and Accountability Act (HIPAA) est un impératif. Dans cet article, vous allez apprendre à choisir des paramètres pour les journaux de diagnostic dans le service FHIR au sein des API Azure Healthcare. Vous allez également examiner des exemples de requêtes pour ces journaux.

> [!IMPORTANT]
> Le service API Azure Healthcare est actuellement en version préliminaire. les [conditions d’utilisation supplémentaires des](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) préversions de Microsoft Azure incluent des conditions juridiques qui s’appliquent aux fonctionnalités Azure qui sont en version bêta, en préversion ou encore non disponibles dans le cadre de la mise à la disposition générale.

## <a name="steps-to-enable-diagnostic-logs"></a>Étapes d’activation des journaux de diagnostic

1. Sélectionnez votre service FHIR dans la Portail Azure.

2. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

3. Sélectionnez **+ Ajouter des paramètres de diagnostic**.

   [![Capture d’écran de la page des paramètres de diagnostic dans le portail Azure. ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png) ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png#lightbox)

4. Entrez le **nom du paramètre de diagnostic**.
 
   [![Capture d’écran des détails de destination et de la case à cocher permettant d’activer ou de désactiver les journaux d’audit. ](media/diagnostic-logs/fhir-diagnostic-settings-add.png) ](media/diagnostic-logs/fhir-diagnostic-settings-add.png#lightbox)

5. Sélectionnez la méthode que vous souhaitez utiliser pour accéder à vos journaux :

   - **Envoyer à log Analytics espace de travail permet** d’envoyer des journaux et des métriques à un espace de travail Log Analytics dans Azure Monitor. Vous devez créer votre Log Analytics espace de travail avant de pouvoir sélectionner cette option.
   
   - L' **archivage dans un compte de stockage** est destiné à l’audit ou à l’inspection manuelle. Le compte de stockage que vous souhaitez utiliser doit déjà être créé. L’option de rétention s’applique uniquement à un compte de stockage. La stratégie de rétention est comprise entre 1 et 365 jours. Si vous ne souhaitez pas appliquer de stratégie de rétention et conserver les données de manière permanente, définissez la durée de rétention (jours) sur 0.

   - Le **flux vers un Event Hub** est destiné à être ingéré par un service tiers ou une solution analytique personnalisée. Vous devez créer un espace de noms Event Hub et une stratégie de Event Hub pour pouvoir configurer cette option.
   
   - L’option **Envoyer à la solution de partenaire** doit être sélectionnée si vous avez activé une solution de partenaire prise en charge par Azure. Pour plus d’informations, consultez [étendre Azure avec des solutions de partenaires](../../partner-solutions/overview.md).

6. Sélectionnez **AuditLogs**.

7. Sélectionnez **Enregistrer**.

> [!NOTE]
> L’affichage des premiers journaux dans l’espace de travail Log Analytics peut prendre jusqu’à 15 minutes, si vous avez sélectionné cette méthode. Si le service FHIR est déplacé d’un groupe de ressources ou d’un abonnement à un autre, mettez à jour les paramètres une fois le déplacement terminé.


## <a name="diagnostic-log-details"></a>Détails du journal de diagnostic

À ce stade, le service FHIR renvoie les champs suivants dans un journal de diagnostic :

|Nom du champ|Type|Notes|
|----------|----|-----|
|`CallerIdentity` |Dynamique|Conteneur de propriétés générique qui contient les informations d’identité.|
|`CallerIdentityIssuer` | String| Émetteur.|
|`CallerIdentityObjectId` | String| ID de l'objet.|
|`CallerIPAddress` | String| Adresse IP de l’appelant.|
|`CorrelationId` | String| L’ID de corrélation.|
|`FhirResourceType` | String| Type de ressource pour lequel l’opération a été exécutée.|
|`LogCategory` | String| La catégorie du journal. (Dans cet article, nous revenons `AuditLogs` .)|
|`Location` | String| Emplacement du serveur qui a traité la demande. Par exemple : `South Central US`.|
|`OperationDuration` | Int| Temps nécessaire pour effectuer cette demande, en secondes.|
|`OperationName` | String| Type d’opération. Par exemple, `update` ou `search-type`.|
|`RequestUri` | String| URI de la demande.|
|`ResultType` | String| État du journal. Les valeurs disponibles sont `Started` , `Succeeded` ou `Failed` .|
|`StatusCode` | Int| Code d’état HTTP. Par exemple : `200`.|
|`TimeGenerated` | DateTime| Date et heure de l’événement.|
|`Properties` | String| Propriétés de `FhirResourceType` .|
|`SourceSystem` | String| Le système source, qui est toujours `Azure` dans ce cas.|
|`TenantId` | String | ID de locataire.|
|`Type` | String| Type de journal, qui est toujours `MicrosoftHealthcareApisAuditLog` dans ce cas.|
|`_ResourceId` | String| Détails sur la ressource.|       
        
## <a name="sample-queries"></a>Exemples de requêtes

vous pouvez utiliser ces requêtes de Application Insights de base pour explorer vos données de journal :

- Exécutez la requête suivante pour afficher les journaux *des 100 les plus récents* :

  `Insights
  MicrosoftHealthcareApisAuditLogs
  | limit 100`

- Exécutez la requête suivante pour regrouper les opérations par *type de ressource FHIR*:

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | summarize count() by FhirResourceType`

- Exécutez la requête suivante pour obtenir tous les *résultats ayant échoué*:

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | where ResultType == "Failed"`   

## <a name="conclusion"></a>Conclusion

Il est essentiel d’avoir accès aux journaux de diagnostic pour surveiller un service et fournir des rapports de conformité. Dans cet article, vous avez appris à activer ces journaux pour le service FHIR. 

> [!NOTE]
> Des mesures seront ajoutées lorsque le service API Azure Healthcare sera mis à la disposition générale.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service FHIR, consultez :

>[!div class="nextstepaction"]
>[Qu’est-ce que le service FHIR ?](overview.md)   

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
