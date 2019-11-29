---
title: Matrice de version et module Ansible pour Azure | Microsoft Docs
description: Matrice de version et module Ansible pour Azure
keywords: ansible, rôles, matrice, version, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
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
| azure_rm_availabilityset                   | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_availabilityset_info              | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_deployment                         | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_functionapp                        | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_functionapp_info                  | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_image                              | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_image_info                        | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_resource                           | -            | -                           | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_resource_info                     | -            | -                           | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_resourcegroup                      | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_resourcegroup_info                | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_virtualmachine                     | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachineextension            | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_virtualmachineimage_info          | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescaleset             | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescaleset_info       | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | OUI          | OUI          | OUI          |
| **Mise en réseau**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | OUI          | OUI          | OUI          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | OUI          | OUI          | OUI          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | OUI          | OUI          | OUI          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | OUI          | OUI          | OUI          |
| azure_rm_dnsrecordset                       | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_dnsrecordset_info                 | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_dnszone                            | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_dnszone_info                      | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_loadbalancer                       | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_loadbalancer_info                 | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_networkinterface                   | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_networkinterface_info             | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_publicipaddress                    | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_publicipaddress_info              | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_route                              | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_routetable                         | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_routetable_info                   | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_securitygroup                      | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_subnet                             | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualnetwork                     | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualnetwork_info               | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | OUI          | OUI          | OUI          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | OUI          | OUI          | OUI          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | OUI          | OUI          |
| **Stockage**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_manageddisk_info                  | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_storageaccount                     | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_storageaccount_info               | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_storageblob                        | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_webapp                             | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_webapp_info                       | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | OUI          | OUI          | OUI          |
| **Containers**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | OUI          | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_aks                                | -            | -                           | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_aks_info                          | -            | -                           | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_containerinstance                  | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_containerregistry                  | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_containerregistry_info            | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | OUI          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | OUI          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | OUI          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | OUI          |
| **Bases de données**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mysqldatabase                      | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_mysqlserver                        | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_postgresqldatabase                 | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_postgresqlserver                   | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_sqldatabase                        | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | OUI          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | OUI          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | OUI          | OUI          | OUI          | OUI          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_sqlserver                          | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_sqlserver_info                    | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| **Analyse**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | OUI          | OUI          |
| **Intégration**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| **Sécurité**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | OUI          | OUI          | OUI          |
| azure_rm_keyvaultkey                        | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_keyvaultsecret                     | -            | OUI                         | OUI          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | OUI          | OUI          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_autoscale_info                    | -            | -                         | -          | OUI          | OUI          | OUI          | OUI          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | OUI          | OUI          | OUI          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | OUI          | OUI          |
| **Gestion et gouvernance**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | OUI        | OUI          |
| **Internet des objets**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | OUI        | OUI          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | OUI        | OUI          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduction au rôle de playbook pour Azure

Le [rôle de playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) et inclut tous les derniers modules Azure. Les mises à jour et correctifs de bogues sont effectués de façon plus opportune qu’avec la version Ansible officielle. Si vous utilisez Ansible pour l’approvisionnement de ressources Azure, nous vous invitons à installer le rôle de playbook `azure_preview_module`.

Le rôle de playbook `azure_preview_module` est publié toutes les trois semaines.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les rôles de playbook, consultez [Creating Reusable Playbooks](https://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Création de playbooks réutilisables). 