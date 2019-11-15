---
title: Déployer Azure FarmBeats
description: Décrit comment déployer FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797242"
---
# <a name="deploy-farmbeats"></a>Déployer FarmBeats

Cet article décrit comment configurer Azure FarmBeats.

Azure FarmBeats est une solution extensible et spécifique à l’industrie, pour l’agriculture pilotée par les données, qui permet une mise en service et une connectivité des appareils capteurs en toute transparence sur le Cloud Azure, la collecte des données de télémétrie et l’agrégation. Azure FarmBeats dispose de différents capteurs, tels que des caméras, des drones, des capteurs de sol et la gestion des appareils à partir du Cloud, incluant l’infrastructure et les services dans Azure pour les appareils prêts pour l’IoT (Internet des objets) vers une application Web extensible et mobile pour fournir des visualisations, des alertes et des Insights.

> [!NOTE]
> Azure FarmBeats est uniquement pris en charge dans les environnements de cloud public. Pour plus d’informations sur l’environnement cloud, consultez [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure FarmBeats contient les deux composants suivants :

- **Data Hub** : Data Hub est la couche de plateforme d’Azure FarmBeats qui vous permet de créer, de stocker et de traiter des données et de tirer des Insights à partir de pipelines de données nouvelles ou existantes. Cette couche de plate-forme est utile pour exécuter et générer vos modèles et pipelines de données agricoles.

- **Accélérateur** : L’accélérateur est la couche de solution d’Azure FarmBeats qui dispose d’une application intégrée pour illustrer les fonctionnalités d’Azure FarmBeats à l’aide des modèles d’agriculture créés au préalable. Cette couche de solution vous permet de créer des limites de batterie de serveurs et de tirer des Insights à partir des données d’agriculture au sein d’une limite de la batterie de serveurs.

Un déploiement rapide d’Azure FarmBeats doit prendre moins d’une heure. Les coûts liés à Data Hub et à l’accélérateur varient en fonction de l’utilisation.

## <a name="deployed-resources"></a>Ressources déployées

Le déploiement d’Azure FarmBeats crée les ressources ci-dessous dans votre abonnement :

|N°  |Nom de la ressource  |Composant Azure FarmBeats  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Data Hub       |
|2  |    Application Insights      |     Data Hub/Accélérateur     |
|3  |Cache Azure pour Redis   |Data Hub   |
|4  |       Azure KeyVault    |  Data Hub/Accélérateur        |
|5\.  |    Time Series Insights       |     Data Hub      |
|6 |      Espace de noms Event Hub    |  Data Hub       |
|7  |    Azure Data Factory V2       |     Data Hub/Accélérateur      |
|8  |Compte Batch    |Data Hub   |
|9  |       Compte de stockage     |  Data Hub/Accélérateur        |
|10  |    Application logique        |     Data Hub      |
|11  |    Connexion d’API        |     Data Hub      |
|12|      App Service      |  Data Hub/Accélérateur       |
|13 |    Plan App Service        |     Data Hub/Accélérateur      |
|14 |Compte Azure Maps     |Accélérateur    |
|15 |       Time Series Insights      |  Data Hub     |

Azure FarmBeats peut être téléchargé depuis la place de marché Azure. Vous pouvez y accéder directement à partir du portail Azure.  

## <a name="prepare"></a>Préparation

Pour déployer Azure FarmBeats, vous avez besoin des autorisations suivantes :

- Locataire : Accès en lecture
- Abonnement : contributeur au propriétaire
- Groupe de ressources : propriétaire

## <a name="before-you-begin"></a>Avant de commencer

Avant de lancer le déploiement, assurez-vous que vous disposez des éléments suivants :

- Compte Sentinel
- Azure Active Directory (inscription d’application)
- Azure FarmBeats

## <a name="create-a-sentinel-account"></a>Créer un compte Sentinel    

Un compte avec Sentinel vous aide à télécharger l’imagerie satellite Sentinel depuis son site Web officiel sur votre appareil. Pour créer un compte gratuit, effectuez les étapes suivantes :

1. Accédez à https://scihub.copernicus.eu/dhus/#/self-registration Sur la page d’inscription, indiquez le prénom, le nom, le nom d’utilisateur, le mot de passe et l’adresse e-mail.
2. Dans le menu déroulant **Sélectionner un domaine**, sélectionnez l’option **Terrestre**.
3. Dans le menu déroulant **Sélectionner l’utilisation**, sélectionnez l’option **Éducation**.
4. Dans le menu déroulant **Sélectionner le pays**, sélectionnez votre pays.
5. Sélectionnez **Inscrire** pour terminer le processus d’inscription.

Un e-mail de vérification sera envoyé à l’adresse e-mail inscrite pour confirmation. Cliquez sur le lien et confirmez. Votre processus d’inscription est terminé.

## <a name="create-azure-ad-app-registration"></a>Créer une inscription d’application Azure AD

Pour l’authentification et l’autorisation sur Azure FarmBeats, vous devez disposer d’une inscription d’application Azure Active Directory qui :

- Cas 1 : Le programme d’installation peut créé automatiquement (à condition que vous disposiez des autorisations d’accès de groupe de ressources, du locataire et de l’abonnement nécessaires).
- Cas 2 : Vous pouvez créer et configurer avant de déployer Azure FarmBeats (nécessite des étapes manuelles).

**Cas n° 1** : Le programme d’installation suppose que vous disposez des droits nécessaires pour créer une inscription d’application Azure Active Directory dans l’abonnement souhaité. Pour vous inscrire, connectez-vous au portail, accédez à **Azure Active Directory** > **inscription d’application** > **nouvelle inscription**.

Si vous disposez déjà d’un abonnement, vous pouvez passer directement à la procédure suivante.

**Cas n° 2** : Cette méthode est l’étape par défaut lorsque vous n’avez pas les droits suffisants pour créer et configurer une inscription d’application Azure AD dans votre abonnement. Demandez à votre administrateur d’utiliser le [script personnalisé](https://aka.ms/FarmBeatsAADScript), qui aidera l’administrateur informatique à générer et configurer automatiquement l’inscription d’application Azure AD sur le portail Azure. En guise de sortie de l’exécution de ce script personnalisé à l’aide de l’environnement PowerShell, l’administrateur informatique doit partager un ID client et un mot de passe d’application Azure Active Directory avec vous. Prenez note de ces valeurs.

Procédez comme suit pour exécuter le script d’inscription d’application Azure AD :

1. Obtenez le [script](https://aka.ms/FarmBeatsAADScript) d'inscription.
2. Connectez-vous au portail Azure et sélectionnez votre abonnement et votre locataire Active Directory.
3. Lancez Cloud Shell dans le volet de navigation supérieur du Portail Azure.

    ![Projet FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)


4. La première fois, les utilisateurs doivent sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files. Sélectionnez **Créer le stockage**.
5. La première fois, les utilisateurs sont invités à choisir l’expérience d’interpréteur de commandes préféré, Bash ou PowerShell. Choisissez PowerShell.
6. Dans Cloud Shell, entrez les commandes ci-dessous pour exécuter le script.

    ```powershell
    ./create_aad_script.ps1
    ```
7. Notez l’ID d’application Azure AD et la clé secrète client à partager avec la personne déployant Azure FarmBeats.

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Créer une offre Azure FarmBeats sur la place de marché

Suivez ces étapes pour créer une offre Azure FarmBeats sur la place de marché :

1. Connectez-vous au **portail Azure** et choisissez votre compte dans le coin supérieur droit, puis basculez vers le locataire où vous voulez déployer Microsoft Azure FarmBeats.
2. Sélectionnez **Rechercher/Place de marché** ou **Créer des ressources**. Tapez **FarmBeats** pour afficher les détails de l’offre. Téléchargez les guides disponibles via les liens hypertextes aka.ms sur cette page avant de passer aux étapes suivantes.
3. Sélectionnez **Créer** et saisissez les informations suivantes :

   - Entrer le nom de l'abonnement.
   - Entrez un nom de groupe de ressources existant (groupe de ressources vide uniquement) ou créez un nouveau groupe de ressources pour le déploiement d’Azure FarmBeats. Prenez note de ce groupe de ressources pour le saisir plus tard dans le fichier. JSON.
   - Entrez la région dans laquelle vous souhaitez installer Azure FarmBeats. FarmBeats peut actuellement être installé dans les régions suivantes : Centre des États-Unis, Europe de l’Ouest, Est des États-Unis 2, Europe du Nord, Ouest des États-Unis, Asie du sud-est, Est des États-Unis, Est de l’Australie, Ouest des États-Unis 2.
4. Sélectionnez **OK**, vous êtes alors rediriger vers la page Conditions d’utilisation. Passez en revue les termes standards de la place de marché ou sélectionnez le lien hypertexte pour passer en revue les conditions d’utilisation.
5. Sélectionnez **Fermer**, la case à cocher « J’accepte », puis sélectionnez **Créer**.
6. Vous avez maintenant correctement signé le contrat de licence utilisateur final (CLUF) d’Azure FarmBeats sur la place de marché. Pour poursuivre le déploiement, suivez les étapes suivantes de ce guide.

### <a name="prepare-inputjson-file"></a>Préparer le fichier Input.Json

Il s’agit de votre fichier d’entrée pour Azure Cloud Shell et les paramètres dont les valeurs sont spécifiées dans ce fichier avant le chargement ne vous sont pas demandés pendant le déploiement, ce qui vous permet de gagner du temps.  

> [!NOTE]
> Ce fichier permet d’entrer des valeurs dans Azure Cloud Shell.  Pour gagner du temps pendant le déploiement, les paramètres saisis dans ce fichier ne vous seront pas demandés. Seuls les paramètres manquants vous seront demandés.

Passez en revue les paramètres avant de préparer le fichier.

|Commande | Description|
|--- | ---|
|« sku »  | Offre la possibilité de télécharger l’un ou l’autre des composants d’Azure FarmBeats ou les deux. Spécifie les composants à télécharger. Pour installer uniquement Data Hub, utilisez « onlydatabhub ». Pour installer Data Hub et l’accélérateur, utilisez « both ».|
|« subscriptionId »  | Spécifie l’abonnement pour l’installation de FarmBeats|
|« datahubResourceGroup »  | Nom du groupe de ressources pour les ressources de Data Hub.|
|« datahubLocation » | Emplacement où vous souhaitez stocker les ressources de Data Hub.|
|« acceleratorWebsiteName »  |Préfixe d’URL unique pour nommer votre Data Hub
Site Web Swagger. La valeur par défaut est le nom du groupe de ressources du Data Hub. Appuyez sur Entrée pour continuer avec la valeur par défaut.|
|« acceleratorResourceGroup »  | Nom du groupe de ressources pour les ressources de Data Hub. |
|« acceleratorLocation »  | Emplacement pour le stockage des ressources de Data Hub
« acceleratorWebsiteName »  | Préfixe d’URL unique pour nommer votre site Web Accélérateur. La valeur par défaut est accélérateur. Appuyez sur Entrée pour continuer avec la valeur par défaut.|
|« sentinelUsername » | Nom d’utilisateur pour se connecter : https://scihub.copernicus.eu/dhus/#/self-registration.|
|« sentinelPassword »  | Mot de passe pour se connecter : https://scihub.copernicus.eu/dhus/#/self-registration.|
|« farmbeatsAppId »  | Valeurs à partager par Team Azure FarmBeats.  |
|« farmbeatsPassword »  | Valeurs à partager par Team Azure FarmBeats.|
|« notificationEmailAddress »  | Adresse e-mail pour recevoir les notifications des alertes que vous configurez dans Data Hub.|
|“upda"aadAppClientId" "  |[**facultatif**] Paramètre à inclure dans Input.Json uniquement si une application Azure AD existe déjà.  - True/False. False pour une première installation et True pour un scénario de mise à niveau.|
|« aadAppClientId »  | [**facultatif**] Paramètre à inclure dans Input.Json uniquement si une application Azure AD existe déjà.  |
|« aadAppClientSecret »   | [**facultatif**] Paramètre à inclure dans Input.Json uniquement si une application Azure AD existe déjà.|


Exemple d’entrée JSON :

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Déployer dans un ligne de commande basée sur un navigateur (Cloud Shell)

Dans le cadre du flux de travail de la place de marché, vous avez créé un groupe de ressources et signé le contrat de licence utilisateur final, qui peut être revu de nouveau dans le cadre du déploiement réel. Le déploiement est effectué via Azure Cloud Shell (ligne de commande basée sur un navigateur) à l’aide de l’environnement Bash.  

> [!NOTE]
> Les sessions Cloud Shell inactives expirent au bout de 20 minutes. Essayez d’effectuer le déploiement dans ce délai.

1. Connectez-vous au portail **Azure** et sélectionnez l’abonnement et locataire Active Directory désirés.
2. Lancez **Cloud Shell** dans le volet de navigation en haut du portail **Azure**.

   ![Projet FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

3. Sélectionnez un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files.
4. Sélectionnez **Créer le stockage**.
5. Sélectionnez la liste déroulante des environnements située à gauche de la fenêtre de l’interpréteur de commandes (Bash).

   ![Projet FarmBeats](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>Scénario de déploiement n°1

Le programme d’installation crée le Azure AD (vous disposez des autorisations de lecture du locataire Active Directory).  

1. Téléchargez le modèle [Input.json](https://aka.ms/PPInputJsonTemplate). Incluez l’ID client d’application Azure Application et le mot de passe dans le fichier Input.json, puis enregistrez-le.
2. Ouvrez le fichier téléchargé dans un bloc-notes et remplissez-le en entrant les valeurs.

    ```json
    {
    "sku":"both",
    "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
    "datahubResourceGroup":"dummy-test-dh1",  
    "location":"westus2",  
    "datahubWebsiteName":"dummy-test-dh1",  
    "acceleratorResourceGroup":"dummy-test-acc1",  
    "acceleratorWebsiteName":"dummy-test-acc1",  
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummyuser@org1.com",
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > Vous pouvez ignorer cette étape et les entrées vous seront demandées au moment de l’exécution.

3. Enregistrez le fichier et prenez note du chemin d’accès (sur votre ordinateur local).  
4. Accédez à Azure Cloud Shell et, une fois l’authentification réussie, sélectionnez le chargement (voir l’icône en surbrillance dans l’image ci-dessous) et chargez le fichier Input.json dans Cloud Shell Storage. Vous n’avez pas besoin de transmettre le paramètre Azure AD dans le fichier json, car le programme d’installation créera et configurera l’inscription d’application Azure AD pour vous.

   ![Projet FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

5. Saisissez ou collez la « commande de déploiement » dans Cloud Shell. Veillez à modifier le chemin d’accès. du fichier Json et appuyez sur Entrée.  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Le script télécharge automatiquement toutes les dépendances et génère le déployeur. Ensuite, vous êtes invité à accepter le contrat de licence utilisateur final (CLUF) d’Azure FarmBeats.

   - Entrez « Y » si vous êtes d’accord et passez à l’étape suivante.
   - Entrez « N » si vous n’acceptez pas les termes du contrat, le déploiement va alors s’arrêter.

   Vous serez ensuite invité à entrer un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à https://microsoft.com/devicelogin à l’aide de vos informations d’identification Azure.

   > [!NOTE]
   > Le code expire au bout de 60 minutes. Lorsqu’il expire, vous pouvez redémarrer en tapant à nouveau la commande de déploiement.

6. À l’invite suivante, entrez le mot de passe du compte Sentinel.
7. Le programme d’installation valide et démarre le déploiement, qui peut prendre environ 20 minutes.
8. Une fois le déploiement réussi, vous recevrez les liens de sortie ci-dessous :
    - **URL de Data hub** : Lien Swagger pour essayer les API Azure FarmBeats.
    - **URL de l’accélérateur** : Interface utilisateur pour explorer l’accélérateur de fermes intelligentes d’Azure FarmBeats.
    - **Fichier journal du déploiement** : fichier journal créé lors du déploiement. Il sert à des fins de dépannage.

    - Entrez « Y » si vous êtes d’accord et passez à l’étape suivante.
    - Entrez « N » si vous n’acceptez pas les termes du contrat, le déploiement va alors s’arrêter.

   Vous serez ensuite invité à entrer un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à https://microsoft.com/devicelogin à l’aide de vos informations d’identification Azure.

   > [!NOTE]
    > Prenez note de ces informations et conservez le chemin d’accès du fichier journal de déploiement pour une utilisation ultérieure.


### <a name="deployment-scenario-2"></a>Scénario de déploiement n°2

L’inscription d’application Azure Active Directory existante est utilisée pour le déploiement.

1. Téléchargez [input.json](https://aka.ms/PPInputJsonTemplate), incluez-y l’ID client d’application Azure Application et le mot de passeson, puis enregistrez-le.

    ```json
       {
       "sku":"both",
       "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
       "datahubResourceGroup":"dummy-test-dh1",  
       "location":"westus2",  
       "datahubWebsiteName":"dummy-test-dh1",  
       "acceleratorResourceGroup":"dummy-test-acc1",  
       "acceleratorWebsiteName":"dummy-test-acc1",  
       "sentinelUsername":"dummy-dev",
       "notificationEmailAddress":"dummyuser@org1.com",
       "updateIfExists": true,
       "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
       "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"
       }
     ```

     Suivez le reste des étapes :

2. Prenez note du chemin d’accès vers votre fichier Input.json (sur votre ordinateur local).
3. Accédez à Azure Cloud Shell une nouvelle fois et, une fois l’authentification réussie, sélectionnez le bouton de chargement (voir l’icône en surbrillance dans l’image ci-dessous) et chargez le fichier Input.json dans Cloud Shell Storage.

    ![Projet FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

4. Saisissez ou collez la *commande de déploiement* dans Cloud Shell. Veillez à modifier le chemin d’accès. du fichier Json et appuyez sur Entrée.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Suivez le reste des étapes :

5. Le script télécharge automatiquement toutes les dépendances et génère le déployeur.
6. Vous êtes ensuite invité à lire et à accepter le contrat de licence utilisateur final (CLUF) d’Azure FarmBeats.

   - Entrez « Y » si vous êtes d’accord et passez à l’étape suivante.
   - Entrez « N » si vous n’acceptez pas les termes du contrat, le déploiement va alors s’arrêter.

7. Vous serez ensuite invité à entrer un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à https://microsoft.com/devicelogin à l’aide de vos informations d’identification Azure.
8. Le programme d’installation est maintenant validé et commence la création des ressources, qui peut prendre environ 20 minutes. Laissez la session active sur Cloud Shell pendant cette période.
9. Une fois le déploiement réussi, vous recevrez les liens de sortie ci-dessous :
   - **URL de Data hub** : Lien Swagger pour essayer les API FarmBeats.
   - **URL de l’accélérateur** : Interface utilisateur pour explorer l’accélérateur de fermes intelligentes FarmBeats.
   - **Fichier journal du déploiement** : enregistre les journaux pendant le déploiement et peut être utilisé pour les dépannages.

Si vous rencontrez des problèmes, consultez [Dépannage](troubleshoot-project-farmbeats.md).


## <a name="validate-deployment"></a>Valider le déploiement

### <a name="data-hub"></a>Data Hub

Une fois que l’installation de Data Hub est terminée, vous recevrez l’URL permettant d’accéder aux API Azure FarmBeats via l’interface Swagger au format suivant : https://\<yourdatahub-website-name>.azurewebsites.net

1. Pour vous connecter par le biais de Swagger, copiez et collez l’URL dans le navigateur.
2. Connectez-vous avec les informations d'identification Azure AD.
3. Test de validité (facultatif)

     - Possibilité de se connecter au portail Swagger à l’aide du lien Data Hub, que vous avez reçu comme sortie d’un déploiement réussi.
     - Types étendus d’API Get - Sélectionnez « Essayer / Exécuter »
     - Vous devez recevoir le code de réponse du serveur 200 et non pas une exception telle que 403 « utilisateur non autorisé ».

### <a name="accelerator"></a>Accélérateur

Une fois que l’installation de l’accélérateur est terminée, vous recevrez l’URL permettant d’accéder à l’interface utilisateur FarmBeats au format suivant : https://\<accelerator-website-name>.azurewebsites.net

1. Pour vous connecter par le biais de l’accélérateur, copiez et collez l’URL dans le navigateur.
2. Connectez-vous avec les informations d'identification Azure AD.
3. Exécutez un test de validité facultatif.

    - Vérifiez si vous pouvez vous connecter au portail de l’accélérateur à l’aide du lien reçu comme sortie d’un déploiement réussi.
    - Sélectionnez **Créer une ferme**.
    - Sous l’icône «  ? », ouvrez les guides FarmBeats à l’aide du bouton **Mise en route**.

## <a name="upgrade"></a>Mise à niveau

Les étapes de mise à niveau sont similaires à celles de la première installation. Procédez comme suit :

1. Connectez-vous au portail Azure et sélectionnez votre abonnement et votre locataire Active Directory.
2. Lancez Cloud Shell dans le volet de navigation supérieur du Portail Azure.

   ![Projet FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

3. Sélectionner un abonnement pour créer un compte de stockage et un partage de fichiers Azure Files.
4. Sélectionnez **Créer le stockage**.
5. Sélectionnez la liste déroulante des environnements située à gauche de la fenêtre de l’interpréteur de commandes (Bash).
6. Apportez des modifications à votre fichier input.json uniquement si nécessaire et chargez-le sur Azure Cloud Shell. Par exemple, vous pouvez mettre à jour votre adresse e-mail pour la notification que vous souhaitez recevoir.
7. Prenez note du chemin d’accès vers votre fichier Input.json (sur votre ordinateur local).
8. Ouvrez Azure Cloud Shell. Une fois l’authentification réussie, sélectionnez le bouton de chargement (voir l’icône en surbrillance dans l’image ci-dessous) et chargez le fichier Input.json dans Cloud Shell Storage.

   ![Projet FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

9. Saisissez ou collez la **commande de déploiement** dans Cloud Shell. Veillez à modifier le chemin d’accès. du fichier Json et appuyez sur Entrée.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    Suivez le reste des étapes :

10. Le programme d’installation demande automatiquement les entrées nécessaires au moment de l’exécution :

    - Entrez un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à https://microsoft.com/devicelogin à l’aide de vos informations d’identification Azure.

     > [!NOTE]
     > Le code expire au bout de 60 minutes. Lorsqu’il expire, vous pouvez redémarrer en tapant à nouveau la commande de déploiement.

     - Mot de passe Sentinel

11. Le programme d’installation est maintenant validé et commence la création des ressources, qui peut prendre environ 20 minutes.
12. Une fois le déploiement réussi, vous recevrez les liens de sortie ci-dessous :

    - **URL de Data hub** : Lien Swagger pour essayer les API FarmBeats.
    - **URL de l’accélérateur** : Interface utilisateur pour explorer l’accélérateur de fermes intelligentes FarmBeats.
    - **Fichier journal du déploiement** : enregistre les journaux pendant le déploiement. Il sert à des fins de dépannage.

    > [!NOTE]
    > Prenez note des liens ci-dessus et conservez le chemin d’accès du fichier journal de déploiement pour une utilisation ultérieure.

## <a name="uninstall"></a>Désinstaller l’interface

Actuellement, nous ne prenons pas en charge la désinstallation automatisée de FarmBeats à l’aide du programme d’installation. Pour supprimer le Data Hub ou l’accélérateur dans le Portail Azure, supprimez le groupe de ressources au sein duquel ces composants sont installés, ou supprimez les ressources manuellement.

Par exemple, si vous avez déployé Data Hub et l’accélérateur dans deux groupes de ressources différents, vous supprimez ces groupes de ressources comme suit :

1. Connectez-vous au portail Azure.
2. Sélectionnez votre compte en haut à droite, puis basculez vers le locataire Azure AD où vous voulez déployer Microsoft FarmBeats.

   > [!NOTE]
   > Data Hub est nécessaire pour que l’accélérateur fonctionne correctement. Nous vous déconseillons de désinstaller Data Hub sans désinstaller l’accélérateur.

3. Sélectionnez Groupes de ressources, puis tapez le nom du groupe de ressources du Data Hub ou de l’accélérateur que vous souhaitez supprimer.
4. Sélectionnez le nom du groupe de ressources. Retapez le nom pour effectuer une double vérification, puis cliquez sur Supprimer pour supprimer le groupe de ressources et toutes ses ressources sous-jacentes.
5. Vous pouvez également supprimer chaque ressource manuellement, ce qui n’est pas recommandé.
7. Pour supprimer ou désinstaller Data Hub, accédez au groupe de ressources directement sur Azure et supprimez le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous avez déployé Azure FarmBeats. Maintenant, apprenez comment [créer des fermes](manage-farms.md#create-farms).
