---
title: Matrice de version et module Ansible pour Azure | Microsoft Docs
description: Matrice de version et module Ansible pour Azure
keywords: ansible, rôles, matrice, version, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3f5bda36368ee1fe1f37527422c6072c3ffda177
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763332"
---
# <a name="ansible-module-and-version-matrix"></a>Matrice de version et module Ansible

Ansible comprend une suite de modules pour une utilisation dans l’approvisionnement et la configuration des ressources Azure. Ces ressources incluent les machines virtuelles, des groupes identiques, mise en réseau des services et les services de conteneur. Cet article répertorie les différents modules Ansible pour Azure et les versions d’Ansible dans lequel elles sont fournies.

## <a name="ansible-modules-for-azure"></a>Modules Ansible pour Azure

Les modules suivants peuvent être exécutées directement sur les hôtes distants ou par le biais des playbooks.

Ces modules sont disponibles à partir de la version officielle d’Ansible et de rôles de playbook Microsoft suivants.

| Module Ansible pour Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Rôle Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Calcul**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_availabilityset_facts              | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_deployment                         | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_functionapp                        | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_functionapp_facts                  | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_image                              | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_resource                           | -            | -                           | Oui          | OUI          | OUI          | Oui          |
| azure_rm_resource_facts                     | -            | -                           | Oui          | OUI          | OUI          | Oui          |
| azure_rm_resourcegroup                      | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_resourcegroup_facts                | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualmachine                     | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_virtualmachineextension           | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_virtualmachineimage_facts          | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualmachinescaleset            | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualmachinescaleset_facts      | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Oui          | Oui          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Oui          | Oui          |
| **Mise en réseau**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Oui          | Oui          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Oui          | Oui          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Oui          | Oui          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Oui          | Oui          |
| azure_rm_dnsrecordset                       | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_dnsrecordset_facts                 | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_dnszone                            | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_dnszone_facts                      | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_loadbalancer                       | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_loadbalancer_facts                 | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_networkinterface                   | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_networkinterface_facts             | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_publicipaddress                    | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_publicipaddress_facts              | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_route                              | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_routetable                         | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_securitygroup                      | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_subnet                             | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_virtualnetwork                     | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualnetwork_facts               | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Oui          | Oui          |
| **Stockage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_manageddisk_facts                  | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_storageaccount                     | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_storageaccount_facts               | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_storageblob                        | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| **web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_webapp                             | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Oui          | Oui          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Oui          | OUI                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_aks                                | -            | -                           | Oui          | OUI          | OUI          | Oui          |
| azure_rm_aks_facts                          | -            | -                           | Oui          | OUI          | OUI          | Oui          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_containerinstance                  | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_containerregistry                  | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Oui          | Oui          |
| **Bases de données**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mysqldatabase                      | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_mysqlserver                        | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_postgresqldatabase                 | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_postgresqlserver                   | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_sqldatabase                        | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Oui          | OUI          | Oui          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_sqlserver                          | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_sqlserver_facts                    | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| **Analyse**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Oui          | Oui          |
| **Intégration**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Oui          | Oui          |
| **Sécurité**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Oui          | Oui          |
| azure_rm_keyvaultkey                        | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_keyvaultsecret                     | -            | Oui                         | OUI          | OUI          | OUI          | Oui          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Oui          | Oui          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Oui          | Oui          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Oui          | OUI          | Oui          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Oui          | Oui          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Oui          | Oui          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduction au rôle de playbook pour Azure

Le [rôle de playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) inclut tous les derniers modules Azure. Les mises à jour et correctifs de bogues sont effectués de façon plus opportune qu’avec la version Ansible officielle. Si vous utilisez Ansible à des fins de provisionnement de ressources Azure, vous êtes invités à installer le `azure_preview_module` rôle de playbook.

Le `azure_preview_module` rôle de playbook est publié toutes les trois semaines.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les rôles de playbook, consultez [création de playbooks réutilisables](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 