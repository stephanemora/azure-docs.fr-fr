---
title: Configurer les alertes de Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Comment configurer vos alertes Metrics Advisor à l’aide de hooks d’e-mail, web et Azure DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: f63ea23cce0ab43dd5c9e0864619ff32d5dae50d
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341307"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Procédure : Configurer des alertes et obtenir des notifications à l’aide d’un hook

Une fois qu’une anomalie a été détectée par Metrics Advisor, une notification d’alerte est déclenchée à l’aide d’un hook en fonction des paramètres d’alerte. Un paramètre d’alerte peut être utilisé avec plusieurs configurations de détection, différents paramètres étant disponibles pour personnaliser votre règle d’alerte.

## <a name="create-a-hook"></a>Créer un hook

Metrics Advisor prend en charge quatre types de hooks différents : e-mail, Teams, webhook et Azure DevOps. Vous pouvez choisir celui qui fonctionne pour votre scénario spécifique.      

### <a name="email-hook"></a>Hook par e-mail

> [!Note]
> Avant que des alertes d’anomalie ne puissent être envoyées, les administrateurs de ressources de Metrics Advisor doivent configurer les paramètres de messagerie et entrer les **informations relatives à SMTP** dans Metrics Advisor. L’administrateur du groupe de ressources ou l’administrateur des abonnements doit attribuer au moins un rôle *Administrateur Metrics Advisor Cognitive Services* dans l’onglet Contrôle d’accès de la ressource Metrics Advisor. [Découvrez-en plus sur la configuration des paramètres de messagerie](../faq.yml#how-to-set-up-email-settings-and-enable-alerting-by-email-). 


Un hook d’e-mail est le canal des alertes d’anomalies à envoyer aux adresses e-mail spécifiées dans la section **E-mail à**. Deux types d’e-mails d’alerte sont envoyés : Des alertes **Le flux de données n’est pas disponible** et des **Rapports d’incidents** qui contiennent une ou plusieurs anomalies. 

Pour créer un hook d’e-mail, les paramètres suivants sont disponibles : 

|Paramètre |Description  |
|---------|---------|
| Nom | Nom du hook d’e-mail |
| E-mail à| Adresses e-mail auxquelles envoyer des alertes|
| Lien externe | Champ facultatif, qui active une redirection personnalisée, par exemple pour les notes relatives à la résolution des problèmes. |
| Titre d’alerte d’anomalie personnalisé | Le modèle de titre prend en charge `${severity}`, `${alertSettingName}`, `${datafeedName}`, `${metricName}`, `${detectConfigName}`, `${timestamp}`, `${topDimension}`, `${incidentCount}`, `${anomalyCount}`

Une fois que vous avez sélectionné **OK**, un hook d’e-mail est créé. Vous pouvez l’utiliser dans n’importe quel paramètre d’alerte pour recevoir des alertes d’anomalie. Pour obtenir des instructions détaillées, consultez le tutoriel relatif à l’[activation des notifications d’anomalies dans Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp).

### <a name="teams-hook"></a>Raccordement Teams

Un hook Teams est le canal d’envoi des alertes d’anomalies à un canal dans Microsoft Teams. Un hook Teams est implémenté par le biais d’un connecteur « Webhook entrant ». Vous devrez peut-être créer un connecteur « Webhook entrant » à l’avance dans votre canal Teams cible et obtenir son URL. Ensuite, revenez à l’espace de travail Metrics Advisor. 

Sélectionnez l’onglet « Hooks » dans la barre de navigation de gauche, puis sélectionnez le bouton « Créer un hook » en haut à droite de la page. Choisissez le type de hook « Teams ». Les paramètres suivants sont fournis : 

|Paramètre |Description  |
|---------|---------|
| Name | Nom du hook Teams | 
| URL du connecteur | URL qui vient d’être copiée à partir du connecteur « Webhook entrant » qui a été créé dans le canal Teams cible. |

Une fois que vous avez sélectionné **OK**, un hook Teams est créé. Vous pouvez l’utiliser dans n’importe quel paramètre d’alerte pour notifier le canal Teams cible des alertes d’anomalies. Pour obtenir des instructions détaillées, consultez le tutoriel relatif à l’[activation des notifications d’anomalies dans Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp).

### <a name="web-hook"></a>Webhook

Un webhook est un autre canal de notification utilisant un point de terminaison fourni par le client. Toute anomalie détectée sur la série chronologique est notifiée par le biais d’un webhook. Plusieurs étapes sont nécessaires pour activer un webhook en tant que canal de notification d’alerte dans Metrics Advisor. 

**Étape 1.**  Activer l’identité managée dans votre ressource Metrics Advisor

Une identité managée affectée par le système est limitée à une par ressource et liée au cycle de vie de celle-ci. Pour accorder des autorisations à l’identité managée, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure. L’identité managée est authentifiée avec Azure AD. Vous n’avez donc pas besoin de stocker des informations d’identification dans le code. 

Accédez à la ressource Metrics Advisor dans le portail Azure, sélectionnez « Identité », activez cette option. L’identité managée est alors activée. 

**Étape 2.** Créer un webhook dans l’espace de travail Metrics Advisor

Connectez-vous à votre espace de travail et sélectionnez l’onglet « Hooks », puis le bouton « Créer un hook ». 


Pour créer un webhook, vous devrez ajouter les informations suivantes :

|Paramètre |Description  |
|---------|---------|
|Point de terminaison     | L’adresse API à appeler lorsqu’une alerte est déclenchée. **DOIT être Https**.       |
|Nom d’utilisateur / mot de passe | Pour l’authentification auprès de l’adresse d’API. Ne pas remplir si l’authentification n’est pas nécessaire.         |
|En-tête     | En-têtes personnalisés dans l’appel d’API.        |
|Identificateur de certificat dans les coffres de clés Azure| Si l’accès au point de terminaison doit être authentifié par un certificat, celui-ci doit être stocké dans les coffres de clés Azure. Entrez l’identificateur ici.

> [!Note]
> Lors de la création ou de la modification d’un webhook, le point de terminaison est appelé comme test avec **un corps de demande vide**. Votre API doit retourner un code HTTP 200 pour réussir la validation.

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="fenêtre de création de webhook.":::

- La méthode de demande est **POST**
- Délai d’attente 30 s
- Réessayer en cas d’erreur 5xx, ignorer les autres erreurs. Ne suivra pas la demande de redirection 301/302.
- Corps de la demande : 
```
{
"value": [{
    "hookId": "b0f27e91-28cf-4aa2-aa66-ac0275df14dd",
    "alertType": "Anomaly",
    "alertInfo": {
        "anomalyAlertingConfigurationId": "1bc6052e-9a2a-430b-9cbd-80cd07a78c64",
        "alertId": "172536dbc00",
        "timestamp": "2020-05-27T00:00:00Z",
        "createdTime": "2020-05-29T10:04:45.590Z",
        "modifiedTime": "2020-05-29T10:04:45.590Z"
    },
    "callBackUrl": "https://kensho2-api.azurewebsites.net/alert/anomaly/configurations/1bc6052e-9a2a-430b-9cbd-80cd07a78c64/alerts/172536dbc00/incidents"
}]
}
```

**Étape 3 (facultatif).** Stocker votre certificat dans les coffres de clés Azure et obtenir l’identificateur. Comme indiqué, si l’accès au point de terminaison doit être authentifié par un certificat, celui-ci doit être stocké dans les coffres de clés Azure. 

- Consultez [Définir et récupérer un certificat dans Azure Key Vault à l’aide du portail Azure](../../../key-vault/certificates/quick-create-portal.md)
- Cliquez sur le certificat que vous avez ajouté pour copier l’« identificateur de certificat ». 
- Sélectionnez ensuite « Stratégies d’accès » et « Ajouter une stratégie d’accès », accordez l’autorisation « get » pour « Autorisations de clé », « Autorisations de secret » et « Autorisations de certificat ». Sélectionnez principal comme nom de votre ressource Metrics Advisor. Sélectionnez « Ajouter » et le bouton « Enregistrer » dans la page « Stratégies d’accès ». 

**Étape 4.** Recevoir une notification d’anomalie. Quand une notification est envoyée via un webhook, vous pouvez récupérer les données des incidents en appelant la requête de webhook « callBackUrl ». Détails pour cette API :

-   [/alert/anomaly/configurations/{configurationId}/alerts/{alertId}/incidents](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/operations/getIncidentsFromAlertByAnomalyAlertingConfiguration)

En utilisant un webhook et Azure Logic Apps, il est possible d’envoyer une notification par e-mail **sans qu’un serveur SMTP soit configuré**. Pour obtenir des instructions détaillées, consultez le tutoriel relatif à l’[activation des notifications d’anomalies dans Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp).

### <a name="azure-devops"></a>Azure DevOps

Metrics Advisor prend également en charge la création automatique d’un élément de travail dans Azure DevOps pour suivre les problèmes/bogues quand une anomalie est détectée. Toutes les alertes peuvent être envoyées via des hooks DevOps.

Pour créer un hook Azure DevOps, vous devrez ajouter les informations suivantes

|Paramètre |Description  |
|---------|---------|
| Nom | Nom du hook |
| Organisation | Organisation à laquelle votre DevOps appartient |
| Projet | Projet spécifique dans DevOps. |
| Jeton d'accès |  Jeton pour l’authentification auprès de DevOps. | 

> [!Note]
> Vous devez accorder des autorisations d’écriture si vous souhaitez que Metrics Advisor crée des éléments de travail en fonction des alertes d’anomalie. Après avoir créé des hooks, vous pouvez les utiliser dans tous vos paramètres d’alerte. Gérez vos hooks à la page **Paramètres de hooks**. 

## <a name="add-or-edit-alert-settings"></a>Ajouter ou modifier des paramètres d’alerte

Accédez à la page de détails des métriques pour y rechercher la section **Paramètres d’alerte** en bas à gauche. Elle répertorie tous les paramètres d’alerte qui s’appliquent à la configuration de détection sélectionnée. Lorsqu’une nouvelle configuration de détection est créée, il n’existe aucun paramètre d’alerte et aucune alerte n’est envoyée.  
Vous pouvez utiliser les icônes **Ajouter**, **Modifier** et **Supprimer** pour modifier les paramètres d’alerte.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Élément de menu Paramètres d’alerte.":::

Sélectionnez le bouton **ajouter** ou **modifier** pour obtenir une fenêtre permettant d’ajouter ou de modifier vos paramètres d’alerte.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Ajouter ou modifier des paramètres d’alerte":::

**Nom du paramètre d’alerte** : nom du paramètre d’alerte. Il s’affichera dans le titre de l’e-mail d’alerte.

**Hooks** : liste des hooks auxquels envoyer des alertes.

La section marquée dans la capture d’écran ci-dessus est celle des paramètres pour une configuration de détection. Vous pouvez définir différents paramètres d’alerte pour différentes configurations de détection. Choisissez la configuration cible à l’aide de la troisième liste déroulante dans cette fenêtre. 

### <a name="filter-settings"></a>Paramètres du filtre 

Voici les paramètres de filtre pour une configuration de détection.

**Alerte pour** a quatre options pour filtrer les anomalies :

* **Anomalies dans toutes les séries** : toutes les anomalies seront incluses dans l’alerte.         
* **Anomalies dans le groupe de séries** : filtrer les séries par valeurs de dimension. Définir des valeurs spécifiques pour certaines dimensions. Les anomalies ne seront incluses dans l’alerte que si la série correspond à la valeur spécifiée.       
* **Anomalies dans les séries de favoris** : seule la série marquée comme favori sera incluse dans l’alerte.        |
* **Anomalies dans les N premières séries** : ce filtre doit être utilisé si vous vous intéressez uniquement à la série dont la valeur se trouve parmi les N premières. Metrics Advisor va examiner certains horodatages précédents et vérifier si la valeur de la série sur ces horodatages est dans les N premières. Si le nombre de « dans les n premières » est supérieur au nombre spécifié, l’anomalie sera incluse dans une alerte.        |

**Les options de filtre d’anomalie constituent un filtre supplémentaire qui offre les options suivantes** :

- **Gravité** : l’anomalie sera incluse uniquement si la gravité de l’anomalie est comprise dans la plage spécifiée.
- **Répéter** : arrête temporairement les alertes pour les anomalies aux N points suivants (période) lorsqu’elles sont déclenchées dans une alerte.
    - **Type de répétition** : lorsqu’elle est définie sur **Série**, une anomalie déclenchée se répétera uniquement dans sa série. Pour **Métrique**, une anomalie déclenchée se répétera dans toutes les séries de cette mesure.
    - **Nombre de répétitions** : nombre de points (périodes) à répéter.
    - **Réinitialiser pour une anomalie non consécutive** : lorsque cette option est sélectionnée, une anomalie déclenchée ne répétera que les n anomalies successives suivantes. Si l’un des points de données suivants n’est pas une anomalie, la répétition sera réinitialisée à partir de ce point. Lorsqu’elle est désélectionnée, une anomalie déclenchée se répétera pour les n points suivants (période), même si les points de données successifs ne sont pas des anomalies.
- **Valeur** (facultatif) : filtrer par valeur. Uniquement les valeurs de points qui remplissent la condition, l’anomalie sera incluse. Si vous utilisez la valeur correspondante d’une autre mesure, les noms des dimensions des deux métriques doivent être cohérents.

Les anomalies non éliminées par le filtrage sont envoyées dans une alerte.

### <a name="add-cross-metric-settings"></a>Ajouter des paramètres de métriques croisées

Sélectionnez **+ Ajouter des paramètres de métriques** dans la page Paramètres d’alerte pour ajouter une autre section.

Le sélecteur d’**Opérateur** est la relation logique de chaque section pour déterminer si une alerte doit être envoyée.


|Opérateur  |Description  |
|---------|---------|
|AND     | Envoyer une alerte uniquement si une série correspond à chaque section d’alerte et que tous les points de données sont des anomalies. Si les métriques ont des noms de dimensions différents, une alerte ne sera jamais déclenchée.         |
|OU     | Envoyer l’alerte si au moins une section contient des anomalies.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Opérateur pour plusieurs sections des paramètres d’alerte":::

## <a name="next-steps"></a>Étapes suivantes

- [Ajustement de la détection d’anomalie selon les commentaires](anomaly-feedback.md)
- [Diagnostiquer un incident](diagnose-an-incident.md).
- [Configurer des métriques et affiner la configuration de la détection](configure-metrics.md)
