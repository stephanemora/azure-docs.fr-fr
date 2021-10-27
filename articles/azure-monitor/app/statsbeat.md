---
title: Statsbeat dans Azure Application Insights | Microsoft Docs
description: Statistiques sur les SDK Application Insights et instrumentation automatique
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 440f6651f759bce9340763ea141c8057d5a1e4b8
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167803"
---
# <a name="statsbeat-in-azure-application-insights"></a>Statsbeat dans Azure Application Insights

Statsbeat collecte des [métriques personnalisées](../essentials/metrics-custom-overview.md) essentielles et non essentielles sur les SDK Application Insights et l’instrumentation automatique. Statsbeat présente trois avantages pour les clients Azure Monitor Application Insights :
-   Intégrité et fiabilité du service (supervision de l’extérieur vers l’intérieur de la connectivité au point de terminaison d’ingestion)
-   Diagnostics de support (insights d’aide autonome et insights CSS)
-   Amélioration du produit (insights pour l’optimisation de la conception)

Les données Statsbeat sont stockées dans un magasin de données Microsoft.  Cela n’a aucun impact sur le volume et le coût de la supervision globale des clients. 

## <a name="what-data-does-statsbeat-collect"></a>Quelles données Statsbeat collecte-t-il ?

Statsbeat collecte les métriques essentielles et non essentielles.

## <a name="supported-languages"></a>Langues prises en charge

| C#                        | Java            | JavaScript                | Node.js         | Python          |
|---------------------------|-----------------|---------------------------|-----------------|-----------------|
| Actuellement non pris en charge   | Prise en charge       | Actuellement non pris en charge   | Prise en charge       | Prise en charge       |


### <a name="essential-statsbeat"></a>Statsbeat - Métriques essentielles

#### <a name="network-statsbeat"></a>Statsbeat - Réseau

|Nom de métrique|Unité|Dimensions prises en charge|
|-----|-----|-----|
|Nombre de réussites de la demande|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Nombre d’échecs de la demande|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Durée de la demande|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Nombre de tentatives|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Nombre de limitations|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|Nombre d’exceptions|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|

#### <a name="attach-statsbeat"></a>Statsbeat - Lier

|Nom de métrique|Unité|Dimensions prises en charge|
|-----|-----|-----|
|Joindre|Count| `Resource Provider`, `Resource Provider Identifier`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

#### <a name="feature-statsbeat"></a>Statsbeat - Fonctionnalité

|Nom de métrique|Unité|Dimensions prises en charge|
|-----|-----|-----|
|Fonctionnalité|Count| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Feature`, `Type`, `Operating System`, `Language`, `Version`|

### <a name="non-essential-statsbeat"></a>Statsbeat - Métriques non essentielles

- Effectue le suivi des réussites et des échecs de la persistance des disques
- Métriques réseau en temps réel Statsbeat
- Service de métadonnées Azure pour réseau Statsbeat
- Générateur de profils Statsbeat

### <a name="configure-statsbeat"></a>Configurer Statsbeat

#### <a name="java"></a>[Java](#tab/java)

Pour désactiver les métriques Statsbeat non essentielles, ajoutez la configuration ci-dessous à votre fichier de configuration.

```json
{
  "preview": {
    "statsbeat": {
        "disabled": "true"
    }
  }
}
```

Vous pouvez également désactiver cette fonctionnalité en affectant à la variable d’environnement `APPLICATIONINSIGHTS_STATSBEAT_DISABLED` la valeur true (qui sera alors prioritaire sur la valeur de désactivation spécifiée dans la configuration JSON).

#### <a name="node"></a>[Nœud](#tab/node)

N/A

#### <a name="python"></a>[Python](#tab/python)

N/A

---
