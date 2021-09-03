---
title: Modifications après la migration des Services Cloud Azure (prise en charge étendue)
description: Vue d’ensemble des modifications après la migration vers les Services cloud (prise en charge étendue)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: df9a41b6832c9ca93aa7078b0d06082f1441a5e5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439085"
---
# <a name="post-migration-changes"></a>Changements après la migration
Le déploiement de services cloud (classiques) est converti en déploiement de services cloud (support étendu). Pour plus d’informations, consultez [Documentation sur les Services cloud (support étendu)](deploy-prerequisite.md).  

## <a name="changes-to-deployment-files"></a>Modifications apportées aux fichiers de déploiement 

Des modifications mineures sont apportées au fichier .csdef et .cscfg du client pour que les fichiers de déploiement soient conformes aux exigences d’Azure Resource Manager et des services cloud (support étendu). Après la migration, vos nouveaux fichiers de déploiement sont récupérés ou les fichiers existants sont mis à jour. Cela est nécessaire pour les opérations de mise à jour/suppression.  

- Le réseau virtuel utilise l’ID de ressource complet Azure Resource Manager au lieu du nom de ressource dans la section NetworkConfiguration du fichier .cscfg. Par exemple : `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. Pour les réseaux virtuels appartenant au même groupe de ressources que le service cloud, vous pouvez choisir de mettre à jour le fichier .cscfg à l’aide du nom de réseau virtuel uniquement.  

- Les tailles classiques comme Small, Large, ExtraLarge sont remplacées par leurs nouveaux noms de taille, Standard_A*. Les noms de taille doivent être remplacés par leurs nouveaux noms dans le fichier .csdef. Pour plus d’informations, consultez [Prérequis pour le déploiement de Cloud Services (support étendu)](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Utilisez l’API Get pour récupérer la copie la plus récente des fichiers de déploiement. 
    - Récupérez le modèle à l’aide du [portail](../azure-resource-manager/templates/export-template-portal.md), de [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), de l’[interface CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) et de l’[API REST](/rest/api/resources/resourcegroups/exporttemplate) 
    - Récupérez le fichier .csdef à l’aide de [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) ou de l’[API REST](/rest/api/compute/cloudservices/rest-get-package). 
    - Récupérez le fichier .cscfg à l’aide de [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) ou de l’[API REST](/rest/api/compute/cloudservices/rest-get-package). 
    
 

## <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Modifications apportées à l’automatisation, au pipeline CI/CD, aux scripts personnalisés, aux tableaux de bord personnalisés, aux outils personnalisés, etc. du client.  

Les clients doivent mettre à jour leurs outils et leur automatisation pour commencer à utiliser les nouvelles API/commandes afin de gérer leur déploiement. Le client peut facilement adopter de nouvelles fonctionnalités et les fonctionnalités d’Azure Resource Manager/Cloud Services (support étendu) dans le cadre de cette modification. 

- Modifications apportées aux noms de ressources et de groupes de ressources après la migration
    - Dans le cadre de la migration, les noms de quelques ressources comme le service cloud, les adresses IP publiques, etc. changent. Ces modifications devront peut-être être reflétées dans les fichiers de déploiement avant la mise à jour du service cloud. [En savoir plus sur les noms des ressources qui changent](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Recréer les règles et les stratégies nécessaires pour gérer et mettre à l’échelle les services cloud 
    - Les [règles de mise à l’échelle automatique](configure-scaling.md) ne sont pas migrées. Après la migration, recréez les règles de mise à l’échelle automatique.  
    - Les [alertes](enable-alerts.md) ne sont pas migrées. Après la migration, recréez les alertes.
    - Le coffre de clés est créé sans stratégie d’accès. [Créez les stratégies appropriées](../key-vault/general/assign-access-policy-portal.md) sur le coffre de clés pour afficher ou gérer vos certificats. Les certificats sont visibles sous les paramètres sous l’onglet Secrets.


## <a name="changes-to-certificate-management-post-migration"></a>Modifications apportées à la gestion des certificats après la migration 

Pour gérer vos certificats, il est recommandé d’ajouter tous les fichiers de certificat .pfx valides au magasin de certificats dans Key Vault. La mise à jour fonctionne parfaitement avec n’importe quel portail client, PowerShell ou API REST.

Actuellement, le Portail Azure effectue une validation pour vérifier si tous les certificats requis sont chargés dans le magasin de certificats dans Key Vault et avertit si aucun certificat n’est trouvé. Toutefois, si vous envisagez d’utiliser des certificats comme des secrets, ceux-ci ne peuvent pas être validés pour leur empreinte et toute opération de mise à jour impliquant l’ajout de secrets échoue via le portail. Il est recommandé aux clients d’utiliser PowerShell ou RestAPI pour continuer les mises à jour impliquant des secrets.


## <a name="changes-for-update-via-visual-studio"></a>Modifications pour la mise à jour via Visual Studio
Si vous publiez des mises à jour via Visual Studio directement, vous devez d’abord télécharger le dernier fichier CSCFG à partir de votre déploiement après la migration. Utilisez ce fichier comme référence pour ajouter les détails de Configuration réseau à votre fichier CSCFG actuel dans leprojet Visual Studio. Générez ensuite la solution et publiez-la. Vous devrez peut-être choisir le coffre de clés et le groupe de ressources pour cette mise à jour.


## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrer vers Cloud Services (support étendu) à l’aide du [portail Azure](in-place-migration-portal.md)
- Migrer vers Cloud Services (support étendu) à l’aide de [PowerShell](in-place-migration-powershell.md)
