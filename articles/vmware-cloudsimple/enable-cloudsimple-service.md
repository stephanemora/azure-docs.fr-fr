---
title: Activer la Solution de VMware Azure par service de CloudSimple
description: Décrit comment activer le service CloudSimple sur un abonnement Azure et puis inscrire le fournisseur de ressources CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676937"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Inscrire le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure

Le service CloudSimple vous permet de vous permettent de consommer la Solution de VMware Azure par CloudSimple. Pour utiliser le service CloudSimple, il doit tout d’abord être activé sur votre abonnement Azure. Vous pouvez inscrire le service Microsoft.VMwareCloudSimple en tant que votre fournisseur de ressources.

## <a name="enable-the-cloudsimple-service"></a>Activer le service CloudSimple

Pour activer le service CloudSimple sur votre abonnement Azure, ouvrez une demande de support avec [prise en charge de Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Sélectionnez les options suivantes lorsque vous envoyez la demande.

* Type de problème : **Technique**
* Abonnement : **Votre ID d’abonnement**
* Type de service : **Solution de VMware à CloudSimple**
* Type de problème : **Quota de nœuds dédié**
* Sous-type du problème : **Augmenter le quota de nœuds dédiés**
* Objet : **Activer le service de CloudSimple**

Vous pouvez également contacter votre responsable de compte Microsoft à [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Fournissez votre ID d’abonnement Azure dans l’e-mail.  

Une fois que le service CloudSimple est activé pour votre abonnement, vous pouvez activer le fournisseur de ressources sur l’abonnement.

## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Tous les services**.

3. Recherchez et sélectionnez **abonnements**.

    ![Sélectionner des abonnements](media/cloudsimple-service-select-subscriptions.png)

4. Sélectionnez l’abonnement sur lequel vous souhaitez activer le service de CloudSimple.

5. Cliquez sur **fournisseurs de ressources** pour l’abonnement.

6. Utilisez **Microsoft.VMwareCloudSimple** pour filtrer le fournisseur de ressources.

7. Sélectionnez le fournisseur de ressources, cliquez sur **inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un service CloudSimple](create-cloudsimple-service.md)
* Découvrez comment [configurer un environnement de cloud privé](quickstart-create-private-cloud.md)