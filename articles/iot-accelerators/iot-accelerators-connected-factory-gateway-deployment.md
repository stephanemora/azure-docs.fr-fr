---
title: Déployer votre passerelle d’usine connectée - Azure | Microsoft Docs
description: Comment déployer une passerelle sur Windows ou Linux pour activer la connectivité à l’accélérateur de solution d’usine connectée.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450188"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Déployer une passerelle de périmètre pour l’accélérateur de solution d’usine connectée sur Windows ou Linux

Vous avez besoin de deux composants logiciels pour déployer une passerelle de périmètre pour l’accélérateur de solution *d’usine connectée* :

- Le  *proxy OPC*  établit une connexion à la solution Usine connectée. Le proxy OPC attend ensuite les messages de contrôle et de commande du navigateur OPC intégré qui s’exécute dans le portail de la solution Usine connectée.

-  *L’éditeur OPC*  se connecte aux serveurs OPC UA locaux existants, et transfère leurs messages de télémétrie à la solution Usine connectée. Vous pouvez connecter un appareil classique OPC à l’aide de [l’adaptateur classique OPC pour OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Les deux composants sont open source et disponibles en tant que sources sur GitHub et en tant que conteneurs Docker sur DockerHub :

| GitHub | DockerHub |
| ------ | --------- |
| [Éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Éditeur d’OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [Proxy OPC](https://github.com/Azure/iot-edge-opc-proxy)         | [Proxy OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Aucune adresse IP publique ni aucun port entrant ouvert dans le pare-feu de la passerelle ne sont nécessaires pour l’un ou l’autre des deux composants. Les composants Proxy OPC et Éditeur d’OPC utilisent uniquement le port sortant 443.

Les étapes décrites dans cet article montrent comment déployer une passerelle de périphérie à l’aide de Docker dans Windows ou Linux. La passerelle active la connectivité à l’accélérateur de solution d’usine connectée. Vous pouvez également utiliser les composants sans la solution d’usine connectée.

> [!NOTE]
> Les deux composants peuvent être utilisés en tant que modules dans  [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Choisir un appareil de passerelle

Si vous ne possédez pas encore d’appareil de passerelle, Microsoft vous recommande d’acheter une passerelle disponible dans le commerce auprès de l’un de nos partenaires. Pour obtenir la liste des appareils de passerelle compatibles avec la solution Usine connectée, consultez le  [catalogue d’appareils Azure IoT](https://catalog.azureiotsolutions.com/?q=opc). Suivez les instructions fournies avec la passerelle pour la configurer.

Pour configurer manuellement un appareil de passerelle existant, utilisez les instructions suivantes.

## <a name="install-and-configure-docker"></a>Installer et configurer Docker

Installez  [Docker pour Windows](https://www.docker.com/docker-windows)  sur votre appareil de passerelle basé sur Windows, ou utilisez un gestionnaire de package pour installer Docker sur votre appareil de passerelle basé sur Linux.

Pendant l’installation de Docker pour Windows, sélectionnez sur l’ordinateur hôte un lecteur à partager avec Docker. La capture d’écran suivante montre le partage du lecteur **D** sur votre système Windows. Le partage d’un lecteur permet d’accéder au lecteur hôte à partir d’un conteneur Docker :

![Installer Docker pour Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Vous pouvez également effectuer cette étape après l’installation de Docker, dans la boîte de dialogue  **Paramètres** . Cliquez avec le bouton droit sur l’icône **Docker** dans la barre d’état du système Windows, puis choisissez **Paramètres**. Si des mises à jour Windows importantes ont été déployées sur le système, telles que la mise à jour de Windows Fall Creators, annulez le partage des lecteurs, puis partagez-les de nouveau pour actualiser les droits d’accès.

Si vous utilisez Linux, aucune autre configuration n’est nécessaire pour permettre l’accès au système de fichiers.

Sur Windows, créez un dossier sur le lecteur que vous avez partagé avec Docker ; sur Linux, créez un dossier sous le système de fichiers racine. Cette procédure pas à pas fait référence à ce dossier sous la forme `<SharedFolder>`.

Quand vous faites référence à `<SharedFolder>` dans une commande Docker, veillez à utiliser la syntaxe correcte pour votre système d’exploitation. Voici deux exemples, l’un pour Windows et l’autre pour Linux :

- Si vous utilisez le dossier `D:\shared` sur Windows en tant que `<SharedFolder>`, la syntaxe de la commande Docker est `d:/shared`.

- Si vous utilisez le dossier `/shared` sur Linux en tant que `<SharedFolder>`, la syntaxe de la commande Docker est `/shared`.

Pour plus d’informations, consultez le guide de référence sur le moteur Docker [Use volumes](https://docs.docker.com/engine/admin/volumes/volumes/).

## <a name="configure-the-opc-components"></a>Configurer les composants OPC

Avant d’installer les composants OPC, effectuez les étapes suivantes pour préparer votre environnement :

1. Pour terminer le déploiement de la passerelle, vous avez besoin de la chaîne de connexion  **iothubowner**  du hub IoT dans le déploiement de votre solution Usine connectée. Dans le  [portail Azure](https://portal.azure.com/), accédez à votre hub IoT dans le groupe de ressources que vous avez créé durant le déploiement de la solution Usine connectée. Cliquez sur  **Stratégies d’accès partagé**  pour accéder à la chaîne de connexion  **iothubowner**  :

    ![Recherche de la chaîne de connexion IoT Hub](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Copiez la valeur  **Clé primaire de la chaîne de connexion** .

1. Pour permettre la communication entre les conteneurs Docker, vous avez besoin d’un réseau de pont défini par l’utilisateur. Pour créer un réseau de pont pour vos conteneurs, exécutez les commandes suivantes depuis une invite de commandes :

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Pour vérifier que le réseau de pont **iot_edge** a été créé, exécutez la commande suivante :

    ```cmd/sh
    docker network ls
    ```

    Votre réseau de pont **iot_edge** est inclus dans la liste des réseaux.

Pour exécuter l’Éditeur d’OPC, exécutez la commande suivante depuis une invite de commandes :

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- Le [dépôt GitHub de l’Éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher) et le [guide de référence sur la commande docker run](https://docs.docker.com/engine/reference/run/) fournissent plus d’informations sur les éléments suivants :

  - Les options de ligne de commande Docker spécifiées avant le nom du conteneur (`microsoft/iot-edge-opc-publisher:2.1.4`)
  - La signification des paramètres de ligne de commande de l’Éditeur d’OPC spécifiés après le nom du conteneur (`microsoft/iot-edge-opc-publisher:2.1.4`)

- `<IoTHubOwnerConnectionString>` est la chaîne de connexion **iothubowner** de stratégie d’accès partagé à partir du portail Azure. Vous avez copié cette chaîne de connexion à l’étape précédente. Vous n’avez besoin de cette chaîne de connexion que pour la première exécution de l’Éditeur d’OPC. Pour les exécutions suivantes, vous devrez l’omettre, car elle présente un risque de sécurité.

- Le `<SharedFolder>` que vous utilisez et sa syntaxe sont décrits dans la section [Installer et configurer Docker](#install-and-configure-docker). L’éditeur d’OPC utilise `<SharedFolder>` pour :

    - Lire et écrire des données dans le fichier de configuration de l’éditeur d’OPC
    - Écrire des données dans le fichier journal
    - Rendre ces deux fichiers disponibles en dehors du conteneur

- L’Éditeur d’OPC lit sa configuration à partir du fichier **publishednodes.json**, qui est lu à partir de et écrit dans le dossier `<SharedFolder>/docker`. Ce fichier de configuration définit les données de nœud OPC UA sur un serveur OPC UA spécifique auxquelles l’Éditeur d’OPC doit s’abonner. La syntaxe complète du fichier **publishednodes.json** est décrite dans la page GitHub consacrée à [l’Éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher). Quand vous ajoutez une passerelle, placez un fichier **publishednodes.json** vide dans le dossier :

    ```json
    [
    ]
    ```

- Chaque fois que le serveur OPC UA notifie l’Éditeur d’OPC d’une modification de données, la nouvelle valeur est envoyée à IoT Hub. Selon les paramètres de traitement par lot, l’Éditeur d’OPC peut accumuler les données avant de les envoyer à IoT Hub dans un lot.

- Docker ne prend pas en charge la résolution de noms NetBIOS, mais uniquement la résolution de noms DNS. Si vous n’avez pas de serveur DNS sur le réseau, vous pouvez utiliser la solution de contournement indiquée dans l’exemple de ligne de commande précédent. L’exemple de ligne de commande précédent utilise le paramètre `--add-host` pour ajouter une entrée dans le fichier d’hôtes de conteneurs. Cette entrée permet d’effectuer une recherche de nom d’hôte pour le `<OpcServerHostname>` particulier et de le résoudre en l’adresse IP `<IpAddressOfOpcServerHostname>` donnée.

- OPC UA utilise des certificats X.509 pour l’authentification et le chiffrement. Placez le certificat de l’Éditeur d’OPC sur le serveur OPC UA auquel vous vous connectez, afin qu’il approuve l’Éditeur d’OPC. Le magasin du certificat de l’Éditeur d’OPC se trouve dans le dossier `<SharedFolder>/CertificateStores`. Le certificat de l’Éditeur d’OPC se trouve dans le dossier `trusted/certs` du dossier `CertificateStores`.

  Les étapes nécessaires à la configuration du serveur OPC UA dépendent de l’appareil que vous utilisez. Ces étapes sont généralement documentées dans le manuel de l’utilisateur du serveur OPC UA.

Pour exécuter le Proxy OPC, exécutez la commande suivante depuis une invite de commandes :

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Vous ne devez exécuter l’installation qu’une seule fois sur un système.

Utilisez la commande suivante pour exécuter le Proxy OPC :

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

Le Proxy OPC enregistre la chaîne de connexion pendant l’installation. Pour les exécutions suivantes, vous devrez omettre la chaîne de connexion, car elle présente un risque de sécurité.

## <a name="enable-your-gateway"></a>Activer votre passerelle

Effectuez les étapes suivantes pour activer votre passerelle dans l’accélérateur de solution d’usine connectée :

1. Quand les deux composants sont en cours d’exécution, accédez à la page  **Connectez votre propre serveur OPC UA**  du portail de la solution Usine connectée. Cette page est uniquement disponible pour les administrateurs dans la solution. Entrez l’URL du point de terminaison du serveur de publication (opc.tcp://publisher:62222), puis cliquez sur  **Connecter**.

1. Établissez une relation d’approbation entre le portail de la solution d’usine connectée et l’éditeur OPC. Quand vous voyez un avertissement de certificat, cliquez sur  **Continuer**. Un message d’erreur apparaît alors, indiquant que l’Éditeur d’OPC n’approuve pas le client web UA. Pour résoudre cette erreur, copiez le certificat du  **client web UA**  situé dans le dossier `<SharedFolder>/CertificateStores/rejected/certs` vers le dossier `<SharedFolder>/CertificateStores/trusted/certs` sur la passerelle. Il n’est pas nécessaire de redémarrer la passerelle.

Vous pouvez désormais vous connecter à la passerelle à partir du cloud et ajouter des serveurs OPC UA à la solution.

## <a name="add-your-own-opc-ua-servers"></a>Ajouter vos propres serveurs OPC UA

Pour ajouter vos propres serveurs OPC UA à l’accélérateur de solution d’usine connectée :

1. Accédez à la page  **Connectez votre propre serveur OPC UA**  du portail de la solution Usine connectée.

    1. Démarrez le serveur OPC UA auquel vous souhaitez vous connecter. Vérifiez que votre serveur OPC UA est accessible à partir de l’Éditeur OPC et du proxy OPC exécutés dans le conteneur. Reportez-vous aux commentaires précédents sur la résolution des noms.
    1. Entrez l’URL du point de terminaison de votre serveur OPC UA (`opc.tcp://<host>:<port>`) et cliquez sur **Connexion**.
    1. Le processus de configuration de la connexion établit une relation d’approbation entre le portail de la solution Usine connectée (client OPC UA) et le serveur OPC UA auquel vous essayez de vous connecter. Dans le tableau de bord de la solution Usine connectée, un avertissement du type **Impossible de vérifier le certificat du serveur auquel vous souhaitez vous connecter** s’affiche. Quand vous voyez un avertissement de certificat, cliquez sur **Continuer**.
    1. La configuration du certificat du serveur OPC UA auquel vous essayez de vous connecter est plus difficile à effectuer. Pour les serveurs OPC UA de type PC, il vous suffit généralement de cliquer sur « OK » dans une boîte de dialogue d’avertissement qui s’affiche dans le tableau de bord. Pour les systèmes de serveur OPC UA incorporés, consultez la documentation de votre serveur OPC UA pour connaître la procédure à suivre. Pour effectuer cette tâche, vous aurez peut-être besoin du certificat du client OPC UA du portail de la solution d’usine connectée. Les administrateurs peuvent télécharger ce certificat à partir de la page **Connectez votre propre serveur OPC UA** :

        ![Portail de la solution](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Naviguez dans l’arborescence de nœuds OPC UA de votre serveur OPC UA, cliquez avec le bouton droit sur les nœuds OPC voulus pour envoyer les valeurs à la solution Usine connectée, puis sélectionnez  **Publier**.

1. Les données de télémétrie transitent maintenant à partir de la passerelle. Vous pouvez voir les données de télémétrie dans la vue  **Emplacements des usines**  du portail de la solution Usine connectée sous  **New Factory** (Nouvelle usine).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’architecture de l’accélérateur de solution Usine connectée, consultez  [Procédure pas à pas de l’accélérateur de solution d’usine connectée](iot-accelerators-connected-factory-sample-walkthrough.md).

Découvrez  [l’implémentation de référence de l’éditeur OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).