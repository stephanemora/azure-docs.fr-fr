---
title: Activer la journalisation des diagnostics dans l’API Azure pour FHIR
description: Cet article explique comment activer la journalisation des diagnostics dans l’API Azure pour FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: d8f7a2b2f31fb192147c1950866cff77064a3671
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019156"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Activer la journalisation des diagnostics dans l’API Azure pour FHIR

Dans cet article, vous allez apprendre à activer la journalisation des diagnostics dans l’API Azure pour FHIR et à passer en revue des exemples de requêtes pour ces journaux. L’accès aux journaux de diagnostic est essentiel pour tout service de santé dans lequel la conformité aux exigences réglementaires (telles que HIPAA) est indispensable. La fonctionnalité de l’API Azure pour FHIR qui active les journaux de diagnostic s’appelle [**Paramètres de diagnostic**](../../azure-monitor/essentials/diagnostic-settings.md) dans le portail Azure. 

## <a name="view-and-download-fhir-metrics-data"></a>Afficher et télécharger des données de métriques FHIR

Vous pouvez afficher les métriques sous surveillance | Mesures à partir du portail. Les métriques incluent le nombre de demandes, la latence moyenne, le nombre d’erreurs, la taille des données, le nombre de demandes utilisées, le nombre de requêtes qui ont dépassé la capacité et la disponibilité (en%). La capture d’écran ci-dessous montre le RUs utilisé pour un exemple d’environnement avec très peu d’activités au cours des 7 derniers jours. Vous pouvez télécharger les données au format JSON.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="API Azure pour les métriques FHIR à partir du portail" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Activer les journaux d’audit
1. Pour activer la journalisation des diagnostics dans l’API Azure pour FHIR, sélectionnez votre service Azure API pour FHIR dans le portail Azure 
2. Accédez à **Paramètres de diagnostic**. 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Ajoutez les paramètres de diagnostic Azure FHIR." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Sélectionnez **+ Ajouter un paramètre de diagnostic**.

4. Attribuer un nom au paramètre

5. Sélectionnez la méthode que vous souhaitez utiliser pour accéder à vos journaux de diagnostic :

    1. **Archivez les journaux dans un compte de stockage** pour les auditer ou les inspecter manuellement. Le compte de stockage que vous souhaitez utiliser doit déjà être créé.
    2. **Envoyez les journaux à Event Hub** pour ingestion par un service tiers ou une solution d’analytique personnalisée. Vous devez créer un espace de noms et une stratégie Event Hub pour pouvoir configurer cette étape.
    3. **Envoyez les journaux vers l’espace de travail Log Analytics** dans Azure Monitor. Vous devrez créer votre espace de travail Logs Analytics avant de pouvoir sélectionner cette option.

6. Sélectionnez **AuditLogs** et/ou **AllMetrics**. Les métriques incluent le nom du service, la disponibilité, la taille des données, la latence totale, les demandes totales, le nombre total d’erreurs et l’horodatage. Vous trouverez plus de détails sur les [mesures prises en charge](https://docs.microsoft.com/azure/azure-monitor/essentials/metrics-supported#microsofthealthcareapisservices). 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Paramètres des diagnostics Azure FHIR. Sélectionnez AuditLogs et/ou AllMetrics." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Sélectionnez **Enregistrer**.


> [!Note] 
> L’affichage des premiers journaux dans Log Analytics peut prendre jusqu’à 15 minutes. En outre, si l’API Azure pour FHIR est déplacée d’un groupe de ressources ou d’un abonnement à un autre, mettez à jour le paramètre une fois le déplacement terminé. 
 
Pour plus d’informations sur l’utilisation des journaux de diagnostic, reportez-vous à la [documentation sur les journaux de ressources d’Azure](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>Détails du journal d'audit
À ce stade, le service API Azure pour FHIR retourne les champs suivants dans le journal d’audit : 

|Nom du champ  |Type  |Notes  |
|---------|---------|---------|
|CallerIdentity|Dynamique|Conteneur de propriétés générique contenant des informations d’identité
|CallerIdentityIssuer|String|Émetteur 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|Adresse IP de l’appelant 
|CorrelationId|String| ID de corrélation :
|FhirResourceType|String|Type de ressource pour lequel l’opération a été exécutée
|LogCategory|String|Catégorie du journal (nous revenons actuellement à « AuditLogs » pour LogCategory)
|Emplacement|String|Emplacement du serveur qui a traité la requête (par exemple, région sud du centre des États-Unis)
|OperationDuration|Int|Temps nécessaire pour effectuer cette requête en secondes
|NomOpération|String| Décrit le type d’opération (par exemple, mise à jour, recherche)
|RequestUri|String|URI de requête 
|ResultType|String|Les valeurs disponibles sont actuellement **Démarré(e)** , **Réussi(e)** ou **Échoué(e)**
|StatusCode|Int|Code d’état HTTP. (par exemple 200) 
|TimeGenerated|DateTime|Date et heure de l'événement|
|Propriétés|String| Décrit les propriétés de fhirResourceType
|SourceSystem|String| Système source (toujours Azure dans ce cas)
|TenantId|String|ID client
|Type|String|Type de journal (toujours MicrosoftHealthcareApisAuditLog dans le cas présent)
|_ResourceId|String|Détails sur la ressource

## <a name="sample-queries"></a>Exemples de requêtes

Voici quelques requêtes Application Insights simples dont vous pouvez vous servir pour explorer vos données de journal.

Exécutez cette requête pour afficher les **100 journaux les plus récents** :

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Exécutez cette requête pour regrouper les opérations par **Type de ressource FHIR** :

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Exécutez cette requête pour obtenir tous les **résultats ayant échoué**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusion 
Il est essentiel d’avoir accès aux journaux de diagnostic pour surveiller un service et fournir des rapports de conformité. L’API Azure pour FHIR vous permet d’effectuer ces actions via les journaux de diagnostic. 
 
FHIR est la marque déposée de HL7 et est utilisé avec l’autorisation de HL7.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à activer les journaux d’audit pour l’API Azure pour FHIR. À la prochaine étape, vous allez découvrir les autres paramètres que vous pouvez configurer dans l’API Azure pour FHIR.
 
>[!div class="nextstepaction"]
>[Paramètres supplémentaires](azure-api-for-fhir-additional-settings.md)
