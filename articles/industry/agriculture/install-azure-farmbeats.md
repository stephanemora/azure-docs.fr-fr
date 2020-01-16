---
title: Installer Azure FarmBeats
description: Cet article explique comment installer Azure FarmBeats dans votre abonnement Azure
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475743"
---
# <a name="install-azure-farmbeats"></a>Installer Azure FarmBeats

Cet article explique comment installer Azure FarmBeats dans votre abonnement Azure.

Azure FarmBeats est une offre B2B (entreprise à entreprise) disponible sur la Place de marché Azure. Cette solution permet d’agréger des jeux de données agricoles provenant de différents fournisseurs et de générer des insights exploitables. Avec Azure FarmBeats, il est possible de créer des modèles d’intelligence artificielle (IA) ou de Machine Learning (ML) basés sur les jeux de données fusionnés. Les deux principaux composants d’Azure FarmBeats sont :

- **Datahub** : couche d’API qui permet l’agrégation, la normalisation et la contextualisation de divers jeux de données agricoles issus de différents fournisseurs.

- **Accélérateur** : exemple d’application web basée sur Datahub. Il vous permet d’accélérer le développement et la visualisation de votre modèle. L’accélérateur se sert des API Azure FarmBeats pour assurer la visualisation des données de capteurs ingérées sous forme de graphiques et la visualisation de la sortie du modèle sous forme de cartes.

## <a name="before-you-start"></a>Avant de commencer
### <a name="components-installed"></a>Composants installés

Quand vous installez Azure FarmBeats, voici les ressources qui sont provisionnées dans votre abonnement Azure :

| Ressources Azure installées  | Composant Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Datahub et Accélérateur      |
| App Service     |     Datahub et Accélérateur     |
| Plan App Service   | Datahub et Accélérateur  |
| ApiConnection    |  Datahub       |
| Cache Azure pour Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub et Accélérateur      |
| Compte Azure Batch    | Datahub   |
| Azure Key Vault |  Datahub et Accélérateur        |
| Compte Azure Maps       |     Accélérateur    |
| Espace de noms Event Hub    |     Datahub      |
| Application logique      |  Datahub       |
| Compte de stockage      |     Datahub et Accélérateur      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Coûts induits

Les coûts d’Azure FarmBeats représentent la somme des coûts des services Azure sous-jacents. Le prix des services Azure peut être calculé à l’aide de la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator). Le coût réel de l’installation complète varie en fonction de l’utilisation. Pour les deux composants, le coût à l’état stable est le suivant :

* Datahub – moins de 10 USD par jour
* Accélérateur – moins de 2 USD par jour

### <a name="regions-supported"></a>Régions prises en charge

Actuellement, Azure FarmBeats est pris en charge dans les environnement de cloud public dans les régions suivantes :
* Australie Est
* USA Centre
* USA Est
* USA Est 2
* USA Ouest
* USA Ouest 2
* Europe Nord
* Europe Ouest
* Asie Sud-Est

### <a name="time-taken"></a>Temps nécessaire

L’installation complète d’Azure FarmBeats, préparation et installation comprises, prend moins d’une heure.

## <a name="prerequisites"></a>Conditions préalables requises    

Avant de commencer l’installation réelle d’Azure FarmBeats, vous devez effectuer les étapes suivantes :

### <a name="create-sentinel-account"></a>Créer un compte Sentinel
Votre installation Azure FarmBeats vous fait bénéficier de l’imagerie satellite gratuite de la mission du satellite [Sentinel-2](https://scihub.copernicus.eu/) de l’Agence spatiale européenne pour votre exploitation agricole. Pour configurer cette installation, vous avez besoin d’un compte Sentinel.

Suivez les étapes permettant de créer un compte gratuit Sentinel :

1. Accédez à la page d’[inscription](https://scihub.copernicus.eu/dhus/#/self-registration) officielle.
2. Fournissez les informations nécessaires (prénom, nom, nom d’utilisateur, mot de passe et ID d’e-mail) et complétez le formulaire.
3. Un lien de vérification est ensuite envoyé à l’ID d’e-mail enregistré. Sélectionnez le lien fourni dans l’e-mail et effectuez la vérification.

Votre processus d’inscription est terminé une fois que vous avez effectué la vérification. Notez votre **nom d’utilisateur Sentinel** et votre **mot de passe Sentinel**.

### <a name="decide-subscription-and-region"></a>Choisir un abonnement et une région

Vous aurez besoin de l’ID d’abonnement Azure et de la région dans laquelle vous souhaitez installer Azure FarmBeats. Choisissez l’une des régions listées dans la section [Régions prises en charge](#regions-supported).

Notez l’**ID d’abonnement Azure** et la **région Azure**.

### <a name="verify-permissions"></a>Vérifier les autorisations

Vous devez vérifier si vous disposez de privilèges et d’autorisations suffisants dans le locataire Azure dans lequel vous comptez installer Azure FarmBeats.

Vous pouvez vérifier vos autorisations d’accès sur le portail Azure en suivant les instructions relatives au [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/check-access).

Pour installer Azure FarmBeats, vous avez besoin des autorisations suivantes :
- Locataire – Accès en lecture
- Abonnement – Contributeur ou propriétaire
- Groupe de ressources – Propriétaire.

Par ailleurs, Azure FarmBeats exige des inscriptions d’applications Azure Active Directory. Il existe deux façons de procéder à l’installation d’Azure AD exigée :

**Cas n° 1** : vous disposez d’autorisations d’**écriture** dans le locataire Azure sur lequel vous effectuez l’installation. Ce cas signifie que vous disposez des autorisations nécessaires pour créer l’inscription d’application AAD de manière dynamique pendant l’installation.

Vous pouvez passer directement à la section [Effectuer l’inscription sur la Place de marché](#complete-azure-marketplace-sign-up).


**Cas n° 2** : vous NE disposez PAS d’autorisations d’**écriture** dans le locataire Azure. Ce cas de figure est courant quand vous essayez d’installer Azure FarmBeats dans l’abonnement Azure de votre entreprise et que votre accès en **écriture** se limite uniquement au groupe de ressources dont vous êtes propriétaire.
Dans ce cas, demandez à votre administrateur informatique de suivre les étapes ci-dessous pour générer et effectuer automatiquement l’inscription d’application Azure AD sur le portail Azure.

1. Téléchargez et extrayez le [script générateur d’application AAD](https://aka.ms/FarmBeatsAADScript) sur votre ordinateur local.
2. Connectez-vous au portail Azure et sélectionnez votre abonnement et votre locataire Azure AD.
3. Lancez Cloud Shell dans la barre d’outils en haut du Portail Azure.

    ![Projet FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Choisissez PowerShell comme expérience shell par défaut. À leur première utilisation, les utilisateurs sont invités à sélectionner un abonnement et à créer un compte de stockage. Effectuez l’installation en suivant les instructions.
5. Chargez le script (de l’étape 1) dans Cloud Shell et notez l’emplacement du fichier chargé.

    > [!NOTE]
    > Par défaut, le fichier est chargé dans votre répertoire de base.

6. Accédez au répertoire de base en utilisant la commande « cd », puis exécutez le script suivant :

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Entrez le nom du **site web Datahub** et le nom du **site web Accélérateur**. Note la sortie du script et partagez-la avec la personne qui installe Azure FarmBeats.

Une fois que votre administrateur informatique vous a fourni les détails nécessaires, notez l’**ID client AAD, la clé secrète client AAD, le nom du site web Datahub et le nom du site web Accélérateur**.

   > [!NOTE]
   > Si vous suivez les instructions du cas n° 2, n’oubliez pas d’ajouter l’ID client AAD et la clé secrète client AAD comme paramètres distincts dans le [fichier de paramètres](#prepare-parameters-file)

Maintenant, vous disposez de toutes les informations nécessaires pour passer à la section suivante.

### <a name="complete-azure-marketplace-sign-up"></a>Effectuer l’inscription sur la Place de marché Azure

Vous devez vous inscrire à l’offre Azure FarmBeats sur la Place de marché Azure avant de commencer l’installation basée sur Cloud Shell. Suivez les étapes ci-dessous pour effectuer l’inscription :

1.  Connectez-vous au portail Azure. Sélectionnez votre compte en haut à droite, puis basculez vers le locataire Azure AD où vous voulez installer Azure FarmBeats.

2.  Accédez à la Place de marché Azure sur le portail et recherchez-y **Azure FarmBeats**.

3.  Une nouvelle fenêtre s’affiche avec une vue d’ensemble d’Azure FarmBeats. Sélectionnez le bouton **Créer**.

4.  Une nouvelle fenêtre s’affiche. Terminez le processus d’inscription en choisissant l’abonnement, le groupe de ressources et l’emplacement où vous souhaitez installer Azure FarmBeats.

5.  Une fois que les détails entrés ont été validés, sélectionnez **OK**. La page Conditions d’utilisation apparaît. Passez en revue les conditions et sélectionnez **Créer** pour terminer le processus d’inscription.

Cette étape met fin au processus d’inscription sur la Place de marché Azure. Vous êtes maintenant prêt à vous lancer dans la préparation du fichier de paramètres.

### <a name="prepare-parameters-file"></a>Préparer le fichier de paramètres
La dernière étape prérequise consiste à créer un fichier JSON qui servira d’entrée pendant l’installation Cloud Shell. Les paramètres du fichier JSON doivent être remplacés par les valeurs appropriées.

Vous trouverez ci-après un exemple de fichier JSON. Téléchargez l’exemple et mettez à jour les détails nécessaires.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Vous pouvez vous reporter au tableau des paramètres ci-dessous pour mieux comprendre chacun des paramètres.

| Paramètre | Valeur|
|--- | ---|
|sku  | Permet à l’utilisateur de choisir d’installer à la fois Datahub et l’accélérateur ou simplement Datahub. Pour installer uniquement Datahub, utilisez « databhub ». Pour installer Datahub et l’accélérateur, utilisez « both »|
|subscriptionId | Spécifie l’abonnement Azure pour l’installation d’Azure FarmBeats|
|datahubResourceGroup| Spécifie le nom du groupe de ressources pour vos ressources Datahub. Entrez ici le nom du groupe de ressources que vous avez créé sur la Place de marché Azure|
|location |Localisation/région Azure où vous souhaitez installer Azure FarmBeats|
|datahubWebsiteName  | Préfixe d’URL unique pour votre application web Datahub |
|acceleratorResourceGroup  | Spécifie le nom du groupe de ressources pour vos ressources Accélérateur|
|acceleratorWebsiteName |Préfixe d’URL unique pour votre application web Accélérateur|
|sentinelUsername | Nom d’utilisateur pour accéder à l’imagerie satellite Sentinel|
|notificationEmailAddress  | Adresse e-mail qui recevra les notifications pour les alertes que vous configurez dans votre instance Datahub|
|updateIfExists| (Facultatif) Paramètre à inclure dans le fichier de paramètres seulement si vous souhaitez mettre à niveau une instance Azure FarmBeats existante. Pour une mise à niveau, d’autres détails, comme les noms et les emplacements des groupes de ressources doivent être identiques|
|aadAppClientId | (Facultatif) Paramètre à inclure dans le fichier de paramètres seulement si vous utilisez une application AAD précréée. Reportez-vous au cas n° 2 dans la section [Vérifier les autorisations](#verify-permissions) pour plus de détails |
|aadAppClientSecret  | (Facultatif) Paramètre à inclure dans le fichier de paramètres seulement si vous utilisez une application AAD précréée. Reportez-vous au cas n° 2 dans la section [Vérifier les autorisations](#verify-permissions) pour plus de détails|

Sur la base du tableau ci-dessus et de l’exemple de fichier JSON, créez votre fichier JSON de paramètres et enregistrez-le sur votre ordinateur local.

## <a name="install"></a>Installer

L’installation réelle des ressources Azure FarmBeats se produit dans l’interface de ligne de commande Cloud Shell basée sur navigateur avec l’environnement Bash. Suivez les instructions ci-dessous pour installer Azure FarmBeats :

1. Connectez-vous au portail Azure. Sélectionnez l’abonnement et le locataire Azure où vous souhaitez installer Azure FarmBeats.
2. Lancez **Cloud Shell** à partir de la barre d’outils en haut à droite du portail Azure.
3. Choisissez Bash comme expérience shell par défaut. Sélectionnez le bouton **Charger** (mis en évidence dans l’image ci-dessous), puis chargez le fichier de paramètres JSON préparé.

      ![Projet FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. **Copiez** la commande ci-dessous et **remplacez \<username>** par la valeur appropriée de façon à faire pointer la commande vers le chemin du fichier téléchargé.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Exécutez la commande modifiée pour lancer le processus d’installation. Vous êtes alors invité à :
 - Accepter les termes du contrat de **licence Azure FarmBeats**. Entrez « Y » pour passer à l’étape suivante si vous acceptez les conditions d’utilisation. Entrez « N » pour mettre fin à l’installation si vous n’acceptez pas les conditions d’utilisation.

 - Vous êtes ensuite invité à entrer un jeton d’accès pour le déploiement. Copiez le code généré et connectez-vous à la [page de connexion de l’appareil](https://microsoft.com/devicelogin) avec vos **informations d’identification Azure**.

 - Une fois la connexion établie, le programme d’installation vous invite à entrer le mot de passe de votre compte Sentinel. Entrez le **mot de passe de votre compte Sentinel**.

6. Le fichier de paramètres est validé et l’installation des ressources Azure démarre. L’installation prend environ **25 minutes**.    
> [!NOTE]
> Les sessions Cloud Shell inactives expirent au bout de **20 minutes**. Veillez à maintenir la session Cloud Shell active pendant que le programme d’installation déploie les ressources Azure. Si la session expire, vous devrez recommencer le processus d’installation.

Une fois l’installation terminée, vous recevez les liens de sortie suivants :
* **URL de Datahub** : lien Swagger pour accéder aux API Datahub.
* **URL de l’accélérateur** : application web permettant d’explorer l’accélérateur Azure FarmBeats.
* **Fichier journal du programme d’installation** : fichier journal contenant les détails de l’installation. Ce fichier journal peut servir à résoudre les problèmes de l’installation, si nécessaire.

Vous pouvez vérifier que votre installation d’Azure FarmBeats a bien abouti en effectuant les vérifications suivantes :

**Datahub**
1. Connectez-vous à l’URL de l’accélérateur fournie (au format **https://\<nom-siteweb-datahub-personnel>.azurewebsites.net/swagger**) avec vos informations d’identification Azure.
2. Vous devez être en mesure de voir les différents objets d’API FarmBeats et d’effectuer des opérations REST sur les API.

**Accélérateur**
1. Connectez-vous à l’URL de l’accélérateur fournie (au format **https://\<nom-siteweb-accelerateur-personnel>.azurewebsites.net/swagger**) avec vos informations d’identification Azure.
2. Vous devez être en mesure de voir l’interface utilisateur de l’accélérateur avec une option permettant de créer des exploitations agricoles dans votre navigateur.

Le fait que vous puissiez effectuer les opérations ci-dessus indique que l’installation d’Azure FarmBeats a abouti.

## <a name="upgrade"></a>Mettre à niveau
Dans la préversion publique, pour mettre à niveau une installation existante d’Azure FarmBeats, vous devez exécuter à nouveau la commande d’installation dans Cloud Shell, avec un paramètre « **updateIfExists** » supplémentaire dans le fichier de paramètres défini sur « **true** ». Reportez-vous à la dernière ligne de l’exemple JSON ci-dessous pour le paramètre de mise à jour.

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
La commande met à jour l’installation d’Azure FarmBeats existante vers la dernière version et fournit les liens de sortie.

## <a name="uninstall"></a>Désinstaller l’interface

Pour désinstaller Azure FarmBeats Datahub ou l’accélérateur, effectuez les étapes suivantes :

1.  Connectez-vous au portail Azure et **supprimez les groupes de ressources** où sont installés ces composants.

2.  Accédez à Azure Active Directory et **supprimez l’application Azure AD** liée à l’installation d’Azure FarmBeats. Cela a pour effet de supprimer l’installation d’Azure FarmBeats de votre abonnement Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous avez découvert comment installer Azure FarmBeats dans votre abonnement Azure. À présent, découvrez comment [ajouter des utilisateurs](manage-users-in-azure-farmbeats.md#manage-users) à votre instance Azure FarmBeats.
