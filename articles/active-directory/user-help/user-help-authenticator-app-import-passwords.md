---
title: Importation de mots de passe dans l’application Microsoft Authenticator – Azure AD
description: Guide pratique pour importer des mots de passe dans l’application Microsoft Authenticator à partir des principaux gestionnaires de mots de passe.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806153"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Importation de mots de passe dans l’application Microsoft Authenticator

Microsoft Authenticator gère l’importation de mots de passe à partir de Google Chrome, Firefox, LastPass, Bitwarden et RoboForm. Si votre gestionnaire de mots de passe actuel n’est pas pris en charge pour le moment, vous pouvez [entrer manuellement les informations d’identification de connexion dans notre modèle CSV](https://go.microsoft.com/fwlink/?linkid=2134938). Pour importer vos mots de passe existants et les gérer dans l’application Authenticator, il vous suffit de les exporter dans notre format CSV (valeurs séparées par des virgules) à partir de votre gestionnaire de mots de passe actuel. Ensuite, importez le fichier CSV exporté dans Authenticator avec notre extension du navigateur Chrome ou directement dans l’application Authenticator (Android et iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importation à partir de Google Chrome ou d’Android Smart Lock

Vous pouvez importer vos mots de passe dans Authenticator à partir de Google Chrome ou d’Android Smart Lock sur votre smartphone ou sur votre ordinateur de bureau. Vous pouvez :

- [Importation à partir de Chrome sur Android et iOS](#import-from-chrome-on-android-and-ios)
- [Importation à partir du navigateur de bureau Chrome](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importation à partir de Chrome sur Android et iOS

Les utilisateurs de Google Chrome sur des appareils Android et Apple peuvent importer directement leurs mots de passe à partir de leur téléphone en quelques étapes simples.

1. Installez l’application Authenticator sur votre téléphone et ouvrez l’onglet **Mots de passe**.

1. Connectez-vous à Google Chrome sur votre téléphone.

1. Appuyez sur le ![menu déroulant Google Chrome](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) en haut à droite pour les téléphones Android ou en bas à droite pour les appareils iOS, puis appuyez sur **Paramètres**.

   Plateforme | Lien
   ---------- | --------
   Android | ![Emplacement du menu Paramètres Google Chrome](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Icône du menu Paramètres Google Chrome](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. Dans **Paramètres**, ouvrez **Mots de passe**.

   Plateforme | Lien
   ---------- | --------
   Android | ![Emplacement de la commande Mots de passe Chrome Android](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Emplacement de la commande Mots de passe Chrome Apple](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Sur les appareils Android, appuyez sur le ![menu déroulant Google Chrome](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) en haut à droite pour les téléphones Android ou en bas à droite pour les appareils iOS, puis appuyez sur **Exporter les mots de passe**.

   Plateforme | Lien
   ---------- | --------
   Android | ![Emplacement Exporter les mots de passe Chrome Android](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Emplacement Exporter les mots de passe Chrome Apple](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Vous devez fournir un code secret, une empreinte digitale ou une reconnaissance faciale. Confirmez votre identité et appuyez de nouveau sur **Exporter les mots de passe** pour commencer l’exportation.

1. Une fois les mots de passe exportés, Chrome vous invite à choisir l’application dans laquelle vous effectuez l’importation. Sélectionnez **Authenticator** pour commencer l’importation des mots de passe. L’état de l’importation vous sera communiqué lorsqu’elle sera terminée.

   Plateforme | Lien
   ---------- | --------
   Android | ![Emplacement Importer les mots de passe Chrome Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Emplacement Importer les mots de passe Chrome Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importation à partir du navigateur de bureau Chrome

Avant de commencer, vous devez installer [l’extension Remplissage auto Microsoft](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) et vous y connecter sur votre navigateur Chrome.

1. Ouvrez [Google Password Manager](https://passwords.google.com) dans n’importe quel navigateur. Si ce n’est pas déjà fait, connectez-vous à votre compte Google.

1. Sélectionnez l’icône d’engrenage ![Icône d’engrenage du gestionnaire de mots de passe sur ordinateur de bureau](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) pour ouvrir la page Paramètres de mot de passe.

1. Sélectionnez **Exporter**, puis, sur la page suivante, de nouveau **Exporter** pour commencer l’exportation de vos mots de passe. Indiquez votre mot de passe Google lorsque cela vous est demandé pour confirmer votre identité. L’état de l’importation vous sera communiqué lorsqu’elle sera terminée.

   ![Emplacement de la commande Exporter les mots de passe du navigateur Chrome de bureau](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Ouvrez l’extension Chrome Remplissage auto et sélectionnez **Paramètres**.

   ![Emplacement des paramètres de l’extension Remplissage auto du navigateur Chrome de bureau](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Sélectionnez **Importer les données** pour ouvrir une boîte de dialogue. Ensuite, sélectionnez **Choisir un fichier** pour rechercher et importer le fichier CSV.

   ![Emplacement Importer les données au format CSV du navigateur Chrome de bureau](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importation à partir de Firefox

Firefox ne permet l’exportation de mots de passe qu’à partir du navigateur de bureau. Vérifiez donc que vous avez accès à ce navigateur avant d’importer les mots de passe à partir de Firefox.

1. Connectez-vous à la dernière version de Firefox sur votre ordinateur de bureau, puis sélectionnez le menu ![Menu « hamburger » de Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) en haut à droite de l’écran.

1. Sélectionnez **Connexions et mots de passe**.

   ![Emplacement de Connexions et mots de passe du navigateur Firefox de bureau](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Sur la page Firefox Lockwise, sélectionnez le ![menu déroulant Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png), puis **Exporter les connexions** et confirmez votre intention en sélectionnant **Exporter**. Il vous est demandé de vous identifier en entrant votre code secret, le mot de passe de votre appareil ou en scannant vos empreintes digitales. Une fois l’identification réussie, Firefox exporte vos mots de passe au format CSV à l’emplacement sélectionné.

   ![Emplacement Exporter les mots de passe du navigateur Firefox de bureau](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. Vous pouvez importer vos mots de passe dans Authenticator à partir d’un navigateur de bureau ou sur des téléphones iOS et Android. Pour effectuer l’importation dans l’application Authenticator sur votre téléphone, procédez comme suit :

      1. Transférez le fichier CSV exporté sur votre téléphone Android ou iOS suivant la méthode sécurisée de votre choix, puis téléchargez-le. Ensuite, partagez-le avec l’application Authenticator pour commencer l’importation.

         Plateforme | Lien
         ---------- | --------
         Android | ![Emplacement Importer les mots de passe Chrome Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Emplacement Importer les mots de passe Chrome Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Après avoir importé votre mot de passe dans Authenticator, supprimez le fichier CSV de votre ordinateur de bureau ou de votre téléphone mobile.

## <a name="import-from-lastpass"></a>Importation à partir de LastPass

LastPass ne prend en charge l’exportation de mots de passe qu’à partir d’un navigateur de bureau. Vérifiez donc que vous avez accès à un navigateur de ce type avant de commencer à importer les mots de passe.

1. Connectez-vous au [site web LastPass](https://lastpass.com) et sélectionnez **Options avancées**, puis **Exporter**.

   ![Emplacement Exporter les mots de passe LastPass sur ordinateur de bureau](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Identifiez-vous quand cela vous est demandé en indiquant votre mot de passe principal. Vous verrez alors les mots de passe exportés sur la page web.

1. Copiez le contenu de la page web.

1. Ouvrez le Bloc-notes (ou votre éditeur de texte favori) et collez le contenu copié.

1. Enregistrez ce fichier Bloc-notes en sélectionnant **Fichier** &gt; **Enregistrer sous**. Indiquez un nom qui se termine par « .csv » (par exemple, LastPass.csv) à un emplacement sécurisé sur votre ordinateur de bureau.

   ![Enregistrement du fichier CSV LastPass sur ordinateur de bureau](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. Vous pouvez importer vos mots de passe dans Authenticator sur un navigateur de bureau ou sur des téléphones iOS et Android. Pour effectuer l’importation dans l’application Authenticator sur votre téléphone, procédez comme suit :

      1. Transférez le fichier CSV exporté sur votre smartphone suivant la méthode sécurisée de votre choix, puis téléchargez-le. Ensuite, partagez-le avec l’application Authenticator pour commencer l’importation.

         Plateforme | Lien
         ---------- | --------
         Android | ![Emplacement Importer les mots de passe LastPass Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Emplacement Importer les mots de passe LastPass Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Après avoir importé votre mot de passe dans Authenticator, supprimez le fichier CSV de votre ordinateur de bureau ou de votre téléphone mobile.

## <a name="import-from-bitwarden"></a>Importation à partir de Bitwarden

Bitwarden ne prend en charge l’exportation de mots de passe qu’à partir d’un navigateur de bureau. Vérifiez donc que vous avez accès à un navigateur de ce type avant de commencer à importer les mots de passe.

1. Connectez-vous à https://vault.bitwarden.com/, puis sélectionnez **Outils** &gt; **Exporter le coffre**. Choisissez le format de fichier CSV, indiquez votre mot de passe principal, puis sélectionnez **Exporter le coffre** pour commencer l’exportation.

   ![Emplacement Exporter le coffre Bitwarden](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. Vous pouvez importer vos mots de passe dans Authenticator sur un navigateur de bureau ou sur des téléphones iOS et Android. Pour effectuer l’importation dans l’application Authenticator sur votre téléphone, procédez comme suit :

      1. Transférez le fichier CSV exporté sur votre smartphone suivant la méthode sécurisée de votre choix, puis téléchargez-le. Ensuite, partagez-le avec l’application Authenticator pour commencer l’importation.

         Plateforme | Lien
         ---------- | --------
         Android | ![Emplacement Importer les mots de passe Bitwarden Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Emplacement Importer les mots de passe Bitwarden Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Après avoir importé votre mot de passe dans Authenticator, supprimez le fichier CSV de votre ordinateur de bureau ou de votre téléphone mobile.

## <a name="import-from-roboform"></a>Importation à partir de RoboForm

RoboForm ne permet l’exportation de mots de passe qu’à partir de son application de bureau. Vérifiez donc que vous avez accès à l’application RoboForm sur un ordinateur de bureau avant de commencer l’importation.

1. Démarrez RoboForm sur votre client de bureau et connectez-vous à votre compte.

1. Sélectionnez **Options** dans le menu **RoboForm**.

   ![Menu Options RoboForm sur ordinateur de bureau](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Sélectionnez **Compte et données** &gt; **Exporter**.

   ![Emplacement de la commande Exporter RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Choisissez un emplacement sûr pour enregistrer votre fichier exporté. Sélectionnez le type **Données** **Connexions**, puis le format de fichier CSV et enfin **Exporter**.

   ![Boîte de dialogue Exporter RoboForm sur ordinateur de bureau](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Confirmez votre intention. Le fichier CSV est ensuite exporté à l’emplacement sélectionné.

   ![Boîte de dialogue de confirmation Exporter RoboForm sur ordinateur de bureau](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. Vous pouvez importer vos mots de passe dans Authenticator sur un navigateur de bureau ou sur des téléphones iOS et Android. Pour effectuer l’importation dans l’application Authenticator sur votre téléphone, procédez comme suit :

      1. Transférez le fichier CSV exporté sur votre smartphone suivant la méthode sécurisée de votre choix, puis téléchargez-le. Ensuite, partagez-le avec l’application Authenticator pour commencer l’importation.

         Plateforme | Lien
         ---------- | --------
         Android | ![Emplacement Importer les mots de passe RoboForm Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Emplacement Importer les mots de passe RoboForm Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Après avoir importé votre mot de passe dans Authenticator, supprimez le fichier CSV de votre ordinateur de bureau ou de votre téléphone mobile.

## <a name="import-by-creating-a-csv"></a>Importation en créant un fichier CSV

Si la procédure d’importation des mots de passe à partir de votre gestionnaire de mots de passe ne figure pas dans cet article, vous pouvez créer un fichier CSV vous permettant d’importer vos mots de passe dans Authenticator. Microsoft vous recommande de suivre ces étapes sur un ordinateur de bureau pour faciliter la mise en forme.

1. Sur votre ordinateur de bureau, [téléchargez et ouvrez le modèle d’importation](https://go.microsoft.com/fwlink/?linkid=2134938). Si vous utilisez Apple iPhone, Safari et Keychain, vous pouvez maintenant passer à l’étape 4.

1. Exportez vos mots de passe dans un fichier CSV non chiffré à partir de votre gestionnaire de mots de passe actuel.

1. Copiez les colonnes appropriées de votre fichier CSV exporté dans le modèle CSV, puis enregistrez.

1. Si vous ne disposez pas d’un fichier CSV exporté, vous pouvez copier toutes les connexions de votre gestionnaire de mots de passe actuel vers le modèle CSV. Ne supprimez ni ne modifiez la ligne d’en-tête. Lorsque vous avez terminé, vérifiez l’intégrité de vos données avant de commencer l’étape suivante.

1. Vous pouvez importer vos mots de passe dans Authenticator sur un navigateur de bureau ou sur des téléphones iOS et Android. Pour effectuer l’importation dans l’application Authenticator sur votre téléphone, procédez comme suit :

      1. Transférez le fichier CSV exporté sur votre smartphone suivant la méthode sécurisée de votre choix, puis téléchargez-le. Ensuite, partagez-le avec l’application Authenticator pour commencer l’importation.

         Plateforme | Lien
         ---------- | --------
         Android | ![Emplacement Importer les mots de passe CSV Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Emplacement Importer les mots de passe CSV Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Après avoir importé votre mot de passe dans Authenticator, supprimez le fichier CSV de votre ordinateur de bureau ou de votre téléphone mobile.

## <a name="troubleshooting-steps"></a>Étapes de dépannage

La cause la plus courante d’échec de l’importation est une mise en forme incorrecte dans le fichier CSV. Vous pouvez essayer de suivre la procédure suivante pour résoudre le problème.

- Consultez cet article pour savoir si nous prenons déjà en charge l’importation de mots de passe à partir de votre gestionnaire de mots de passe actuel. Si c’est le cas, vous pouvez réessayer l’importation en suivant la procédure indiquée pour votre fournisseur.

- Si nous ne prenons pas en charge l’importation du format de votre gestionnaire de mots de passe, vous pouvez réessayer en [créant manuellement votre fichier CSV](#import-by-creating-a-csv).

- Vous pouvez vérifier l’intégrité des données CSV selon les suggestions suivantes :

  - La première ligne doit contenir un en-tête avec trois colonnes : **url**, **username** (nom d’utilisateur) et **password** (mot de passe).

  - Chaque ligne doit contenir une valeur sous les colonnes **url** et **passwords** (mots de passe).

- Vous pouvez recréer le fichier CSV en collant votre contenu dans le [modèle de fichier CSV](https://go.microsoft.com/fwlink/?linkid=2134938).

- Si rien d’autre ne fonctionne, signalez votre problème à l’aide du lien **Envoyer des commentaires** dans les paramètres de l’application Authenticator.
