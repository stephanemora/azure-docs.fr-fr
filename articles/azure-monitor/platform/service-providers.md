---
title: Azure Monitor pour les fournisseurs de services | Microsoft Docs
description: Azure Monitor permet aux fournisseurs de services gérés (MSP), aux grandes entreprises, aux éditeurs de logiciels indépendants (ISV) et aux fournisseurs de service d’hébergement de gérer et de surveiller les serveurs situés dans l’infrastructure locale ou cloud d’un client.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: meirm
ms.openlocfilehash: 97d8d6fac93ebabac8fb319ce2f1ab8719f5f86b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60452651"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor pour les fournisseurs de services
Les espaces de travail Log Analytics dans Azure Monitor permettent aux fournisseurs de services managés (MSP), aux grandes entreprises, aux éditeurs de logiciels indépendants (ISV) et aux fournisseurs de service d’hébergement de gérer et de surveiller les serveurs situés dans l’infrastructure locale ou cloud d’un client. 

Les grandes entreprises et les fournisseurs de services présentent de nombreux points communs, en particulier si les ressources informatiques de plusieurs divisions sont gérées par une équipe informatique centralisée. Le terme *fournisseur de services* est utilisé dans ce document par souci de simplicité, mais sachez que les entreprises et d’autres clients bénéficient de la même fonctionnalité.

Pour les partenaires et fournisseurs de services qui font partie du programme [Fournisseur de solutions cloud (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), Log Analytics dans Azure Monitor est l’un des services Azure disponibles dans les [abonnements Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architecture des fournisseurs de services

Les espaces de travail Log Analytics fournissent une méthode à l’administrateur pour qu’il contrôle le flux et l’isolation des journaux d’activité, et pour qu’il crée une architecture de journaux d’activité qui réponde à ses besoins métier. [Cet article](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) apporte des informations générales sur la gestion des espaces de travail. Les fournisseurs de services n’y sont pas abordés.

En ce qui concerne les espaces de travail Log Analytics, il existe trois architectures possibles pour les fournisseurs de services :

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuée : les journaux d’activité sont stockés dans les espaces de travail situés dans le locataire du client 

Dans cette architecture, un espace de travail est déployé dans le locataire du client lequel est utilisé pour tous les journaux d’activité de ce client. Les administrateurs des fournisseurs de services obtiennent l’accès à cet espace de travail à l’aide des [utilisateurs invités d’Azure Active Directory (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Pour accéder à ces espaces de travail, l’administrateur du fournisseur de services doit basculer vers l’annuaire du client, dans le portail Microsoft Azure.

Les avantages de cette architecture sont les suivants :
* Le client peut gérer l’accès aux journaux d’activité à l’aide de son propre [accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Chaque client peut avoir des paramètres différents pour son espace de travail, par exemple, pour la rétention ou la limitation des données.
* Elle permet l’isolation entre les clients pour répondre aux exigences réglementaires et de conformité.
* Les frais liés à chaque espace de travail sont intégrés à l’abonnement du client.
* Les journaux d’activité peuvent être collectés à partir de tous les types de ressources, et non uniquement celles basées sur l’agent. Par exemple, les journaux d’activité d’audit Azure.

Les désavantages de cette architecture sont les suivants :
* Il est plus difficile pour le fournisseur de services de gérer de nombreux locataires du client à la fois.
* Les administrateurs des fournisseurs de services doivent être approvisionnés dans l’annuaire du client.
* Le fournisseur de services ne peut pas analyser les données de tous ses clients.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central : les journaux d’activité sont stockés dans un espace de travail situé dans le locataire du fournisseur de services

Dans cette architecture, les journaux d’activité ne sont pas stockés dans les locataires du client, mais dans un emplacement central situé dans l’un des abonnements du fournisseur de services. Les agents qui sont installés sur les machines virtuelles du client sont configurés pour envoyer leurs journaux d’activité vers cet espace de travail à l’aide de l’ID et de la clé secrète de l’espace de travail.

Les avantages de cette architecture sont les suivants :
* Il est facile de gérer de nombreux clients et de les intégrer à différents systèmes backend.
* Le fournisseur de services dispose d’une propriété complète sur les journaux d’activité et les divers artefacts, tels que les fonctions et les requêtes enregistrées.
* Le fournisseur de services peut effectuer des analyses sur l’ensemble de ses clients.

Les désavantages de cette architecture sont les suivants :
* Cette architecture est applicable uniquement pour les données de machines virtuelles basées sur agent. Elle ne prend pas en compte les sources de données PaaS, SaaS et Azure Fabric.
* Il peut être difficile de distinguer les données des différents clients lorsqu’elles sont fusionnées dans un même espace de travail. La seule bonne méthode consiste à utiliser le nom de domaine complet (FQDN) de l’ordinateur ou l’ID de l’abonnement Azure. 
* Toutes les données de tous les clients sont stockées dans la même région avec une seule facture, et les mêmes paramètres de rétention et de configuration.
* Les services Azure Fabric et PaaS, par exemple Diagnostics Azure et les journaux d’audit Azure, nécessitent que l’espace de travail se trouve dans le même locataire que la ressource. Ils ne peuvent donc pas envoyer les journaux vers l’espace de travail central.
* Tous les agents de machine virtuelle de l’ensemble des clients sont authentifiés auprès de l’espace de travail central à l’aide du même ID et de la même clé d’espace de travail. Il n’existe aucune méthode permettant de bloquer les journaux d’activité d’un client sans interrompre les autres clients.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybride : les journaux d’activité sont stockés dans l’espace de travail situé dans le locataire du client, et certains d’entre eux sont envoyés vers un emplacement central.

Cette troisième architecture est un mélange des deux précédentes. Elle est basée sur l’architecture distribuée où les journaux d’activité sont stockés localement pour chaque client, mais elle utilise un mécanisme permettant de créer un référentiel central pour les journaux d’activité. Une partie des journaux d’activité est extraite et envoyée vers un emplacement central à des fins de création de rapports et d’analyse. Cette partie correspond parfois à un petit nombre de types de données ou à un récapitulatif d’activité, par exemple des statistiques quotidiennes.

Il existe deux options pour implémenter des journaux dans un emplacement central :

1. Espace de travail central : le fournisseur de services peut créer un espace de travail dans son locataire et utiliser un script qui utilise [l’API de requête](https://dev.loganalytics.io/) avec [l’API de collecte de données](../../azure-monitor/platform/data-collector-api.md) pour importer les données des différents espaces de travail dans l’emplacement central. Une autre option (autre que le script) consiste à utiliser une [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI comme emplacement central : Power BI peut servir d’emplacement central quand les différents espaces de travail exportent des données vers lui en utilisant l’intégration entre l’espace de travail Log Analytics et [Power BI](../../azure-monitor/platform/powerbi.md). 


## <a name="next-steps"></a>Étapes suivantes
* Automatiser la création et la configuration des espaces de travail à l’aide de [modèles Resource Manager](template-workspace-configuration.md)
* Automatiser la création des espaces de travail à l’aide de [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 
* Utiliser [Alertes](../../azure-monitor/platform/alerts-overview.md) pour intégrer les espaces de travail aux systèmes existants
* Générer des rapports de synthèse à l’aide de [Power BI](../../azure-monitor/platform/powerbi.md)
* Passez en revue le processus de [configuration de Log Analytics et de Power BI pour surveiller plusieurs clients CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
