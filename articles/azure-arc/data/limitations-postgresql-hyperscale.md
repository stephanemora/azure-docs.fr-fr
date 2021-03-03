---
title: Limitations de PostgreSQL Hyperscale avec Azure Arc
description: Limitations de PostgreSQL Hyperscale avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416592"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Limitations de PostgreSQL Hyperscale avec Azure Arc

Cet article décrit les limitations de PostgreSQL Hyperscale avec Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Sauvegarde et restauration

- La restauration dans le temps (telle que la restauration à une date et une heure spécifiques) sur le même groupe de serveurs n’est pas prise en charge. Quand vous procédez à une restauration dans le temps, vous devez restaurer sur un autre groupe de serveurs que vous avez préalablement déployé. Après la restauration sur le nouveau groupe de serveurs, vous pouvez supprimer le groupe de serveurs d’origine.
- La restauration de la totalité du contenu d’une sauvegarde (par opposition à une restauration jusqu’à un point spécifique dans le temps) sur le même groupe de serveurs est prise en charge pour PostgreSQL version 12. Elle n’est pas prise en charge pour PostgreSQL version 11 en raison d’une limitation du moteur PostgreSQL avec des chronologies. La restauration de l’intégralité du contenu d’une sauvegarde pour un groupe de serveurs PostgreSQL de version 11 ne peut être effectuée que sur un autre groupe de serveurs.


## <a name="databases"></a>Bases de données

L’hébergement de plusieurs bases de données dans un groupe de serveurs n’est pas pris en charge.


## <a name="security"></a>Sécurité

La gestion des utilisateurs et des rôles n’est pas prise en charge. Pour le moment, continuez à utiliser l’utilisateur standard postgres.

## <a name="roles-and-responsibilities"></a>Rôles et responsabilités

Les rôles et les responsabilités entre Microsoft et ses clients diffèrent entre les services PaaS Azure (Platform as a Service) et Azure Hybrid (comme PostgreSQL Hyperscale avec Azure Arc). 

### <a name="frequently-asked-questions"></a>Forum aux questions

Le tableau ci-dessous récapitule les réponses aux questions fréquemment posées sur les rôles et responsabilités de support.

| Question                      | Azure Platform As A Service (PaaS) | Services Azure Arc hybrides |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Qui fournit l’infrastructure ?  | Microsoft                          | Customer                  |
| Qui fournit le logiciel ?*       | Microsoft                          | Microsoft                 |
| Qui effectue les opérations ? | Microsoft                          | Customer                  |
| Est-ce que Microsoft fournit des contrats SLA ?      | Oui                                | Non                        |
| Qui est responsable des contrats SLA ? | Microsoft                          | Customer                  |

\* Services Azure

__Pourquoi Microsoft ne fournit pas de contrats SLA sur les services Azure Arc hybrides ?__ Microsoft n’est pas propriétaire de l’infrastructure et ne l’exploite pas, contrairement aux clients.

## <a name="next-steps"></a>Étapes suivantes

- **Faites un essai.** Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure. 

- **Créez votre propre cluster.** Pour créer sur votre propre cluster Kubernetes, procédez comme suit : 
   1. [Installer les outils clients](install-client-tools.md)
   2. [Créer le contrôleur de données Azure Arc](create-data-controller.md)
   3. [Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Découvrir plus d’informations sur les services de données activés pour Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Découvrir Azure Arc](https://aka.ms/azurearc)
