---
title: Configurer un labo pour enseigner le développement d’applications mobiles avec Android Studio
titleSuffix: Azure Lab Services
description: Découvrez comment configurer un labo pour dispenser un cours sur le développement d’applications mobiles de données, en utilisant Android Studio.  L’article aborde également les ajustements auxquels procéder lors de l’utilisation d’Android Studio sur une machine virtuelle dans Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76851336"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Configurer un labo pour enseigner le développement d’applications mobiles de données avec Android Studio

Cet article vous montre comment configurer un cours d’introduction au développement d’applications mobiles.  Ce cours est axé sur les applications mobiles Android pouvant être publiées sur [Google Play Store](https://play.google.com/store/apps).  Les étudiants apprennent à utiliser [Android Studio](https://developer.android.com/studio) pour créer des applications.  [Visual Studio Emulator pour Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) est utilisé pour tester l’application localement.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de labo, consultez le [tutoriel sur la configuration d’un compte de labo](tutorial-setup-lab-account.md).  Vous pouvez également utiliser un compte Lab existant.

Suivez le [tutoriel sur la configuration d’un labo de salle de cours](tutorial-setup-classroom-lab.md) pour créer un labo, puis appliquez les paramètres suivants :

| Taille de la machine virtuelle | Image |
| -------------------- | ----- |
| Moyenne (virtualisation imbriquée) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configuration du modèle de machine

Lorsque la création de la machine modèle est terminée, [démarrez la machine et connectez-vous à celle-ci](how-to-create-manage-template.md#update-a-template-vm) pour effectuer les tâches suivantes :

1. Ajout du rôle Hyper-V
2. Téléchargement et installation de Java  
3. Téléchargement et installation de Visual Studio Emulator pour Android
4. Téléchargement et installation d’Android Studio
5. Configuration de Visual Studio Emulator pour Android Studio

## <a name="add-hyper-v-role"></a>Ajout du rôle Hyper-V

Hyper-V doit être activé pour la réussite de l’installation de Visual Studio Emulator pour Android.  Suivez les instructions de l’article [Guide pratique pour activer la virtualisation imbriquée dans une machine virtuelle modèle](how-to-enable-nested-virtualization-template-vm.md).

## <a name="install-java"></a>Installer Java

Android Studio nécessite Java.  Suivez les étapes ci-dessous pour télécharger la dernière version de Java.

1. Accédez à la [page de téléchargement de Java](https://www.java.com/download/). Cliquez sur le bouton **Téléchargement gratuit de Java**.
2. Dans la page web Windows 64 bits pour Java, cliquez sur le bouton **Accepter et lancer le téléchargement gratuit**.
3. Quand le programme d'installation **Configuration de Java** s’affiche, cliquez sur **Installer**.
4. Attendez que le titre du programme d’installation passe à **Configuration de Java – Terminée**.  Cliquez sur le bouton **Fermer**.

## <a name="install-visual-studio-emulator-for-android"></a>Installer Visual Studio Emulator pour Android

Pour tester localement une application Android, celle-ci doit utiliser une version virtualisée d’un appareil Android.  Il existe quelques émulateurs Android disponibles qui permettent aux développeurs de tester leurs applications à partir de leurs machines.  Nous utilisons Visual Studio Emulator pour Android, car c’est un émulateur qui prend en charge la virtualisation imbriquée.  La machine virtuelle du service lab étant déjà une machine virtuelle, nous avons besoin d’un émulateur qui prenne en charge la virtualisation imbriquée.  L’émulateur intégré pour Android Studio ne gère pas la virtualisation imbriquée.  Pour savoir quels émulateurs prennent en charge la virtualisation imbriquée, consultez l’article sur l’[accélération matérielle pour les performances de l’émulateur (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Utilisez les instructions suivantes pour télécharger et installer Visual Studio Emulator pour Android.

1. Accédez à la page d'accueil [Visual Studio Emulator pour Android Studio](https://visualstudio.microsoft.com/vs/msft-android-emulator/).
2. Cliquez sur le bouton **Télécharger l’émulateur**.
3. Lorsque vs_emulatorsetup.exe est téléchargé, exécutez le fichier exécutable.
4. Quand la boîte de dialogue de l’installation de Visual Studio s’affiche, cliquez sur le bouton **Installer**.
5. Attendez la fin du programme d’installation.  Cliquez sur le bouton **Redémarrer maintenant** pour redémarrer l’ordinateur et achever l’installation.

Commencez par démarrer l’émulateur, puis déployez votre application en utilisant Android Studio.  Pour plus d’informations sur Visual Studio Emulator pour Android, consultez la [documentation de Visual Studio Emulator pour Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Installer Android Studio

Suivez les instructions ci-dessous pour télécharger et installer [Android Studio](https://developer.android.com/studio).

1. Accédez à la [page de téléchargement d’Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer n’est pas pris en charge par ce site.
2. Cliquez sur le package Android Studio exécutable pour Windows (64 bits).
3. Lisez les conditions légales stipulées dans la fenêtre contextuelle.  Quand vous êtes prêt à poursuivre, cochez la case **I have read and agree with the above terms and conditions** (J’ai lu et j’accepte les conditions générales ci-dessus), puis cliquez sur le bouton **Download Android Studio for Windows** (Télécharger Android Studio pour Windows).
4. Dès qu’il est téléchargé, lancez l’exécutable du programme d’installation d’Android Studio.
5. Dans la page **Welcome to Android Studio Setup** du programme d’installation **Android Studio Setup** (Installation Android Studio), cliquez sur **Next** (Suivant).
6. Dans la page **Configuration Settings** (Paramètres de la configuration), cliquez sur **Next**.
7. Dans la page **Choose Start Menu Folder** (Choisir le dossier du menu Démarrer), cliquez sur **Install**.
8. Attendez que l’installation soit terminée.
9. Dans la page **Installation complete** (Installation terminée), cliquez sur **Next**.
10. Dans la page **Completing Android Studio Setup** (Fin de l’installation Android Studio).  Cliquez sur **Terminer**.
11. Android Studio démarre automatiquement à l’issue de l’installation.
12. Dans la boîte de dialogue **Import Android Settings From...** (Importer les paramètres Android à partir de...), sélectionnez **Do not import settings** (Ne pas importer les paramètres). Cliquez sur **OK**.
13. Dans la page **Welcome** de **Android Studio Setup Wizard** (Assistant Installation Android Studio), cliquez sur **Next**.
14. Dans la page **Install Type**, choisissez **Standard**. Cliquez sur **Suivant**.
15. Dans la page **Select UI Theme**, sélectionnez le thème souhaité. Cliquez sur **Suivant**.
16. Dans la page **Verify Settings** (Vérifier les paramètres), cliquez sur **Next**.
17. Dans la page **Downloading Components** (Téléchargement des composants), attendez que tous les composants soient téléchargés.  Cliquez sur **Terminer**.

    > [!IMPORTANT]
    > L’installation de HAXM devrait normalement échouer.  HAXM ne prend pas en charge la virtualisation imbriquée, c’est la raison pour laquelle nous avons installé Visual Studio Emulator pour Android plus haut dans cet article.

18. La boîte de dialogue **Welcome to Android Studio** s’affichera dès l’Assistant d’installation terminé.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Configurer Android Studio et Visual Studio Emulator pour Android

Android Studio est presque prêt à être utilisé.  Il ne nous reste plus qu’à indiquer à Visual Studio Emulator pour Android l’emplacement où le kit Android SDK est installé.  Tous les émulateurs s’exécutant dans Visual Studio pour Android s’afficheront alors en tant que cibles de déploiement pour le débogage d’Android Studio.

Nous devons définir une clé de Registre spécifique pour signaler l’emplacement du kit Android SDK à Visual Studio Emulator pour Android.  Pour définir la clé de Registre demandée, exécutez le script ci-dessous.  L’emplacement d’installation par défaut du kit Android SDK est l’emplacement du script PowerShell ci-dessous.  Si vous avez installé votre kit Android SDK à un autre endroit, modifiez la valeur de `$androidSdkPath` avant d’exécuter le script.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Redémarrez Visual Studio Emulator pour Android et Android Studio pour que le nouveau paramètre soit pris en compte.

Démarrez la version dont vous avez besoin dans Visual Studio Emulator.  Elle s’affichera comme cible de déploiement pour votre application Android dans Android Studio.  La version minimale du projet Android Studio doit prendre en charge la version s’exécutant dans Visual Studio Emulator pour Android.  Vous êtes maintenant prêt à créer et déboguer des projets avec Android Studio et Visual Studio Emulator pour Android.  Si vous rencontrez des difficultés, consultez la page de résolution des problèmes de l’émulateur Android.

## <a name="cost"></a>Coût

Si vous souhaitez estimer le coût de ce labo, vous pouvez suivre l’exemple ci-dessous.
Pour une classe de 25 étudiants, avec 20 heures de cours prévues et un quota de 10 heures pour le travail personnel ou les devoirs, le prix du labo est le suivant :  

25 étudiants \* 30 heures (20 heures prévues + 10 heures de quota) * 55 unités Lab * 0,01 USD par heure = 412,5 USD

Pour plus d’informations sur les tarifs, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Étapes suivantes

Les étapes suivantes sont communes à la configuration de n’importe quel labo.

- [Créer et gérer un modèle](how-to-create-manage-template.md)
- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)
