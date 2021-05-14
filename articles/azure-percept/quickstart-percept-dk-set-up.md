---
title: Configurer votre DK Azure Percept
description: Connectez votre devkit à Azure et déployez votre premier modèle IA
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 7056027965bd5e0f41c07835d21b454ab772a55a
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108132248"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configurer votre DK Azure Percept et déployer votre premier modèle IA

Procédez à l’installation d’Azure Percept DK pour configurer votre kit de développement et déployer votre premier modèle d’IA. Après avoir vérifié que votre compte Azure est compatible avec Azure Percept, vous allez :

- Connecter votre kit de développement à un réseau Wi-Fi
- Configurer une connexion SSH pour l’accès à distance à votre kit de développement
- Créer un nouveau IoT Hub à utiliser avec Azure Percept
- Connecter votre kit de développement à votre IoT Hub et à votre compte Azure

Si vous rencontrez des problèmes lors de ce processus, reportez-vous au [repère de résolution des problèmes d’installation](./how-to-troubleshoot-setup.md) pour connaître les solutions possibles.

## <a name="prerequisites"></a>Prérequis

- Un Azure Percept DK (kit de développement).
- Un ordinateur hôte basé sur Windows, Linux ou OS X avec fonctionnalité Wi-Fi et un navigateur web.
- Compte Azure avec un abonnement actif. [Créez un compte gratuitement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Le compte Azure doit avoir le rôle de **propriétaire** ou de **contributeur** sur l’abonnement. Procédez comme suit pour vérifier le rôle de votre compte Azure. Pour en savoir plus sur les définitions de rôle Azure, consultez la [documentation sur le contrôle d’accès en fonction du rôle Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)

    > [!CAUTION]
    > Si vous avez plusieurs comptes Azure, votre navigateur peut mettre en cache les informations d’identification d’un autre compte. Pour éviter toute confusion, il est recommandé de fermer toutes les fenêtres de navigateur inutilisées et de vous connecter au [portail Azure](https://portal.azure.com/) avant de commencer l’installation. Pour plus d’informations sur la façon de vérifier que vous êtes connecté avec le compte approprié, consultez le [repère de résolution des problèmes d’installation](./how-to-troubleshoot-setup.md).

### <a name="check-your-azure-account-role"></a>Vérifier le rôle de votre compte Azure

Pour vérifier si votre compte Azure est un « propriétaire » ou un « contributeur » dans l’abonnement, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous avec le même compte Azure que vous avez l’intention d’utiliser avec Azure Percept.

1. Cliquez sur l’icône **Abonnements** (elle ressemble à une clé jaune).

1. Dans la liste, sélectionnez votre abonnement. Si vous ne voyez pas votre abonnement, vérifiez que vous êtes connecté avec le compte Azure approprié. Si vous voulez créer un abonnement, effectuez [les étapes suivantes](../cost-management-billing/manage/create-subscription.md).

1. Dans le menu Abonnement, sélectionnez **Contrôle d’accès (IAM)** .
1. Cliquez sur **Afficher mon accès**.
1. Vérifiez le rôle :
    - Si le rôle est répertorié en tant que **Lecteur** ou si vous recevez un message indiquant que vous ne disposez pas des autorisations appropriées pour afficher les rôles, vous devez suivre le processus nécessaire dans votre organisation pour que le rôle de votre compte soit élevé.
    - Si votre rôle est répertorié en tant que **propriétaire** ou **contributeur**, votre compte fonctionne avec Azure Percept, et vous pouvez poursuivre l’installation.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Lancer l’expérience d’installation du DK Azure Percept

1. Connectez votre ordinateur hôte directement au point d’accès Wi-Fi du kit de développement. Comme pour la connexion à tout autres réseaux Wi-Fi, ouvrez les paramètres réseaux et Internet sur votre ordinateur, cliquez sur le réseau suivant, puis entrez le mot de passe réseau lorsque vous y êtes invité :

    - **Nom réseau** : selon la version du système d’exploitation de votre kit de développement, le nom du point d’accès Wi-Fi est **scz-xxxx** ou **apd-xxxx** (où « xxxx » correspond aux quatre derniers chiffres de l’adresse MAC du kit de développement)
    - **Mot de passe** : disponible sur la carte de bienvenue fournie avec le kit de développement

    > [!WARNING]
    > Une fois connecté au point d’accès Wi-Fi du DK Azure Percept, votre ordinateur hôte perd temporairement sa connexion à Internet. Les téléconférences vidéos actives, la diffusion en continu sur le web ou d’autres expériences basées sur le réseau seront interrompues.

1. Une fois connecté au point d’accès Wi-Fi du kit de développement, l’ordinateur hôte lance automatiquement l’expérience d’installation dans une nouvelle fenêtre de navigateur avec **your.new.device/** dans la barre d'adresse. Si l’onglet ne s’ouvre pas automatiquement, lancez l’expérience d’installation en accédant à [http://10.1.1.1](http://10.1.1.1). Assurez-vous que votre navigateur est connecté avec les mêmes informations d’identification de compte Azure que vous avez l’intention d’utiliser avec Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Page de bienvenue.":::

    > [!CAUTION]
    > L’expérience d’installation du service web s’arrête après 30 minutes sans activité. Dans ce cas, redémarrez le kit de développement afin d’éviter les problèmes de connectivité avec la point d’accès Wi-Fi du kit de développement.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Finaliser l’expérience d’installation du DK Azure Percept

1. Cliquez sur **Suivant** dans l’écran de **Bienvenue**.

1. Sur la page de **Connexion réseau**, cliquez sur **Connexion à un nouveau réseau WiFi**.

    Si vous avez déjà connecté votre kit de développement à votre réseau Wi-Fi, cliquez sur **Ignorer**.

1. Sélectionnez votre réseau Wi-Fi dans la liste des réseaux disponibles, puis cliquez sur **Se connecter**. Entrez votre mot de passe réseau lorsque vous y êtes invité.

1. Une fois que votre kit de développement s’est correctement connecté au réseau choisi, la page affiche l’adresse IPv4 attribuée à votre kit de développement. **Notez l’adresse IPv4 affichée sur la page.** Vous avez besoin de l’adresse IP lors de la connexion à votre kit de développement sur SSH pour la résolution des problèmes et les mises à jour de l’appareil.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiez l’adresse IP.":::

    > [!NOTE]
    > L’adresse IP peut changer à chaque démarrage de l’appareil.

1. Lisez le Contrat de licence, sélectionnez **J’ai lu et j’accepte le Contrat de licence** (vous devez faire défiler le contrat jusqu’en bas), puis cliquez sur **Suivant**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Acceptez le CLUF.":::

1. Entrez un nom de compte et un mot de passe SSH, puis **notez vos informations de connexion pour une utilisation ultérieure**.

    > [!NOTE]
    > SSH (Secure Shell) est un protocole réseau qui vous permet de vous connecter au kit de développement à distance via un ordinateur hôte.

1. Sur la page suivante, cliquez sur **Installer en tant que nouvel appareil** pour créer un nouvel appareil dans votre compte Azure.

1. Cliquez sur **Copier** pour copier le code de votre appareil. Cliquez ensuite sur **Connexion à Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Copiez le code de l’appareil.":::

1. Un nouvel onglet de navigateur s’ouvre avec une fenêtre qui indique **Entrer le code**. Collez le code dans la fenêtre et cliquez sur **Suivant**. Ne fermez pas l’onglet **Bienvenue** avec l’expérience d’installation.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Entrez le code de l’appareil.":::

1. Connectez-vous à Azure Percept à l’aide des informations d’identification du compte Azure que vous utiliserez avec votre kit de développement. Laissez l’onglet navigateur ouvert lorsque vous avez terminé.

    > [!CAUTION]
    > Votre navigateur peut mettre automatiquement en cache d’autres informations d’identification. Vérifiez soigneusement que vous vous connectez avec le compte approprié.

    Après vous être connecté avec succès à Azure Percent sur l’appareil, revenez à l’onglet **Bienvenue** pour poursuivre l’installation.

1. Lorsque la page **Attribuer votre appareil à votre Azure IoT Hub** s’affiche sous l’onglet **Bienvenue**, effectuez l’une des actions suivantes :

    - Si vous disposez déjà d’un IoT Hub que vous souhaitez utiliser avec Azure Percept et qui est répertorié sur cette page, sélectionnez le et passez à l’étape 15.
    - Si vous n’avez pas d’IoT Hub ou si vous souhaitez en créer un nouveau, cliquez sur **Créer un nouvel Azure IoT Hub**.

    > [!IMPORTANT]
    > Si vous avez un IoT Hub, mais qu’il n’apparaît pas dans la liste, vous êtes peut-être connecté à Azure Percept avec des informations d’identification incorrectes. Consultez le [Repère de résolution des problèmes d’installation](./how-to-troubleshoot-setup.md) pour obtenir de l’aide.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Sélectionnez un hub IoT.":::

1. Pour créer un IoT Hub, complétez les champs suivants :

    - Sélectionnez l’abonnement Azure que vous allez utiliser avec Azure Percept.
    - Sélectionnez un groupe de ressources existant. S’il n’en existe pas, cliquez sur **Créer nouveau** et suivez les demandes.
    - Sélectionnez la région Azure la plus proche de votre localisation physique.
    - Donnez un nom à votre nouveau IoT Hub.
    - Sélectionnez le niveau tarifaire S1 (standard).

    > [!NOTE]
    > Si vous avez besoin d’un [débit de message](../iot-hub/iot-hub-scaling.md#message-throughput) supérieur pour vos applications périphériques d’IA, vous pouvez à tout moment [mettre à niveau votre IoT Hub vers un niveau standard plus élevé](../iot-hub/iot-hub-upgrade.md) dans le Portail Azure. Les niveaux B et F ne prennent pas en charge Azure Percept.

1. Ce déploiement IoT Hub peut prendre quelques minutes. Une fois le déploiement terminé, cliquez sur **Inscrire**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="Hub IoT correctement déployé.":::

1. Entrez un nom d’appareil pour votre kit de développement, puis cliquez sur **Suivant**.

1. Attendez le téléchargement des modules d’appareil. Cette opération prend quelques minutes.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalisation de l’installation.":::

1. Lorsque la page **Installation de l’appareil terminée !** s’affiche, votre kit de développement a été correctement lié à votre IoT Hub et a téléchargé les logiciels nécessaires. Votre kit de développement se déconnecte automatiquement du point d’accès Wi-Fi, ce qui envoie ces deux notifications :

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Avertissement de déconnexion de l’expérience d’installation.":::

1. Connectez votre ordinateur hôte au réseau Wi-Fi auquel votre kit de développement est connecté durant l’étape 2.

1. Cliquez sur **Continuer vers le portail Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Accédez à Azure Percept Studio.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Afficher le flux vidéo de votre kit de développement et déployer un échantillon de modèle

1. La [page de présentation Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)est votre point de départ pour accéder à de nombreux workflows différents pour le développement initial et avancé de solutions d’IA périphériques. Pour commencer, cliquez sur **Appareils** dans le menu de gauche.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Affichez votre liste d’appareils.":::

1. Vérifiez que votre kit de développement est répertorié comme **Connecté**, puis cliquez dessus pour afficher la page de l’appareil.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Sélectionnez votre appareil.":::

1. Cliquez sur **View your device stream** (Afficher le flux de votre appareil). S’il s’agit de la première fois que vous affichez le flux vidéo de votre appareil, une notification indiquant qu’un nouveau modèle est déployé s’affiche dans le coin supérieur droit. Cela peut prendre quelques minutes.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Affichez votre flux vidéo.":::

    Une fois que le modèle est déployé, vous obtenez une autre notification avec un lien **Afficher le flux**. Cliquez sur le lien pour afficher le flux vidéo de votre caméra Azure Percept Vision dans une nouvelle fenêtre de navigateur. Le kit de développement est préchargé avec un modèle d’IA qui effectue automatiquement la détection d’objets pour de nombreux objets communs.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Examinez la détection d’objet.":::

1. Azure Percept Studio possède également un certain nombre d’échantillons de modèles d’IA. Pour déployer un échantillon de modèle dans votre kit de développement, revenez à la page de votre appareil, puis cliquez sur **Déployer un échantillon de modèle**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Explorez les modèles prédéfinis.":::

1. Sélectionnez un échantillon de modèle dans la bibliothèque et cliquez sur **Déployer sur l’appareil**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Examinez la détection d’objet en action.":::

1. Une fois que le modèle a été déployé avec succès, une notification avec un lien **Afficher le flux** s’affiche dans le coin supérieur droit de l’écran. Pour afficher l’inférence de modèle en action, cliquez sur le lien dans la notification ou revenez sur la page de l’appareil, puis cliquez sur **Afficher le flux de votre appareil**. Tous les modèles qui se sont exécutés précédemment sur le kit de développement sont désormais remplacés par le nouveau modèle.

## <a name="video-walkthrough"></a>Vidéo de procédure pas à pas

Pour une démonstration visuelle des étapes décrites ci-dessus, consultez la vidéo suivante (l’expérience d’installation débute à 0:50) :

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une solution de vision sans code](./tutorial-nocode-vision.md)