---
title: Log Analytics pour les fournisseurs de services | Microsoft Docs
description: Log Analytics permet aux fournisseurs de services gérés (MSP), grandes entreprises, éditeurs de logiciels indépendants (ISV) et fournisseurs de service d’hébergement de gérer et de surveiller les serveurs situés dans l’infrastructure locale ou cloud d’un client.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: meirm
ms.component: na
ms.openlocfilehash: ad0a3b8e0ee5f1114ea1db95cfe2f4176b8e2ddb
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931988"
---
# <a name="log-analytics-for-service-providers"></a>Log Analytics pour les fournisseurs de services
Log Analytics permet aux fournisseurs de services gérés (MSP), grandes entreprises, éditeurs de logiciels indépendants (ISV) et fournisseurs de service d’hébergement de gérer et de surveiller les serveurs situés dans l’infrastructure locale ou cloud d’un client. 

Les grandes entreprises et les fournisseurs de services présentent de nombreux points communs, en particulier si les ressources informatiques de plusieurs divisions sont gérées par une équipe informatique centralisée. Le terme *fournisseur de services* est utilisé dans ce document par souci de simplicité, mais sachez que les entreprises et d’autres clients bénéficient de la même fonctionnalité.

Pour les partenaires et fournisseurs de services qui font partie du programme [Fournisseur de solutions cloud (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), Log Analytics est l’un des services Azure offerts dans le cadre d’un [abonnement Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architecture des fournisseurs de services

Les espaces de travail Log Analytics fournissent une méthode à l’administrateur pour qu’il contrôle le flux et l’isolation des journaux, et pour qu’il crée une architecture de journaux qui réponde à ses besoins métier. [Cet article](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) apporte des informations générales sur la gestion des espaces de travail. Les fournisseurs de services n’y sont pas abordés.

En ce qui concerne les espaces de travail Log Analytics, il existe trois architectures possibles pour les fournisseurs de services :

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuée : les journaux sont stockés dans les espaces de travail situés dans le locataire du client 

Dans cette architecture, l’espace de travail est déployé dans le locataire du client qui est utilisé pour tous les journaux de ce client. Les administrateurs des fournisseurs de services obtiennent l’accès à cet espace de travail à l’aide des [utilisateurs invités d’Azure Active Directory (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). Pour accéder à ces espaces de travail, l’administrateur du fournisseur de services doit basculer vers l’annuaire du client dans le portail Azure.

Les avantages de cette architecture sont les suivants :
* Le client peut gérer l’accès aux journaux à l’aide de son propre [accès en fonction du rôle](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Chaque client peut avoir des paramètres différents pour son espace de travail, par exemple, pour la rétention ou la limitation des données.
* Elle permet l’isolation entre les clients pour répondre aux exigences réglementaires et de conformité.
* Les frais liés à chaque espace de travail sont intégrés à l’abonnement du client.
* Les journaux peuvent être collectés à partir de tous les types de ressources, et non uniquement celles basées sur l’agent. Par exemple, l’audit Azure.

Les désavantages de cette architecture sont les suivants :
* Il est plus difficile pour le fournisseur de services de gérer tous les locataires du client à la fois.
* Les administrateurs des fournisseurs de services doivent être provisionnés dans l’annuaire du client.
* Le fournisseur de services ne peut pas analyser les données de tous ses clients.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Centrale : les journaux sont stockés dans l’espace de travail situé dans le locataire du fournisseur de services

Dans cette architecture, les journaux ne sont pas stockés dans les locataires du client, mais dans un emplacement central situé dans l’un des abonnements du fournisseur de services. Les agents qui sont installés sur les machines virtuelles du client sont configurés pour envoyer leurs journaux vers cet espace de travail à l’aide de l’ID et de la clé secrète de l’espace de travail.

Les avantages de cette architecture sont les suivants :
* Il est facile de gérer le grand nombre de clients et de les intégrer à divers systèmes backend.
* Le fournisseur de services dispose d’une propriété complète sur les journaux et les divers artefacts, tels que les fonctions et les requêtes enregistrées.
* Le fournisseur de services peut lancer des analyses sur l’ensemble des clients.

Les désavantages de cette architecture sont les suivants :
* Cette architecture est applicable uniquement pour les données de machines virtuelles basées sur agent. Elle ne prend pas en compte les sources de données PaaS, SaaS et Azure Fabric.
* Il peut être difficile de distinguer les données des différents clients lorsqu’elles sont fusionnées dans un même espace de travail. La seule bonne méthode consiste à utiliser le nom de domaine complet (FQDN) de l’ordinateur ou l’ID de l’abonnement Azure. 
* Toutes les données de tous les clients sont stockées dans la même région avec une seule facture, et les mêmes paramètres de rétention et de configuration.
* Les services Azure Fabric et PaaS, tels qu’Azure Diagnostics et l’audit Azure, nécessitent que l’espace de travail se trouve dans le même locataire que la ressource. Ils ne peuvent donc pas envoyer les journaux vers l’espace de travail central.
* Tous les agents de machine virtuelle de l’ensemble des clients sont authentifiés auprès de l’espace de travail central à l’aide du même ID et de la même clé d’espace de travail. Il n’existe aucune méthode permettant de bloquer les journaux d’un client sans interrompre les autres clients.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybride : les journaux sont stockés dans l’espace de travail situé dans le locataire du client, et certains d’entre eux sont envoyés vers un emplacement central.

Cette troisième architecture est un mélange des deux précédentes. Elle est basée sur l’architecture distribuée où les journaux sont stockés localement pour chaque client, mais elle utilise un mécanisme permettant de créer un référentiel central pour les journaux. Une partie des journaux est extraite et envoyée vers un emplacement central à des fins de création de rapports et d’analyse. Cette partie peut correspondre à un petit nombre de types de données ou à un récapitulatif d’activité, comme des statistiques quotidiennes.

Il existe deux options pour implémenter l’emplacement central dans Log Analytics :

1. Espace de travail central : le fournisseur de services peut créer un espace de travail dans son locataire et utiliser un script qui utilise [l’API de requête](https://dev.loganalytics.io/) avec [l’API de collecte de données](log-analytics-data-collector-api.md) pour importer les données des différents espaces de travail dans l’emplacement central. Une autre option, autre que le script, consiste à utiliser une [application logique Azure](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Power BI comme emplacement central : Power BI peut servir d’emplacement central quand les différents espaces de travail exportent des données vers lui en utilisant l’intégration de Log Analytics à [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Étapes suivantes
* Automatiser la création et la configuration des espaces de travail à l’aide de [modèles Resource Manager](log-analytics-template-workspace-configuration.md)
* Automatiser la création des espaces de travail à l’aide de [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Utiliser [Alertes](log-analytics-alerts.md) pour intégrer les espaces de travail aux systèmes existants
* Générer des rapports de synthèse à l’aide de [Power BI](log-analytics-powerbi.md)

