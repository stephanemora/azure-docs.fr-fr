---
title: Notification et aide concernant la mise hors service d’Azure Blockchain Service
description: Migrer Azure Blockchain Service vers une offre de blockchain managée ou automanagée
ms.date: 05/10/2021
ms.topic: how-to
ms.openlocfilehash: 6fb86e426e446ba5515a285b04587093ee6fe4e5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752730"
---
# <a name="migrate-azure-blockchain-service"></a>Migrer Azure Blockchain Service

Vous pouvez migrer les données de registre d’Azure Blockchain Service vers une autre offre.

> [!IMPORTANT]
> Azure Blockchain sera mis hors service le **10 septembre 2021**. Vous devez migrer les données de registre d’Azure Blockchain Service vers une autre offre en fonction de l’état de votre développement (production ou évaluation).

## <a name="evaluate-alternatives"></a>Évaluer les autres offres

La première étape de la planification d’une migration consiste à évaluer les autres offres disponibles. Évaluez les offres suivantes en fonction de l’état de votre développement : production ou évaluation.

### <a name="production-or-pilot-phase"></a>Phase de production ou pilote

Si vous avez déjà déployé et développé une solution de blockchain qui est en phase de production ou pilote, considérez les offres suivantes.

#### <a name="quorum-blockchain-service"></a>Quorum Blockchain Service

Proposée par ConsenSys, Quorum Blockchain Service est une offre managée sur Azure qui prend en charge Quorum comme technologie de registre.

- **Offre managée** - Quorum Blockchain Service ne cause aucune surcharge de gestion supplémentaire par rapport à Azure Blockchain Service.
- **Technologie de registre** - Basée sur ConsenSys Quorum, une version améliorée de la technologie GoQuorum Ledger utilisée dans Azure Blockchain Service. Aucune nouvelle formation n’est nécessaire. Pour plus d’informations, consultez les [questions fréquentes sur Consensys Quorum](https://consensys.net/quorum/faq).
- **Continuité** - Vous pouvez migrer vos données existantes vers Quorum Blockchain Service de ConsenSys. Pour plus d’informations, consultez [Exporter des données à partir d’Azure Blockchain Service](#export-data-from-azure-blockchain-service).

Pour plus d’informations, consultez [Quorum Blockchain Service](https://consensys.net/QBS).

#### <a name="azure-vm-based-deployment"></a>Déploiement basé sur une machine virtuelle Azure

Pour déployer des blockchains sur des machines virtuelles IaaS, plusieurs modèles de gestion des ressources blockchain sont disponibles.

- **Technologie de registre** - Vous pouvez continuer à utiliser la technologie de registre Quorum, notamment la nouvelle plateforme ConsenSys Quorum.
- **Autogestion** - Une fois le déploiement effectué, vous gérez l’infrastructure et la pile de blockchain.

### <a name="new-deployment-or-evaluation-phase"></a>Nouvelle phase de déploiement ou d’évaluation

Si vous commencez à développer une nouvelle solution ou que vous êtes en phase d’évaluation, envisagez les offres suivantes en fonction des exigences de votre scénario.

- [Modèle Quorum dans la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.quorum-dev-quickstart)
- [Modèle Besu dans la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.hyperledger-besu-quickstart)

### <a name="how-to-migrate-to-an-alternative"></a>Comment migrer vers une autre offre

Pour migrer une charge de travail de production, commencez par [exporter vos données à partir d’Azure Blockchain Service](#export-data-from-azure-blockchain-service). Une fois que vous disposez d’une copie de vos données, vous pouvez acheminer ces données vers votre offre préférée.

La destination de migration recommandée est ConsenSys Quorum Blockchain Service. Pour intégrer ce service, accédez à la page [Quorum Blockchain Service](https://consensys.net/QBS) et inscrivez-vous.

Pour autogérer votre solution de blockchain à l’aide de machines virtuelles dans Azure, consultez l’[aide relative à Quorum reposant sur une machine virtuelle Azure](#azure-vm-based-quorum-guidance) afin de configurer des nœuds de transaction et validateurs.
## <a name="export-data-from-azure-blockchain-service"></a>Exporter des données à partir d’Azure Blockchain Service

En fonction de l’état de votre développement actuel, vous pouvez soit utiliser les données de registre existantes sur Azure Blockchain Service, soit démarrer un nouveau réseau et utiliser la solution de votre choix. Nous vous recommandons de créer un consortium basé sur une solution de votre choix dans tous les scénarios où vous n’avez ni le besoin ni l’intention d’utiliser des données de registre existantes sur Azure Blockchain Service.

### <a name="open-support-case"></a>Ouvrir un cas de support

Si vous avez un plan de support payant, ouvrez un ticket de support Microsoft pour suspendre le consortium et exporter vos données de blockchain.

1. Utiliser le portail Azure pour ouvrir un ticket de support Dans *Description du problème*, entrez les informations suivantes :

    ![Formulaire de description du problème du ticket de support dans le portail Azure](./media/migration-guide/problem-description.png)

    | Champ | response |
    |-------|--------- |
    | Type de problème | Prérequis techniques |
    | Service | Azure Blockchain Service - Préversion |
    | Résumé | Demander des données pour la migration |
    | Type de problème | autre |

1. Dans *Détails supplémentaires*, indiquez les informations suivantes :

    ![Formulaire de détails supplémentaires du ticket de support dans le portail Azure](./media/migration-guide/additional-details.png)

    - ID d’abonnement ou ID de ressource Azure Resource Manager
    - Locataire
    - Nom du consortium
    - Région
    - Nom du membre
    - Date/Heure par défaut de lancement de la migration

Si votre consortium a plusieurs membres, chaque membre est tenu d’ouvrir un ticket de support distinct pour ses propres données.

### <a name="pause-consortium"></a>Suspendre le consortium

Vous devez coordonner vos efforts avec les membres du consortium pour l’exportation des données. En effet, le consortium sera suspendu pendant l’exportation des données et les transactions effectuées durant cette période échoueront.

L’équipe Azure Blockchain Service suspend le consortium, exporte un instantané des données et rend les données accessibles en téléchargement dans un format chiffré par le biais d’une URL SAS à courte durée de vie. Le consortium reprend une fois l’instantané capturé.

> [!IMPORTANT]
> Vous devez arrêter toutes les applications qui lancent de nouvelles transactions de blockchain sur le réseau. Les applications actives peuvent entraîner une perte de données ou la non-synchronisation des réseaux d’origine et migrés.

### <a name="download-data"></a>Télécharger des données

Utilisez le lien URL SAS à courte durée de vie fourni par Support Microsoft pour télécharger les données.

> [!IMPORTANT]
> Vous avez sept jours pour télécharger vos données.

Déchiffrez les données à l’aide de la clé d’accès API. Vous pouvez [obtenir la clé à partir du portail Azure](configure-transaction-nodes.md#access-keys) ou [par le biais de l’API REST](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys).

> [!CAUTION]
> Seule la clé d’accès à l’API du nœud de transaction par défaut 1 est utilisée pour chiffrer toutes les données des nœuds de ce membre.
>
> Ne réinitialisez pas la clé d’accès à l’API au cours de la migration.

Vous pouvez utiliser les données avec ConsenSys Quorum Blockchain Service ou votre déploiement basé sur une machine virtuelle IaaS.

Pour la migration avec ConsenSys Quorum Blockchain Service, contactez ConsenSys à l’adresse [qbsmigration@consensys.net](mailto:qbsmigration@consensys.net).

Pour utiliser les données avec votre déploiement basé sur une machine virtuelle IaaS, effectuez les étapes de la section [Aide relative à Quorum reposant sur une machine virtuelle Azure](#azure-vm-based-quorum-guidance) dans cet art icle.

### <a name="delete-resources"></a>Supprimer des ressources

Une fois vos données copiées, il est recommandé de supprimer les ressources des membres Azure Blockchain. Sachez que vous êtes facturé tant que ces ressources existent.

## <a name="azure-vm-based-quorum-guidance"></a>Aide relative à Quorum reposant sur une machine virtuelle Azure

Utilisez les étapes suivantes pour créer des nœuds de transaction et des nœuds validateurs.

### <a name="transaction-node"></a>Nœud de transaction

Un nœud de transaction a deux composants. Tessera est utilisé pour les transactions privées et Geth pour l’application Quorum. Les nœuds validateurs nécessitent uniquement le composant Geth.

#### <a name="tessera"></a>Tessera

1. Installez Java 11. Par exemple : `apt install default-jre`.
1. Mettez à jour les chemins dans `tessera-config.json`. Remplacez toutes les références de `/working-dir/**` par `/opt/blockchain/data/working-dir/**`.
1. Mettez à jour l’adresse IP des autres nœuds de transaction en fonction de la nouvelle adresse IP. HTTPS ne fonctionne pas, car il n’est pas activé dans la configuration Tessera. Pour plus d’informations, consultez l’article traitant de [la configuration de TLS sur le site web de Tessera](https://docs.tessera.consensys.net/en/stable/HowTo/Configure/TLS/).
1. Mettez à jour les règles NSG pour autoriser les connexions entrantes sur le port 9000.
1. Utilisez la commande suivante pour exécuter Tessera :

    ```bash
    java -Xms512M -Xmx1731M -Dlogback.configurationFile=/tessera/logback-tessera.xml -jar tessera.jar -configfile /opt/blockchain/data/working-dir/tessera-config.json > tessera.log 2>&1 &
    ```

#### <a name="geth"></a>Geth

1. Mettez à jour les adresses IP dans les adresses de nœud dans `/opt/blockchain/data/working-dir/dd/static-nodes.json`. L’adresse IP publique est autorisée.
1. Apportez les mêmes modifications d’adresse IP sous la clé StaticNodes dans `/geth/config.toml`.
1. Mettez à jour les règles NSG pour autoriser les connexions entrantes sur le port 30303.
1. Exécutez Geth en utilisant les commandes suivantes :

    ```bash
    export NETWORK_ID='' # Get network ID from metadata. The network ID is the same for consortium.

    PRIVATE_CONFIG=tm.ipc geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul
    ```

### <a name="validator-node"></a>Nœud validateur

Les étapes du nœud validateur sont similaires à celles du nœud de transaction, sauf que la commande de démarrage Geth a l’indicateur supplémentaire `-mine`. Tessera n’est pas démarré sur un nœud validateur. Pour exécuter Geth sans un Tessera associé, passez `PRIVATE_CONFIG=ignore` dans la commande Geth. Exécutez Geth en utilisant les commandes suivantes :

```bash
export NETWORK_ID=`j q '.APP_SETTINGS | fromjson | ."network-id"' env.json`

PRIVATE_CONFIG=ignore geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul –mine
```

## <a name="upgrading-quorum"></a>Mise à niveau de Quorum

Azure Blockchain Service peut exécuter l’une des versions listées ci-après de Quorum. Vous pouvez soit utiliser la même version de Quorum, soit effectuer les étapes ci-dessous pour utiliser la dernière version de ConsenSys Quorum.

### <a name="upgrade-quorum-version-260-or-270-to-consensys-2110"></a>Mettre à niveau Quorum version 2.6.0 ou 2.7.0 vers ConsenSys 21.1.0

La mise à niveau à partir de la version 2.6 ou 2.7 de Quorum est simple. Utilisez les liens de téléchargement suivants pour effectuer la mise à jour.
1. Téléchargez [ConsenSys Quorum v21.1.0 et les binaires associés](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0).
1. Téléchargez la dernière version de Tessera : [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0).

### <a name="upgrade-quorum-version-250-to-consensys-2110"></a>Mettre à niveau Quorum version 2.5.0 vers ConsenSys 21.1.0

1. Téléchargez [ConsenSys Quorum v21.1.0 et les binaires associés](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0).
1. Téléchargez la dernière version de Tessera : [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0).
Pour les versions 2.5.0, des modifications mineures doivent être apportées au fichier genesis. Apportez les modifications suivantes dans le fichier genesis.

1. La valeur `byzantiumBlock` a été définie sur 1 et ne peut pas être inférieure à `constantinopleBlock` (qui a la valeur 0). Définissez `byzantiumBlock` avec la valeur 0.
1. Définissez `petersburgBlock`, `istanbulBlock` avec un bloc futur. Cette valeur doit être la même pour tous les nœuds.
1. Cette étape est facultative. `ceil2Nby3Block` a été incorrectement placé dans Quorum version 2.5.0 d’Azure Blockchain Service. Cet élément doit se trouver dans la configuration d’istanbul et définir le bloc futur de la valeur. Cette valeur doit être la même pour tous les nœuds.
1. Exécutez geth pour réinitialiser le bloc genesis avec la commande suivante :

    ```bash
    geth --datadir "Data Directory Path" init "genesis file path"
    ```

1.  Exécutez Geth.

## <a name="exported-data-reference"></a>Référence sur les données exportées

Cette section décrit les métadonnées et la structure des dossiers pour vous aider à importer les données dans votre déploiement basé sur une machine virtuelle IaaS.

### <a name="metadata-info"></a>Informations sur les métadonnées

| Nom               | Exemple                | Description           |
|--------------------|-----------------------|-----------------------|
| consortium_name    | \<ConsortiumName\>    | Nom du consortium (doit être unique dans Azure Blockchain Service). |
| Consortium_Member_Count || Nombre de membres dans le consortium. |
| member_name        | \<memberName\>        | Nom du membre blockchain (doit être unique dans Azure Blockchain Service). |
| node_name          | transaction-node      | Nom du nœud (chaque membre a plusieurs nœuds). |
| network_id         | 543                   | ID réseau de Geth.      |
| is_miner           | False                 | Is_Miner == true (nœud validateur), Is_Miner == false (nœud de transaction) |
| quorum_version     | 2.7.0                 | Version de Quorum     |
| tessera_version    | 0.10.5                | Version de Tessera      |
| java_version       | java-11-openjdk-amd64 | Version de Java utilisée par Tessera |
| CurrentBlockNumber |                       | Numéro de bloc actuel du réseau de blockchain |

## <a name="migrated-data-folder-structure"></a>Structure de dossiers des données migrées

Au niveau supérieur, les dossiers suivants correspondent à chacun des nœuds des membres.

- **Référence SKU Standard** - Deux nœuds validateurs (validateur-node-0 et validateur-node-1)
- **Référence SKU De base** - Un nœud validateur (validateur-node-0)
- **Nœud de transaction** - Nœud de transaction par défaut nommé transaction-node.

Les autres dossiers de nœud de transaction sont nommés en fonction du nœud de transaction.

### <a name="node-level-folder-structure"></a>Structure de dossiers au niveau du nœud

Chaque dossier au niveau du nœud contient un fichier zip chiffré avec la clé de chiffrement. Pour plus d’informations sur l’obtention de la clé de chiffrement, consultez la section [Télécharger des données](#download-data) de cet article.

| Directory/File | Description |
|----------------|--------------|
| /config/config.toml | Paramètres Geth. Les paramètres de ligne de commande sont prioritaires. |
| /config/genesis.json | Fichier genesis. |
| /config/logback-tessera.xml | Configuration Logback pour Tessera. |
| /config/static-nodes.json | Nœuds statiques. Les nœuds de démarrage sont supprimés et la détection automatique est désactivée. |
| /config/tessera-config.json | Configuration de Tessera. |
| /data/c/ | Base de données Tessera. |
| /data/dd/ | Répertoire de données Geth. |
| /env/env | Métadonnées |
| /keys/ | Clés Tessera. |
| /scripts/ | Scripts de démarrage (fournis uniquement à titre de référence). |

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="what-does-service-retirement-mean-for-existing-customers"></a>Que signifie cette mise hors service pour les clients existants ?

Les déploiements existants d’Azure Blockchain Service ne fonctionneront plus après le 10 septembre 2021. Commencez à évaluer les offres suggérées dans cet article en fonction de vos besoins avant la date de mise hors service.

### <a name="what-happens-to-existing-deployments-after-the-announcement-of-retirement"></a>Que deviennent les déploiements existants après l’annonce de la mise hors service ?

Les déploiements existants seront pris en charge jusqu’au 10 septembre 2021. Évaluez les offres suggérées, migrez les données vers l’une des ces offres, gérez les prérequis, puis commencez la migration à partir du déploiement sur Azure Blockchain Service.

### <a name="how-long-will-the-existing-deployments-be-supported-on-azure-blockchain-service"></a>Combien de temps les déploiements existants seront-ils pris en charge sur Azure Blockchain Service ?

Les déploiements existants seront pris en charge jusqu’au 10 septembre 2021.

### <a name="will-i-be-allowed-to-create-new-azure-blockchain-members-while-in-retirement-phase"></a>Est-ce que je suis autorisé à créer des membres Azure Blockchain lors de la phase de mise hors service ?

Après le 10 mai 2021, il sera impossible de déployer ou de créer de nouveaux membres.
