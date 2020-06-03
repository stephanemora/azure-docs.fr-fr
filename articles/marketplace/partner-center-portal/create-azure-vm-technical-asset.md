---
title: Créer vos ressources techniques Machine virtuelle Azure
description: Découvrez comment créer et configurer des ressources techniques pour une offre de machine virtuelle pour la Place de marché Azure.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: e126ee2bd4133281195d4a86c5cb6f1c47bbd6ac
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266914"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Créer vos ressources techniques Machine virtuelle Azure

Cet article explique comment créer et configurer des ressources techniques pour une offre de machine virtuelle pour la Place de marché Azure. Une machine virtuelle héberge deux composants, le disque dur virtuel du système d’exploitation et les éventuels disques durs virtuels de données associés :

* **Disque dur virtuel du système d’exploitation** : contient le système d’exploitation et la solution déployée avec votre offre. Le processus de préparation du disque dur virtuel est différent en fonction du type de machine virtuelle (Linux, Windows ou personnalisée).
* **Disques durs virtuels de données** : stockage persistant dédié à une machine virtuelle. N’utilisez pas le disque dur virtuel du système d’exploitation (par exemple le lecteur C:) pour stocker des informations persistantes.

Une image de machine virtuelle contient un disque de système d’exploitation et jusqu’à 16 disques de données. Utilisez un disque dur virtuel par disque de données, même si le disque est vide.

> [!NOTE]
> Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données requis par la solution. Les clients ne peuvent pas supprimer les disques qui font partie d’une image au moment du déploiement, mais ils peuvent toujours ajouter des disques pendant ou après le déploiement.

> [!IMPORTANT]
> Chaque image de machine virtuelle d’un plan doit avoir le même nombre de disques de données.

## <a name="fundamental-technical-knowledge"></a>Connaissances techniques fondamentales

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre. En plus de votre domaine de solution, votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes :

* Connaissances de base des [services Azure](https://azure.microsoft.com/services/)
* Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
* Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking)
* Expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Expérience de travail avec [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Outils suggérés – facultatif

Vous pouvez utiliser l’un des environnements de script suivants pour faciliter la gestion des machines virtuelles et des disques durs virtuels :

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

Vous pouvez aussi ajouter les outils suivants à votre environnement de développement :

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Examinez les outils disponibles dans la page [Outils de développement Azure](https://azure.microsoft.com/product-categories/developer-tools/) et, si vous utilisez Visual Studio, sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Créer une image de machine virtuelle à l’aide d’une base approuvée

> [!NOTE]
> Pour créer vos ressources techniques de machine virtuelle à l’aide d’une image que vous avez créée dans vos propres locaux, accédez à [Créer une machine virtuelle à l’aide de votre propre image](#create-a-vm-using-your-own-image).

Cette section décrit différents aspects de l’utilisation d’une base approuvée, comme l’utilisation du protocole RDP (Remote Desktop Protocol), la sélection d’une taille pour la machine virtuelle, l’installation des dernières mises à jour de Windows et la généralisation de l’image de disque dur virtuel.

Les sections suivantes sont axées principalement sur les disques durs virtuels basés sur Windows. Pour plus d’informations sur la création de disques durs virtuels basés sur Linux, consultez [Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Suivez les instructions de cette rubrique pour utiliser Azure afin de créer une machine virtuelle contenant un système d’exploitation pris en charge préconfiguré. Si ce n’est pas compatible avec votre solution, il est possible de créer et de configurer une machine virtuelle locale à l’aide d’un système d’exploitation approuvé. Vous pouvez ensuite la configurer et la préparer pour le chargement comme décrit dans [Préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Sélectionner une base approuvée

Sélectionnez le système d’exploitation Windows ou Linux comme base.

#### <a name="windows"></a>Windows

Le disque dur virtuel de système d’exploitation pour votre image de machine virtuelle Windows doit être basé sur une image de base approuvée par Azure contenant Windows Server ou SQL Server. Pour commencer, créez une machine virtuelle à partir de l’une des images suivantes dans le portail Azure :

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!NOTE]
> Si vous utilisez le portail Azure actuel ou Azure PowerShell, les images Windows Server publiées à compter du 8 septembre 2014 sont approuvées.

#### <a name="linux"></a>Linux

Azure offre toute une gamme de distributions de Linux approuvées. Pour obtenir la liste actuelle, consultez [Linux sur les distributions approuvées par Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Créer une machine virtuelle dans le portail Azure

Effectuez les étapes suivantes pour créer l’image de machine virtuelle de base dans le [portail Azure](https://ms.portal.azure.com/) :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/) avec le compte Microsoft associé à l’abonnement Azure que vous souhaitez utiliser pour publier votre offre de machine virtuelle.
2. Créez un nouveau groupe de ressources et fournissez le **Nom du groupe de ressources**, l’**Abonnement** et l’**Emplacement du groupe de ressources**. Pour plus d’informations, consultez [Gérer les ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Sélectionnez **Machines virtuelles** à gauche pour afficher la page de détails des machines virtuelles.
4. Sélectionnez **+ Ajouter** pour ouvrir l’**expérience de création de machine virtuelle**.
5. Sélectionnez l’image dans la liste déroulante ou cliquez sur **Parcourir toutes les images publiques et privées** pour rechercher ou parcourir toutes les images de machines virtuelles disponibles.
6. Sélectionnez la taille de la machine virtuelle à déployer à l’aide des suggestions suivantes :
    * Si vous envisagez de développer le disque dur virtuel localement, la taille n’a pas d’importance. Vous pouvez utiliser une des machines virtuelles plus petites.
    * Si vous envisagez de développer l’image dans Azure, vous pouvez utiliser les tailles de machine virtuelle recommandées pour l’image sélectionnée.

7. Dans la section **Disques**, développez la section **Avancé** et affectez la valeur **Non** à l’option **Utiliser des disques managés**.
8. Fournissez les autres informations requises pour créer la machine virtuelle.
9. Sélectionnez **Vérifier + créer** pour passer en revue vos choix. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

Azure commence le provisionnement de la machine virtuelle que vous avez spécifiée. Vous pouvez suivre sa progression en sélectionnant l’onglet **Machines virtuelles** sur la gauche. Une fois la création terminée, l’état passe à **En cours d’exécution**.

Si vous rencontrez des difficultés pour créer votre nouveau disque dur virtuel basé sur Azure, consultez [Problèmes courants pendant la création de disque dur virtuel (FAQ)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation).

### <a name="connect-to-your-azure-vm"></a>Se connecter à votre machine virtuelle Azure

Cette section explique comment se connecter à la machine virtuelle que vous avez créée sur Azure et ouvrir une session. Une fois connecté, vous pouvez utiliser la machine virtuelle comme si vous étiez connecté localement à son serveur hôte.

#### <a name="connect-to-a-windows-based-vm"></a>Se connecter à une machine virtuelle Windows

Utilisez le client Bureau à distance pour vous connecter à la machine virtuelle Windows hébergée sur Azure. La plupart des versions de Windows incluent en mode natif la prise en charge du protocole RDP (Remote Desktop Protocol). Pour les autres systèmes d’exploitation, vous trouverez plus d’informations sur les clients dans l’article [Clients Bureau à distance](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Pour plus d’informations sur l’utilisation de la prise en charge intégrée de Windows RDP pour vous connecter à votre machine virtuelle, consultez cet article : [Connexion à une machine virtuelle Azure exécutant Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Des avertissements de sécurité peuvent s’afficher pendant le processus. Par exemple, des avertissements tels que « Le fichier .rdp provient d’un éditeur inconnu » ou « Vos informations d’identification d’utilisateur ne sont pas vérifiables ». Vous pouvez ignorer ces avertissements.

#### <a name="connect-to-a-linux-based-vm"></a>Se connecter à une machine virtuelle Linux

Pour vous connecter à une machine virtuelle Linux, vous devez disposer d’un client de protocole Secure Shell (SSH). Les étapes suivantes utilisent la version gratuite du terminal SSH [PuTTY](https://www.ssh.com/ssh/putty/).

1. Accédez au [portail Azure](https://ms.portal.azure.com/).
2. Recherchez et sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle à laquelle vous souhaitez vous connecter.
4. Démarrez la machine virtuelle si elle n’est pas en cours d’exécution.
5. Sélectionnez le nom de la machine virtuelle pour ouvrir sa page **Vue d’ensemble**.
6. Notez l’adresse IP publique et le nom DNS de votre machine virtuelle (si ces valeurs ne sont pas définies, vous devez [créer une interface réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Ouvrez l’application PuTTY.
8. Dans la boîte de dialogue de configuration PuTTY, entrez l’adresse IP ou le nom DNS de votre machine virtuelle.

    :::image type="content" source="media/avm-putty.png" alt-text="Illustre les paramètres du terminal PuTTY. Les zones Nom d’hôte ou adresse IP et Port sont mises en surbrillance.":::

9. Sélectionnez **Ouvrir** pour ouvrir un terminal PuTTY.
10. À l’invite, entrez le nom du compte et le mot de passe de votre compte de machine virtuelle Linux.

Si vous rencontrez des problèmes de connexion, consultez la documentation de votre client SSH. Par exemple, [Chapitre 10 : Messages d'erreur courants](https://www.ssh.com/ssh/putty/putty-manuals).

Pour plus d’informations, notamment concernant l’ajout d’un Bureau à une machine virtuelle Linux provisionnée, consultez l’article [Installer et configurer le Bureau à distance pour effectuer une connexion à une machine virtuelle Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Créer une machine virtuelle à l’aide de votre propre image

Cette section décrit comment créer et déployer une image de machine virtuelle fournie par l’utilisateur. Pour ce faire, vous pouvez fournir des images de disque dur virtuel du système d’exploitation et de disque de données à partir d’un disque dur virtuel déployé par Azure.

> [!NOTE]
> Pour utiliser éventuellement une image de base approuvée, suivez les instructions fournies dans [Créer une image de machine virtuelle à l’aide d’une base approuvée](#create-a-vm-image-using-an-approved-base).

1. Chargez vos images dans le compte Stockage Azure.
2. Déployez l’image de machine virtuelle.
3. Capturez l’image de machine virtuelle.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Charger vos images dans un compte de stockage Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Chargez votre disque dur virtuel du système d’exploitation généralisé et vos disques durs virtuels de disques de données dans votre compte de stockage Azure.

### <a name="deploy-your-image"></a>Déployer votre image

Créez votre image à l’aide du portail Azure ou d’Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Déployer à l’aide du portail Azure

1. Dans la page d’accueil, sélectionnez **Créer une ressource**, recherchez « déploiement de modèle », puis sélectionnez **Créer**.
2. Choisissez **Créer votre propre modèle dans l’éditeur**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Illustre la page Déploiement personnalisé.":::

3. Collez ce [modèle JSON](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) dans l’éditeur, puis sélectionnez **Enregistrer**.
4. Indiquez les valeurs des paramètres pour les pages de propriétés **Déploiement personnalisé** affichées.

    | Paramètre | Description |
    | ------------ | ------------- |
    | User Storage Account Name (Nom du compte de stockage de l’utilisateur) | Contenu de la cellule 2 |
    | User Storage Container Name (Nom du conteneur de stockage de l’utilisateur) | Nom du compte de stockage où se trouve le disque dur virtuel généralisé. |
    | DNS Name for Public IP (Nom DNS pour adresse IP publique) | Nom DNS d’adresse IP publique. Définissez le nom DNS de l’adresse IP publique dans le portail Azure une fois l’offre déployée. |
    | Nom d’utilisateur administrateur | Nom d’utilisateur du compte administrateur pour une nouvelle machine virtuelle |
    | Mot de passe d’administrateur | Mot de passe du compte administrateur pour une nouvelle machine virtuelle |
    | Type de système d'exploitation | Système d’exploitation de machine virtuelle : Windows ou Linux |
    | Identifiant d’abonnement | Identificateur de l’abonnement sélectionné |
    | Emplacement | Emplacement géographique du déploiement |
    | Taille de la machine virtuelle | [Taille de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), par exemple Standard_A2 |
    | Nom de l’adresse IP publique | Nom de votre adresse IP publique |
    | Nom de la machine virtuelle | Nom de la nouvelle machine virtuelle |
    | Nom du réseau virtuel | Nom du réseau virtuel utilisé par la machine virtuelle |
    | Nom de la carte réseau | Nom de la carte d’interface réseau exécutant le réseau virtuel |
    | URL du disque dur virtuel | URL complète du disque dur virtuel du disque de système d’exploitation |
    |  |  |

5. Après avoir indiqué ces valeurs, sélectionnez **Achat**.

Azure commence le déploiement. Une nouvelle machine virtuelle est créée avec le disque dur virtuel non managé spécifié, au chemin du compte de stockage spécifié. Pour en suivre la progression dans le portail Azure, sélectionnez **Machines virtuelles** sur le côté gauche du portail. Une fois la machine virtuelle créée, l’état passe de Démarrage à En cours d’exécution.

#### <a name="deploy-using-azure-powershell"></a>Déployer avec Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Capturer l’image de machine virtuelle

Utilisez les instructions suivantes qui correspondent à votre approche :

* Azure PowerShell : [Comment créer une image de machine virtuelle non managée à partir d’une machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI : [Créer une image d’une machine virtuelle ou d’un disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API : [Machines virtuelles : capturer](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configurer la machine virtuelle

Cette section décrit comment dimensionner, mettre à jour et généraliser une machine virtuelle Azure. Ces étapes sont nécessaires pour préparer le déploiement de votre machine virtuelle sur la Place de marché Microsoft Azure.

### <a name="sizing-the-vhds"></a>Dimensionnement des disques durs virtuels (VHD)

Si vous avez sélectionné l’une des machines virtuelles préconfigurées avec un système d’exploitation (et éventuellement d’autres services), vous avez déjà choisi une taille de machine virtuelle Azure standard. Il est recommandé de démarrer votre solution avec un système d’exploitation préconfiguré. Toutefois, si vous installez un système d’exploitation manuellement, vous devez dimensionner votre VHD principal dans votre image de machine virtuelle :

* Pour Windows, le VHD du système d’exploitation doit être créé en tant que VHD au format fixe de 127–128 Go.
* Pour Linux, ce VHD doit être créé en tant que VHD au format fixe de 30–50 Go.

Si la taille physique est inférieure à 127–128 Go, le VHD doit être extensible (fragmenté/dynamique). Les images Windows et SQL Server de base fournies satisfont déjà les exigences requises. Ne modifiez donc pas le format ou la taille du disque dur virtuel.

Les disques de données peuvent avoir une taille maximale de 1 To. Quand vous choisissez la taille, rappelez-vous que les clients ne peuvent pas redimensionner les disques durs virtuels dans une image lors du déploiement. Les VHD de données doivent être créés en tant que VHD au format fixe. Ils doivent également être extensibles (fragmentés/dynamiques). À l’origine, les disques de données peuvent être vides ou contenir des données.

### <a name="install-the-most-current-updates"></a>Installer les dernières mises à jour

Les images de base des machines virtuelles de système d’exploitation doivent contenir les dernières mises à jour jusqu’à leur date de publication. Avant de publier le VHD du système d’exploitation que vous avez créé, veillez à mettre à jour le système d’exploitation et tous les services installés avec l’ensemble des derniers correctifs de sécurité et de maintenance.

Pour Windows Server, exécutez la commande **Rechercher les mises à jour**.

Pour les distributions Linux, les mises à jour sont couramment téléchargées et installées par le biais d’un outil en ligne de commande ou d’un utilitaire graphique. Par exemple, Ubuntu Linux fournit la commande [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) et l’outil [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pour la mise à jour du système d’exploitation.

### <a name="perform-additional-security-checks"></a>Effectuer des vérifications de sécurité supplémentaires

Maintenez un niveau élevé de sécurité pour vos images de solution sur la Place de marché Azure. Pour vous aider, l’article ci-dessous fournit une liste de contrôle de configurations et de procédures de sécurité : [Recommandations de sécurité pour les images Place de marché Microsoft Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Certaines de ces recommandations sont propres aux images basées sur Linux, mais la plupart s’appliquent à n’importe quelle image de machine virtuelle.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Exécuter des tâches planifiées de configuration personnalisée

Si d’autres étapes de configuration sont nécessaires, utilisez une tâche planifiée exécutée au démarrage pour apporter des modifications finales à la machine virtuelle après sont déploiement. Tenez également compte des suggestions suivantes :

* S’il s’agit d’une tâche à exécution unique, la tâche doit se supprimer une fois qu’elle s’est terminée avec succès.
* Les configurations ne doivent pas s’appuyer sur des lecteurs autres que C ou D, car seuls ces deux lecteurs sont toujours présents (le lecteur C est le disque du système d’exploitation et le lecteur D est le disque local temporaire).

Pour plus d’informations sur les personnalisations Linux, consultez l’article [Extensions et fonctionnalités de machine virtuelle pour Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Généraliser l’image

Toutes les images dans Azure Marketplace doivent être réutilisables de façon générale. Pour autoriser cela, le VHD du système d’exploitation doit être généralisé. Cette opération consiste à supprimer d’une machine virtuelle tous les pilotes logiciels et identificateurs propres à une instance.

### <a name="windows"></a>Windows

Les disques de système d’exploitation Windows sont généralisés à l’aide de [l’outil sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Si vous mettez à jour ou reconfigurez le système d’exploitation par la suite, vous devrez réexécuter sysprep.

> [!WARNING]
> Étant donné que les mises à jour peuvent s’exécuter automatiquement, une fois que vous avez exécuté sysprep, éteignez la machine virtuelle jusqu’à son déploiement. Cet arrêt évite que des mises à jour ultérieures apportent des modifications propres à une instance au système d’exploitation ou aux services installés. Pour plus d’informations sur l’exécution de sysprep, consultez [Étapes de généralisation d’un disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Le processus suivant généralise une machine virtuelle Linux et la redéploie sous la forme d’une machine virtuelle distincte. Pour plus d’informations, consultez [Guide pratique pour créer une image de machine virtuelle ou de disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Vous pouvez arrêter quand vous atteignez la section « Créer une machine virtuelle à partir de l’image capturée ».

1. **Supprimer l’agent Linux Azure**

    1. Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
    2. Dans la fenêtre SSH, entrez la commande suivante : `sudo waagent -deprovision+user`.
    3. Tapez **Y** pour continuer (vous pouvez ajouter le paramètre **-force** à la commande précédente pour éviter l’étape de confirmation).
    d. Une fois la commande exécutée, tapez **Exit** pour fermer le client SSH.

2. **Arrêter la machine virtuelle**

    1. Dans le portail Azure, sélectionnez votre groupe de ressources et désallouez la machine virtuelle.
    2. Votre VHD est désormais généralisé, et vous pouvez créer une autre machine virtuelle à l’aide de ce VHD.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez rencontré des difficultés pour créer votre nouveau disque dur virtuel basé sur Azure, consultez [Common issues during VHD creation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) (Problèmes courants pendant la création de disque dur virtuel).

Sinon :

* [Certifier votre image de machine virtuelle](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) explique comment tester et soumettre une image de machine virtuelle pour la certification de la Place de marché Azure, y compris où obtenir l’outil *Certification Test Tool for Azure Certified*  et comment l’utiliser pour certifier votre image de machine virtuelle.
