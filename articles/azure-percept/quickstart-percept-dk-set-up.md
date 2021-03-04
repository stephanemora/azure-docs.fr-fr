---
title: Configurer votre DK Azure Percept
description: Connectez votre devkit à Azure et déployez votre premier modèle IA
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 3540204d66bb589c567514f92a9a8acb2159e343
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664222"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configurer votre DK Azure Percept et déployer votre premier modèle IA

Commencez à utiliser le DK Azure Percept et Azure Percept Studio en vous appuyant sur l’expérience d’installation du DK Azure Percept pour connecter votre appareil à Azure et déployer votre premier modèle IA. Après avoir vérifié que votre compte Azure est compatible avec Azure Percept Studio, vous terminez l’installation pour vous assurer que votre DK Azure Percept est configuré pour créer une preuve de concepts IA Edge.

Si vous rencontrez des problèmes lorsque vous suivez ce guide de démarrage rapide, reportez-vous au guide de [résolution des problèmes](./troubleshoot-dev-kit.md) pour connaître les solutions possibles.

## <a name="prerequisites"></a>Prérequis

- Un DK Azure Percept.
- Un ordinateur hôte basé sur Windows, Linux ou OS X avec fonctionnalité Wi-Fi et un navigateur web.
- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Le compte Azure doit avoir le rôle « propriétaire » ou « contributeur » sur l’abonnement. Apprenez-en davantage sur les définitions de rôles Azure

### <a name="prerequisite-check"></a>Vérification du prérequis

Pour vérifier si votre compte Azure est un « propriétaire » ou un « contributeur » de l’abonnement, procédez comme suit.

1. Accédez au portail Azure et connectez-vous avec le compte Azure que vous avez l’intention d’utiliser avec Azure Percept Studio. 

    > [!NOTE]
    > Si vous avez plusieurs comptes Azure, votre navigateur peut mettre en cache les informations d’identification d’un autre compte. Pour plus d’informations sur la façon de vérifier que vous êtes connecté avec le compte approprié, consultez le guide de résolution des problèmes.

1. Développez le menu principal depuis le coin supérieur gauche de l’écran et cliquez sur « Abonnements » ou sélectionnez « Abonnements » dans le menu des icônes de la page d’accueil. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Dans la liste, sélectionnez votre abonnement. Si vous ne voyez pas votre abonnement dans la liste, vérifiez que vous êtes connecté avec le compte Azure approprié. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Si vous voulez créer un abonnement, effectuez [les étapes suivantes](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. Dans le menu Abonnement, sélectionnez Contrôle d’accès (IAM)
1. Cliquez sur le bouton Afficher mon accès
1. Vérifiez le rôle
    - Si le rôle de « lecteur » est affiché ou si vous obtenez un message indiquant que vous ne disposez pas des autorisations appropriées pour afficher les rôles, vous devez suivre le processus nécessaire dans votre organisation pour que votre rôle de compte soit élevé.
    - Si le rôle de « propriétaire » ou « contributeur » est affiché, votre compte fonctionnera avec Azure Percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Lancer l’expérience d’installation du DK Azure Percept

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Connectez votre ordinateur hôte directement au point d’accès Wi-Fi du devkit. Vous effectuez cette opération de la même façon que la connexion à tout autre réseau Wi-Fi.
    - **nom du réseau** : scz-xxxx (où « xxxx » correspond aux quatre derniers chiffres de l’adresse réseau Mac du devkit)
    - **mot de passe** : disponible sur la carte de bienvenue fournie avec le devkit

    > [!WARNING]
    > Une fois connecté au point d’accès Wi-Fi du DK Azure Percept, votre ordinateur hôte perd temporairement sa connexion à Internet. Les appels de vidéoconférence actifs, le streaming web ou d’autres expériences réseau sont interrompus jusqu’à la fin de l’étape 3 de l’expérience d’installation du DK Azure Percept.

1. Une fois connecté au point d’accès Wi-Fi du devkit, l’appareil hôte lance automatiquement l’expérience d’installation du DK Azure Percept dans une nouvelle fenêtre de navigateur. Si elle ne s’ouvre pas automatiquement, vous pouvez la lancer manuellement en ouvrant une fenêtre de navigateur et en accédant à [http://10.1.1.1](http://10.1.1.1). 

1. Maintenant que vous avez lancé l’expérience d’installation Azure Percept, vous êtes prêt à poursuivre l’installation. 

    > [!NOTE]
    > Le service web de l’expérience d’installation du DK Azure Percept s’arrête après 30 minutes de non-utilisation et à l’issue de l’installation. Dans ce cas, il est recommandé de redémarrer le devkit afin d’éviter les problèmes de connectivité liés au point d’accès Wi-Fi du devkit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Finaliser l’expérience d’installation du DK Azure Percept

1. Bien démarrer - Dans l’écran de bienvenue, cliquez sur **Suivant**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Page de bienvenue.":::

1. Connectez-vous au Wi-Fi - Dans la page Connexion réseau, cliquez sur **Se connecter à un nouveau réseau Wi-Fi** pour connecter votre devkit à votre réseau Wi-Fi.

    Si vous avez déjà connecté ce devkit à votre réseau Wi-Fi ou si vous êtes déjà connecté à l’expérience d’installation du DK Azure Percept par le biais de votre réseau Wi-Fi, cliquez sur le lien **Ignorer**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Connectez-vous au Wi-Fi.":::

1. Sélectionnez votre réseau Wi-Fi parmi les connexions disponibles et connectez-vous. (Vous aurez besoin de votre mot de passe Wi-Fi local)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Sélectionnez un réseau Wi-Fi."::: 

1. Copiez votre adresse IP - Une fois que votre devkit s’est correctement connecté au réseau de votre choix, notez l’**adresse IPv4** affichée dans la page. **Vous aurez besoin de cette adresse IP ultérieurement dans ce guide de démarrage rapide**. 

    > [!NOTE]
    > L’adresse IP peut changer à chaque démarrage de l’appareil.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiez l’adresse IP.":::

1. Lisez et acceptez le contrat de licence - Lisez le contrat de licence, sélectionnez **J’ai lu et j’accepte les termes du contrat de licence** (vous devez faire défiler le contrat jusqu’en bas), puis cliquez sur **Suivant**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Acceptez le CLUF.":::

1. Créez votre compte de connexion SSH - Configurez SSH pour l’accès à distance à votre devkit. Créez un nom d’utilisateur SSH et un mot de passe. 

    > [!NOTE]
    > SSH (Secure Shell) est un protocole réseau utilisé pour la communication sécurisée entre l’appareil hôte et le devkit. Pour plus d’informations sur SSH, consultez [cet article](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="Créez un compte SSH."::: 

1. Commencez le processus de connexion du devkit : dans l’écran suivant, cliquez sur **Se connecter avec un nouvel appareil** pour commencer le processus de connexion de votre devkit à Azure IoT Hub. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Connectez-vous à Azure."::: 

1. Copiez le code de l’appareil - Cliquez sur le lien **Copier** pour copier le code de votre appareil. Cliquez ensuite sur **Connexion à Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Copiez le code de l’appareil."::: 

1. Collez le code de l’appareil - Un nouvel onglet de navigateur s’ouvre avec une fenêtre qui demande le code de l’appareil copié. Collez le code dans la fenêtre et cliquez sur **Suivant**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Entrez le code de l’appareil."::: 

1. Connectez-vous à Azure - La fenêtre suivante vous oblige à vous connecter avec le compte Azure que vous avez vérifié au début du guide de démarrage rapide. Entrez ces informations d’identification de connexion, puis cliquez sur **Suivant**. 

    > [!NOTE]
    > Votre navigateur peut mettre automatiquement en cache d’autres informations d’identification. Vérifiez soigneusement que vous vous connectez avec le compte approprié.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Connectez-vous à Azure.":::
 
1. Ne fermez pas l’onglet du navigateur de l’expérience d’installation à ce stade - Après vous être connecté, un écran s’affiche et confirme que vous vous êtes connecté. Même s’il indique que vous pouvez fermer la fenêtre, **nous vous recommandons de ne fermer aucune fenêtre**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Connexion effectuée."::: 

1. Revenez à l’onglet du navigateur qui héberge l’expérience d’installation.
1. Option de sélection de votre hub IoT
    - Si vous disposez déjà d’un hub IoT et qu’il est listé dans cette page, vous pouvez le sélectionner et **passer à l’étape 17**.
    - Si vous n’avez pas de hub IoT ou si vous souhaitez en créer un, accédez au bas de la liste et cliquez sur **Créer un hub IoT Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Sélectionnez un hub IoT."::: 

1. Créez votre hub IoT – Remplissez tous les champs de cette page. 
    - Sélectionnez l’abonnement Azure que vous allez utiliser avec Azure Percept Studio
    - Sélectionnez un groupe de ressources existant. S’il n’en existe pas, cliquez sur Créer nouveau et suivez les invites. 
    - Sélectionnez la région Azure. 
    - Donnez un nom à votre nouveau hub IoT
    - Sélectionnez le niveau tarifaire (il correspond généralement à l’abonnement)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Créez un hub IoT."::: 

1. Attendez que le hub IoT soit déployé – Cela peut prendre quelques minutes

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="Déployez le hub IoT."::: 

1. Inscrivez votre devkit - Une fois le déploiement terminé, cliquez sur le bouton **Inscrire**

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="Hub IoT correctement déployé."::: 

1. Nommez votre devkit - Entrez un nom d’appareil pour votre devkit, puis cliquez sur **Suivant**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Donnez un nom à l’appareil."::: 

1. Attendez le téléchargement des modèles IA par défaut – Cette opération prend quelques minutes

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalisation de l’installation."::: 

1. Voyez le modèle IA de vision en action - Votre devkit a été correctement lié à votre hub IoT Azure et il a reçu le modèle de détection d’objet IA de vision par défaut. L’appareil photo Azure Percept Vision peut désormais rendre l’inférence de la détection d’objet sans connexion au cloud. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Cliquez sur Aperçu de la sortie vidéo."::: 
       
    > [!NOTE]
    > Le processus d’intégration et la connexion à l’accès Wi-Fi de l’appareil pour votre ordinateur hôte s’arrête à ce stade, mais votre devkit restera connecté à Internet.   Vous pouvez redémarrer l’expérience d’intégration avec un redémarrage du devkit, ce qui vous permettra de reprendre toute la procédure d’intégration et de reconnecter l’appareil à un autre hub IoT associé au même ou à un autre abonnement Azure.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Avertissement de déconnexion de l’expérience d’installation."::: 

1. Accédez au portail Azure – Revenez à la fenêtre de l’expérience d’installation et cliquez sur le bouton **Continuer vers le portail Azure** pour commencer à créer vos modèles IA personnalisés dans Azure Percept Studio.

    > [!NOTE]
    > Vérifiez que votre ordinateur hôte n’est plus connecté au point d’accès du devkit dans vos paramètres Wi-Fi et qu’il est maintenant reconnecté à votre Wi-Fi local.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Accédez à Azure Percept Studio."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Affichez votre appareil dans Azure Percept Studio et déployez des exemples d’applications prédéfinis courants

1. Affichez votre liste d’appareils à partir de la page de vue d’ensemble d’Azure Percept. La page de vue d’ensemble d’Azure Percept est votre point de départ pour accéder à de nombreux workflows différents pour le développement initial et avancé de modèles et de solutions IA Edge

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Affichez votre liste d’appareils.":::
    
1. Vérifiez que l’appareil que vous venez de créer est connecté, puis cliquez dessus.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Sélectionnez votre appareil.":::

1. Affichez le flux de votre appareil pour voir ce que l’appareil photo du devkit voit. Un modèle de détection d’objet par défaut est déployé à l’origine et détecte un certain nombre d’objets courants.

    > [!NOTE]
    > La première fois que vous effectuez cette opération sur un nouvel appareil, vous recevez une notification indiquant qu’un nouveau module est en cours de déploiement dans le coin supérieur droit.  Une fois cette opération terminée, vous pourrez lancer la fenêtre avec le flux vidéo de l’appareil photo. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Affichez votre flux vidéo.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Examinez la détection d’objet.":::

1. Explorez les exemples de modèles IA prédéfinis.   Azure Precept Studio propose un certain nombre d’exemples prédéfinis courants qui peuvent être déployés en un seul clic.   Sélectionnez Déployer un exemple de modèle pour les afficher et les déployer.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Explorez les modèles prédéfinis.":::
    
1. Déployez un nouvel exemple prédéfini sur votre appareil connecté. Sélectionnez un exemple dans la bibliothèque et cliquez sur Déployer sur l’appareil

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-1-select-prebuilt.png" alt-text="Sélectionnez un exemple prédéfini.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Examinez la détection d’objet en action.":::

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez suivre un flux similaire pour essayer des [modèles vocaux prédéfinis](./tutorial-no-code-speech.md).