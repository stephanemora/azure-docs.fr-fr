---
title: Créer une application de conteneur Linux sur Service Fabric dans Azure | Microsoft Docs
description: Dans ce démarrage rapide, vous allez concevoir une image Docker avec votre application, envoyez l’image vers un registre de conteneurs, puis déployez votre conteneur sur un cluster Service Fabric.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b0ded0fb274f6b64935ddaba75abf23a94063120
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452649"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Démarrage rapide : Déployer des conteneurs Linux sur Service Fabric

Azure Service Fabric est une plateforme de systèmes distribués pour le déploiement et la gestion de microservices et conteneurs extensibles et fiables.

Ce démarrage rapide montre comment déployer des conteneurs Linux sur un cluster Service Fabric. Une fois le déploiement terminé, vous disposez d’une application de vote composée d’un serveur web frontal Python et d’un serveur principal Redis en cours d’exécution dans un cluster Service Fabric. Vous apprendrez également comment basculer une application et mettre à l’échelle une application dans votre cluster.

![Page web de l’application de vote][quickstartpic]

Dans ce démarrage rapide, vous utilisez l’environnement Bash dans Azure Cloud Shell pour exécuter des commandes CLI de Service Fabric. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si c’est la première fois que vous utilisez Cloud Shell, vous êtes invité à configurer votre partage de fichiers `clouddrive`. Vous pouvez accepter les valeurs par défaut ou joindre un partage de fichiers existant. Pour plus d’informations, consultez [Configurer un partage de fichiers `clouddrive`](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Obtenir le package d’application

Pour déployer des conteneurs sur Service Fabric, vous avez besoin d’un ensemble de fichiers manifestes (la définition d’application), qui décrivent les conteneurs individuels et l’application.

Dans Cloud Shell, utilisez git pour cloner une copie de la définition d’application, puis remplacez les répertoires par le répertoire `Voting` dans votre clone.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Créer un cluster Service Fabric

Pour déployer l’application sur Azure, vous avez besoin d’un cluster Service Fabric pour exécuter l’application. Les clusters tiers permettent de créer rapidement et facilement un cluster Service Fabric. Les clusters tiers sont des clusters Service Fabric gratuits, limités dans le temps et hébergés sur Azure. En outre, ils sont exécutés par l’équipe Service Fabric. Vous pouvez utiliser des clusters tiers pour déployer des applications et en apprendre davantage sur la plateforme. Le cluster utilise un seul certificat auto-signé pour la sécurité de nœud à nœud et de client à nœud.

Connectez-vous et rejoignez un [cluster Linux](http://aka.ms/tryservicefabric). Téléchargez le certificat PFX sur votre ordinateur en cliquant sur le lien **PFX**. Cliquez sur le lien **Lisez-moi** pour obtenir le mot de passe du certificat et les instructions associées à la configuration de plusieurs environnements pour utiliser le certificat. Gardez les pages **d’accueil** et **Lisez-moi** ouvertes, car vous allez suivre certaines des instructions qui y sont affichées dans la procédure ci-après.

> [!Note]
> Le nombre de clusters tiers disponibles par heure est limité. Si vous obtenez une erreur lors de votre inscription à un cluster tiers, attendez un peu et réessayez. Vous pouvez également suivre la procédure décrite dans [Didacticiel : Déployer un cluster Service Fabric Linux dans un réseau virtuel Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) pour créer un cluster dans votre abonnement.
>
>Si vous créez votre propre cluster, notez que le service web frontal est configuré pour écouter le trafic entrant sur le port 80. Assurez-vous que ce port est ouvert dans votre cluster. (Si vous utilisez un cluster tiers, ce port est ouvert.)
>

## <a name="configure-your-environment"></a>Configurer votre environnement

Service Fabric propose plusieurs outils que vous pouvez utiliser pour gérer un cluster et ses applications :

- Service Fabric Explorer, un outil reposant sur un navigateur.
- L’interface CLI Service Fabric, qui s’exécute sur Azure CLI 2.0.
- Commandes PowerShell.

Dans ce démarrage rapide, vous utilisez la CLI Service Fabric dans Cloud Shell et Service Fabric Explorer. Les sections suivantes vous montrent comment installer le certificat nécessaire pour vous connecter à votre cluster sécurisé grâce à ces outils.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Configurer le certificat pour la CLI Service Fabric

Pour utiliser la CLI dans Cloud Shell, vous devez charger le fichier PFX du certificat dans Cloud Shell et l’utiliser pour créer un fichier PEM.

1. Pour charger le certificat dans votre répertoire de travail actuel dans Cloud Shell, faites glisser le fichier PFX du certificat du dossier dans lequel il a été téléchargé sur votre ordinateur et déposez-le dans votre fenêtre Cloud Shell.

2. Pour convertir le fichier PFX en fichier PEM, utilisez la commande suivante. (Pour les clusters tiers, vous pouvez copier une commande spécifique à votre fichier PFX et le mot de passe dans les instructions de la page **Lisez-moi**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ```

### <a name="configure-certificate-for-service-fabric-explorer"></a>Configurer le certificat pour Service Fabric Explorer

Pour utiliser Service Fabric Explorer, vous devez importer le fichier PFX du certificat que vous avez téléchargé depuis le site du cluster tiers dans votre magasin de certificats (Windows ou Mac) ou dans le navigateur lui-même (Ubuntu). Vous avez besoin du mot de passe de la clé privée PFX, que vous pouvez obtenir depuis la page **Lisez-moi**.

Utilisez la méthode qui vous convient le mieux pour importer les certificats sur votre système. Par exemple :

- Sur Windows : double-cliquez sur le fichier PFX et suivez les invites pour installer le certificat dans votre magasin personnel, `Certificates - Current User\Personal\Certificates`. Vous pouvez également utiliser la commande PowerShell dans les instructions **Lisez-moi**.
- Sur Mac : double-cliquez sur le fichier PFX et suivez les invites pour installer le certificat dans votre application Keychain.
- Sur Ubuntu : Mozilla Firefox est le navigateur par défaut dans Ubuntu 16.04. Pour importer le certificat dans Firefox, cliquez sur le bouton de menu dans le coin supérieur droit de votre navigateur, puis cliquez sur **Options**. Sur la page **Préférences**, recherchez « certificats » via le champ de recherche. Cliquez sur **Afficher les certificats**, sélectionnez l’onglet **Your Certificates** (Vos certificats), cliquez sur **Importer** et suivez les invites pour importer le certificat.

   ![Installer le certificat sur Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Déployer l’application Service Fabric

1. Dans Cloud Shell, connectez-vous au cluster Service Fabric dans Azure à l’aide de la CLI. Le point de terminaison est le point de terminaison de gestion de votre cluster. Dans la section précédente, vous avez créé le fichier PEM. (Pour les clusters tiers, vous pouvez copier une commande spécifique à votre fichier PEM et le point de terminaison de gestion dans les instructions de la page **Lisez-moi**.)

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Utilisez le script d’installation pour copier la définition d’application de vote dans le cluster, inscrivez le type d’application et créez une instance de l’application.

    ```bash
    ./install.sh
    ```

3. Ouvrez un navigateur web et accédez au point de terminaison Service Fabric Explorer pour votre cluster. Le point de terminaison a le format suivant : **https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**. Par exemple, `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(Pour les clusters tiers, vous pouvez trouver le point de terminaison de Service Fabric Explorer de votre cluster sur la page **d’accueil**.)

4. Développez le nœud **Applications** pour vous assurer qu’une entrée s’affiche pour le type d’application de vote et l’instance que vous avez créée.

    ![Service Fabric Explorer][sfx]

5. Pour vous connecter au conteneur en cours d’exécution, ouvrez un navigateur web et accédez à l’URL de votre cluster. Par exemple, `http://linh1x87d1d.westus.cloudapp.azure.com:80`. L’application de vote doit s’afficher dans le navigateur.

    ![Page web de l’application de vote][quickstartpic]

> [!NOTE]
> Vous pouvez également déployer des applications Service Fabric avec Docker Compose. Par exemple, la commande suivante peut servir à déployer et installer l’application sur le cluster Service Fabric à l’aide de Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Basculer un conteneur dans un cluster

Service Fabric s’assure que vos instances de conteneur se déplacent automatiquement vers d’autres nœuds du cluster en cas d’échec. Il vous est également possible de drainer manuellement un nœud pour les conteneurs, puis de le déplacer normalement vers d’autres nœuds du cluster. Service Fabric propose plusieurs options pour mettre à l’échelle vos services. Dans les étapes suivantes, vous utilisez Service Fabric Explorer.

Pour basculer le conteneur frontal, effectuez les étapes suivantes :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Cliquez sur le nœud **fabric:/Voting/azurevotefront** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID). Notez le nom du nœud dans l’arborescence, qui affiche les nœuds en cours d’exécution dans le conteneur. Par exemple, `_nodetype_4`.
3. Développez le nœud **Nœuds** dans l’arborescence. Cliquez sur les points de suspension (...) à côté du nœud qui exécute le conteneur.
4. Choisissez **Redémarrer** pour redémarrer ce nœud et confirmer l’action de redémarrage. Le redémarrage entraîne le basculement du conteneur vers un autre nœud du cluster.

    ![Affichage du nœud dans Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Mettre à l’échelle les applications et services dans un cluster

Les services Service Fabric peuvent être facilement mis à l’échelle dans un cluster pour s’adapter à la charge sur les services. Pour mettre à l’échelle un service, vous modifiez le nombre d’instances s’exécutant dans le cluster.

Pour mettre à l’échelle le service frontal web, procédez comme suit :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Cliquez sur les points de suspension (trois points) à côté du nœud **fabric:/Voting/azurevotefront** dans l’arborescence, puis choisissez **Mettre le service à l’échelle**.

    ![Lancement du service de mise à l’échelle Service Fabric Explorer][containersquickstartscale]

    Vous pouvez maintenant choisir de mettre à l’échelle le nombre d’instances du service frontal web.

3. Définissez le nombre sur **2**, puis cliquez sur **Scale Service** (Mettre à l’échelle le service).
4. Cliquez sur le nœud **fabric:/Voting/azurevotefront** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID).

    ![Fin du service de mise à l’échelle Service Fabric Explorer][containersquickstartscaledone]

    Vous constatez désormais que le service possède deux instances. Les nœuds exécutés par les instances sont visibles dans l’arborescence.

Via cette tâche de gestion simple, vous avez doublé les ressources disponibles pour permettre au service frontal de traiter la charge utilisateur. Il est important de comprendre que vous n’avez pas besoin de plusieurs instances d’un service pour que celui-ci s’exécute de manière fiable. En cas de défaillance d’un service, Service Fabric veille à ce qu’une nouvelle instance du service s’exécute dans le cluster.

## <a name="clean-up-resources"></a>Supprimer des ressources

1. Utilisez le script de désinstallation (uninstall.sh) fourni dans le modèle pour supprimer l’instance de l’application du cluster et annuler l’inscription du type d’application. Ce script prend un certain temps pour nettoyer l’instance. Par conséquent, veillez à ne pas exécuter le script d’installation immédiatement après ce script. Vous pouvez utiliser Service Fabric Explorer afin de déterminer quand la suppression de l’instance et l’annulation de l’inscription du type d’application ont eu lieu.

    ```bash
    ./uninstall.sh
    ```

2. Si vous avez fini d’utiliser votre cluster, vous pouvez supprimer le certificat du magasin de certificats. Par exemple :
   - Sur Windows : utilisez le [composant logiciel enfichable MMC Certificats](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Veillez à sélectionner **My user account** (Mon compte d’utilisateur) lors de l’ajout du composant logiciel enfichable. Accédez à `Certificates - Current User\Personal\Certificates` et supprimez le certificat.
   - Sur Mac : utilisez l’application Keychain.
   - Sur Ubuntu : suivez les étapes que vous avez utilisées pour afficher les certificats, puis supprimez le certificat.

3. Si vous n’avez plus besoin de Cloud Shell, vous pouvez supprimer le compte de stockage associé pour éviter d’avoir à payer des frais. Fermez votre session Cloud Shell. Dans le portail Azure, cliquez sur le compte de stockage associé à Cloud Shell, puis cliquez sur **Supprimer** en haut de la page et répondez aux invites.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez déployé une application de conteneur Linux dans un cluster Service Fabric dans Azure, vous avez basculé l’application et vous l’avez mise à l’échelle dans le cluster. Pour en savoir plus sur l’utilisation de conteneurs de Linux dans Service Fabric, passez au didacticiel sur les applications de conteneur Linux.

> [!div class="nextstepaction"]
> [Créer une application de conteneur Linux](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
