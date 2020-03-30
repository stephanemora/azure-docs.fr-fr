---
title: Créer des ressources techniques pour une offre de machine virtuelle de la Place de marché Microsoft Azure
description: Explique comment créer les ressources techniques d’une offre de machine virtuelle dans la Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: 57f56a341cfc3db6a5f0664503809e6ab6cf3d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278022"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Créer des ressources techniques pour une offre de machine virtuelle

Cette section vous accompagne dans la création et la configuration des ressources techniques d’une offre de machine virtuelle de la Place de marché Microsoft Azure.  Une machine virtuelle héberge deux composants : le disque dur virtuel de la solution et les éventuels disques de données associés.  

- Les *disques durs virtuels*, contenant le système d’exploitation et votre solution, que vous allez déployer dans votre offre de la Place de marché Microsoft Azure. Le processus de préparation du disque dur virtuel est différent en fonction du type de machine virtuelle (Linux, Windows ou personnalisée).
- Les *disques de données* représentent le stockage persistant et dédié d’une machine virtuelle. N’utilisez *pas* le disque dur virtuel de la solution (par exemple le lecteur `C:`) pour stocker des informations persistantes.

Une image de machine virtuelle contient un disque de système d’exploitation et aucun ou plusieurs disques de données. Chaque disque a un disque dur virtuel est nécessaire par disque. Même les disques de données vides ont besoin de la création d’un disque dur virtuel.
Vous devez configurer le système d’exploitation de la machine virtuelle, la taille de la machine virtuelle, les ports à ouvrir et jusqu’à 15 disques de données attachés.

> [!TIP] 
> Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données requis par la référence SKU. Les clients ne suppriment pas les disques qui font partie d’une image lors du déploiement, mais peuvent toujours ajouter des disques pendant ou après le déploiement. 

> [!IMPORTANT]
> *Ne modifiez pas le nombre de disques dans une nouvelle version de l’image.* Si vous devez reconfigurer les disques de données dans l’image, définissez une nouvelle référence SKU. La publication d’une nouvelle version de l’image avec un nombre de disques différent peut potentiellement arrêter le nouveau déploiement basé sur la nouvelle version de l’image en cas de mise à l’échelle automatique, de déploiements automatiques de solutions via des modèles Azure Resource Manager et autres scénarios.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Connaissances techniques fondamentales

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre. En plus de votre domaine de solution, votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes : 
-   Connaissances de base des [services Azure](https://azure.microsoft.com/services/) 
-   Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
-   Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking)
-   Expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Expérience de travail avec [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Outils suggérés 

Choisissez un ou plusieurs des environnements de scripts suivants pour faciliter la gestion des disques durs virtuels et des machines virtuelles :
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

En outre, nous vous recommandons d’ajouter les outils suivants à votre environnement de développement : 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Nous vous suggérons également d’examiner les outils disponibles sur la page [Outils de développement Azure](https://azure.microsoft.com/tools/) et, si vous utilisez Visual Studio, sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Étapes suivantes

Les articles suivants présentés dans cette section vous guident tout au long des étapes de création et d’inscription de ces ressources de machine virtuelle :

1. L’article [Create an Azure-compatible VHD](./cpp-create-vhd.md) (Créer un disque dur virtuel compatible avec Azure) explique comment créer un disque dur virtuel Windows ou Linux compatible avec Azure.  Il inclut des meilleures pratiques, comme le dimensionnement, l’application de correctifs et la préparation de la machine virtuelle pour le chargement.

2. L’article [Connect to your Azure-based virtual machine](./cpp-connect-vm.md) (Se connecter à votre machine virtuelle Azure) explique comment se connecter à distance à votre machine virtuelle nouvellement créée et comment vous identifier.  Cet article explique également comment arrêter la machine virtuelle pour réduire les coûts d’utilisation.

3. L’article [Configure the Azure-hosted VM](./cpp-configure-vm.md) (Configurer la machine virtuelle hébergée sur Azure) explique comment choisir la bonne taille de disque dur virtuel, généraliser votre image, appliquer des mises à jour récentes (correctifs) et planifier des configurations personnalisées.

4. L’article [Deploy a VM from your VHDs](./cpp-deploy-vm-vhd.md) (Déployer une machine virtuelle à partir de vos disques durs virtuels) explique comment inscrire une machine virtuelle auprès d’un disque dur virtuel déployé Azure.  Il répertorie les outils nécessaires et comment les utiliser pour créer une image de machine virtuelle d’utilisateur, puis la déployer vers Azure à l’aide du [Portail Microsoft Azure](https://ms.portal.azure.com/) ou des scripts PowerShell. 

5. L’article [Certify your VM image](./cpp-certify-vm.md) (Certifier votre image de machine virtuelle) explique comment tester et envoyer une image de machine virtuelle pour la certification de la Place de marché Microsoft Azure. Il explique comment récupérer l’outil *Certification Test Tool for Azure Certified* et comment l’utiliser pour certifier votre image de machine virtuelle. 

6. L’article [Get shared access signature URI for your VM image](./cpp-get-sas-uri.md) (Obtenir l’URI de la signature d’accès partagé pour votre image de machine virtuelle) vous explique comment récupérer l’URI de la signature d’accès partagé de vos images de machine virtuelle.
 
L’article de support [Common SAS URL issues and fixes](./cpp-common-sas-url-issues.md) (Problèmes et solutions d’URL SAP courants) répertorie certains des problèmes courants liés à l’utilisation des URI SAP, ainsi que les solutions possibles associées.

Après avoir effectué toutes ces étapes, vous êtes prêt à [publier votre offre de machine virtuelle](./cpp-publish-offer.md) sur la Place de marché Microsoft Azure.
