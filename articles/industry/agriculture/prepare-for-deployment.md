---
title: Déployer Azure FarmBeats
description: Décrit comment déployer FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0fc7eb6c3b582c4fc95d78397c4cb2820ebb4ea8
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534245"
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

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Créer une offre Azure FarmBeats sur la place de marché

Suivez ces étapes pour créer une offre Azure FarmBeats sur la place de marché :

1. Connectez-vous au Portail Azure et choisissez votre compte dans le coin supérieur droit, puis basculez vers le locataire Azure AD où vous voulez déployer Microsoft Azure FarmBeats.
2. Azure FarmBeats est disponible sur la Place de marché Azure. Dans la page de la Place de marché, sélectionnez « Obtenir maintenant ».
3. Sélectionnez Créer et entrez les informations suivantes :
    - Le nom de l’abonnement.
    - Un nom de groupe de ressources existant (groupe de ressources vide uniquement). Vous pouvez également créer un nouveau groupe de ressources pour le déploiement d’Azure FarmBeats. Notez ce groupe de ressources pour les sections suivantes.
4. Indiquez la région où vous souhaitez installer Azure FarmBeats. Actuellement, Azure FarmBeats est pris en charge dans les régions suivantes : Centre des États-Unis, Europe de l’Ouest, Est des États-Unis 2, Europe du Nord, Ouest des États-Unis, Asie du sud-est, Est des États-Unis, Est de l’Australie, Ouest des États-Unis 2.
5. Sélectionnez **OK**.
La page Conditions d’utilisation apparaît. Passez en revue les termes standards de la place de marché ou sélectionnez le lien hypertexte pour passer en revue les conditions d’utilisation.
6. Sélectionnez **Fermer**, la case à cocher « J’accepte », puis sélectionnez **Créer**.
7. Vous avez maintenant correctement signé le contrat de licence utilisateur final (CLUF) d’Azure FarmBeats sur la place de marché.  
7. Pour poursuivre le déploiement, suivez les étapes suivantes de ce guide.

> [!NOTE]
> La saisie semi-automatique de l’offre dans la Place de marché Azure ne termine qu’une partie de la configuration. Suivez les instructions ci-dessous pour terminer le déploiement d’Azure FarmBeats dans votre abonnement Azure.

## <a name="prepare"></a>Préparation

Pour déployer Azure FarmBeats, vous avez besoin des autorisations suivantes :

- Locataire : Accès en lecture
- Abonnement : contributeur ou propriétaire
- Groupe de ressources : propriétaire

## <a name="before-you-begin"></a>Avant de commencer

Avant de lancer le déploiement, assurez-vous que vous disposez des éléments suivants :

- Compte Sentinel
- Inscription d’application Azure Active Directory (AD)

## <a name="create-a-sentinel-account"></a>Créer un compte Sentinel    

Un compte avec Sentinel vous aide à télécharger l’imagerie satellite Sentinel depuis son site Web officiel sur votre appareil. Pour créer un compte gratuit, effectuez les étapes suivantes :

Accédez à https://scihub.copernicus.eu/dhus/#/self-registration Sur la page d’inscription, indiquez le prénom, le nom, le nom d’utilisateur, le mot de passe et l’adresse e-mail.
Un e-mail de vérification sera envoyé à l’adresse e-mail inscrite pour confirmation. Cliquez sur le lien et confirmez. Votre processus d’inscription est terminé.

## <a name="create-azure-ad-app-registration"></a>Créer une inscription d’application Azure AD

Pour l’authentification et l’autorisation sur Azure FarmBeats, vous devez disposer d’une inscription d’application Azure Active Directory qui :

- Cas 1 : Le programme d’installation peut créé automatiquement (à condition que vous disposiez des autorisations d’accès de groupe de ressources, du locataire et de l’abonnement nécessaires).
- Cas 2 : Vous pouvez créer et configurer avant de déployer Azure FarmBeats (nécessite des étapes manuelles).

**Cas 1** : Si vous avez accès à la création d’une inscription d’application AAD, vous pouvez ignorer cette étape et laisser le programme d’installation créer l’inscription de l’application. Passez à la section suivante : [Préparer le fichier input.json](#prepare-input-json-file)

Si vous disposez déjà d’un abonnement, vous pouvez passer directement à la procédure suivante.

**Cas n° 2** : Cette méthode est l’étape par défaut lorsque vous n’avez pas les droits suffisants pour créer et configurer une inscription d’application Azure AD dans votre abonnement. Demandez à votre administrateur d’utiliser le [script personnalisé](https://aka.ms/FarmBeatsAADScript), qui aidera l’administrateur informatique à générer et configurer automatiquement l’inscription d’application Azure AD sur le portail Azure. En guise de sortie de l’exécution de ce script personnalisé à l’aide de l’environnement PowerShell, l’administrateur informatique doit partager un ID client et un mot de passe d’application Azure Active Directory avec vous. Prenez note de ces valeurs.

Procédez comme suit pour exécuter le script d’inscription d’application Azure AD :

1. Téléchargez le [script](https://aka.ms/FarmBeatsAADScript).
2. Connectez-vous au portail Azure et sélectionnez votre abonnement et votre locataire Active Directory.
3. Lancez Cloud Shell dans le volet de navigation supérieur du Portail Azure.

    ![Projet FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)


4. La première fois, les utilisateurs doivent sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files. Sélectionnez **Créer le stockage**.
5. La première fois, les utilisateurs sont invités à choisir l’expérience d’interpréteur de commandes préféré, Bash ou PowerShell. Choisissez PowerShell.
6. Chargez le script (de l’étape 1) dans Cloud Shell et notez l’emplacement du fichier chargé.

    > [!NOTE]
    > Par défaut, il est chargé dans votre répertoire de base.

    Utilisez le script suivant :

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Notez l’ID d’application Azure AD et la clé secrète client à partager avec la personne déployant Azure FarmBeats.

### <a name="prepare-input-json-file"></a>Préparer le fichier input.json

Dans le cadre de l’installation, créez un fichier input.json comme suit :

```json
    {  
       "sku":"both",
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
       "datahubResourceGroup":"dummy-test-dh1",
       "location":"westus2",
       "datahubWebsiteName":"dummy-test-dh1",
       "acceleratorResourceGroup":" dummy-test-acc1",
       "acceleratorWebsiteName":" dummy-test-acc1",
       "sentinelUsername":"dummy-dev",
       "notificationEmailAddress":"dummy@yourorg.com",
       "updateIfExists":true
    }
```

Ce fichier est votre fichier d’entrée pour Azure Cloud Shell et les paramètres dont les valeurs sont utilisées pendant l’installation. Tous les paramètres du code JSON doivent être remplacés par les valeurs appropriées ou supprimés. S’ils sont supprimés, le programme d’installation vous invitera à les entrer au cours de l’installation.


Passez en revue les paramètres avant de préparer le fichier.

|Commande | Description|
|--- | ---|
|sku  | Offre la possibilité de télécharger l’un ou l’autre des composants d’Azure FarmBeats ou les deux. Spécifie les composants à télécharger. Pour installer uniquement Data Hub, utilisez « onlydatabhub ». Pour installer Data Hub et l’accélérateur, utilisez « both ».|
|subscriptionId | Spécifie l’abonnement pour l’installation d’Azure FarmBeats|
|datahubResourceGroup| Nom du groupe de ressources pour les ressources Data Hub|
|location |Emplacement où vous souhaitez créer les ressources|
|acceleratorWebsiteName |Préfixe d’URL unique pour nommer votre Data Hub|
|acceleratorResourceGroup  | Préfixe d’URL unique à utiliser pour nommer votre site web d’accélérateur|
|datahubWebsiteName  | Préfixe d’URL unique à utiliser pour nommer votre site web Data Hub |
|sentinelUsername | Nom d’utilisateur pour se connecter : https://scihub.copernicus.eu/dhus/#/self-registration.|
|notificationEmailAddress  | Adresse e-mail pour recevoir les notifications des alertes que vous configurez dans Data Hub.|
|updateIfExists|[Facultatif] Paramètre à inclure dans input.json seulement si vous souhaitez mettre à niveau une instance Azure FarmBeats existante. Pour la mise à niveau, les autres détails, tels que les noms des groupes de ressources, les emplacements, etc., doivent être identiques.|
|aadAppClientId | [**facultatif**] Paramètre à inclure dans Input.Json uniquement si une application Azure AD existe déjà.  |
|aadAppClientSecret  | [**facultatif**] Paramètre à inclure dans Input.Json uniquement si une application Azure AD existe déjà.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Déployer dans un ligne de commande basée sur un navigateur (Cloud Shell)

Dans le cadre du flux de travail de la Place de marché ci-dessus, vous devez avoir créé un groupe de ressources et signé le contrat de licence utilisateur final, qui peut être revu de nouveau dans le cadre du déploiement réel. Le déploiement peut être effectué via Azure Cloud Shell (ligne de commande basée sur un navigateur) à l’aide de l’environnement Bash. Passez aux sections suivantes pour effectuer un déploiement via Cloud Shell.

> [!NOTE]
> Les sessions Cloud Shell inactives expirent au bout de 20 minutes. Essayez d’effectuer le déploiement dans ce délai.

1. Connectez-vous au Portail Azure et sélectionnez l’abonnement et le locataire Active Directory désirés.
2. Lancez Cloud Shell dans le volet de navigation supérieur du Portail Azure.
3. Si vous utilisez Cloud Shell pour la première fois, vous êtes invité à sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files.
4. Sélectionnez **Créer un stockage**.  

Sélectionnez l’environnement Bash (et non PowerShell).

## <a name="deployment-scenario-1"></a>Scénario de déploiement n°1

Le programme d’installation crée l’inscription d’application Azure AD (cas 1 ci-dessus).

1. Copiez le modèle suivant et nommez-le input.json.  
Exemple d’entrée JSON :

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

2. Enregistrez le fichier et prenez note du chemin d’accès (sur votre ordinateur local).
3. Accédez à Azure Cloud Shell et, une fois l’authentification réussie, sélectionnez le chargement (voir l’icône en surbrillance dans l’image ci-dessous) et chargez le fichier Input.json dans Cloud Shell Storage.  

    ![Projet FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

4. Accédez à votre répertoire de base dans Cloud Shell. Par défaut, il s’agit de /home/<username>
5. Tapez ou collez la commande suivante dans Cloud Shell. Veillez à modifier le chemin d’accès. du fichier Json et appuyez sur Entrée.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Le programme d’installation télécharge automatiquement toutes les dépendances et génère le système de déploiement. Vous êtes invité à accepter le contrat de licence utilisateur final (CLUF) d’Azure FarmBeats.

     - Entrez « Y » si vous êtes d’accord et passez à l’étape suivante.
     - Entrez « N » si vous n’acceptez pas les termes du contrat, le déploiement va alors s’arrêter.

6. Vous serez ensuite invité à entrer un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à https://microsoft.com/devicelogin à l’aide de vos informations d’identification Azure.

    > [!NOTE]
    > Le jeton expire au bout de 60 minutes. Lorsqu’il expire, vous pouvez redémarrer en tapant à nouveau la commande de déploiement.

7. Quand vous y êtes invité, entrez le mot de passe de votre compte Sentinel.
8. Le programme d’installation valide et démarre le déploiement, qui peut prendre environ 20 minutes.
9. Une fois le déploiement réussi, vous recevrez les liens de sortie ci-dessous :

 - **URL de Data hub** : Lien Swagger pour essayer les API Azure FarmBeats.
 - **URL de l’accélérateur** : Interface utilisateur pour explorer l’accélérateur de fermes intelligentes d’Azure FarmBeats.
 - **Fichier journal du déploiement** : fichier journal créé lors du déploiement. Il peut être utilisé à des fins de dépannage, si nécessaire.

## <a name="deployment-scenario-2"></a>Scénario de déploiement n°2

L’inscription d’application Azure Active Directory existante est utilisée pour le déploiement (cas 2 ci-dessus).

1. Copiez le fichier JSON ci-dessous, qui inclut l’ID client et le mot de passe Azure Application dans le fichier input.json, puis enregistrez-le.

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

4. Accédez à votre répertoire de base dans Cloud Shell. Par défaut, il s’agit de /home/<username>
5. Tapez ou collez la commande suivante dans Cloud Shell. Veillez à modifier le chemin d’accès. du fichier Json et appuyez sur Entrée.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

Suivez les instructions qui s’affichent à l’écran.

6. Le script télécharge automatiquement toutes les dépendances et génère le système de déploiement.
7. Vous serez invité à lire et à accepter le contrat de licence utilisateur final (CLUF) d’Azure FarmBeats.

    - Entrez « Y » si vous êtes d’accord et passez à l’étape suivante.
    - Entrez « N » si vous n’acceptez pas les termes du contrat, le déploiement va alors s’arrêter.

8. Vous serez invité à entrer un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à https://microsoft.com/devicelogin à l’aide de vos informations d’identification Azure.
9. Le programme d’installation est maintenant validé et commence la création des ressources, qui peut prendre environ 20 minutes. Laissez la session active sur Cloud Shell pendant cette période.
10. Une fois le déploiement réussi, vous recevrez les liens de sortie ci-dessous :

 - **URL de Data hub** : Lien Swagger pour essayer les API Azure FarmBeats.
 - **URL de l’accélérateur** : Interface utilisateur pour explorer l’accélérateur Azure FarmBeats.
 - **Fichier journal du système de déploiement** : Fichier journal créé pendant le déploiement. Il peut être utilisé à des fins de dépannage, si nécessaire.

Si vous rencontrez des problèmes, consultez [Dépannage](troubleshoot-project-farmbeats.md).


## <a name="validate-deployment"></a>Valider le déploiement

### <a name="data-hub"></a>Hub de données

Une fois l’installation du hub de données terminée, vous recevrez l’URL d’accès aux API Azure FarmBeats via l’interface Swagger au format : https://\<yourdatahub-website-name>.azurewebsites.net/swagger

1. Pour vous connecter par le biais de Swagger, copiez et collez l’URL dans le navigateur.
2. Connectez-vous avec les informations d'identification Azure AD.
3. Test de validité (facultatif)

     - Possibilité de se connecter au portail Swagger à l’aide du lien Data Hub, que vous avez reçu comme sortie d’un déploiement réussi.
     - Types étendus d’API Get - Sélectionnez « Essayer / Exécuter »
     - Vous devez recevoir le code de réponse du serveur 200 et non pas une exception telle que 403 « utilisateur non autorisé ».

### <a name="accelerator"></a>Accélérateur

Une fois l’installation de l’accélérateur terminée, vous recevrez l’URL d’accès à l’interface Azure FarmBeats au format : https://\<accelerator-website-name>.azurewebsites.net

1. Pour vous connecter par le biais de l’accélérateur, copiez et collez l’URL dans le navigateur.
2. Connectez-vous avec les informations d'identification Azure AD.

## <a name="upgrade"></a>Mise à niveau

Les étapes de mise à niveau sont similaires à celles de la première installation. Procédez comme suit :

1. Connectez-vous au portail Azure et sélectionnez votre abonnement et votre locataire Active Directory.
2. Lancez Cloud Shell dans le volet de navigation supérieur du Portail Azure.

   ![Projet FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

3. Sélectionnez l’environnement « Bash » dans la liste déroulante à gauche de la fenêtre de l’interpréteur.
4. Apportez des modifications à votre fichier input.json uniquement si nécessaire et chargez-le sur Azure Cloud Shell. Par exemple, vous pouvez mettre à jour votre adresse e-mail pour la notification que vous souhaitez recevoir.
5. Chargez le fichier input.json dans Azure Cloud Shell.
6. Tapez ou collez les deux commandes suivantes dans Cloud Shell. Veillez à modifier le chemin vers le fichier input.json et appuyez sur Entrée.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
Suivez les instructions qui s’affichent à l’écran :

7. Le programme d’installation demande automatiquement les entrées nécessaires au moment de l’exécution :
8. Entrez un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à https://microsoft.com/devicelogin à l’aide de vos informations d’identification Azure.
9. Mot de passe Sentinel
10. Le programme d’installation est maintenant validé et commence la création des ressources, qui peut prendre environ 20 minutes.
11. Une fois le déploiement réussi, vous recevrez les liens de sortie ci-dessous :
 - **URL de Data hub** : Lien Swagger pour essayer les API Azure FarmBeats.
 - **URL de l’accélérateur** : Interface utilisateur pour explorer l’accélérateur Azure FarmBeats.
 - **Fichier journal du système de déploiement** : enregistre les journaux pendant le déploiement. Il sert à des fins de dépannage.

> [!NOTE]
> Notez les valeurs ci-dessus pour pouvoir les utiliser ultérieurement.


## <a name="uninstall"></a>Désinstaller l’interface

La désinstallation automatique d’Azure FarmBeats n’est pas actuellement prise en charge à l’aide du programme d'installation. Pour supprimer le Data Hub ou l’accélérateur dans le Portail Azure, supprimez le groupe de ressources au sein duquel ces composants sont installés, ou supprimez les ressources manuellement.

Par exemple, si vous avez déployé Data Hub et l’accélérateur dans deux groupes de ressources différents, vous supprimez ces groupes de ressources comme suit :

1. Connectez-vous au portail Azure.
2. Sélectionnez votre compte en haut à droite, puis basculez vers l’abonné Azure AD où vous voulez déployer Microsoft FarmBeats.

   > [!NOTE]
   > Data Hub est nécessaire pour que l’accélérateur fonctionne correctement. Nous vous déconseillons de désinstaller Data Hub sans désinstaller l’accélérateur.

3. Sélectionnez Groupes de ressources, puis tapez le nom du groupe de ressources du Data Hub ou de l’accélérateur que vous souhaitez supprimer.
4. Sélectionnez le nom du groupe de ressources. Tapez de nouveau le nom pour effectuer une double vérification, puis sélectionnez Supprimer pour supprimer le groupe de ressources et toutes ses ressources sous-jacentes.
5. Vous pouvez également supprimer chaque ressource manuellement, ce qui n’est pas recommandé.
7. Pour supprimer ou désinstaller Data Hub, accédez au groupe de ressources directement sur Azure et supprimez le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous avez déployé Azure FarmBeats. Maintenant, apprenez comment [créer des fermes](manage-farms.md#create-farms).
