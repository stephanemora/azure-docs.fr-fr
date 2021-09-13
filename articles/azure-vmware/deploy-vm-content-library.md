---
title: Créer une bibliothèque de contenu pour déployer des machines virtuelles dans Azure VMware Solution
description: Créez une bibliothèque de contenu pour déployer une machine virtuelle dans un cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 06/28/2021
ms.openlocfilehash: ed98d48037df6cfb50c3c94bb6a7187097f5b0e9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563875"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Créer une bibliothèque de contenu pour déployer des machines virtuelles dans Azure VMware Solution

Une bibliothèque de contenu stocke et gère du contenu sous la forme d’éléments de bibliothèque. Un élément de bibliothèque unique se compose de fichiers que vous utilisez pour déployer des machines virtuelles.

Dans cet article, vous allez créer une bibliothèque de contenu dans le client vSphere, puis déployer une machine virtuelle à l’aide d’une image ISO à partir de la bibliothèque de contenu.

## <a name="prerequisites"></a>Configuration requise

Pour suivre ce tutoriel, un segment NSX-T et un service DHCP managé.  Pour plus d’informations, consultez [Configurer le protocole DHCP dans Azure VMware Solution](configure-dhcp-azure-vmware-solution.md).  

## <a name="create-a-content-library"></a>Créer une bibliothèque de contenu

1. Dans le client vSphere local, sélectionnez **Menu** > **Content Libraries**.

   :::image type="content" source="media/content-library/vsphere-menu-content-libraries.png" alt-text="Capture d’écran montrant l’option Content Libraries dans le client vSphere.":::

1. Sélectionnez **Add** pour créer une bibliothèque de contenu.

   :::image type="content" source="media/content-library/create-new-content-library.png" alt-text="Capture d’écran montrant comment créer une bibliothèque de contenu dans vSphere.":::

1. Spécifiez un nom, vérifiez l’adresse IP du serveur vCenter, puis sélectionnez **Next**.

   :::image type="content" source="media/content-library/new-content-library-step-1.png" alt-text="Capture d’écran montrant le nom et l’IP du serveur vCenter pour la nouvelle bibliothèque de contenu.":::

1. Sélectionnez la **Bibliothèque de contenu locale**, puis **Suivant**.

   :::image type="content" source="media/content-library/new-content-library-step-2.png" alt-text="Capture d’écran montrant l’option Local content library sélectionnée pour la nouvelle bibliothèque de contenu.":::

1. Sélectionnez le magasin de données qui stockera votre bibliothèque de contenu, puis sélectionnez **Next**.

   :::image type="content" source="media/content-library/new-content-library-step-3.png" alt-text="Capture d’écran montrant l’emplacement de stockage vsanDatastore sélectionné.":::

1. Vérifiez les paramètres de la bibliothèque de contenu, puis sélectionnez **Finish**.

   :::image type="content" source="media/content-library/new-content-library-step-4.png" alt-text="Capture d’écran montrant les paramètres de la nouvelle bibliothèque de contenu.":::

## <a name="upload-an-iso-image-to-the-content-library"></a>Charger une image ISO dans la bibliothèque de contenu

Maintenant que vous avez créé la bibliothèque de contenu, vous pouvez ajouter une image ISO pour déployer une machine virtuelle sur un cluster de cloud privé. 

1. Dans le client vSphere, sélectionnez **Menu** > **Content Libraries**.

1. Cliquez avec le bouton droit sur la bibliothèque de contenu que vous voulez utiliser pour la nouvelle image ISO, puis sélectionnez **Importer un élément**.

1. Importez un élément de bibliothèque pour la source en procédant de l’une des façons suivantes, puis sélectionnez **Importer** :
   1. Sélectionnez « URL », puis fournissez une URL pour télécharger une image ISO.

   1. Sélectionnez **Fichier local** pour effectuer le chargement à partir de votre système local.

   > [!TIP]
   > Si vous le souhaitez, vous pouvez définir un nom et des notes d’élément personnalisés pour la Destination.

1. Ouvrez la bibliothèque, puis sélectionnez l’onglet **Autres types** pour vérifier que votre image ISO a été correctement chargée.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Déployer une machine virtuelle sur un cluster de cloud privé

1. Dans le client vSphere, sélectionnez **Menu** > **Hosts and Clusters**.

1. Dans le volet gauche, développez l’arborescence, puis sélectionnez un cluster.

1. Sélectionnez **Actions** > **New Virtual Machine**.

1. Suivez les étapes de l’Assistant, puis modifiez les paramètres de votre choix.

1. Sélectionnez **New CD/DVD Drive** > **Client Device** > **Content Library ISO File**.

1. Sélectionnez le fichier ISO chargé à la section précédente, puis sélectionnez **OK**.

1. Cochez la case **Se connecter** afin que l’image ISO soit montée au moment de la mise sous tension.

1. Sélectionnez **New Network** > **Select dropdown** > **Browse**.

1. Sélectionnez le **commutateur logique (segment)** , puis sélectionnez **OK**.

1. Modifiez tout autre paramètre matériel, puis sélectionnez **Suivant**.

1. Vérifiez les paramètres, puis sélectionnez **Terminer**.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé une bibliothèque de contenu pour déployer des machines virtuelles dans Azure VMware Solution, vous souhaitez peut-être en savoir plus sur les sujets suivants :

- [Migration de charges de travail de machine virtuelle vers votre cloud privé](configure-vmware-hcx.md)
- [Intégration des services natifs Azure dans Azure VMware Solution](integrate-azure-native-services.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
