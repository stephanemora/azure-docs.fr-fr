---
title: FAQ et problèmes connus en lien avec les identités managées pour ressources Azure
description: Problèmes connus en lien avec les identités managées pour ressources Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 2a759aea4288af2e90335b47244408d6a537e24b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295579"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>FAQ et problèmes connus en lien avec les identités managées pour ressources Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Forum Aux Questions (FAQ)

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Les identités managées pour ressources Azure fonctionnent-elles avec Azure Cloud Services ?

Non, il n’existe aucun plan de prise en charge des identités managées pour ressources Azure dans Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Les identités managées pour ressources Azure fonctionnent-elles avec la bibliothèque d’authentification Active Directory (ADAL) et la bibliothèque d’authentification Microsoft (MSAL) ?

Non, les identités managées pour ressources Azure ne sont pas encore intégrées avec les bibliothèques ADAL et MSAL. Pour plus d’informations sur l’acquisition d’un jeton pour des identités managées pour ressources Azure à l’aide du point de terminaison REST, voir le [Guide pratique d’utilisation d’identités managées pour ressources Azure sur une machine virtuelle Azure pour acquérir un jeton d’accès](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Quelle est la limite de sécurité des identités managées pour ressources Azure ?

La limite de sécurité de l’identité est la ressource à laquelle elle est attachée. Par exemple, la limite de sécurité activée pour une machine virtuelle avec des identités managées pour ressources Azure est la machine virtuelle. Tout code s’exécutant sur cette machine virtuelle est en mesure d’appeler les identités managées pour des jetons de point de terminaison et de demande de ressources Azure. L’expérience est similaire à celle d’autres ressources qui prennent en charge les identités managées pour ressources Azure.

### <a name="should-i-use-the-managed-identities-for-azure-resources-vm-imds-endpoint-or-the-vm-extension-endpoint"></a>Dois-je utiliser le point de terminaison Instance Metadata Service (IMDS) de machine virtuelle ou le point de terminaison d’extension de machine virtuelle des identités managées pour ressources Azure ?

Lorsque vous utilisez des identités managées pour ressources Azure avec des machines virtuelles, nous vous encourageons à utiliser le point de terminaison IMDS des identités managées pour ressources Azure. Azure Instance Metadata Service (IMDS) est un point de terminaison REST accessible à toutes les machines virtuelles IaaS créées par le biais d’Azure Resource Manager. Voici quelques avantages liés à l’utilisation d’identités managées pour ressources Azure sur IMDS :

1. Tous les systèmes d’exploitation pris en charge par Azure IaaS peuvent utiliser des identités managées pour ressources Azure sur IMDS. 
2. N’est plus nécessaire d’installer une extension sur votre machine virtuelle pour activer les identités managées pour ressources Azure. 
3. Les certificats utilisés par les identités managées pour ressources Azure ne sont plus présents dans la machine virtuelle. 
4. Le point de terminaison IMDS est une adresse IP non routable bien connue, uniquement accessible à partir de la machine virtuelle. 

L’extension de machine virtuelle des identités managées pour ressources Azure est toujours disponible et utilisable aujourd’hui. Toutefois, à l’avenir, nous utiliserons le point de terminaison IMDS par défaut. L’extension de machine virtuelle des identités managées pour ressources Azure sera abandonnée en janvier 2019. 

Pour plus d’informations sur Azure Instance Metadata Service, voir la [documentation d’IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="what-are-the-supported-linux-distributions"></a>Quelles sont les distributions de Linux prises en charge ?

Toutes les distributions Linux prises en charge par Azure IaaS peuvent être utilisées avec des identités managées pour ressources Azure via le point de terminaison IMDS. 

Remarque : l’extension de machine virtuelle des identités managées pour ressources Azure (dont l’abandon est prévu en janvier 2019) ne prend en charge que les distributions Linux suivantes :
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Les autres distributions de Linux ne sont actuellement pas prises en charge, et l’extension peut échouer sur les distributions non prises en charge.

L’extension fonctionne sur CentOS 6.9. Toutefois, en raison de l’absence de prise en charge système dans 6.9, l’extension ne redémarre pas automatiquement en cas d’incident ou d’arrêt. Elle redémarre au redémarrage de la machine virtuelle. Pour redémarrer l’extension manuellement, voir [Comment redémarrer l’extension des identités managées pour ressources Azure ?](#how-do-you-restart-the-managed-identities-for-Azure-resources-extension)

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Comment redémarrer l’extension des identités managées pour ressources Azure ?
Sur Windows et certaines versions de Linux, si l’extension s’arrête, la cmdlet suivante peut être utilisée pour redémarrer manuellement l’extension :

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Où : 
- Le type et le nom de l’extension pour Windows est : ManagedIdentityExtensionForWindows
- Le type et le nom de l’extension pour Linux est : ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problèmes connus

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Le « Script d’automatisation » échoue lors de la tentative d’exportation de schéma pour l’extension des entités managées pour ressources Azure

Lorsque des identités managées pour ressources Azure sont activées sur une machine virtuelle, l’erreur suivante s’affiche en cas de tentative d’utiliser la fonctionnalité « Script d’automatisation » pour la machine virtuelle ou son groupe de ressources :

![Erreur d’exportation de script d’automatisation d’identités managées pour ressources Azure](./media/msi-known-issues/automation-script-export-error.png)

L’extension de machine virtuelle des identités managées pour ressources Azure (dont l’abandon est prévu en janvier 2019) ne prend pas en charge actuellement la possibilité d’exporter son schéma vers un modèle de groupe de ressources. Par conséquent, le modèle généré n’affiche pas les paramètres de configuration permettant d’activer des identités managées pour ressources Azure sur la ressource. Ces sections peuvent être ajoutées manuellement en suivant les exemples fournis dans [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant un modèle](qs-configure-template-windows-vm.md).

Lorsque la fonctionnalité d’exportation de schéma sera disponible pour l’extension de machine virtuelle des identités managées pour ressources Azure (dont l’abandon est prévu en janvier 2019), elle sera répertoriée dans [Exportation de groupes de ressources contenant des extensions de machine virtuelle](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Le panneau de configuration n’apparaît pas dans le portail Azure

Si le panneau Configuration de la machine virtuelle n’apparaît pas sur votre machine virtuelle, cela signifie que les identités managées pour ressources Azure n’ont pas encore été activées sur le portail de votre région.  Revérifiez ultérieurement.  Vous pouvez également activer des identités managées pour ressources Azure pour votre machine virtuelle en utilisant [PowerShell](qs-configure-powershell-windows-vm.md) ou [Azure CLI](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Impossible d’attribuer un accès aux machines virtuelles dans le panneau de contrôle d’accès (IAM)

Si **Machine virtuelle** n’apparaît pas sur le portail Azure en tant que choix pour **Attribuer un accès** dans **Contrôle d’accès (IAM)** > **Ajouter autorisations**, cela signifie que les identités managées pour ressources Azure n’ont pas été activées sur le portail de votre région. Revérifiez ultérieurement.  Vous pouvez toujours sélectionner l’identité de la machine virtuelle pour l’attribution de rôle en recherchant le principal du service des identités managées pour ressources Azure.  Entrez le nom de la machine virtuelle dans le champ **Sélectionner**. Le principal de service s’affiche dans le résultat de la recherche.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La machine virtuelle ne démarre pas après avoir été déplacée d’un groupe de ressources ou d’un abonnement

Si vous déplacez une machine virtuelle en cours d’exécution, elle continue de s’exécuter pendant le déplacement. Toutefois, si la machine virtuelle est arrêtée et redémarrée après le déplacement, elle ne démarre plus. Ce problème se produit parce que la machine virtuelle ne met pas à jour la référence à l’identité des identités managées pour ressources Azure, mais continue de pointer dessus dans l’ancien groupe de ressources.

**Solution de contournement** 
 
Déclenchez une mise à jour sur la machine virtuelle afin qu’elle obtienne les valeurs correctes pour les identités managées pour ressources Azure. Vous pouvez modifier les propriétés d’une machine virtuelle pour mettre à jour la référence à l’identité des identités managées pour ressources Azure. Par exemple, vous pouvez définir une nouvelle valeur de balise sur la machine virtuelle avec la commande suivante :

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Cette commande définit une nouvelle balise « fixVM » avec une valeur de 1 sur la machine virtuelle. 
 
En définissant cette propriété, la machine virtuelle se met à jour avec l’URI de ressource appropriée des identités managées pour ressources Azure. Vous devriez ensuite être en mesure de démarrer la machine virtuelle. 
 
Une fois que la machine virtuelle est démarrée, la balise peut être supprimée en utilisant la commande suivante :

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Problèmes connus en lien avec les identités managées affectées par l’utilisateur

- Les attributions d’identités managées affectées par l’utilisateur sont disponibles uniquement pour les machines virtuelles et les groupes de machines virtuelles identiques (VMSS). IMPORTANT : les attributions d’identités managées affectées par l’utilisateur seront modifiées dans les prochains mois.
- La duplication d’identités managées affectées par l’utilisateur sur une même machine virtuelle ou un même groupe de machines virtuelles identiques entraînera une défaillance de ceux-ci. Cela inclut les identités qui sont ajoutées avec une casse différente. par ex. MyUserAssignedIdentity et myuserassignedidentity. 
- L’approvisionnement de l’extension de machine virtuelle (dont l’abandon est prévu en janvier 2019) sur une machine virtuelle peut échouer en raison d’échecs de recherche DNS. Redémarrez la machine virtuelle, puis réessayez. 
- L’ajout d’une identité affectée par l’utilisateur « inexistante » entraînera une défaillance de la machine virtuelle. 
- La création d’une identité affectée par l’utilisateur avec des caractères spéciaux (par exemple, traits de soulignement) dans le nom n’est pas prise en charge.
- Les noms d’identités managées affectées par l’utilisateur sont limités à 24 caractères pour le scénario de bout en bout. Les identités managées affectées par l’utilisateur dont le nom comptera plus de 24 caractères ne pourront pas être attribuées.
- Si vous utilisez l’extension de machine virtuelle d’identité managée (dont l’abandon est prévu en janvier 2019), la limite prise en charge est de 32 identités managées affectées par l’utilisateur. Sans l’extension de machine virtuelle d’identité managée, la prise en charge est limitée à 512 identités managées.  
- Lors de l’ajout d’une deuxième identité affectée par l’utilisateur, il se peut que l’ID de client ne soit pas disponible pour des jetons de demandes pour l’extension de machine virtuelle. Pour résoudre ce problème, redémarrez l’extension de machine virtuelle des identités managées pour ressources Azure avec les deux commandes bash suivantes :
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Quand une machine virtuelle dispose d’une identité affectée par l’utilisateur, mais d’aucune identité affectée par le système, l’interface utilisateur du portail indique que les identités managées pour ressources Azure sont désactivées. Pour activer l’identité affectée par le système, utilisez un modèle Azure Resource Manager, une interface de ligne de commande Azure ou un Kit de développement logiciel (SDK).
