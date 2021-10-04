---
title: Utiliser l’Explorateur Stockage Azure pour gérer des disques managés Azure
description: Découvrez comment charger, télécharger et migrer un disque managé Azure d’une région à une autre, et créer une capture instantanée d’un disque managé, à l’aide de l’Explorateur Stockage Azure.
author: roygara
ms.author: rogarana
ms.date: 09/07/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 84b2ea53ebb0b6102edf5bc501e0e1b9b6f21726
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124781724"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Utiliser l’Explorateur Stockage Azure pour gérer des disques managés Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

L’application Explorateur Stockage Azure est dotée de nombreuses fonctionnalités vous permettant de :

- charger, télécharger et copier des disques managés ;
- créer des captures instantanées à partir d’un disque dur virtuel (VHD) de système d’exploitation ou de disque de données ;
- migrer des données de l’environnement local vers Azure ;
- ou migrer des données entre des régions Azure.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- un abonnement à Azure ;
- au moins un disque managé Azure ;
- et la version la plus récente de l’application [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="connect-to-an-azure-subscription"></a>Connexion à un abonnement Azure

Si votre application Explorateur Stockage n’est pas connectée à Azure, vous ne pourrez pas l’utiliser pour manager vos ressources. Pour connecter l’application Explorateur Stockage à votre compte Azure, suivez la procédure de cette section. Ensuite, vous pourrez utiliser l’application pour manager vos disques.

1. Ouvrez l’application Explorateur Stockage Azure, puis, dans la barre d’outils, sélectionnez l’icône **Connecter**.

    [![Capture d’écran montrant l’icône Connecter de l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-lrg.png#lightbox)

1. Dans la boîte de dialogue **Se connecter à Stockage Azure**, sélectionnez **Abonnement**.

    [![Capture d’écran indiquant l’emplacement de l’option Abonnement dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-lrg.png#lightbox)

1. Sélectionnez l’environnement adéquat, puis **Suivant**. Vous pouvez également sélectionner **Gérer les environnements personnalisés** pour configurer et ajouter un environnement personnalisé.

    [![Capture d’écran indiquant l’emplacement de l’option de sélection de l’environnement Azure dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-lrg.png#lightbox)

1. Dans la boîte de dialogue **Connexion**, entrez vos informations d’identification Azure.

    ![Capture d’écran montrant la boîte de dialogue Connexion.](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Sélectionnez votre abonnement dans la liste, puis **Ouvrir l'Explorateur**.

    [![Capture d’écran indiquant l’emplacement du bouton Ouvrir l'Explorateur dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-lrg.png#lightbox)

## <a name="upload-an-on-premises-vhd"></a>Charger un disque dur virtuel (VDH) local

Vous pouvez charger un fichier de disque dur virtuel (VHD) local dans Azure et l’utiliser pour créer une image. Pour charger votre fichier source, suivez les étapes de cette section.

1. Dans le volet **Explorateur**, développez **Disques**, puis sélectionnez le groupe de ressources dans lequel vous souhaitez charger votre disque.

    [![Capture d’écran montrant l’emplacement du nœud Disques dans l’application Explorateur Stockage Azure. Ce nœud permet de charger un disque.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. Dans le volet de détails du groupe de ressources, sélectionnez **Charger**.

    [![Capture d’écran indiquant l’emplacement du bouton Charger dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-lrg.png#lightbox)

1. Dans la boîte de dialogue **Charger le VHD**, spécifiez le fichier source VHD, le nom du disque, le type de système d’exploitation, la région vers laquelle vous souhaitez charger le disque et le type de compte. Si la région prend en charge les zones de disponibilité, vous pouvez sélectionner une zone de votre choix. Sélectionnez **Créer** pour commencer le chargement de votre disque.

    [![Capture d’écran de la boîte de dialogue Charger le VHD de l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-lrg.png#lightbox)

1. L’état du chargement s’affiche à présent dans **Activités**.

    [![Capture d’écran indiquant l’emplacement du volet Activités dans l’application Explorateur Stockage Azure. Ce volet contient les messages d’état de chargement.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-lrg.png#lightbox)

Si le chargement est terminé et que vous ne voyez pas le disque dans le volet **Activités**, sélectionnez **Actualiser**.

## <a name="download-a-managed-disk"></a>Télécharger un disque managé

Pour télécharger un disque managé sur un disque dur virtuel (VHD) local, suivez les étapes de cette section. Vous pouvez uniquement télécharger les disques dont l’état est **Non attaché**.

1. Dans le volet **Explorateur**, développez **Disques**, puis sélectionnez le groupe de ressources depuis lequel vous souhaitez télécharger votre disque.

    [![Capture d’écran montrant l’emplacement du nœud Disques dans l’application Explorateur Stockage Azure. Ce nœud permet de télécharger un disque.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. Dans le volet de détails du groupe de ressources, sélectionnez le disque que vous souhaitez télécharger.
1. Sélectionnez **Télécharger** puis choisissez l’emplacement où enregistrer le disque.

    [![Capture d’écran indiquant l’emplacement du bouton Télécharger dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-lrg.png#lightbox)

1. Pour lancer le téléchargement, sélectionnez **Enregistrer**. L’état du téléchargement s’affiche dans **Activités**.

    [![Capture d’écran indiquant l’emplacement du volet Activités dans l’application Explorateur Stockage Azure. Ce volet contient les messages d’état de téléchargement.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-lrg.png#lightbox)

## <a name="copy-a-managed-disk"></a>Copier un disque managé

Avec Explorateur Stockage, vous pouvez copier un disque managé dans ou entre différentes régions. Pour copier un disque :

1. Dans le volet **Explorateur**, déroulez la liste **Disques**, puis sélectionnez le groupe de ressources contenant le disque que vous souhaitez copier.

    [![Capture d’écran montrant l’emplacement du nœud Disques dans l’application Explorateur Stockage Azure. Ce nœud permet de copier un disque.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. Dans le volet de détails du groupe de ressources, sélectionnez le disque à copier, puis **Copier**.

    [![Capture d’écran indiquant l’emplacement du bouton Copier dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-lrg.png#lightbox)

1. Dans le volet **Explorateur**, développez **Disques**, puis sélectionnez le groupe de ressources dans lequel vous souhaitez coller votre disque.

    [![Capture d’écran montrant l’emplacement du nœud Disques dans l’application Explorateur Stockage Azure. Ce nœud permet de coller un disque.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-lrg.png#lightbox)

1. Dans le volet de détails du groupe de ressources, sélectionnez **Copier**.

    [![Capture d’écran indiquant l’emplacement du bouton Coller dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-lrg.png#lightbox)

1. Dans la boîte de dialogue **Coller le disque**, renseignez les valeurs. Vous pouvez également spécifier une zone de disponibilité dans les régions prises en charge.

    [![Capture d’écran du formulaire Coller le disque de l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-lrg.png#lightbox)

1. Sélectionnez **Coller** pour lancer l’opération de collage du disque copié. L’état est affiché dans **Activités**.

    [![Capture d’écran indiquant l’emplacement du volet Activités dans l’application Explorateur Stockage Azure. Ce volet contient les messages d’état des opérations de copie et de collage.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-lrg.png#lightbox)

## <a name="create-a-snapshot"></a>Créer une capture instantanée

1. Dans le volet **Explorateur**, déroulez la liste **Disques**, puis sélectionnez le groupe de ressources contenant le disque à partir duquel vous souhaitez créer une capture instantanée.

    [![Capture d’écran montrant l’emplacement du nœud Disques dans l’application Explorateur Stockage Azure. Ce nœud permet de créer des captures instantanées de disque.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. Dans le volet de détails du groupe de ressources, sélectionnez le disque à partir duquel vous souhaitez créer une capture instantanée, puis **Créer une capture instantanée**.

    [![Capture d’écran indiquant l’emplacement du bouton Créer une capture instantanée dans l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-lrg.png#lightbox)

1. Dans **Créer une capture instantanée**, spécifiez le nom de la capture instantanée, ainsi que le groupe de ressources dans lequel vous souhaitez la créer. Sélectionnez **Create** (Créer).

    [![Capture d’écran de la boîte de dialogue Créer une capture instantanée de l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-lrg.png#lightbox)

1. Une fois la capture instantanée créée, vous pouvez sélectionner **Ouvrir dans le portail** sous **Activités** pour afficher la capture instantanée dans le portail Azure.

    [![Capture d’écran indiquant l’emplacement du lien permettant d’effectuer une capture instantanée dans le volet Activités de l’application Explorateur Stockage Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-lrg.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- [Créer une machine virtuelle à partir d’un disque dur virtuel à l’aide du portail Azure](/azure/virtual-machines/windows/create-vm-specialized-portal)
- [Attacher un disque de données managé à une machine virtuelle Windows à l’aide du portail Azure](/azure/virtual-machines/windows/attach-managed-disk-portal)
