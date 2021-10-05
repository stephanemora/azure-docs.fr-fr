---
title: Limitations de SQL Managed Instance avec Azure Arc
description: Limitations de SQL Managed Instance avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 09/07/2021
ms.topic: conceptual
ms.openlocfilehash: 8709504d93f22ee5f2704b7b1a8d71a5e66df796
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838500"
---
# <a name="limitations-of-azure-arc-enabled-sql-managed-instance"></a>Limitations de SQL Managed Instance avec Azure Arc

Cet article décrit les limitations de SQL Managed Instance avec Azure Arc. 

Le niveau de service Critique pour l’entreprise est actuellement en préversion publique. Le niveau de service Usage général est mis à la disposition générale.

## <a name="backup-and-restore"></a>Sauvegarde et restauration

### <a name="automated-backups"></a>Sauvegardes automatisées 

-  Les bases de données utilisateur avec le mode de récupération SIMPLE ne sont pas sauvegardées.
-  Le `model` de base de données système n’est pas sauvegardé afin d’éviter toute interférence avec la création/suppression de la base de données. La base de données est verrouillée lorsque des opérations d’administration sont effectuées.

### <a name="point-in-time-restore-pitr"></a>Restauration à un point dans le temps

-  Ne prend pas en charge la restauration d’une SQL Managed Instance avec Azure Arc vers une autre SQL Managed Instance avec Azure Arc.  La base de données peut uniquement être restaurée sur la même instance gérée SQL avec Azure Arc où les sauvegardes ont été créées.
-  Le changement de nom d’une base de données n’est actuellement pas pris en charge, à des fins de limite de restauration dans le temps.
-  Aucune prise en charge de la restauration d’une base de données avec TDE actuellement.
-  Une base de données supprimée ne peut pas être restaurée actuellement.

## <a name="other-limitations"></a>Autres limitations 

-  La réplication transactionnelle n’est actuellement pas prise en charge.
-  La copie des journaux de transaction est actuellement bloquée.
-  Seule l’authentification SQL Server est prise en charge.

## <a name="roles-and-responsibilities"></a>Rôles et responsabilités

Les rôles et les responsabilités entre Microsoft et ses clients diffèrent entre les services PaaS Azure (Platform as a Service) et Azure Hybrid (comme SQL Managed Instance avec Azure Arc). 

### <a name="frequently-asked-questions"></a>Forum aux questions

Le tableau ci-dessous récapitule les réponses aux questions fréquemment posées sur les rôles et responsabilités de support.

| Question                          | Azure Platform As A Service (PaaS) | Services Azure Arc hybrides |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Qui fournit l’infrastructure ?  | Microsoft                          | Customer                  |
| Qui fournit le logiciel ?*       | Microsoft                          | Microsoft                 |
| Qui effectue les opérations ?          | Microsoft                          | Customer                  |
| Est-ce que Microsoft fournit des contrats SLA ?      | Oui                                | Non                        |
| Qui est responsable des contrats SLA ?          | Microsoft                          | Customer                  |

\* Services Azure

__Pourquoi Microsoft ne fournit pas de contrats SLA sur les services Azure Arc hybrides ?__ Microsoft n’est pas propriétaire de l’infrastructure et ne l’exploite pas, contrairement aux clients.

## <a name="next-steps"></a>Étapes suivantes

- **Faites un essai.** Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure. 

- **Créez votre propre cluster.** Pour créer sur votre propre cluster Kubernetes, procédez comme suit : 
   1. [Installer les outils clients](install-client-tools.md)
   2. [Créer le contrôleur de données Azure Arc](create-data-controller.md)
   3. [Créer une instance SQL Managed Instance avec Azure Arc](create-sql-managed-instance.md) 

- **Learn**
   - [En savoir plus sur les services de données avec Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Découvrir Azure Arc](https://aka.ms/azurearc)
