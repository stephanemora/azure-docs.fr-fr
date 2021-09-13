---
title: Installer HANA dans SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Apprenez à installer HANA dans SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/4/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e4e7aec0422d3dfe5772748215c350073155a30
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559785"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Installation de HANA sur SAP HANA sur Azure (grandes instances)

Dans cet article, nous allons passer en revue l’installation de HANA dans SAP HANA sur Azure (grandes instances), également appelé « infrastructure BareMetal ».

## <a name="prerequisites"></a>Configuration requise

Pour installer HANA dans SAP HANA sur Azure (grandes instances), en premier lieu :

- Fournissez à Microsoft toutes les données à déployer pour vous sur une grande instance SAP HANA.
- Recevez la grande instance SAP HANA de Microsoft.
- Créez un réseau virtuel Azure connecté à votre réseau local.
- Connectez au même réseau virtuel Azure le circuit ExpressRoute pour les grandes instances HANA.
- Installez une machine virtuelle Azure destinée à servir de serveur intermédiaire pour les grandes instances HANA.
- Vérifiez que vous pouvez vous connecter à partir du serveur intermédiaire sur votre grande instance HANA, et inversement.
- Assurez-vous que tous les packages et correctifs nécessaires sont installés.
- Lisez la documentation et les notes SAP qui traitent de l’installation de HANA sur le système d’exploitation que vous utilisez. Assurez-vous que la version de HANA que vous avez choisie est prise en charge sur la version de votre système d’exploitation.

## <a name="download-the-sap-hana-installation-bits"></a>Télécharger les bits d’installation de SAP HANA

Nous allons à présent télécharger les packages d’installation de HANA sur la machine virtuelle du serveur intermédiaire. Dans cet exemple, le système d’exploitation est Windows.

Les unités de grande instance HANA ne sont pas directement connectées à Internet. Vous ne pouvez pas télécharger directement les packages d’installation depuis SAP vers la machine virtuelle de grande instance HANA. Au lieu de cela, vous téléchargez les packages sur la machine virtuelle du serveur intermédiaire.

Vous avez besoin d’un utilisateur S ou d’un autre utilisateur SAP, qui vous permet d’accéder à la place de marché SAP.

1. Connectez-vous, puis accédez à [SAP Service Marketplace](https://support.sap.com/en/index.html) (Place de marché de service SAP). Sélectionnez **Download Software** > **Installations and Upgrade** > **By Alphabetical Index** (Télécharger le logiciel > Installations et mise à niveau > Par index alphabétique). Ensuite, sélectionnez **Under H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Installation** (Sous H – Édition de plateforme SAP HANA > Édition de plateforme SAP HANA 2.0 > Installation). Téléchargez les fichiers qui s’affichent dans la capture d’écran suivante.

   ![Capture d’écran des fichiers d’installation HANA à télécharger.](./media/hana-installation/image16_download_hana.PNG)

2. Dans cet exemple, nous avons téléchargé les packages d’installation SAP HANA 2.0. Sur la machine virtuelle Azure faisant office de serveur intermédiaire, développez les archives à extraction automatique dans le répertoire, comme illustré ci-dessous.

   ![Capture d’écran de l’archive à extraction automatique.](./media/hana-installation/image17_extract_hana.PNG)

3. Une fois les archives extraites, copiez le répertoire qui a été créé par l’extraction (dans ce cas, 51052030). Copiez le répertoire à partir du volume /hana/shared de l’unité de grande instance HANA dans un répertoire que vous avez créé.

   > [!Important]
   > Ne copiez pas les packages d’installation dans la racine ou dans le numéro d’unité logique de démarrage. L’espace est limité et doit être utilisé également par d’autres processus.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installer SAP HANA sur l’unité de grande instance HANA

1. Pour installer SAP HANA, connectez-vous en tant qu’utilisateur racine. Seule la racine dispose d’autorisations suffisantes pour installer SAP HANA. Définissez des autorisations sur le répertoire que vous avez copié dans /hana/shared.

    ```
    chmod –R 744 <Installation bits folder>
    ```
    
    Pour installer SAP HANA à l’aide de la configuration d’interface graphique utilisateur, le package gtk2 doit être installé sur les grandes instances HANA. Pour voir si cette configuration est installée, exécutez la commande suivante :
    
    ```
    rpm –qa | grep gtk2
    ```

    (Dans des étapes ultérieures, nous verrons la configuration de SAP HANA avec l’interface utilisateur graphique.)         

2. Accédez au répertoire d’installation, puis naviguez dans le sous-répertoire HDB_LCM_LINUX_X86_64. 

    Commencez en dehors de ce répertoire :
    
    ```
    ./hdblcmgui 
    ```
3. À présent, vous allez passer d’un écran à l’autre et fournir les données pour l’installation. Dans cet exemple, nous installons le serveur de base de données SAP HANA et les composants du client SAP HANA. Par conséquent, notre sélection est **SAP HANA Database** (Base de données SAP HANA).

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec la base de données SAP HANA sélectionnée.](./media/hana-installation/image18_hana_selection.PNG)

4. Sélectionnez **Installer le nouveau système**.

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec l’option d’installation d’un nouveau système sélectionnée.](./media/hana-installation/image19_select_new.PNG)

5. Effectuez votre sélection parmi les autres composants que vous pouvez installer.

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec la liste des composants supplémentaires](./media/hana-installation/image20_select_components.PNG)

6. Choisissez le client SAP HANA et le studio SAP HANA. Installez également une instance de scale-up. Puis, sélectionnez **Single-Host System** (Système à un seul hôte). 

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec l’option du système à un seul hôte sélectionnée.](./media/hana-installation/image21_single_host.PNG)

7. Ensuite, fournissez certains renseignements. Pour le chemin d'accès de l’installation, utilisez le répertoire /hana/shared.

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec les champs des propriétés système à renseigner.](./media/hana-installation/image22_provide_sid.PNG)

    > [!Important]
    > En tant qu’ID système (SID) HANA, vous devez fournir le même SID que celui fourni à Microsoft lors de la commande du déploiement de la grande instance HANA. Le choix d’un SID différent fait échouer l’installation en raison de problèmes d’autorisation d’accès sur les différents volumes.

8. Fournissez les emplacements des fichiers de données et des fichiers journaux de HANA.

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec les champs de données et de journaux](./media/hana-installation/image23_provide_log.PNG)

    > [!Note]
    > Le SID que vous avez spécifié lorsque vous avez défini les propriétés système (deux écrans au préalable) doit correspondre au SID des points de montage. Si tel n’est pas le cas, revenez en arrière et définissez la valeur du SID sur celle dont disposent les points de montage.

9. Vérifiez le nom d’hôte et corrigez-le si nécessaire. 

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec le nom d’hôte.](./media/hana-installation/image24_review_host_name.PNG)

10. Récupérez les données que vous avez communiquées à Microsoft lors de la commande du déploiement de la grande instance HANA. 

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec les champs administrateur système à renseigner](./media/hana-installation/image25_provide_guid.PNG)

    > [!Important]
    > Fournissez les **ID d’administrateur système** et **ID de groupe d’utilisateurs** que vous avez fournis à Microsoft lors de la commande du déploiement de l’unité. Si vous ne le faites pas, l’installation de SAP HANA sur l’unité de grande instance HANA échouera.

11. Les deux écrans suivants ne sont pas illustrés ici. Ils vous permettent de fournir le mot de passe pour l’utilisateur système de la base de données SAP HANA et le mot de passe pour l’utilisateur sapadm. Ce dernier est utilisé pour l’agent hôte SAP, qui est installé avec l’instance de base de données SAP HANA.

    Une fois le mot de passe défini, un écran de confirmation s’affiche. Vérifiez toutes les données affichées, puis poursuivez l’installation. Vous accédez à un écran renseignant sur la progression de l’installation, comme celui-ci :

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, avec les indicateurs de progression de l’installation.](./media/hana-installation/image27_show_progress.PNG)

12. Une fois l’installation terminée, vous devriez voir un écran semblable à celui-ci :

    ![Capture d’écran de la gestion du cycle de vie de SAP HANA, indiquant que l’installation est terminée.](./media/hana-installation/image28_install_finished.PNG)

    L’instance SAP HANA doit maintenant être opérationnelle et prête à l’emploi. Vous pouvez vous y connecter à partir de SAP HANA Studio. Veillez à rechercher et à appliquer les dernières mises à jour.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la haute disponibilité et la reprise d’activité après sinistre de SAP HANA - Grandes instances sur Azure.

> [!div class="nextstepaction"]
> [Haute disponibilité et récupération d’urgence des grandes instances SAP HANA sur Azure](hana-overview-high-availability-disaster-recovery.md)
