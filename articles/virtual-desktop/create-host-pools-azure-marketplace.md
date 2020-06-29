---
title: Pool d’hôtes Windows Virtual Desktop Portail Azure - Azure
description: Guide pratique pour créer un pool d’hôtes Windows Virtual Desktop à l’aide du portail Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a01d8568c50cca181ec10f91b316715d64d2c37e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737833"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Tutoriel : Créer un pool d’hôtes avec le portail Azure

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Les objets que vous créez avec la version Automne 2019 de Windows Virtual Desktop ne peuvent pas être gérés avec le portail Azure.
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les pools d’hôtes sont des ensembles d’une ou plusieurs machines virtuelles identiques dans des environnements Windows Virtual Desktop. Chaque pool d’hôtes peut contenir un groupe d’applications avec lequel les utilisateurs peuvent interagir comme ils le feraient sur un ordinateur de bureau physique.

Cet article vous guide tout au long du processus de création d’un pool d’hôtes pour un environnement Windows Virtual Desktop par le biais du portail Azure. Cette méthode offre une interface basée sur un navigateur pour créer un pool d’hôtes dans Windows Virtual Desktop, créer un groupe de ressources avec des machines virtuelles dans un abonnement Azure, joindre ces machines virtuelles au domaine Azure Active Directory (AD) et inscrire les machines virtuelles auprès de Windows Virtual Desktop.

## <a name="prerequisites"></a>Prérequis

Vous devez entrer les paramètres suivants pour créer un pool d’hôtes :

- Nom de l’image de machine virtuelle
- Configuration des machines virtuelles
- Propriétés du domaine et du réseau
- Propriétés du pool d’hôtes Windows Virtual Desktop

Vous devez également connaître les éléments suivants :

- Emplacement de la source de l’image que vous voulez utiliser. Provient-elle de la galerie Azure ou s’agit-il d’une image personnalisée ?
- Informations d’identification de jonction de domaine.

En outre, assurez-vous d’avoir inscrit le fournisseur de ressources Microsoft.DesktopVirtualization. Si vous ne l’avez pas encore fait, accédez à **Abonnements**, sélectionnez le nom de votre abonnement, puis sélectionnez **Fournisseurs de ressources Azure**.

Lorsque vous créez un pool d’hôtes Windows Virtual Desktop avec le modèle Azure Resource Manager, vous pouvez créer une machine virtuelle à partir de la galerie Azure, d’une image managée ou d’une image non managée. Pour en savoir plus sur la création d’images de machines virtuelles, voir [Préparer un disque dur virtuel Windows à charger sur Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) et [Créer une image managée d’une machine virtuelle généralisée dans Azure](../virtual-machines/windows/capture-image-resource.md).

Si vous n’avez pas encore d’abonnement Azure, veillez à [créer un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer à suivre ces instructions.

## <a name="begin-the-host-pool-setup-process"></a>Démarrer le processus de création du pool d’hôtes

Pour commencer à créer votre nouveau pool d’hôtes

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Entrez **Windows Virtual Desktop** dans la barre de recherche, puis recherchez et sélectionnez **Windows Virtual Desktop** sous Services.

3. Dans la page de vue d’ensemble de **Windows Virtual Desktop**, sélectionnez **Créer un pool d’hôtes**.

4. Sous l’onglet **De base**, sélectionnez l’abonnement approprié sous Détails du projet.

5. Sélectionnez un groupe de ressources existant dans le menu déroulant ou sélectionnez **Créer** pour créer un groupe de ressources.

6. Entrez un nom unique pour votre pool d’hôtes.

7. Dans le champ Emplacement, sélectionnez dans le menu déroulant la région dans laquelle vous souhaitez créer le pool d’hôtes.
   
   La zone géographique Azure associée aux régions sélectionnées est l’emplacement où seront stockées les métadonnées de ce pool d’hôtes et ses objets connexes. Veillez à choisir les régions à l’intérieur de la zone géographique dans laquelle vous souhaitez stocker les métadonnées de service.

     ![Capture d’écran du portail Azure montrant le champ Emplacement avec l’emplacement USA Est sélectionné. En regard du champ figure le texte « Les métadonnées seront stockées dans USA Est ».](media/portal-location-field.png)

8. Sous Type de pool d’hôtes, indiquez si votre pool d’hôtes sera **Personnel** ou **Groupé**.

    - Si vous choisissez **Personnel**, sélectionnez **Automatique** ou **Direct** dans le champ Type d’affectation.

      ![Capture d’écran du menu déroulant du champ Type d’affectation. L’utilisateur a sélectionné Automatique.](media/assignment-type-field.png)

9. Si vous choisissez **Groupé**, entrez les informations suivantes :

     - Pour **Limite de session maximale**, entrez le nombre maximal d’utilisateurs dont vous souhaitez équilibrer la charge sur un seul hôte de session.
     - Pour **Algorithme d’équilibrage de charge**, choisissez l’équilibrage de charge en profondeur d’abord ou en largeur d’abord, en fonction de votre modèle d’utilisation.

       ![Capture d’écran du champ Type d’affectation avec l’option « Groupé » sélectionnée. L’utilisateur pointe son curseur sur Largeur d’abord dans le menu déroulant de l’équilibrage de charge.](media/pooled-assignment-type.png)

10. Sélectionnez **Suivant : Détails de la machine virtuelle**.

11. Si vous avez déjà créé des machines virtuelles et souhaitez les utiliser avec le nouveau pool d’hôtes, sélectionnez **Non**. Si vous souhaitez créer des machines virtuelles et les inscrire auprès du nouveau pool d’hôtes, sélectionnez **Oui**.

Maintenant que vous avez terminé la première partie, passons à la partie suivante du processus, où nous allons créer la machine virtuelle.

## <a name="virtual-machine-details"></a>Détails de machine virtuelle

La première partie étant terminée, vous allez devoir configurer votre machine virtuelle.

Pour configurer votre machine virtuelle durant le processus de création du pool d’hôtes

1. Sous Groupe de ressources, choisissez le groupe de ressources dans lequel vous souhaitez créer les machines virtuelles. Il peut s’agir d’un groupe de ressources différent de celui que vous avez utilisé pour le pool d’hôtes.

2. Choisissez la **Région de machine virtuelle** où vous souhaitez créer les machines virtuelles. Elle peut être identique ou différente de la région que vous avez sélectionnée pour le pool d’hôtes.

3. Ensuite, choisissez la taille de la machine virtuelle que vous souhaitez créer. Vous pouvez conserver la taille par défaut telle quelle ou sélectionner **Modifier la taille**. Si vous sélectionnez **Modifier la taille**, dans la fenêtre qui s’affiche, choisissez la taille de la machine virtuelle adaptée à votre charge de travail.

4. Sous Nombre de machines virtuelles, indiquez le nombre de machines virtuelles que vous souhaitez créer pour votre pool d’hôtes.

    >[!NOTE]
    >Le processus de création peut créer jusqu’à 400 machines virtuelles lors de la configuration de votre pool d’hôtes, et chaque processus de création de machine virtuelle crée quatre objets dans votre groupe de ressources. Étant donné que le processus de création ne vérifie pas votre quota d’abonnement, vérifiez que le nombre de machines virtuelles que vous entrez figure dans les limites d’API et de machines virtuelles Azure pour votre groupe de ressources et votre abonnement. Vous pourrez ajouter d’autres machines virtuelles une fois que vous autre terminé la création de votre pool d’hôtes.

5. Après cela, spécifiez un **Préfixe de nom** pour nommer les machines virtuelles créées par le processus de création. Le suffixe sera `-` avec des nombres commençant à 0.

6. Ensuite, choisissez l’image qui doit être utilisée pour créer la machine virtuelle. Vous pouvez choisir **Galerie** ou **Objet blob de stockage**.

    - Si vous choisissez **Galerie**, sélectionnez l’une des images recommandées dans le menu déroulant :

      - Windows 10 Entreprise multisession, version 1909 + Microsoft 365 Apps for enterprise – Gen 1
      - Windows 10 Entreprise multisession, version 1909 – Gen 1
      - Windows Server 2019 Datacenter – Gen 1

     Si vous ne voyez pas l’image souhaitée, sélectionnez **Parcourir toutes les images et tous les disques** afin de pouvoir sélectionner une autre image dans votre galerie ou une image fournie par Microsoft ou d’autres éditeurs.

     ![Capture d’écran de la Place de marché avec une liste d’images Microsoft.](media/marketplace-images.png)

     Vous pouvez également accéder à **Mes éléments** et choisir une image personnalisée que vous avez déjà chargée.

     ![Capture d’écran de l’onglet Mes éléments.](media/my-items.png)

    - Si vous choisissez **Objet blob de stockage**, vous pouvez tirer parti de votre propre build d’image via Hyper-V ou sur une machine virtuelle Azure. Il vous suffit d’entrer l’emplacement de l’image dans l’objet blob de stockage sous la forme d’un URI.

7. Choisissez le type de disques de système d’exploitation que vous souhaitez que vos machines virtuelles utilisent : SSD Standard, SSD Premium ou HDD Standard.

8. Sous Réseau et sécurité, sélectionnez le réseau virtuel et le sous-réseau où vous souhaitez placer les machines virtuelles que vous créez. Vérifiez que le réseau virtuel peut se connecter au contrôleur de domaine, car vous devrez joindre les machines virtuelles qui se trouvent sur le réseau virtuel au domaine. Ensuite, indiquez si vous souhaitez utiliser une adresse IP publique pour les machines virtuelles. Nous vous recommandons de sélectionner **Non**, car une adresse IP privée est plus sécurisée.

9. Sélectionnez le type de groupe de sécurité souhaité : **De base**, **Avancé** ou **Aucun**.

    Si vous sélectionnez **De base**, vous devez choisir si vous souhaitez que des ports entrants soient ouverts. Si vous sélectionnez **Oui**, choisissez dans la liste les ports standard sur lesquels autoriser les connexions entrantes.

    >[!NOTE]
    >Pour une sécurité accrue, nous vous recommandons de ne pas ouvrir de ports entrants publics.

    ![Capture d’écran de la page de groupe de sécurité qui montre la liste des ports disponibles dans un menu déroulant.](media/available-ports.png)
    
    Si vous choisissez **Avancé**, sélectionnez un groupe de sécurité réseau existant que vous avez déjà configuré.

10. Après cela, indiquez si vous souhaitez que les machines virtuelles soient jointes à un domaine et une unité d’organisation spécifiques. Si vous choisissez **Oui**, spécifiez le domaine à joindre. Vous pouvez également ajouter une unité d’organisation spécifique à laquelle doivent appartenir les machines virtuelles.

11. Sous Compte d’administrateur, entrez les informations d’identification de l’administrateur de domaine Active Directory du réseau virtuel que vous avez sélectionné.

12. Sélectionnez **Espace de travail**.

Nous sommes maintenant prêts à commencer la phase suivante de la configuration de votre pool d’hôtes : l’inscription de votre groupe d’applications auprès d’un espace de travail.

## <a name="workspace-information"></a>Informations sur l’espace de travail

Le processus de création du pool d’hôtes crée un groupe d’applications de bureau par défaut. Pour que le pool d’hôtes fonctionne comme prévu, vous devez publier ce groupe d’applications sur des utilisateurs ou des groupes d’utilisateurs, et vous devez inscrire le groupe d’applications auprès d’un espace de travail. 

Pour inscrire le groupe d’applications de bureau auprès d’un espace de travail

1. Sélectionnez **Oui**.

   Si vous sélectionnez **Non**, vous pourrez inscrire le groupe d’applications ultérieurement, mais nous vous recommandons d’effectuer l’inscription auprès de l’espace de travail dès que vous le pouvez afin que votre pool d’hôtes fonctionne correctement.

2. Ensuite, choisissez si vous souhaitez créer un espace de travail ou en sélectionner un parmi les espaces de travail existants. Vous ne pourrez inscrire le groupe d’applications qu’auprès d’un espace de travail créé au même emplacement que le pool d’hôtes.

3. Si vous le souhaitez, vous pouvez sélectionner **Étiquettes**.

    Ici, vous pouvez ajouter des étiquettes pour pouvoir regrouper les objets avec des métadonnées afin de faciliter la tâche des administrateurs.

4. Quand vous avez terminé, sélectionnez **Vérifier + créer**. 

     >[!NOTE]
     >Le processus de validation Vérifier + créer ne vérifie pas si votre mot de passe répond aux normes de sécurité ou si votre architecture est correcte. Vous devez donc vérifier ces éléments vous-même. 

5. Passez en revue les informations relatives à votre déploiement pour vérifier que tout semble correct. Sélectionnez **Créer** lorsque vous avez terminé. Cela démarre le processus de déploiement, qui crée les objets suivants :

     - Votre nouveau pool d’hôtes
     - Un groupe d’applications de bureau
     - Un espace de travail, si vous avez choisi d’en créer un
     - Si vous avez choisi d’inscrire le groupe d’applications de bureau, l’inscription sera terminée
     - Des machines virtuelles, si vous avez choisi d’en créer, qui sont jointes au domaine et inscrites auprès du nouveau pool d’hôtes
     - Un lien de téléchargement pour un modèle Azure Resource Management basé sur votre configuration

Après cela, vous avez terminé !

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé votre pool d’hôtes, vous pouvez le remplir avec des programmes RemoteApps. Pour en savoir plus sur la façon de gérer des applications dans Windows Virtual Desktop, consultez le tutoriel suivant :

> [!div class="nextstepaction"]
> [Gérer les groupes d’applications](./manage-app-groups.md)
