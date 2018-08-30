---
title: Matrice de version et module Ansible pour Azure
description: Matrice de version et module Ansible pour Azure
ms.service: ansible
keywords: ansible, rôles, matrice, version, azure, devops
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 07/02/2018
ms.topic: article
ms.openlocfilehash: 8bbb2563991b45fe7a20ce243751c1c1a1f75b99
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094905"
---
# <a name="ansible-module-and-version-matrix"></a>Matrice de version et module Ansible

## <a name="ansible-modules-for-azure"></a>Modules Ansible pour Azure
Ansible est fourni avec une série de modules qui peuvent être exécutés directement sur les hôtes distants ou par le biais de playbooks.
Cet article répertorie les modules Ansible pour Azure qui peuvent provisionner des ressources cloud Azure telles qu’une machine virtuelle, une mise en réseau et des services conteneur. Vous pouvez obtenir ces modules par le biais de la version officielle d’Ansible ou des rôles de playbook suivants publiés par Microsoft.

| Module Ansible pour Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 |  Rôle de playbook [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------| 
| **Calcul**                    |           |                          |                          |                                  | 
| azure_rm_availabilityset                    | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_availabilityset_facts              | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_deployment                         | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_resource                           | -            | -                           | Oui          | Oui                                 | 
| azure_rm_resource_facts                     | -            | -                           | Oui          | Oui                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_virtualmachineimage_facts          | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_resourcegroup                      | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_resourcegroup_facts                | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_virtualmachine                     | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_virtualmachine_extension           | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_virtualmachine_scaleset            | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_image                              |              | Oui                         | OUI          | Oui                                 | 
| **Mise en réseau**                    |           |                          |                          |                                  | 
| azure_rm_virtualnetwork                     | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_virtualnetwork_facts               | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_subnet                             | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_networkinterface                   | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_networkinterface_facts             | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_publicipaddress                    | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_publicipaddress_facts              | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_dnsrecordset                       | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_dnsrecordset_facts                 | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_dnszone                            | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_dnszone_facts                      | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_loadbalancer                       | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_loadbalancer_facts                 | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_appgw                              | -            | -                           | -            | Oui                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | Oui                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | Oui                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | Oui                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | Oui                                 |
| azure_rm_securitygroup                      | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | Oui                                 | 
| **Stockage**                    |           |                          |                          |                                  | 
| azure_rm_storageaccount                     | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_storageaccount_facts               | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_storageblob                        | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_managed_disk                       | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_managed_disk_facts                 | Oui          | OUI                         | OUI          | Oui                                 | 
| **Containers**                    |           |                          |                          |                                  | 
| azure_rm_aks                                | -            | -                           | Oui          | Oui                                 | 
| azure_rm_aks_facts                          | -            | -                           | Oui          | Oui                                 | 
| azure_rm_acs                                | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_containerinstance                  | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -            | Oui                                 | 
| azure_rm_containerregistry                  | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Oui                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | Oui                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | Oui                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | Oui                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | Oui                                 | 
| **Azure Functions**                    |           |                          |                          |                                  | 
| azure_rm_functionapp                        | Oui          | OUI                         | OUI          | Oui                                 | 
| azure_rm_functionapp_facts                  | Oui          | OUI                         | OUI          | Oui                                 | 
| **Bases de données**                    |           |                          |                          |                                  | 
| azure_rm_sqlserver                          | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_sqlserver_facts                    | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_sqldatabase                        | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | Oui                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | Oui                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | Oui                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Oui                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | Oui                                 | 
| azure_rm_mysqlserver                        | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Oui                                 | 
| azure_rm_mysqldatabase                      | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Oui                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | Oui                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | Oui                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | Oui                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | Oui                                 | 
| azure_rm_postgresqlserver                   | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Oui                                 | 
| azure_rm_postgresqldatabase                 | -            | Oui                         | OUI          | Oui                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Oui                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | Oui                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | Oui                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | Oui                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | Oui                                 | 
| **Key Vault**                    |           |                          |                          |                                  | 
| azure_rm_keyvault                           | -            | Oui                         | OUI          | Oui                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -            | Oui                                 |
| azure_rm_keyvaultkey                        | -            | Oui                         | OUI          | Oui                                 |
| azure_rm_keyvaultsecret                     | -            | Oui                         | OUI          | Oui                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Introduction au rôle de playbook pour Azure
Le [rôle de playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) est le rôle le plus complet et inclut tous les derniers modules Azure. Les mises à jour et correctifs de bogues sont effectués de façon plus opportune qu’avec la version Ansible officielle. Si vous utilisez Ansible à des fins de provisionnement de ressources Azure, nous vous invitons à installer le rôle azure_preview_module.

Le rôle de playbook azure_preview_module est publié toutes les trois semaines.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les rôles de playbook, consultez [Creating Reusable Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Création de playbooks réutilisables). 
