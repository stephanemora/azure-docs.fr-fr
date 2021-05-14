---
title: Bonnes pratiques relatives au Magasin des requêtes dans Azure Database pour PostgreSQL - Serveur flexible
description: Cet article décrit les bonnes pratiques relatives au Magasin des requêtes dans Azure Database pour PostgreSQL - Serveur flexible.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558601"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Bonnes pratiques pour le Magasin des requêtes - Serveur flexible

**S’applique à :** Azure Database pour PostgreSQL - Serveur flexible versions 11, 12

Cet article décrit les bonnes pratiques relatives à l’utilisation du Magasin des requêtes dans Azure Database pour PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Définir le mode de capture de requête optimal
Laissez le Magasin des requêtes capturer les données qui vous intéressent. 

|**pg_qs.query_capture_mode** | **Scénario**|
|---|---|
|_Tout_  |Analysez attentivement votre charge de travail concernant toutes les requêtes, leur fréquence d’exécution et d’autres statistiques. Identifiez les nouvelles requêtes dans votre charge de travail. Détectez si des requêtes ad hoc sont utilisées pour identifier les opportunités de paramétrage défini par l’utilisateur ou automatique. _All_ s’accompagne du coût de la consommation accrue des ressources. |
|_Top_  |Concentrez votre attention sur les principales requêtes : celles émises par les clients.
|_Aucun_ |Si la valeur est Aucun, le Magasin des requêtes ne capture aucune nouvelle requête. Vous avez déjà capturé un ensemble de requêtes et une fenêtre de temps que vous souhaitez examiner, et vous voulez éliminer les distractions que d’autres requêtes peuvent introduire. _None_ est adapté pour les environnements de test et d’évaluation. _None_ doit être utilisé avec précaution, car vous risquez de laisser passer l’opportunité de suivre et d’optimiser d’importantes nouvelles requêtes. |


> [!NOTE] 
> **pg_qs.query_capture_mode** remplace **pgms_wait_sampling.query_capture_mode**. Si pg_qs.query_capture_mode a la valeur _none_, le paramètre pgms_wait_sampling.query_capture_mode n’a aucun effet. 


## <a name="keep-the-data-you-need"></a>Conserver les données dont vous avez besoin
Le paramètre **pg_qs.retention_period_in_days** spécifie, en jours, la période de conservation des données pour le Magasin des requêtes. Les données des requêtes et des statistiques plus anciennes sont supprimées. Par défaut, le Magasin des requêtes est configuré pour conserver les données pendant 7 jours. Évitez de conserver les données historiques que vous ne prévoyez pas d’utiliser. Augmentez la valeur si vous avez besoin de conserver les données plus longtemps.


## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment obtenir ou définir des paramètres à l’aide du [portail Azure](howto-configure-server-parameters-using-portal.md) ou de l’interface [Azure CLI](howto-configure-server-parameters-using-cli.md).
