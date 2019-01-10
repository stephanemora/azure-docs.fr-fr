---
title: Matrice de version et module Ansible pour Azure
description: Matrice de version et module Ansible pour Azure
ms.service: ansible
keywords: ansible, rôles, matrice, version, azure, devops
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: 5265b6f6ebf779c83792ab2569c1b613d11070da
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051524"
---
# <a name="ansible-module-and-version-matrix"></a>Matrice de version et module Ansible

## <a name="ansible-modules-for-azure"></a>Modules Ansible pour Azure
Ansible est fourni avec une série de modules qui peuvent être exécutés directement sur les hôtes distants ou par le biais de playbooks.
Cet article répertorie les modules Ansible pour Azure qui peuvent provisionner des ressources cloud Azure telles qu’une machine virtuelle, une mise en réseau et des services conteneur. Vous pouvez obtenir ces modules par le biais de la version officielle d’Ansible ou des rôles de playbook suivants publiés par Microsoft.

| Module Ansible pour Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | [Rôle Ansible](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Calcul**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_availabilityset_facts              | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_deployment                         | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_resource                           | -            | -                           | Oui          | OUI          | Oui                                 | 
| azure_rm_resource_facts                     | -            | -                           | Oui          | OUI          | Oui                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_virtualmachineimage_facts          | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_resourcegroup                      | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_resourcegroup_facts                | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_virtualmachine                     | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_virtualmachine_extension           | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_virtualmachine_scaleset            | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_image                              |              | Oui                         | OUI          | OUI          | Oui                                 | 
| **Mise en réseau**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_virtualnetwork_facts               | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_subnet                             | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_networkinterface                   | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_networkinterface_facts             | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_publicipaddress                    | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_publicipaddress_facts              | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_dnsrecordset                       | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_dnsrecordset_facts                 | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_dnszone                            | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_dnszone_facts                      | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_loadbalancer                       | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_loadbalancer_facts                 | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Oui                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Oui                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Oui                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_securitygroup                      | Oui          | OUI                         | OUI          | OUI          | Oui                                 |
| azure_rm_route                              | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_routetable                         | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | Oui          | Oui                                 | 
| **Stockage**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_storageaccount_facts               | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_storageblob                        | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_managed_disk                       | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_managed_disk_facts                 | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| **Containers**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | Oui          | OUI          | Oui                                 | 
| azure_rm_aks_facts                          | -            | -                           | Oui          | OUI          | Oui                                 | 
| azure_rm_acs                                | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_containerinstance                  | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | Oui                                 | 
| azure_rm_containerregistry                  | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Oui                                 | 
| **Azure Functions**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| azure_rm_functionapp_facts                  | Oui          | OUI                         | OUI          | OUI          | Oui                                 | 
| **Bases de données**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_sqlserver_facts                    | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_sqldatabase                        | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_mysqlserver                        | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_mysqldatabase                      | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_postgresqlserver                   | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_postgresqldatabase                 | -            | Oui                         | OUI          | OUI          | Oui                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Oui          | Oui                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Oui                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Oui                                 | 
| **Key Vault**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | Oui                         | OUI          | OUI          | Oui                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | Oui                               |
| azure_rm_keyvaultkey                        | -            | Oui                         | OUI          | OUI          | Oui                                 |
| azure_rm_keyvaultsecret                     | -            | Oui                         | OUI          | OUI          | Oui                                 |
| **Web Apps**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | Oui          | Oui                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | Oui          | Oui                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | Oui          | Oui                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | Oui          | Oui                                 | 
| **Traffic Manager**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | Oui          | Oui                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | Oui          | Oui                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | Oui          | Oui                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | Oui          | Oui                                 | 
| **Mise à l’échelle automatique**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | Oui          | Oui                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | Oui          | Oui                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Introduction au rôle de playbook pour Azure
Le [rôle de playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) est le rôle le plus complet et inclut tous les derniers modules Azure. Les mises à jour et correctifs de bogues sont effectués de façon plus opportune qu’avec la version Ansible officielle. Si vous utilisez Ansible pour l’approvisionnement de ressources Azure, nous vous invitons à installer le rôle playbook azure_preview_module.

Le rôle de playbook azure_preview_module est publié toutes les trois semaines.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les rôles de playbook, consultez [Creating Reusable Playbooks](https://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Création de playbooks réutilisables). 
