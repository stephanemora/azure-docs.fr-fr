---
title: Configurer l’appareil Azure Percept DK
description: Configurer votre appareil Azure Percept DK et connectez-le à Azure IoT Hub
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 601970978dc69aea2a6f0947a26cc0bebc1d999d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223026"
---
# <a name="set-up-the-azure-percept-dk-device"></a>Configurer l’appareil Azure Percept DK

Procédez à l’installation d’Azure Percept DK pour configurer votre kit de développement. Après avoir vérifié que votre compte Azure est compatible avec Azure Percept, vous allez :

- Lancer l’installation d’Azure Percept DK
- Connecter votre kit de développement à un réseau Wi-Fi
- Configurer une connexion SSH pour l’accès à distance à votre kit de développement
- Créer un appareil dans Azure IoT Hub

Si vous rencontrez des problèmes lors de ce processus, reportez-vous au [repère de résolution des problèmes d’installation](./how-to-troubleshoot-setup.md) pour connaître les solutions possibles.

> [!NOTE]
> L’expérience d’installation du service web s’arrête automatiquement après 30 minutes sans activité. Si vous ne parvenez pas à vous connecter au kit de développement ou si vous ne voyez pas son point d’accès Wi-Fi, redémarrez l’appareil.

## <a name="prerequisites"></a>Prérequis

- Un Azure Percept DK (kit de développement).
- Un ordinateur hôte basé sur Windows, Linux ou OS X avec fonctionnalité Wi-Fi et un navigateur web.
- Un abonnement Azure actif. [Créez un compte gratuitement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Les utilisateurs doivent avoir le rôle de **propriétaire** ou de **contributeur** sur l’abonnement. Procédez comme suit pour vérifier le rôle de votre compte Azure. Pour en savoir plus sur les définitions de rôle Azure, consultez la [documentation sur le contrôle d’accès en fonction du rôle Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)

    > [!CAUTION]
    > Fermez toutes les fenêtres de navigateur et connectez-vous à votre abonnement via le [portail Azure](https://portal.azure.com/) avant de commencer l’installation. Pour plus d’informations sur la façon de vérifier que vous êtes connecté avec le compte approprié, consultez le [repère de résolution des problèmes d’installation](./how-to-troubleshoot-setup.md).

### <a name="check-your-azure-account-role"></a>Vérifier le rôle de votre compte Azure

Pour vérifier si votre compte Azure est un « propriétaire » ou un « contributeur » dans l’abonnement, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous avec le même compte Azure que vous avez l’intention d’utiliser avec Azure Percept.

1. Cliquez sur l’icône **Abonnements** (elle ressemble à une clé jaune).

1. Dans la liste, sélectionnez votre abonnement. Si vous ne voyez pas votre abonnement, vérifiez que vous êtes connecté avec le compte Azure approprié. Si vous voulez créer un abonnement, effectuez [les étapes suivantes](../cost-management-billing/manage/create-subscription.md).

1. Dans le menu Abonnement, sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Afficher mon accès**.
1. Vérifiez le rôle :
    - Si le rôle est répertorié en tant que **Lecteur** ou si vous recevez un message indiquant que vous ne disposez pas des autorisations appropriées pour afficher les rôles, vous devez suivre le processus nécessaire dans votre organisation pour que le rôle de votre compte soit élevé.
    - Si votre rôle est répertorié en tant que **propriétaire** ou **contributeur**, votre compte fonctionne avec Azure Percept, et vous pouvez poursuivre l’installation.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Lancer l’expérience d’installation du DK Azure Percept

1. Connectez votre ordinateur hôte au point d’accès Wi-Fi du kit de développement. Sélectionnez le réseau suivant, puis entrez le mot de passe Wi-Fi lorsque vous y êtes invité :

    - **Nom du réseau** : **scz-xxxx** ou **apd-xxxx** (où **xxxx** correspond aux quatre derniers chiffres de l’adresse MAC du kit de développement)
    - **Mot de passe** : disponible sur la carte de bienvenue fournie avec le kit de développement

    > [!WARNING]
    > Une fois connecté au point d’accès Wi-Fi de l’appareil Azure Percept DK, votre ordinateur hôte perd temporairement sa connexion à Internet. Les téléconférences vidéos actives, la diffusion en continu sur le web ou d’autres expériences basées sur le réseau seront interrompues.

1. Une fois connecté au point d’accès Wi-Fi du kit de développement, l’ordinateur hôte lance automatiquement l’expérience d’installation dans une nouvelle fenêtre de navigateur avec **your.new.device/** dans la barre d'adresse. Si l’onglet ne s’ouvre pas automatiquement, lancez l’expérience d’installation en accédant à [http://10.1.1.1](http://10.1.1.1) dans un navigateur web. Assurez-vous que votre navigateur est connecté avec les mêmes informations d’identification de compte Azure que vous avez l’intention d’utiliser avec Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-welcome.png" alt-text="Page de bienvenue.":::

    > [!NOTE]
    > **Utilisateurs Mac** : lors de l’installation sur un Mac, une fenêtre de portail captive s’ouvre indiquant qu’elle ne parvient pas à terminer l’installation. Fermez cette fenêtre et ouvrez un navigateur Web pour accéder à https://10.1.1.1, ce qui vous permettra d’effectuer l’installation.

## <a name="connect-your-dev-kit-to-a-wi-fi-network"></a>Connecter votre kit de développement à un réseau Wi-Fi

1. Dans l’écran **Bienvenue**, sélectionnez **Suivant**.

1. Sur la page de **Connexion réseau**, sélectionnez **Connexion à un nouveau réseau WiFi**.

    Si vous avez déjà connecté votre kit de développement à votre réseau Wi-Fi, sélectionnez **Ignorer**.

1. Sélectionnez votre réseau Wi-Fi dans la liste des réseaux disponibles, puis cliquez sur **Se connecter**. Entrez votre mot de passe réseau lorsque vous y êtes invité.

    > [!NOTE]
    > Il est recommandé de définir ce réseau comme « Réseau préféré » (Mac) ou de cocher la case pour « Se connecter automatiquement » (Windows).  Cela permet à l’ordinateur hôte de se reconnecter au point d’accès Wi-Fi du kit de développement si la connexion est interrompue au cours de ce processus.

1. Une fois que votre kit de développement s’est correctement connecté, la page affiche l’adresse IPv4 attribuée à votre kit de développement. **Notez l’adresse IPv4 affichée sur la page.** Vous avez besoin de l’adresse IP lors de la connexion à votre kit de développement sur SSH pour la résolution des problèmes et les mises à jour de l’appareil.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-success-wi-fi.png" alt-text="Copiez l’adresse IP.":::

    > [!NOTE]
    > L’adresse IP peut changer à chaque démarrage de l’appareil.

1. Lisez le Contrat de licence (vous devez faire défiler le contrat jusqu’en bas), sélectionnez **J’ai lu et j’accepte le Contrat de licence**, puis **Suivant**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-eula.png" alt-text="Acceptez le CLUF.":::

## <a name="set-up-an-ssh-login-for-remote-access-to-your-dev-kit"></a>Configurer une connexion SSH pour l’accès à distance à votre kit de développement

1. Créez un nom et une clé publique/un mot de passe de compte SSH, puis sélectionnez **Suivant**.

    Si vous avez déjà créé un compte SSH, vous pouvez ignorer cette étape.

    **Notez vos informations de connexion pour une utilisation ultérieure**.

    > [!NOTE]
    > SSH (Secure Shell) est un protocole réseau qui vous permet de vous connecter au kit de développement à distance via un ordinateur hôte.

##  <a name="create-a-new-device-in-azure-iot-hub"></a>Créer un appareil dans Azure IoT Hub

1. Sélectionnez **Installer en tant que nouvel appareil** pour créer un nouvel appareil dans votre compte Azure.

    Un code d’appareil sera obtenu à partir d’Azure.

1. Sélectionnez **Copier**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-copy-code.png" alt-text="Copiez le code de l’appareil.":::

    > [!NOTE]
    > Si vous recevez une erreur lors de l’utilisation de votre code d’appareil dans les étapes suivantes ou si le code d’appareil ne s’affiche pas, veuillez consulter nos [étapes de dépannage](./how-to-troubleshoot-setup.md) pour plus d’informations. 

1. Sélectionnez **Connexion à Azure**.

1. Un nouvel onglet de navigateur s’ouvre avec une fenêtre qui indique **Entrer le code**. Collez le code dans la fenêtre et sélectionnez **Suivant**. Ne fermez pas l’onglet **Bienvenue** avec l’expérience d’installation.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-enter-code.png" alt-text="Entrez le code de l’appareil.":::

1. Connectez-vous à Azure Percept à l’aide des informations d’identification du compte Azure que vous utiliserez avec votre kit de développement. Laissez l’onglet navigateur ouvert lorsque vous avez terminé.

    > [!CAUTION]
    > Votre navigateur peut mettre automatiquement en cache d’autres informations d’identification. Vérifiez soigneusement que vous vous connectez avec le compte approprié.

    Après vous être connecté avec succès à Azure Percent sur l’appareil, sélectionnez **Autoriser**. 
    
    Retournez dans l’onglet **Bienvenue** et poursuivez l’installation.

1. Lorsque la page **Attribuer votre appareil à votre Azure IoT Hub** s’affiche sous l’onglet **Bienvenue**, effectuez l’une des actions suivantes :

    - Passez directement à **Sélectionner votre Azure IoT Hub**, si votre IoT Hub est listé sur cette page.
    - Si vous n’avez pas d’IoT Hub ou si vous souhaitez en créer un nouveau, cliquez sur **Créer un nouvel Azure IoT Hub**.

    > [!IMPORTANT]
    > Si vous avez un IoT Hub, mais qu’il n’apparaît pas dans la liste, vous êtes peut-être connecté à Azure Percept avec des informations d’identification incorrectes. Consultez le [Repère de résolution des problèmes d’installation](./how-to-troubleshoot-setup.md) pour obtenir de l’aide.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-iot-hub-select.png" alt-text="Sélectionnez un hub IoT.":::

1. Pour créer un IoT Hub

    - Sélectionnez l’abonnement Azure que vous allez utiliser avec Azure Percept.
    - Sélectionnez un groupe de ressources existant. S’il n’en existe pas, cliquez sur **Créer nouveau** et suivez les demandes.
    - Sélectionnez la région Azure la plus proche de votre localisation physique.
    - Donnez un nom à votre nouveau IoT Hub.
    - Sélectionnez le niveau tarifaire S1 (standard).

    > [!NOTE]
    > L’exécution de votre déploiement IoT Hub peut prendre quelques minutes. Si vous avez besoin d’un [débit de message](../iot-hub/iot-hub-scaling.md#message-throughput) supérieur pour vos applications périphériques d’IA, vous pouvez à tout moment [mettre à niveau votre IoT Hub vers un niveau standard plus élevé](../iot-hub/iot-hub-upgrade.md) dans le portail Azure. Les niveaux B et F ne prennent pas en charge Azure Percept.

1. Une fois le déploiement terminé, sélectionnez **Inscrire**.

1. Sélectionner votre Azure IoT Hub

1. Entrez un nom d’appareil pour votre kit de développement, puis cliquez sur **Suivant**.

1. Les modules d’appareil sont déployés sur votre appareil. Cette opération peut prendre quelques minutes.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-finalize.png" alt-text="Finalisation de l’installation.":::

1. **L’appareil est configuré !** Votre kit de développement a été correctement lié à votre IoT Hub et a déployé tous les modules.

    > [!NOTE]
    > Une fois l’opération terminée, le point d’accès Wi-Fi du kit de développement se déconnecte automatiquement et le service web d’installation se ferme, ce qui entraîne deux notifications.

    > [!NOTE]
    > Les conteneurs IoT Edge configurés dans le cadre de ce processus d’installation utilisent des certificats qui expirent au bout de 90 jours. Les certificats peuvent être regénérés automatiquement en redémarrant IoT Edge. Pour plus d’informations, consultez [Gérer des certificats sur un appareil IoT Edge](../iot-edge/how-to-manage-device-certificates.md).

1. Connectez votre ordinateur hôte au réseau Wi-Fi auquel votre kit de développement est connecté.

1. Sélectionnez **Continuer vers le portail Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-Azure-portal-continue.png" alt-text="Accédez à Azure Percept Studio.":::

### <a name="video-walk-through"></a>Visite guidée vidéo 
Consultez la vidéo ci-dessous pour obtenir une présentation visuelle des étapes décrites ci-dessus.
> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre kit de développement est configuré, il est temps de voir Vision AI en action.
- [Afficher le flux vidéo de votre kit de développement](./how-to-view-video-stream.md)
- [Déployer un modèle Vision AI dans votre kit de développement](./how-to-deploy-model.md)