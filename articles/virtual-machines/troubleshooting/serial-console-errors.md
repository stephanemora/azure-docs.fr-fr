---
title: Erreurs de la console série Azure | Microsoft Docs
description: Erreurs courantes au sein de la console série Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129469"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Erreurs courantes au sein de la console série Azure
Il existe un ensemble d’erreurs connues au sein de la console série Azure. Voici une liste de ces erreurs et des étapes d’atténuation pour celles-ci.

## <a name="common-errors"></a>Erreurs courantes

Error                            |   Atténuation
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for *&lt;VMNAME&gt;* . To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) ![Erreur de diagnostics de démarrage](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Vérifiez que les [diagnostics de démarrage](boot-diagnostics.md) sont activés sur la machine virtuelle ou le groupe de machines virtuelles identiques. Si vous utilisez la console série sur une instance de groupe de machines virtuelles identiques, assurez-vous que votre instance possède le modèle le plus récent.
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) ![Erreur désallouée](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | La machine virtuelle ou l’instance de groupe de machines virtuelles identiques doit être à l’état Démarré pour accéder à la console série. Démarrez votre machine virtuelle ou votre instance de groupe de machines virtuelles identiques, puis réessayez.
Une réponse « Interdit » s’est produite lors de l’accès au compte de stockage des diagnostics de démarrage de cette machine virtuelle. ![Erreur de pare-feu de compte de stockage](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Assurez-vous que les diagnostics de démarrage n’ont pas un pare-feu de compte. Un compte de stockage des diagnostics de démarrage accessible est nécessaire au fonctionnement de la console série.
You do not have the required permissions to use this VM with the serial console. Ensure you have at least Virtual Machine Contributor role permissions. (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer des autorisations du rôle Contributeur de machine virtuelle au minimum.)| L’accès à la console série nécessite que vous disposiez d’un accès de niveau contributeur ou d’une version supérieure sur votre machine virtuelle ou votre groupe de machines virtuelles identiques. Pour plus d’informations, consultez la [page de présentation](serial-console-overview.md).
Le compte de stockage '' utilisé pour les diagnostics de démarrage sur cette machine virtuelle est introuvable. Vérifiez que les diagnostics de démarrage sont activés pour cette machine virtuelle, que ce compte de stockage n’a pas été supprimé et que vous avez accès à ce compte de stockage. | Vérifiez que vous n’avez pas supprimé le compte de stockage des diagnostics de démarrage pour votre machine virtuelle ou votre groupe de machines virtuelles identiques
L’approvisionnement pour cette machine virtuelle n’a pas encore réussi. Vérifiez que la machine virtuelle est entièrement déployée et recommencez la connexion à la console série. | Il se peut que votre machine virtuelle ou votre groupe de machines virtuelles identiques soit toujours en cours d’approvisionnement. Patientez quelques instants, puis réessayez.
Vous ne disposez pas des autorisations nécessaires pour écrire dans le compte de stockage des diagnostics de démarrage pour cette machine virtuelle. Vérifiez que vous disposez au moins d’autorisations de collaborateur de machine virtuelle sur ''. | L’accès à la console série nécessite un accès de niveau contributeur sur le compte de stockage des diagnostics de démarrage. Pour plus d’informations, consultez la [page de présentation](serial-console-overview.md).
Unable to determine the resource group for the boot diagnostics storage account *&lt;STORAGEACCOUNTNAME&gt;* . Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite un accès de niveau contributeur sur le compte de stockage des diagnostics de démarrage. Pour plus d’informations, consultez la [page de présentation](serial-console-overview.md).
WebSocket est fermé ou n’a pas pu être ouvert. | Vous devrez peut-être ajouter l’accès du pare-feu à `*.console.azure.com`. Une approche plus détaillée mais plus longue consiste à autoriser l’accès du pare-feu aux [plages d’adresses IP du centre de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui changent assez régulièrement.


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [console série Azure pour les machines virtuelles Linux](./serial-console-linux.md)
* En savoir plus sur la [console série Azure pour les machines virtuelles Windows](./serial-console-windows.md)