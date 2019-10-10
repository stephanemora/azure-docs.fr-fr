---
title: Matrice de version et module Ansible pour Azure
description: Matrice de version et module Ansible pour Azure
keywords: ansible, rôles, matrice, version, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 18654500a78178c46e72f9f6cd01e8507fa179f0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241540"
---
# <a name="ansible-module-and-version-matrix"></a>Matrice de version et module Ansible

Ansible comprend une suite de modules à utiliser dans le provisionnement et la configuration des ressources Azure. Ces ressources incluent les machines virtuelles, les groupes identiques, les services réseau et les services de conteneur. Cet article énumère les divers modules Ansible pour Azure et les versions d’Ansible dans lesquelles ils sont fournis.

## <a name="ansible-modules-for-azure"></a>Modules Ansible pour Azure

Les modules suivants peuvent être exécutés directement sur les hôtes distants ou par le biais de playbooks.

Ces modules sont disponibles à partir de la version officielle d’Ansible et des rôles de playbook Microsoft suivants.

| Module Ansible pour Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Rôle Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Calcul**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_availabilityset_facts              | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_deployment                         | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_functionapp                        | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_functionapp_facts                  | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_image                              | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_resource                           | -            | -                           | OUI          | OUI          | OUI          | OUI          |
| azure_rm_resource_facts                     | -            | -                           | OUI          | OUI          | OUI          | OUI          |
| azure_rm_resourcegroup                      | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_resourcegroup_facts                | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachine                     | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_virtualmachineextension           | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_virtualmachineimage_facts          | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescaleset            | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescaleset_facts      | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | OUI          | OUI          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | OUI          | OUI          |
| **Mise en réseau**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | OUI          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | OUI          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | OUI          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | OUI          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | OUI          | OUI          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | OUI          | OUI          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | OUI          | OUI          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | OUI          | OUI          |
| azure_rm_dnsrecordset                       | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_dnsrecordset_facts                 | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_dnszone                            | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_dnszone_facts                      | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_loadbalancer                       | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_loadbalancer_facts                 | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_networkinterface                   | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_networkinterface_facts             | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_publicipaddress                    | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_publicipaddress_facts              | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_route                              | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_routetable                         | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_routetable_facts                   | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_securitygroup                      | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_subnet                             | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_virtualnetwork                     | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualnetwork_facts               | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | OUI          | OUI          |
| **Stockage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_manageddisk_facts                  | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_storageaccount                     | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_storageaccount_facts               | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_storageblob                        | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_webapp                             | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_webapp_facts                       | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | OUI          | OUI          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_aks                                | -            | -                           | OUI          | OUI          | OUI          | OUI          |
| azure_rm_aks_facts                          | -            | -                           | OUI          | OUI          | OUI          | OUI          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_containerinstance                  | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_containerregistry                  | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | OUI          | OUI          |
| **Bases de données**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mysqldatabase                      | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_mysqlserver                        | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_postgresqldatabase                 | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_postgresqlserver                   | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_sqldatabase                        | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | OUI          | OUI          | OUI          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_sqlserver                          | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_sqlserver_facts                    | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| **Analyse**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | OUI          | OUI          |
| **Intégration**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | OUI          | OUI          |
| **Sécurité**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | OUI          | OUI          |
| azure_rm_keyvaultkey                        | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_keyvaultsecret                     | -            | OUI                         | OUI          | OUI          | OUI          | OUI          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | OUI          | OUI          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | OUI          | OUI          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_autoscale_facts            | -            | -                         | -          | OUI          | OUI          | OUI          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | OUI          | OUI          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | OUI          | OUI          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduction au rôle de playbook pour Azure

Le [rôle de playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) et inclut tous les derniers modules Azure. Les mises à jour et correctifs de bogues sont effectués de façon plus opportune qu’avec la version Ansible officielle. Si vous utilisez Ansible pour l’approvisionnement de ressources Azure, nous vous invitons à installer le rôle de playbook `azure_preview_module`.

Le rôle de playbook `azure_preview_module` est publié toutes les trois semaines.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les rôles de playbook, consultez [Creating Reusable Playbooks](https://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Création de playbooks réutilisables). 
