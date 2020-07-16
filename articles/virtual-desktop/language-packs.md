---
title: Installer des modules linguistiques sur des machines virtuelles Windows 10 dans Windows Virtual Desktop – Azure
description: Comment installer des modules linguistiques pour les machines virtuelles multisessions Windows 10 dans Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70592d940e3766597475f4a7b90a3902a53406d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361300"
---
# <a name="install-language-packs"></a>Installer des modules linguistiques

Quand vous configurez des déploiements Windows Virtual Desktop à l’échelle mondiale, il est judicieux de vous assurer que votre déploiement prend en charge plusieurs langues. Vous pouvez installer des modules linguistiques sur une image de machine virtuelle multisession Windows 10 Entreprise pour prendre en charge toutes les langues dont votre organisation a besoin. Cet article vous indique comment installer des modules linguistiques et capturer des images qui permettent à vos utilisateurs de choisir leurs propres langues d’affichage.

Pour plus d’informations sur le déploiement d’une machine virtuelle dans Azure, consultez [Créer une machine virtuelle Windows dans une zone de disponibilité avec le Portail Azure](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Cet article s’applique aux machines virtuelles multisessions Windows 10 Entreprise.

## <a name="install-a-language-pack"></a>Installer un module linguistique

Pour créer une image de machine virtuelle avec des modules linguistiques, vous devez d’abord installer des modules linguistiques sur un ordinateur et en capturer une image.

Pour installer les modules linguistiques :

1. Connectez-vous en tant qu’administrateur.
2. Vérifiez que vous avez installé toutes les mises à jour Windows et Windows Store les plus récentes.
3. Accédez à **Paramètres** > **Heure et langue** > **Région**.
4. Sous **Pays ou région**, sélectionnez le pays ou la région de votre choix dans le menu déroulant.
    Dans cet exemple, nous allons sélectionner **France**, comme illustré dans la capture d’écran suivante :

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page Région. La région actuellement sélectionnée est la France.](media/region-page-france.png)

5. Après cela, sélectionnez **Langue**, puis **Ajouter une langue par défaut**. Choisissez la langue que vous souhaitez installer dans la liste, puis sélectionnez **Suivant**.
6. Lorsque la fenêtre **Installer des fonctionnalités linguistiques** s’ouvre, activez les cases à cocher intitulées **Installer le module linguistique et Définir comme langue d’affichage**.
7. Sélectionnez **Installer**.
8. Pour ajouter plusieurs langues à la fois, sélectionnez **Ajouter une langue par défaut**, puis répétez le processus pour ajouter une langue aux étapes 5 et 6. Répétez ce processus pour chaque langue que vous souhaitez installer. Toutefois, vous ne pouvez définir qu’une seule langue à la fois en tant que langue d’affichage.

    Voici une rapide démonstration visuelle. Les images suivantes montrent comment installer les modules linguistiques français et néerlandais, puis définir le français comme langue d’affichage.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page Langue au début du processus. La langue d’affichage Windows sélectionnée est l’anglais.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la fenêtre de sélection de la langue. L’utilisateur a entré « français » dans la barre de recherche pour trouver les modules linguistiques en français.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page Installer des fonctionnalités linguistiques. Le français est sélectionné comme langue préférée. Les options sélectionnées sont « Définir comme langue d’affichage », « Installer le module linguistique », « Reconnaissance vocale » et « Écriture manuscrite ».](media/install-language-features.png)

    Une fois les modules linguistiques installés, vous devez voir les noms de vos modules linguistiques dans la liste des langues.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page Langue avec les nouveaux modules linguistiques installés. Les modules linguistiques français et néerlandais sont listés sous « Langues préférées ».](media/language-page-complete.png)

9. Si une fenêtre s’affiche et vous invite à vous déconnecter de votre session : Déconnectez-vous, puis reconnectez-vous. La langue d’affichage doit maintenant être la langue que vous avez sélectionnée.

10.  Accédez à **Panneau de configuration** > **Horloge et région** > **Région**.

11.  Lorsque la fenêtre **Région** s’ouvre, sélectionnez l’onglet **Administration**, puis sélectionnez **Copier les paramètres**.

12.  Activez les cases à cocher intitulées **Écran d’accueil et comptes système** et **Nouveaux comptes d’utilisateurs**.

13.  Sélectionnez **OK**.

14.  Une fenêtre s’ouvre et vous demande de redémarrer votre session. Sélectionnez **Redémarrer maintenant**.

15.  Après vous être reconnecté, revenez à **Panneau de configuration** > **Horloge et région** > **Région**.

16.  Sélectionnez l’onglet **Administration**.

17.  Sélectionnez **Modifier les paramètres régionaux**.

18. Dans le menu déroulant sous **Paramètres régionaux actuels**, sélectionnez la langue des paramètres régionaux que vous souhaitez utiliser. Après cela, sélectionnez **OK**.

19. Sélectionnez **Redémarrer maintenant** pour redémarrer votre session une nouvelle fois.

Félicitations, vous avez installé vos modules linguistiques.

Avant de continuer, assurez-vous que les versions les plus récentes de Windows et du Windows Store sont installées sur votre système.

## <a name="sysprep"></a>Sysprep

Ensuite, vous devez utiliser Sysprep pour préparer votre ordinateur pour le processus de capture d’image.

Pour exécuter Sysprep sur votre ordinateur :

1. Ouvrez PowerShell en tant qu’administrateur.
2. Exécutez la cmdlet suivante pour accéder au répertoire approprié :

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Ensuite, exécutez la cmdlet suivante :

    ```powershell
    .\sysprep.exe
    ```

4. Lorsque la fenêtre de l’outil de préparation du système s’ouvre, activez la case à cocher intitulée **Généraliser**, puis accédez à **Options d’arrêt** et sélectionnez **Arrêter** dans le menu déroulant.

>[!NOTE]
>Le processus Syprep prend quelques minutes pour se terminer. Une fois la machine virtuelle arrêtée, votre session à distance se déconnecte.

### <a name="resolve-sysprep-errors"></a>Résoudre les erreurs Sysprep

Si un message d’erreur s’affiche pendant le processus Sysprep, voici ce que vous devez faire :

1. Ouvrez **Lecteur C** et accédez à **Windows** > **System32 Sysprep** > **Panther**, puis ouvrez le fichier **setuperr**.

   Le texte du fichier d’erreur vous indique que vous devez désinstaller un module linguistique spécifique, comme illustré dans l’image suivante. Copiez le nom du module linguistique pour l’étape suivante.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du fichier setuperr. Le texte avec le nom du module est mis en surbrillance en bleu foncé.](media/setuperr-package-name.png)

2. Ouvrez une nouvelle fenêtre PowerShell et exécutez la cmdlet suivante avec le nom du module que vous avez copié à l’étape 2 pour supprimer le module linguistique :

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Vérifiez que vous avez supprimé le module en exécutant à nouveau la cmdlet `Remove-AppxPackage`. Si vous avez correctement supprimé le module, vous devriez voir un message indiquant que le module que vous essayez de supprimer n’existe pas.

4. Exécutez de nouveau la cmdlet `sysprep.exe`.

## <a name="capture-the-image"></a>capture de l’image

Maintenant que votre système est prêt, vous pouvez capturer une image afin que d’autres utilisateurs puissent utiliser des machines virtuelles basées sur votre système sans avoir à répéter le processus de configuration.

Pour capturer une image :

1. Accédez au Portail Azure et sélectionnez le nom de l’ordinateur que vous avez configuré aux étapes [Installer un module linguistique](#install-a-language-pack) et [Sysprep](#sysprep).

2. Sélectionnez **Capturer**.

3. Entrez un nom pour votre image dans le champ **Nom** et attribuez-le au groupe de ressources à l’aide du menu déroulant **Groupe de ressources**, comme indiqué dans l’image suivante.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la fenêtre Créer une image. Le nom que l’utilisateur a donné à cette image de test est « vmwvd-image-fr » et a été attribué au groupe de ressources « testwvdimagerg ».](media/create-image.png)

4. Sélectionnez **Create** (Créer).

5. Attendez quelques minutes que le processus de capture se termine. Lorsque l’image est prête, un message s’affiche dans le centre de notifications pour vous informer que l’image a été capturée.

Vous pouvez maintenant déployer une machine virtuelle à l’aide de votre nouvelle image. Lorsque vous déployez la machine virtuelle, veillez à suivre les instructions indiquées dans [Créer une machine virtuelle Windows dans une zone de disponibilité avec le Portail Azure](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Comment modifier la langue d’affichage pour les utilisateurs standard

Les utilisateurs standard peuvent modifier la langue d’affichage sur leurs machines virtuelles.

Pour modifier la langue d’affichage :

1. Accédez aux **Paramètres de langue**. Si vous ne savez pas où cela se trouve, vous pouvez entrer **Langue** dans la barre de recherche du menu Démarrer.

2. Dans le menu déroulant Langue d’affichage de Windows, sélectionnez la langue que vous souhaitez utiliser comme langue d’affichage.

3. Déconnectez-vous de votre session, puis reconnectez-vous. La langue d’affichage doit maintenant être la langue que vous avez sélectionnée à l’étape 2.
