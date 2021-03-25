---
title: Configurer un labo de mise en réseau avec Azure Lab Services et GNS3 | Microsoft Docs
description: Découvrez comment configurer un labo à l’aide d’Azure Lab Services pour enseigner la mise en réseau avec GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99499393"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Configurer un labo pour enseigner la mise en réseau 
Cet article explique comment configurer une classe qui se concentre sur la possibilité pour les étudiants d’émuler, de configurer, de tester et de dépanner des réseaux virtuels et réels à l’aide du logiciel [GNS3](https://www.gns3.com/). 

Cet article comprend deux sections principales. La première section explique comment créer le laboratoire. La deuxième section explique comment créer la machine modèle à l’aide de la virtualisation imbriquée et avec GNS3 installé et configuré.

## <a name="lab-configuration"></a>Configuration du laboratoire
Pour configurer ce labo, vous avez besoin d’un abonnement Azure pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services ou utiliser un compte existant. Pour créer un compte lab, consultez le tutoriel suivant : [Tutoriel pour configurer un compte lab](tutorial-setup-lab-account.md).

Suivez [ce tutoriel](tutorial-setup-classroom-lab.md) pour créer un labo et appliquer les paramètres suivants :

| Taille de la machine virtuelle | Image |
| -------------------- | ----- | 
| Grande (virtualisation imbriquée) | Windows 10 Professionnel, version 1909 |

## <a name="template-machine"></a>Machine modèle 

Une fois la machine modèle créée, démarrez-la et connectez-vous à celle-ci pour effectuer les trois tâches principales suivantes. 
 
1. Préparez la machine modèle pour la virtualisation imbriquée.
2. Installez GNS3.
3. Créez une machine virtuelle GNS3 imbriquée dans Hyper-V.
4. Configurez GNS3 pour utiliser une machine virtuelle Windows Hyper-V.
5. Ajoutez les appliances appropriées.
6. Publiez le modèle.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Préparer la machine modèle pour la virtualisation imbriquée
- Suivez les instructions de [cet article](how-to-enable-nested-virtualization-template-vm.md) afin de préparer votre machine virtuelle modèle pour la virtualisation imbriquée. 

### <a name="install-gns3"></a>Installer GNS3
- Suivez les instructions d’[installation de GNS3 ou de Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Veillez à inclure l’installation de la **machine virtuelle GNS3** dans la boîte de dialogue des composants (voir ci-dessous).

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Enfin, vous atteindrez la sélection de la machine virtuelle GNS3. Veillez à sélectionner l’option **Hyper-V**.

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Cette option permet de télécharger le script PowerShell et les fichiers VHD pour créer la machine virtuelle GNS3 dans le gestionnaire Hyper-V. Poursuivez l’installation en utilisant les valeurs par défaut. **Une fois l’installation terminée, ne démarrez pas GNS3**.

### <a name="create-gns3-vm"></a>Créer une machine virtuelle GNS3
Une fois l’installation terminée, un fichier zip **« GNS3.VM.Hyper-V.2.2.17.zip »** est téléchargé dans le même dossier que le fichier d’installation, contenant les lecteurs et le script PowerShell pour créer la machine virtuelle Hyper-V.
- **Extrayez tout** le contenu de l’archive GNS3.VM.Hyper-V.2.2.17.zip.  Cette action extraira les lecteurs et le script PowerShell pour créer la machine virtuelle.
- **Exécutez avec PowerShell** sur le script PowerShell « create-vm.ps1 » en cliquant avec le bouton droit sur le fichier.
- Une demande de modification de la stratégie d’exécution peut s’afficher. Entrez « Y » pour exécuter le script.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Une fois le script terminé, vous pouvez vérifier que la machine virtuelle « GNS3 VM » a été créée dans le gestionnaire Hyper-V.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Configurer GNS3 pour utiliser une machine virtuelle Hyper-V
Maintenant que GNS3 est installé et que la machine virtuelle GNS3 est ajoutée, démarrez GNS3 pour lier les deux.  [L’Assistant d’installation de GNS3 démarrera automatiquement](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard).  
- Utilisez l’option **Exécuter des appliances à partir de la machine virtuelle**. option.  Utilisez les valeurs par défaut pour le reste de l’Assistant jusqu’à ce que vous obteniez l’erreur **L’outil vmrun de VMware est introuvable**. erreur.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Choisissez **OK** et **Annuler** pour quitter l’Assistant.
- Pour terminer la connexion à la machine virtuelle Hyper-V, ouvrez **Modifier** -> **Préférences** -> **Machine virtuelle GNS3**, puis sélectionnez **Activer la machine virtuelle GNS3** et sélectionnez l’option **Hyper-V**.
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Ajouter les appliances appropriées

À ce stade, vous souhaiterez ajouter les [appliances appropriées pour la classe](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace).

### <a name="publish-template"></a>Publier un modèle

Maintenant que le modèle de machine virtuelle est correctement configuré et prêt à être publié, vous devez vérifier quelques points clés.
- Assurez-vous que la machine virtuelle GNS3 est arrêtée ou désactivée.  Publier la machine virtuelle alors qu’elle est en cours d’exécution la corrompra.
- Fermez GNS3 : publier la machine virtuelle alors que le logiciel est en cours d’exécution peut entraîner des effets secondaires inattendus.
- Nettoyez tous les fichiers d’installation ou autres fichiers inutiles.

## <a name="cost"></a>Coût  

Si vous souhaitez estimer le coût de ce labo, vous pouvez utiliser l’exemple suivant : 
 
Pour une classe de 25 étudiants avec 20 heures de cours planifiées et un quota de 10 heures pour le travail à la maison ou les devoirs, le prix du labo est le suivant : 

25 étudiants * (20 + 10) heures * 84 unités *Lab  0,01 USD par heure = 630 USD. 

**Important :** L’estimation du coût est fournie à titre d’exemple uniquement.  Pour en savoir plus sur les tarifs actuels, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusion
Cet article vous a présenté les étapes de création d’un labo pour la configuration d’un réseau à l’aide de GNS3.

## <a name="next-steps"></a>Étapes suivantes
Les étapes suivantes sont communes à la configuration de n’importe quel labo :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)