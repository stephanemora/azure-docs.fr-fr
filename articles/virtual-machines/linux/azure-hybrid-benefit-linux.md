---
title: Azure Hybrid Benefit et machines virtuelles Linux
description: Azure Hybrid Benefit vous permet de réaliser des économies sur vos machines virtuelles Linux s’exécutant sur Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: alsin
ms.openlocfilehash: da17122de8db41b6ba9ae9597d52bc3e1d8d0062
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962392"
---
# <a name="preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Aperçu : Comment Azure Hybrid Benefit s’applique-t-il aux machines virtuelles Linux ?

## <a name="overview"></a>Vue d’ensemble

Azure Hybrid Benefit vous permet de migrer plus facilement vos machines virtuelles Red Hat Enterprise Linux (RHEL) et SUSE Linux Enterprise Server (SLES) locales vers Azure à l’aide de votre propre abonnement logiciel Red Hat ou SUSE préexistant. Avec cet avantage, vous payez uniquement les coûts d’infrastructure de votre machine virtuelle, car les frais liés aux logiciels sont couverts par votre abonnement RHEL ou SLES. L’avantage s’applique à toutes les images PAYG (paiement à l’accès) de la place de marché RHEL et SLES.

> [!IMPORTANT]
> Azure Hybrid Benefit pour machines virtuelles Linux est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefit-description"></a>Description de l’avantage

Azure Hybrid Benefit vous permet de migrer plus facilement vos serveurs locaux RHEL et SLES vers Azure en convertissant les machines virtuelles PAYG RHEL et SLES existantes sur Azure pour la facturation Bring Your Own Subscription (BYOS). En règle générale, les machines virtuelles déployées à partir d’images PAYG sur Azure génèrent des frais d’infrastructure et des frais de logiciel. Avec Azure Hybrid Benefit, les machines virtuelles PAYG peuvent être converties en modèle de facturation BYOS sans redéploiement, ce qui évite tout risque de temps d’arrêt.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Visualisation des coûts Azure Hybrid Benefit sur les machines virtuelles Linux.":::

Lors de l’activation de l’avantage sur une machine virtuelle RHEL ou SLES, vous ne serez plus facturé pour les frais de logiciel supplémentaires généralement engagés sur une machine virtuelle PAYG. Votre machine virtuelle fera l’objet d’une facturation BYOS, ce qui comprend uniquement les frais de matériel de calcul, et non les frais de logiciel.

Si vous le souhaitez, vous pouvez également convertir une machine virtuelle sur laquelle l’avantage a été activé pour revenir à un modèle de facturation PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Étendue de l’éligibilité Azure Hybrid Benefit pour les machines virtuelles Linux

Azure Hybrid Benefit est disponible pour toutes les images PAYG de la place de marché RHEL et SLES. L’avantage n’est pas encore disponible pour les images BYOS ou personnalisées de la place de marché RHEL ou SLES.

Les instances réservées, les hôtes dédiés et les avantages hybrides SQL ne sont pas éligibles à Azure Hybrid Benefit si vous utilisez déjà l’avantage avec des machines virtuelles Linux.

## <a name="how-to-get-started"></a>Pour commencer

Azure Hybrid Benefit est actuellement en phase de préversion pour les machines virtuelles Linux. Après avoir accédé à la préversion, vous pouvez activer l’avantage à l’aide du portail Azure ou d’Azure CLI.

### <a name="preview"></a>Préversion

Au cours de cette phase, vous pouvez accéder à l’avantage en complétant le formulaire disponible [ici](https://aka.ms/ahb-linux-form). Une fois le formulaire rempli, vos abonnements Azure seront activés pour l’avantage et vous recevrez une confirmation de la part de Microsoft sous trois jours ouvrables.

### <a name="red-hat-customers"></a>Clients Red Hat

1.    Remplissez le formulaire de demande de préversion ci-dessus.
1.    Inscrivez-vous au [programme Red Hat Cloud Access](https://aka.ms/rhel-cloud-access).
1.    Activez vos abonnements Azure pour l’accès au cloud et activez les abonnements contenant les machines virtuelles pour lesquelles vous souhaitez utiliser l’avantage.
1.    Appliquez l’avantage à vos machines virtuelles existantes via le portail Azure ou Azure CLI.
1.    Inscrivez vos machines virtuelles recevant l’avantage avec une source de mises à jour distincte.

### <a name="suse-customers"></a>Clients SUSE

1.    Remplissez le formulaire de demande de préversion ci-dessus.
1.    Inscrivez-vous au programme de cloud public SUSE.
1.    Appliquez l’avantage à vos machines virtuelles existantes via le portail Azure ou Azure CLI.
1.    Inscrivez vos machines virtuelles recevant l’avantage avec une source de mises à jour distincte.

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Activez et désactivez l’avantage dans le portail Azure.

Vous pouvez activer l’avantage sur les machines virtuelles existantes en accédant au panneau **Configuration** et en suivant la procédure qui s’y trouve. Vous pouvez activer l’avantage sur les nouvelles machines virtuelles lors de l’expérience de création de machine virtuelle.

### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Activez et désactivez l’avantage dans Azure CLI.

Vous pouvez utiliser la commande « az vm update » pour mettre à jour les machines virtuelles existantes. Pour les machines virtuelles RHEL, exécutez la commande avec un paramètre --license-type « RHEL_BYOS ». Pour les machines virtuelles SLES, exécutez la commande avec un paramètre --license-type « SLES_BYOS ».

#### <a name="cli-example-to-enable-the-benefit"></a>Exemple de CLI pour activer l’avantage :
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Exemple de CLI pour désactiver l’avantage :
Pour désactiver l’avantage, utilisez une valeur licence-type « None ».
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Exemple CLI pour activer l’avantage sur un grand nombre de machines virtuelles
Pour activer l’avantage sur un grand nombre de machines virtuelles, vous pouvez utiliser le paramètre `--ids` dans Azure CLI.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Les exemples suivants illustrent deux méthodes d’obtention d’une liste d’ID de ressource : l’un au niveau du groupe de ressources, l’autre au niveau de l’abonnement.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Vérifier l’état AHB d’une machine virtuelle
Vous pouvez afficher l’état AHB d’une machine virtuelle de trois façons : en consultant le portail, en utilisant Azure CLI ou en utilisant Azure Instance Metadata Service Azure (Azure IMDS).


### <a name="portal"></a>Portail

Affichez le panneau Configuration et vérifiez l’état de la licence pour savoir si AHB est activé pour votre machine virtuelle.

### <a name="azure-cli"></a>Azure CLI

La commande `az vm get-instance-view` peut être utilisée à cet effet. Recherchez un champ licenseType dans la réponse. Si le champ licenseType existe et que la valeur est « RHEL_BYOS » ou « SLES_BYOS », cela signifie que l’avantage est activé sur votre machine virtuelle.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

À partir de la machine virtuelle, vous pouvez interroger les métadonnées attestées IMDS pour déterminer le licenseType de la machine virtuelle. La valeur licenseType « RHEL_BYOS » ou « SLES_BYOS » indique que l’avantage est activé sur votre machine virtuelle. En savoir plus sur les métadonnées attestées [ici](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Conformité

### <a name="red-hat"></a>Red Hat

Pour utiliser Azure Hybrid Benefit pour vos machines virtuelles RHEL, vous devez d’abord être inscrit au programme Red Hat Cloud Access. Vous pouvez le faire via le site Red Hat Cloud Access. Après avoir activé l’avantage sur votre machine virtuelle, vous devez inscrire la machine virtuelle auprès de votre propre source de mises à jour avec Red Hat Subscription Manager ou Red Hat Satellite. L’inscription aux mises à jour vous permet de vous assurer que vous êtes pris en charge.

### <a name="suse"></a>SUSE

Pour utiliser Azure Hybrid Benefit pour vos machines virtuelles SLES, vous devez d’abord être inscrit au programme de cloud public SUSE. Apprenez-en davantage sur le programme ici. Après avoir acheté des abonnements SUSE, vous devez inscrire vos machines virtuelles à l’aide de ces abonnements auprès de votre propre source de mises à jour à l’aide du Centre de clients SUSE, du serveur d’outil de gestion des abonnements ou du gestionnaire SUSE.

## <a name="frequently-asked-questions"></a>Forum aux questions
*Q : Puis-je utiliser un type de licence « RHEL_BYOS » avec une image SLES ou inversement ?*

A : Non, vous ne pouvez pas. Toute tentative d’entrer un type de licence ne correspondant pas à la distribution en cours d’exécution sur votre machine virtuelle ne met pas à jour les métadonnées de facturation. Toutefois, si vous entrez accidentellement un type de licence incorrect, la mise à jour de votre machine virtuelle vers le type de licence qui convient activera l’avantage.

*Q : Je me suis inscrit au programme Red Hat Cloud Access, mais je ne peux toujours pas activer l’avantage sur mes machines virtuelles RHEL. Que faire ?*

A : L’inscription au programme Red Hat Cloud Access peut mettre un certain temps à se propager entre Red Hat et Azure. Si l’erreur persiste après un jour ouvrable, contactez le support technique de Microsoft.

## <a name="common-errors"></a>Erreurs courantes
Cette section contient une liste d’erreurs courantes et de procédures d’atténuation.

| Error | Limitation des risques |
| ----- | ---------- |
| « L’abonnement n’est pas inscrit pour la préversion Linux d’Azure Hybrid Benefit. Pour obtenir des instructions pas à pas, consultez https://aka.ms/ahb-linux  » | Remplissez le formulaire sur https://aka.ms/ahb-linux-form pour vous inscrire à la préversion Linux d’Azure Hybrid Benefit.
| « Impossible de terminer l’action, car les informations dont nous disposons indique que vous n’avez pas réussi à activer Red Hat Cloud Access sur votre abonnement Azure... » | Pour pouvoir utiliser l’avantage avec des machines virtuelles RHEL, vous devez d’abord inscrire vos abonnements Azure auprès de Red Hat Cloud Access. Visitez ce lien pour en savoir plus sur l’inscription de vos abonnements Azure pour Red Hat Cloud Access

## <a name="next-steps"></a>Étapes suivantes
* Pour utiliser la préversion, remplissez le formulaire disponible [ici](https://aka.ms/ahb-linux-form).