---
title: Foire aux questions concernant Azure Cloud Services (support étendu)
description: Foire aux questions concernant Azure Cloud Services (support étendu)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3338f7b6bd418cea2bfdbbcd40692b9342f48cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743944"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Questions fréquentes (FAQ) sur Azure Cloud Services (support étendu)
Cet article traite des questions fréquemment posées relatives à Azure Cloud Services (support étendu).

## <a name="general"></a>Général

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Quel est le nom de la ressource pour Azure Cloud Services (classique) et Azure Cloud Services (support étendu) ?
- Azure Cloud Services (classique) : `microsoft.classiccompute/domainnames`
- Azure Cloud Services (support étendu) : `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Quels sont les emplacements disponibles pour déployer Azure Cloud Services (support étendu) ?
Azure Cloud Services (support étendu) est disponible dans toutes les régions du cloud public.

### <a name="how-does-my-quota-change"></a>Comment mon quota change-t-il ? 
Les clients devront demander un quota en utilisant les mêmes processus que pour tout autre produit Azure Resource Manager. Dans Azure Resource Manager, le quota est régional, et une demande de quota distincte est nécessaire pour chaque région.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Pourquoi ne puis-je plus voir d’emplacement de production et de préproduction ?
Azure Cloud Services (support étendu) ne prend pas en charge le concept logique d’un service hébergé, qui comprenait deux emplacements (production et préproduction). Chaque déploiement est un déploiement indépendant d’Azure Cloud Services (support étendu). Pour tester et mettre en place une nouvelle version d’un service cloud, déployez un service cloud (support étendu) et marquez-le pour indiquer que son adresse IP virtuelle peut être échangée avec celle d’un autre service cloud (support étendu).

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Pourquoi ne puis-je plus créer de service cloud vide ?
Le concept de noms de services hébergés n’existe plus. Vous ne pouvez donc pas créer d’instance vide d’Azure Cloud Services (support étendu).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Azure Cloud Services (support étendu) prend-il en charge Resource Health Check (RHC) ?
Non, Azure Cloud Services (support étendu) ne prend pas en charge Resource Health Check (RHC).

### <a name="how-are-role-instance-metrics-changing"></a>En quoi les métriques d’instance de rôle changent-elles ?
Il n’y a aucune modification dans les métriques d’instance de rôle. 

### <a name="how-are-web--worker-roles-changing"></a>En quoi les rôles Worker et web changent-ils ?
Il n’y a pas de changement dans la conception, l’architecture ou les composants des rôles Worker et web. 

### <a name="how-are-role-instances-changing"></a>En quoi les instances de rôle changent-elles ?
Il n’y a pas de changement dans la conception, l’architecture ou les composants des instances de rôle. 

### <a name="how-will-guest-os-updates-change"></a>En quoi les mises à jour du système d’exploitation invité seront différentes ?
 Il n’y a aucune modification apportée à la méthode de lancement. Azure Cloud Services (classique) et Azure Cloud Services (support étendu) obtiennent les mêmes mises à jour.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Azure Cloud Services (support étendu) prend-il en charge les états Arrêté (alloué) et Arrêté (désalloué) ?

Le déploiement d’Azure Cloud Services (support étendu) prend uniquement en charge l’état Arrêté (alloué) qui apparaît comme « arrêté » dans le portail Azure. L’état Arrêté (désalloué) n’est pas pris en charge. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Les déploiements d’Azure Cloud Services (support étendu) prennent-ils en charge la mise à l’échelle entre les clusters, les zones de disponibilité et les régions ?
Les déploiements d’Azure Cloud Services (support étendu) ne peuvent pas être mis à l’échelle sur plusieurs clusters, zones de disponibilité et régions. 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Existe-t-il des différences de tarification entre Azure Cloud Services (classique) et Azure Cloud Services (support étendu) ?
Azure Cloud Services (support étendu) utilise Azure Key Vault et des IP publiques du niveau De base (ARM). Les clients ayant besoin de certificats doivent utiliser Azure Key Vault pour la gestion des certificats ([en savoir plus](https://azure.microsoft.com/pricing/details/key-vault/) sur la tarification d’Azure Key Vault).  Chaque IP publique pour Azure Cloud Services (support étendu) est facturée séparément ([en savoir plus](https://azure.microsoft.com/pricing/details/ip-addresses/) sur la tarification des IP publiques.) 
## <a name="resources"></a>Ressources 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Quelles sont les ressources relatives à un déploiement d’Azure Cloud Services (support étendu) qui doivent se trouvent dans le même groupe de ressources ?
Les équilibreurs de charge, les groupes de sécurité réseau et les tables de routage doivent se trouvent dans la même région et le même groupe de ressources. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Quelles sont les ressources relatives à un déploiement d’Azure Cloud Services (support étendu) qui doivent se trouvent dans la même région ?
Key Vault, le réseau virtuel, les IP publiques, les groupes de sécurité réseau et les tables de routage doivent se trouver dans la même région.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Quelles sont les ressources relatives à un déploiement d’Azure Cloud Services (support étendu) qui doivent se trouvent dans le même réseau virtuel ?
Les IP publiques, les équilibreurs de charge, les groupes de sécurité réseau et les tables de routage doivent se trouvent dans le même réseau virtuel. 

## <a name="deployment-files"></a>Fichiers de déploiement 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Comment puis-je utiliser un modèle pour déployer ou gérer mon déploiement ?
Les fichiers de modèle et de paramètres peuvent être transmis comme paramètre en utilisant REST, PowerShell ou l’interface CLI. Ils peuvent également être chargés à l’aide du portail Azure.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Ai-je besoin de gérer quatre fichiers maintenant ? (modèle, paramètre, csdef, cscfg)
Les fichiers de modèle et de paramètres sont utilisés uniquement pour l’automatisation du déploiement. Comme pour Azure Cloud Services (classique), vous pouvez créer manuellement des ressources dépendantes, puis un déploiement d’Azure Cloud Services (support étendu) à l’aide de PowerShell, de commandes CLI ou via le portail avec le fichier .csdef existant, le fichier .cscfg.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Comment mon code d’application est-il modifié sur Azure Cloud Services (support étendu) ?
Aucune modification n’est requise pour votre code d’application empaqueté au format cspkg. Vos applications existantes continueront à fonctionner comme avant. 


## <a name="migration"></a>Migration

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Azure Cloud Services (support étendu) atténue-t-il les échecs dus aux échecs d’allocation ?
Non, les déploiements d’Azure Cloud Services (support étendu) sont liés à un cluster, tout comme Azure Cloud Services (classique). Par conséquent, les échecs d’allocation continuent d’exister si le cluster est plein. 

### <a name="when-do-i-need-to-migrate"></a>Quand dois-je effectuer la migration ? 
L’estimation du temps nécessaire et de la complexité de la migration dépend d’une série de variables. La planification est l’étape la plus efficace pour comprendre l’étendue du travail, les blocages et la complexité de la migration.

## <a name="networking"></a>Réseau

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Pourquoi ne puis-je pas créer de déploiement sans réseau virtuel ?
Les réseaux virtuels sont une ressource obligatoire pour tout déploiement sur Azure Resource Manager. Le déploiement d’Azure Cloud Services (support étendu) doit se trouver dans un réseau virtuel. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Pourquoi vois-je maintenant autant de ressources de mise en réseau ? 
Dans Azure Resource Manager, les composants de votre déploiement d’Azure Cloud Services (support étendu) sont exposés comme ressource pour une meilleure visibilité et un meilleur contrôle. Le même type de ressources était utilisé dans Azure Cloud Services (classique), mais elles étaient simplement masquées. Un exemple de ce type de ressource est l’équilibreur de charge public, qui est maintenant une ressource « en lecture seule » explicite créée automatiquement par la plateforme.

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Quelles restrictions s’appliquent à un sous-réseau avec Azure Cloud Services (support étendu) ?
Un sous-réseau contenant des déploiements d’Azure Cloud Services (support étendu) ne peut pas être partagé avec des déploiements d’autres produits de calcul tels que des Machines Virtuelles, Virtual Machine Scale Sets, Service Fabric, etc.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Quelles sont les méthodes d’allocation d’adresses IP prises en charge sur Azure Cloud Services (support étendu) ?
Azure Cloud Services (support étendu) prend en charge les méthodes d’allocation d’adresses IP dynamiques et statiques. Les adresses IP statiques sont référencées en tant qu’adresses IP réservées dans le fichier cscfg.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Pourquoi les adresses IP me sont-elles facturées ?
Les clients sont facturés pour l’utilisation des adresses IP sur Azure Cloud Services (support étendu) tout comme les utilisateurs sont facturés pour les adresses IP associées aux machines virtuelles. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Puis-je utiliser un nom DNS avec Azure Cloud Services (support étendu) ? 
Oui. Azure Cloud Services (support étendu) peut également recevoir un nom DNS. Avec Azure Resource Manager, l’étiquette DNS est une propriété facultative de l’IP publique qui est attribuée au service cloud. Le format du nom DNS pour les déploiements basés sur Azure Resource Manager est `<userlabel>.<region>.cloudapp.azure.com`.

## <a name="certificates--key-vault"></a>Certificats et coffres de clés

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Pourquoi dois-je gérer mes certificats sur Azure Cloud Services (support étendu) ?
Azure Cloud Services (support étendu) a adopté le même processus que les autres offres de calcul où les certificats résident dans des coffres de clés gérés par le client. Cela permet aux clients d’avoir un contrôle total sur leurs secrets et leurs certificats. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Puis-je utiliser un coffre de clés pour tous mes déploiements dans toutes les régions ?
Non. Azure Key Vault est une ressource régionale et les clients ont besoin d’un coffre de clés dans chaque région. Toutefois, un coffre de clés peut être utilisé pour tous les déploiements dans une région donnée.

## <a name="next-steps"></a>Étapes suivantes
Pour commencer à utiliser Azure Cloud Services (support étendu), consultez [Déployer un service cloud (support étendu) à l’aide de PowerShell](deploy-powershell.md).