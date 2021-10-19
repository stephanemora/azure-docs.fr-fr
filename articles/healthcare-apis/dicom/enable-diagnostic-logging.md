---
title: Activer la journalisation des diagnostics dans le service DICOM-API Azure Healthcare
description: Cet article explique comment activer la journalisation des diagnostics dans le service DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 51a162ccbad5813a559cf27c26adcd964adeaf07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122433905"
---
# <a name="enable-diagnostic-logging-in-the-dicom-service"></a>Activer la journalisation des diagnostics dans le service DICOM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à activer la journalisation des diagnostics dans le service DICOM et à passer en revue des exemples de requêtes pour ces journaux. L’accès aux journaux de diagnostic est essentiel pour tout service de santé dans lequel la conformité aux exigences réglementaires est un impératif. La fonctionnalité du service DICOM active les journaux de diagnostic est les [paramètres de diagnostic](../../azure-monitor/essentials/diagnostic-settings.md) de la portail Azure. 

## <a name="enable-audit-logs"></a>Activer les journaux d’audit

1. Pour activer le service de journalisation des diagnostics DICOM, sélectionnez votre service DICOM dans le Portail Azure.
2. Sélectionnez le panneau **Journal d’activité** , puis sélectionnez **paramètres de diagnostic**.

   [![Journal d’activité Azure. ](media/dicom-activity-log.png) ](media/dicom-activity-log.png#lightbox)

3. Sélectionnez **+ Ajouter le paramètre de diagnostic**.

   [![Ajoutez des paramètres de diagnostic. ](media/add-diagnostic-settings.png) ](media/add-diagnostic-settings.png#lightbox)

4. Entrez le **nom des paramètres de diagnostic**.

   [![Configurez les paramètres de diagnostic. ](media/configure-diagnostic-settings.png) ](media/configure-diagnostic-settings.png#lightbox)

5. Sélectionnez les détails de la **catégorie** et de la **destination** pour accéder aux journaux de diagnostic.

   * **Envoyer à log Analytics espace de travail** dans le Azure Monitor. Vous devez créer votre espace de travail journaux Analytics avant de pouvoir sélectionner cette option. Pour plus d’informations sur les journaux de la plateforme, consultez [vue d’ensemble des journaux de la plateforme Azure](../../azure-monitor/essentials/platform-logs-overview.md).
   * **Archivez les journaux dans un compte de stockage** pour les auditer ou les inspecter manuellement. Le compte de stockage que vous souhaitez utiliser doit déjà être créé.
   * **Diffuser en continu vers un Event Hub** pour l’ingestion par un service tiers ou une solution analytique personnalisée. Vous devez créer un espace de noms Event Hub et une stratégie de Event Hub pour pouvoir configurer cette étape.
   * **Envoyez à la solution de partenaire** que vous utilisez en tant qu’organisation partenaire dans Azure. Pour plus d’informations sur les intégrations potentielles de partenaires, consultez [la documentation des solutions de partenaires Azure](../../partner-solutions/overview.md) .

     Pour plus d’informations sur les métriques prises en charge, consultez [mesures prises en charge avec Azure Monitor](.././../azure-monitor/essentials/metrics-supported.md).

6. Sélectionnez **Enregistrer**.


   > [!Note] 
   > L’affichage des premiers journaux dans Log Analytics peut prendre jusqu’à 15 minutes. En outre, si le service DICOM est déplacé d’un groupe de ressources ou d’un abonnement à un autre, mettez à jour les paramètres une fois le déplacement terminé. 
 
   Pour plus d’informations sur l’utilisation des journaux de diagnostic, consultez [la documentation du journal des ressources Azure](../../azure-monitor/essentials/platform-logs-overview.md) .

## <a name="audit-log-details"></a>Détails du journal d'audit

Le service DICOM retourne les champs suivants dans le journal d’audit : 

|Nom du champ  |Type  |Notes  |
|---------|---------|---------|
|correlationId|String|ID de corrélation :
|catégorie|String|Catégorie de journal (nous avons actuellement « AuditLogs ») 
|operationName|String|Décrit le type d’opération (par exemple, récupérer, stocker, interroger, etc.). 
|time|DateTime|Date et heure de l’événement. 
|resourceId|String| Chemin d’accès Azure à la ressource.
|identité|Dynamique|Un conteneur de propriétés générique contenant des informations d’identité (ne s’applique actuellement pas à DICOM).
|callerIpAddress|String|Adresse IP de l’appelant.
|Emplacement|String|Emplacement du serveur qui a traité la demande.
|URI|String|URI de la demande.
|resultType|String| Les valeurs disponibles sont actuellement Started, SUCCEEDED ou failed.
|resultSignature|Int|Code d’état HTTP (par exemple, 200)
|properties|String|Décrit les propriétés, y compris le type de ressource, le nom de la ressource, l’ID d’abonnement, l’action d’audit, etc.
|type|String|Type de journal (il est toujours MicrosoftHealthcareApisAuditLog dans le cas présent).
|Level|String|Niveau de journalisation (information, erreur).
|operationVersion|String| Actuellement vide. Sera utilisé pour afficher la version de l’API.


## <a name="sample-queries"></a>Exemples de requêtes

voici quelques-unes des requêtes de base Application Insights que vous pouvez utiliser pour explorer vos données de journal.

Exécutez la requête suivante pour afficher les journaux **des 100 les plus récents** :

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Exécutez la requête suivante pour regrouper les opérations par **type de ressource DICOM**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by DICOMResourceType
```

Exécutez la requête suivante pour obtenir tous les **résultats ayant échoué**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusion

Il est essentiel d’avoir accès aux journaux de diagnostic pour surveiller un service et fournir des rapports de conformité. Le service DICOM vous permet d’effectuer ces actions via les journaux de diagnostic. 

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à activer les journaux d’audit pour le service DICOM. Pour plus d’informations sur le journal d’activité Azure, consultez.
 
>[!div class="nextstepaction"]
>[Schéma d’événement du journal d’activité Azure](.././../azure-monitor/essentials/activity-log-schema.md)
