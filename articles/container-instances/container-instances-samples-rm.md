---
title: Exemples de modèles Azure Resource Manager - Azure Container Instances
description: Exemples de modèles Azure Resource Manager pour Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259539"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modèles Azure Resource Manager pour Azure Container Instances

Les exemples de modèles suivants déploient des instances de conteneurs avec différentes configurations.

Pour plus d’options de déploiement, consultez la section [Déploiement](#deployment). Si vous souhaitez créer vos propres modèles, consultez la [documentation de référence sur les modèles Resource Manager][ref] d’Azure Container Instances, qui présente en détail le format des modèles et les propriétés disponibles.

## <a name="sample-templates"></a>Exemples de modèles

| | |
|-|-|
| **Applications** ||
| [Wordpress][app-wp] | Crée un site web WordPress et sa base de données MySQL dans une instance de conteneur. Le contenu du site WordPress et de la base de données MySQL est conservé sur un partage Azure Files. |
| [MS NAV avec SQL Server et IIS][app-nav] | Déploie un conteneur Windows avec un environnement Dynamics NAV/Dynamics 365 Business Central autonome, riche en fonctionnalités. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Déploie deux conteneurs Linux qui partagent un volume emptyDir. |
| [gitRepo][vol-gitrepo] | Déploie un conteneur Linux qui clone un dépôt GitHub et le monte comme un volume. |
| [secret][vol-secret] | Déploie un conteneur Linux avec un certificat PFX monté comme un volume de secret. |
| **Mise en réseau** ||
| [Conteneur avec exposition UDP][net-udp] | Déploie un conteneur Windows ou Linux qui expose un port UDP. |
| [Conteneur Linux avec adresse IP publique][net-publicip] | Déploie un conteneur Linux accessible via une adresse IP publique. |
| **Ressources Azure** ||
| [Créer un compte de stockage Azure et un partage de fichiers][az-files] | Utilise l’interface de ligne de commande Azure dans une instance de conteneur pour créer un compte de stockage et un partage Azure Files.

## <a name="deployment"></a>Déploiement

Vous disposez de plusieurs options pour déployer des ressources à l’aide de modèles Resource Manager :

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portail Azure][deploy-portal]

[API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups