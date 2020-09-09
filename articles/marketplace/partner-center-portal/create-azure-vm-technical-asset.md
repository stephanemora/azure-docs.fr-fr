---
title: Créer vos ressources techniques Machines virtuelles Azure
description: Découvrez comment créer et configurer des ressources techniques pour une offre de machine virtuelle pour la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 496cd4aeb96ca1849e950331658014d91dc6d6ba
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89143883"
---
# <a name="create-azure-virtual-machine-technical-assets"></a>Créer vos ressources techniques Machines virtuelles Azure

Lors de la publication de vos images de machine virtuelle sur Place de marché Azure, l’équipe Azure valide l’image de machine virtuelle pour garantir sa capacité de démarrage, sa sécurité et sa compatibilité avec Azure. Si l’un des tests de haute qualité échoue, la publication échouera avec un message contenant l’erreur et les [étapes de correction](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions) possibles.

Cet article explique comment créer et configurer des ressources techniques pour une offre de machine virtuelle pour la Place de marché Azure. Une machine virtuelle héberge deux composants, le disque dur virtuel du système d’exploitation et les éventuels disques durs virtuels de données associés :

1. **Disque dur virtuel du système d’exploitation** : contient le système d’exploitation et la solution déployée avec votre offre. Le processus de préparation du disque dur virtuel est différent en fonction du type de machine virtuelle (Linux, Windows ou personnalisée).

2. **Disques durs virtuels de données** : stockage persistant dédié à une machine virtuelle. N’utilisez pas le disque dur virtuel du système d’exploitation (par exemple le lecteur C:) pour stocker des informations persistantes.

Une image de machine virtuelle contient un disque de système d’exploitation et jusqu’à 16 disques de données. Utilisez un disque dur virtuel par disque de données, même si le disque est vide.

> [!NOTE]
> Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données requis par la solution. Les clients ne peuvent pas supprimer les disques qui font partie d’une image au moment du déploiement, mais ils peuvent toujours ajouter des disques pendant ou après le déploiement.

> [!IMPORTANT]
> Chaque image de machine virtuelle d’un plan doit avoir le même nombre de disques de données.

## <a name="fundamental-technical-knowledge"></a>Connaissances techniques fondamentales

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre. En plus de votre domaine de solution, votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes :

- Connaissances de base des [services Azure](https://azure.microsoft.com/services/)
- Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
- Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking)
- Expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Expérience de travail avec [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Outils suggérés facultatifs

Vous pouvez utiliser l’un des environnements de script suivants pour faciliter la gestion des machines virtuelles et des disques durs virtuels :

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

Vous pouvez aussi ajouter les outils suivants à votre environnement de développement :

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Créer une image de machine virtuelle à l’aide d’une base approuvée

Pour créer vos ressources techniques de machine virtuelle à l’aide d’une image que vous avez créée dans vos propres locaux, consultez [Créer une image de machine virtuelle à l’aide d’une base approuvée](#create-a-vm-image-using-an-approved-base) ci-dessous.

Cette section décrit différents aspects de l’utilisation d’une base approuvée, comme l’utilisation du protocole RDP (Remote Desktop Protocol), la sélection d’une taille pour la machine virtuelle, l’installation des dernières mises à jour de Windows et la généralisation de l’image de disque dur virtuel.

Suivez les instructions de cet article pour utiliser Azure afin de créer une machine virtuelle contenant un système d’exploitation pris en charge préconfiguré. Si ce n’est pas compatible avec votre solution, il est possible de créer et de configurer une machine virtuelle locale à l’aide d’un système d’exploitation approuvé. Vous pouvez ensuite la configurer et la préparer pour le chargement comme décrit dans [Préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Sélectionner une image de base approuvée

Sélectionnez le système d’exploitation Windows ou Linux comme base.

Le disque dur virtuel de système d’exploitation pour votre image de machine virtuelle doit être basé sur une image de base approuvée par Azure contenant Windows Server ou SQL Server.

Quand vous envoyez une demande de republication de votre image avec des mises à jour, le cas de test de vérification du numéro de référence peut échouer. Dans ce cas, votre image ne sera pas approuvée.

Cette erreur se produit quand vous avez utilisé une image de base qui appartient à un autre éditeur et que vous avez mis à jour l’image. Dans ce cas, vous n’êtes pas autorisé à publier votre image.

Pour résoudre ce problème, récupérez votre image la plus récente à partir de la Place de marché Azure et apportez des modifications à cette image. Pour afficher les images de base approuvées et rechercher votre image, consultez les articles suivants :

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

Azure offre toute une gamme de distributions de Linux approuvées. Pour obtenir la liste actuelle, consultez [Linux sur les distributions approuvées par Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Créer une machine virtuelle sur le portail Azure

Effectuez les étapes suivantes pour créer l’image de machine virtuelle de base sur le [portail Azure](https://ms.portal.azure.com/) :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/) avec le compte Microsoft associé à l’abonnement Azure que vous souhaitez utiliser pour publier votre offre de machine virtuelle.
2. Créez un nouveau groupe de ressources et fournissez le **Nom du groupe de ressources**, l’**Abonnement** et l’**Emplacement du groupe de ressources**. Pour plus d’informations, consultez [Gérer les ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Montre le début de la création d’un groupe de ressources.":::

3. Sélectionnez **Machines virtuelles** dans le volet gauche pour afficher la page de détails des machines virtuelles.
4. Sélectionnez **+ Ajouter** pour ouvrir l’**expérience de création de machine virtuelle**.
5. Sélectionnez l’image dans la liste déroulante ou sélectionnez **Parcourir toutes les images publiques et privées** pour rechercher ou parcourir toutes les images de machines virtuelles disponibles. Exemple :

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Montre un exemple d’image de machine virtuelle.":::

6. Sélectionnez la taille de la machine virtuelle à déployer à l’aide des suggestions suivantes :
    1. Si vous envisagez de développer le disque dur virtuel localement, la taille n’a pas d’importance. Vous pouvez utiliser une des machines virtuelles plus petites.
    2. Si vous envisagez de développer l’image dans Azure, vous pouvez utiliser les tailles de machine virtuelle recommandées pour l’image sélectionnée.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="Montre la sélection de la taille de machine virtuelle.":::

7. Dans la section **Disques**, développez la section **Avancé** et affectez la valeur **Non** à l’option **Utiliser des disques managés**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Montre l’option permettant d’utiliser des disques managés.":::

8. Fournissez les autres informations requises pour créer la machine virtuelle.
9. Sélectionnez **Vérifier + créer** pour passer en revue vos choix. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

Azure commence le provisionnement de la machine virtuelle que vous avez spécifiée. Vous pouvez suivre sa progression en sélectionnant l’onglet **Machines virtuelles** sur la gauche. Une fois la création terminée, l’état passe à **En cours d’exécution**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Créer une machine virtuelle de 2e génération sur le portail Azure

Créez une machine virtuelle de 2e génération (Gen2) dans le portail Azure.

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource**.
3. Sélectionnez **Afficher tout** à partir de Place de Marché Azure sur la gauche.
4. Sélectionnez une image qui prend en charge Gen2.
5. Sélectionnez **Create** (Créer).
6. Dans l’onglet **Avancé**, sous la section **Génération de machine virtuelle**, sélectionnez l’option **Gen 2**.
7. Dans l’onglet **De base**, sous **Détails de l’instance**, accédez à **Taille** et ouvrez le panneau **Sélectionner une taille de machine virtuelle**.
8. Sélectionnez une taille recommandée de [machine virtuelle de 2e génération prise en charge](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes).
9. Pour terminer la création de la machine virtuelle, parcourez le [flux de création du portail Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal).

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Montre l’option permettant de sélectionner la génération d’une machine virtuelle.":::

## <a name="connect-to-your-azure-vm"></a>Se connecter à votre machine virtuelle Azure

Cette section explique comment se connecter à la machine virtuelle que vous avez créée sur Azure et ouvrir une session. Une fois connecté, vous pouvez utiliser la machine virtuelle comme si vous étiez connecté localement à son serveur hôte.

### <a name="connect-to-a-windows-based-vm"></a>Se connecter à une machine virtuelle Windows

Utilisez le client Bureau à distance pour vous connecter à la machine virtuelle Windows hébergée sur Azure. La plupart des versions de Windows incluent en mode natif la prise en charge du protocole RDP (Remote Desktop Protocol). Pour les autres systèmes d’exploitation, vous trouverez plus d’informations sur les clients dans l’article [Clients Bureau à distance](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Pour plus d’informations sur l’utilisation de la prise en charge intégrée de Windows RDP pour vous connecter à votre machine virtuelle, consultez cet article : [Connexion à une machine virtuelle Azure exécutant Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Des avertissements de sécurité peuvent s’afficher pendant le processus. Par exemple, des avertissements tels que « Le fichier .rdp provient d’un éditeur inconnu » ou « Vos informations d’identification d’utilisateur ne sont pas vérifiables ». Vous pouvez ignorer ces avertissements.

### <a name="connect-to-a-linux-based-vm"></a>Se connecter à une machine virtuelle Linux

Pour vous connecter à une machine virtuelle Linux, vous devez disposer d’un client de protocole Secure Shell (SSH). Les étapes suivantes utilisent la version gratuite du terminal SSH [PuTTY](https://www.ssh.com/ssh/putty/).

1. Accédez au [portail Azure](https://ms.portal.azure.com/).
2. Recherchez et sélectionnez Machines virtuelles.
3. Sélectionnez la machine virtuelle à laquelle vous souhaitez vous connecter.
4. Démarrez la machine virtuelle si elle n’est pas en cours d’exécution.
5. Sélectionnez le nom de la machine virtuelle pour ouvrir sa page Vue d’ensemble.
6. Notez l’IP publique et le nom DNS de votre machine virtuelle (si ces valeurs ne sont pas définies, vous devez [créer une interface réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Ouvrez l’application PuTTY.
8. Dans la boîte de dialogue de configuration PuTTY, entrez l’adresse IP ou le nom DNS de votre machine virtuelle.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Illustre les paramètres du terminal PuTTY, en mettant en surbrillance les champs Nom d’hôte et Port.":::

9. Sélectionnez **Ouvrir** pour ouvrir un terminal PuTTY.
10. À l’invite, entrez le nom du compte et le mot de passe de votre compte de machine virtuelle Linux.

## <a name="configure-the-virtual-machine"></a>Configurer la machine virtuelle

Cette section décrit comment dimensionner, mettre à jour et généraliser une machine virtuelle Azure. Ces étapes sont nécessaires pour préparer le déploiement de votre machine virtuelle sur la Place de marché Microsoft Azure.

### <a name="sizing-the-vhds"></a>Dimensionnement des disques durs virtuels (VHD)

Les règles suivantes concernent les limitations relatives à la taille du disque du système d’exploitation. Quand vous envoyez une demande, assurez-vous que la taille du disque du système d’exploitation est comprise dans les limites de Linux ou de Windows.

| Système d''exploitation | Taille de disque dur virtuel recommandée |
| --- | --- |
| Linux | 30 à 1023 Go |
| Windows | 30 à 250 Go |

Comme les machines virtuelles autorisent l’accès au système d’exploitation sous-jacent, assurez-vous que la taille du disque dur virtuel est suffisamment grande pour le disque dur virtuel. Les disques n’étant pas extensibles sans temps d’arrêt, utilisez une taille de disque comprise entre 30 et 50&nbsp;Go.

| Taille de disque dur virtuel | Taille réelle occupée | Solution |
| --- | --- | --- |
| >500 To | n/a | Contactez l’équipe du support technique pour obtenir une approbation d’exception. |
| 250 à 500 To | >200 Go de différence par rapport à la taille du blob | Contactez l’équipe du support technique pour obtenir une approbation d’exception. |

### <a name="install-the-most-current-updates"></a>Installer les dernières mises à jour

Les images de base des machines virtuelles de système d’exploitation doivent contenir les dernières mises à jour jusqu’à leur date de publication. Avant de publier le VHD du système d’exploitation que vous avez créé, veillez à mettre à jour le système d’exploitation et tous les services installés avec l’ensemble des derniers correctifs de sécurité et de maintenance.

- Pour Windows Server, exécutez la commande Rechercher les mises à jour.
- Pour les distributions Linux, les mises à jour sont couramment téléchargées et installées par le biais d’un outil en ligne de commande ou d’un utilitaire graphique. Par exemple, Ubuntu Linux fournit la commande [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) et l’outil [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pour la mise à jour du système d’exploitation.

#### <a name="perform-additional-security-checks"></a>Effectuer des vérifications de sécurité supplémentaires

Maintenez un niveau élevé de sécurité pour vos images de solution sur la Place de marché Azure. Pour obtenir une liste de contrôle des configurations et des procédures de sécurité, consultez [Recommandations de sécurité pour les images Place de marché Microsoft Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Certaines de ces recommandations sont propres aux images basées sur Linux, mais la plupart s’appliquent à n’importe quelle image de machine virtuelle.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Exécuter des tâches planifiées de configuration personnalisée

Si vous avez besoin d’une configuration supplémentaire, utilisez une tâche planifiée exécutée au démarrage pour apporter des modifications finales à la machine virtuelle après sont déploiement. Tenez également compte des éléments suivants :

- S’il s’agit d’une tâche à exécution unique, la tâche doit se supprimer une fois qu’elle s’est terminée avec succès.
- Les configurations ne doivent pas s’appuyer sur des lecteurs autres que C ou D, car seuls ces deux lecteurs sont toujours présents (le lecteur C est le disque du système d’exploitation et le lecteur D est le disque local temporaire).

Pour plus d’informations sur les personnalisations Linux, consultez l’article [Extensions et fonctionnalités de machine virtuelle pour Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Généraliser l’image

Toutes les images dans Azure Marketplace doivent être réutilisables de façon générale. Pour autoriser cela, le VHD du système d’exploitation doit être généralisé. Cette opération consiste à supprimer d’une machine virtuelle tous les pilotes logiciels et identificateurs propres à une instance.

### <a name="for-windows"></a>Pour Windows

Les disques de système d’exploitation Windows sont généralisés à l’aide de l’outil [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Si vous mettez à jour ou reconfigurez le système d’exploitation par la suite, vous devrez réexécuter Sysprep.

> [!WARNING]
> Après l’exécution de Sysprep, éteignez la machine virtuelle jusqu’à son déploiement, car les mises à jour peuvent s’exécuter automatiquement. Cet arrêt évite que des mises à jour ultérieures apportent des modifications propres à une instance au système d’exploitation ou aux services installés. Pour plus d’informations sur l’exécution de sysprep, consultez [Étapes de généralisation d’un disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Pour Linux

Le processus suivant généralise une machine virtuelle Linux et la redéploie sous la forme d’une machine virtuelle distincte. Pour plus d’informations, consultez [Guide pratique pour créer une image de machine virtuelle ou de disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Vous pouvez arrêter quand vous atteignez la section intitulée « Créer une machine virtuelle à partir de l’image capturée ».

1. Supprimer l’agent Linux Azure

    1. Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
    2. Dans la fenêtre SSH, entrez la commande suivante : `sudo waagent –deprovision+user`.
    3. Tapez Y pour continuer (vous pouvez ajouter le paramètre -force à la commande précédente pour éviter l’étape de confirmation).
    4. Une fois la commande exécutée, tapez Exit pour fermer le client SSH.

2. Arrêt d’une machine virtuelle

    1. Dans le portail Azure, sélectionnez votre groupe de ressources et désallouez la machine virtuelle.
    2. Votre VHD est désormais généralisé, et vous pouvez créer une autre machine virtuelle à l’aide de ce VHD.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez rencontré des difficultés pour créer votre nouveau disque dur virtuel basé sur Azure, consultez [Common issues during VHD creation](common-issues-during-vhd-creation.md) (Problèmes courants pendant la création de disque dur virtuel).
- Si ce n’est pas le cas, [Tester une machine virtuelle déployée depuis un VHD](azure-vm-image-certification.md) explique comment tester et soumettre une image de machine virtuelle pour la certification Place de marché Azure, notamment où obtenir l’outil Certification Test Tool for Azure Certified et comment l’utiliser pour certifier votre image de machine virtuelle.
