---
title: Matrice de version et module Ansible pour Azure | Microsoft Docs
description: Matrice de version et module Ansible pour Azure
keywords: ansible, rôles, matrice, version, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74155987"
---
# <a name="ansible-module-and-version-matrix"></a>Matrice de version et module Ansible

Ansible comprend une suite de modules à utiliser dans le provisionnement et la configuration des ressources Azure. Ces ressources incluent les machines virtuelles, les groupes identiques, les services réseau et les services de conteneur. Cet article énumère les divers modules Ansible pour Azure et les versions d’Ansible dans lesquelles ils sont fournis.

## <a name="ansible-modules-for-azure"></a>Modules Ansible pour Azure

Les modules suivants peuvent être exécutés directement sur les hôtes distants ou par le biais de playbooks.  

Ces modules sont disponibles à partir de la version officielle d’Ansible et des rôles de playbook Microsoft suivants.

> [!NOTE]
> À partir d’Ansible 2.9, nous avons renommé tous les modules *_facts en *_info pour respecter la convention de nommage Ansible. Les anciens modules et les modules renommés sont liés. Hormis l’affichage d’un avertissement de dépréciation, tous les modules fonctionnent comme avant.

| Module Ansible pour Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 2.9 | Rôle Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Calcul**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_availabilityset_info              | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_deployment                         | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_functionapp                        | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_functionapp_info                  | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_image                              | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_resource                           | -            | -                           | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_resource_info                     | -            | -                           | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_resourcegroup                      | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_resourcegroup_info                | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_virtualmachine                     | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualmachineextension            | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_virtualmachineimage_info          | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualmachinescaleset             | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualmachinescaleset_info       | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Oui          | Oui          | Oui          |
| **Mise en réseau**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Oui          | Oui          | Oui          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Oui          | Oui          | Oui          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Oui          | Oui          | Oui          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Oui          | Oui          | Oui          |
| azure_rm_dnsrecordset                       | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_dnsrecordset_info                 | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_dnszone                            | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_dnszone_info                      | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_loadbalancer                       | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_loadbalancer_info                 | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_networkinterface                   | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_networkinterface_info             | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_publicipaddress                    | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_publicipaddress_info              | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_route                              | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_routetable                         | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_routetable_info                   | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_securitygroup                      | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_subnet                             | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualnetwork                     | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualnetwork_info               | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Oui          | Oui          | Oui          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Oui          | Oui          | Oui          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Oui          | Oui          |
| **Stockage**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_manageddisk_info                  | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_storageaccount                     | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_storageaccount_info               | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_storageblob                        | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_webapp                             | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_webapp_info                       | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Oui          | Oui          | Oui          |
| **Containers**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Oui          | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_aks                                | -            | -                           | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_aks_info                          | -            | -                           | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_containerinstance                  | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_containerregistry                  | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Oui          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Oui          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Oui          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Oui          |
| **Bases de données**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mysqldatabase                      | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_mysqlserver                        | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_postgresqldatabase                 | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_postgresqlserver                   | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_sqldatabase                        | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Oui          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Oui          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Oui          | Oui          | Oui          | Oui          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_sqlserver                          | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_sqlserver_info                    | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| **Analyse**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Oui          | Oui          |
| **Intégration**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| **Sécurité**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Oui          | Oui          | Oui          |
| azure_rm_keyvaultkey                        | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_keyvaultsecret                     | -            | Oui                         | Oui          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Oui          | Oui          | Oui          | Oui          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Oui          | Oui          | Oui          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Oui          | Oui          |
| **Gestion et gouvernance**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Oui        | Oui          |
| **Internet des objets**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Oui        | Oui          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Oui        | Oui          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduction au rôle de playbook pour Azure

Le [rôle de playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) et inclut tous les derniers modules Azure. Les mises à jour et correctifs de bogues sont effectués de façon plus opportune qu’avec la version Ansible officielle. Si vous utilisez Ansible pour l’approvisionnement de ressources Azure, nous vous invitons à installer le rôle de playbook `azure_preview_module`.

Le rôle de playbook `azure_preview_module` est publié toutes les trois semaines.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les rôles de playbook, consultez [Creating Reusable Playbooks](https://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Création de playbooks réutilisables). 