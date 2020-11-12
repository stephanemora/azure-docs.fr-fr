---
title: Activer la journalisation des diagnostics dans l’API Azure pour FHIR®
description: Cet article explique comment activer la journalisation des diagnostics dans l’API Azure pour FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 262509df98b93c7902d83f90756872a16d84198f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398128"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Activer la journalisation des diagnostics dans l’API Azure pour FHIR®

Dans cet article, vous allez apprendre à activer la journalisation des diagnostics dans l’API Azure pour FHIR® et à passer en revue des exemples de requêtes pour ces journaux. L’accès aux journaux de diagnostic est essentiel pour tout service de santé dans lequel la conformité aux exigences réglementaires (telles que HIPAA) est indispensable. La fonctionnalité de l’API Azure pour FHIR® qui active les journaux de diagnostic est le [**Paramètres de diagnostic**](../azure-monitor/platform/diagnostic-settings.md) dans le Portail Azure. 

## <a name="enable-audit-logs"></a>Activer les journaux d’audit
1. Pour activer la journalisation des diagnostics dans l’API Azure pour FHIR®, sélectionnez votre service Azure API pour FHIR® dans le Portail Azure 
2. Accédez à **Paramètres de diagnostic** 
![Paramètres de diagnostic](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Sélectionnez **+ Ajouter un paramètre de diagnostic**.

4. Attribuer un nom au paramètre

5. Sélectionnez la méthode que vous souhaitez utiliser pour accéder à vos journaux de diagnostic :

    1. **Archivez les journaux dans un compte de stockage** pour les auditer ou les inspecter manuellement. Le compte de stockage que vous souhaitez utiliser doit déjà être créé.
    2. **Envoyez les journaux à Event Hub** pour ingestion par un service tiers ou une solution d’analytique personnalisée. Vous devez créer un espace de noms et une stratégie Event Hub pour pouvoir configurer cette étape.
    3. **Envoyez les journaux vers l’espace de travail Log Analytics** dans Azure Monitor. Vous devrez créer votre espace de travail Logs Analytics avant de pouvoir sélectionner cette option.

6. Sélectionnez **AuditLogs** et les métriques que vous souhaitez relever.

7. Cliquez sur Enregistrer.

> [!Note] 
> L’affichage des premiers journaux dans Log Analytics peut prendre jusqu’à 15 minutes.  
 
Pour plus d’informations sur l’utilisation des journaux de diagnostic, reportez-vous à la [documentation sur les journaux de ressources d’Azure](../azure-monitor/platform/platform-logs-overview.md)

## <a name="audit-log-details"></a>Détails du journal d'audit
À ce stade, l’API Azure pour FHIR® service retourne les champs suivants dans le journal d’audit : 

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

Exécutez cette requête pour afficher les **100 journaux les plus récents**  :

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Exécutez cette requête pour regrouper les opérations par **Type de ressource FHIR**  :

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
Il est essentiel d’avoir accès aux journaux de diagnostic pour surveiller un service et fournir des rapports de conformité. L’API Azure pour FHIR® vous permet d’effectuer ces actions via les journaux de diagnostic. 
 
FHIR® est la marque déposée de HL7 et est utilisé avec l’autorisation de HL7.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à activer les journaux d’audit pour l’API Azure pour FHIR®. À la prochaine étape, vous allez découvrir les autres paramètres que vous pouvez configurer dans l’API Azure pour FHIR.
 
>[!div class="nextstepaction"]
>[Paramètres supplémentaires](azure-api-for-fhir-additional-settings.md)