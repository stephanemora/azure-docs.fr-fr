---
title: Résolution des problèmes de changement de stratégie d’accès conditionnel dans Azure Active Directory
description: Diagnostiquez les modifications apportées à la stratégie d’accès conditionnel avec les journaux d’audit Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7683b64449a32013d7ac5e548ea9acaf85c51666
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561619"
---
# <a name="troubleshooting-conditional-access-policy-changes"></a>Résolution des problèmes de modifications des stratégies d’accès conditionnel

Le journal d’audit d’Azure Active Directory (Azure AD) est une source d’informations précieuse pour la résolution des problèmes et la façon dont les modifications des stratégies d’accès conditionnel se sont produites dans votre environnement.

Les données du journal d’audit sont conservées par défaut pendant 30 jours, ce qui peut ne pas être suffisamment long pour toutes les organisations. Les organisations peuvent stocker les données plus longtemps en modifiant les paramètres de diagnostic dans Azure AD : 

- Envoi des données à un espace de travail Log Analytics
- Archivage des journaux dans un compte de stockage
- Diffusion des données en continu vers un Event Hub
- Envoi des données à une solution partenaire
 
Ces options se trouvent dans le **portail Azure** > **Azure Active Directory**, **Paramètres de diagnostic** > **Modifier le paramètre**. Si vous n’avez pas de paramètre de diagnostic, suivez les instructions de l’article [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](../../azure-monitor/essentials/diagnostic-settings.md) pour en créer un. 

## <a name="use-the-audit-log"></a>Utiliser le journal d’audit

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Journaux d’audit**.
1. Sélectionnez la plage de **dates** sur laquelle doit porter la requête.
1. Sélectionnez **Activité** et choisissez une des actions suivantes :
   1. **Ajouter une stratégie d’accès conditionnel** : cette activité répertorie les stratégies nouvellement créées
   1. **Mettre à jour une stratégie d’accès conditionnel** : cette activité répertorie les stratégies modifiées
   1. **Supprimer une stratégie d’accès conditionnel** : cette activité répertorie les stratégies supprimées

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png" alt-text="Entrée du journal d’audit présentant les anciennes et les nouvelles valeurs JSON pour la stratégie d’accès conditionnel" lightbox="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png":::

## <a name="use-log-analytics"></a>Utiliser Log Analytics

Log Analytics permet aux organisations d’interroger des données à l’aide de requêtes intégrées ou de requêtes Kusto personnalisées. Pour plus d’informations, consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png" alt-text="Requête Log Analytics sur les mises à jour des stratégies d’accès conditionnel qui présentent l’emplacement des nouveaux et des anciennes valeurs" lightbox="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png":::

Après l’activation, vous pourrez accéder à Log Analytics dans le **portail Azure** > **Azure AD** > **Log Analytics**. La table la plus intéressante pour les administrateurs d’accès conditionnel est **AuditLogs**.

```kusto
AuditLogs 
| where OperationName == "Update conditional access policy"
```

Les modifications se trouvent sous **TargetResources** > **modifiedProperties**.

## <a name="reading-the-values"></a>Lecture des valeurs

Les anciennes et nouvelles valeurs du journal d’audit et de Log Analytics sont au format JSON. Comparez les deux valeurs pour voir les modifications apportées à la stratégie.

Exemple d’ancienne stratégie :

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
            "a0d3eb5b-6cbe-472b-a960-0baacbd02b51"
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:40.781994+00:00",
    "state": "enabled"
}

```

Exemple de stratégie mise à jour :

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:54.9739405+00:00",
    "state": "enabled"
}

``` 

Dans l’exemple ci-dessus, la stratégie mise à jour n’inclut pas les conditions d’utilisation dans les contrôles d’octroi.

### <a name="restoring-conditional-access-policies"></a>Restauration des stratégies d’accès conditionnel

Pour plus d’informations sur la mise à jour programmatique de vos stratégies d’accès conditionnel à l’aide de l’API Microsoft Graph, consultez l’article [Accès conditionnel : accès programmatique](howto-conditional-access-apis.md).

## <a name="next-steps"></a>Étapes suivantes

- [Présentation de la supervision d’Azure Active Directory](../reports-monitoring/overview-monitoring.md)
- [Installer et utiliser les vues Log Analytics pour Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Accès conditionnel : Accès par programme](howto-conditional-access-apis.md)
