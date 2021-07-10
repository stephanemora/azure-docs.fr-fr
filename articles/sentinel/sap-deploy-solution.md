---
title: Déployer la solution Azure Sentinel pour environnements SAP | Microsoft Docs
description: Découvrez comment déployer la solution Azure Sentinel pour environnements SAP.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/13/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: cf7a9fb700bba135663e0684d8ba25c7ebcf92f0
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466527"
---
# <a name="tutorial-deploy-the-azure-sentinel-solution-for-sap-public-preview"></a>Tutoriel : Déployer la solution Azure Sentinel pour SAP (préversion publique)

Ce tutoriel vous guide pas à pas dans le processus de déploiement de la solution Azure Sentinel pour SAP.

> [!IMPORTANT]
> La solution SAP Azure Sentinel est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="overview"></a>Vue d’ensemble

Les [solutions Azure Sentinel](sentinel-solutions.md) incluent du contenu de sécurité groupé, tel que des détections de menaces, des classeurs et des watchlists. Les solutions vous permettent d’intégrer du contenu de sécurité Azure Sentinel pour un connecteur de données spécifique à l’aide d’un processus unique.

Le connecteur de données SAP Sentinel Azure vous permet de surveiller des systèmes SAP afin de détecter des menaces sophistiquées au sein des couches métier et application.

Le connecteur de données SAP diffuse 14 journaux d’application de l’ensemble du paysage du système SAP, et collecte des journaux tant à partir du langage de programmation ABAP (Advanced Business Application Programming) via des appels RFC NetWeaver, qu’à partir des données de stockage de fichiers via l’interface de contrôle OSSAP. Le connecteur de données SAP ajoute à Azure Sentinel la capacité de surveiller l’infrastructure SAP sous-jacente.

Pour ingérer des journaux SAP dans Azure Sentinel, le connecteur de données SAP Sentinel Azure doit être installé sur votre environnement SAP.
Nous vous recommandons d’utiliser un conteneur Docker sur une machine virtuelle Azure pour le déploiement, comme décrit dans ce tutoriel.

Une fois le connecteur de données SAP déployé, déployez le contenu de sécurité de la solution SAP pour avoir un aperçu de l’environnement SAP de votre organisation et améliorer les fonctionnalités d’opération de sécurité associées.

Ce tutoriel décrit les opérations suivantes :

> [!div class="checklist"]
> * Comment préparer votre système SAP pour le déploiement du connecteur de données SAP
> * Comment utiliser un conteneur Docker et une machine virtuelle Azure pour déployer le connecteur de données SAP
> * Comment déployer le contenu de sécurité de la solution SAP dans Azure Sentinel

## <a name="prerequisites"></a>Prérequis

Pour déployer le connecteur de données SAP Azure Sentinel et le contenu de sécurité comme décrit dans ce tutoriel, vous devez disposer des éléments suivants :

|Domaine  |Description  |
|---------|---------|
|**Conditions préalables pour Azure**     |  **Accès à Azure Sentinel**. Prenez note de l’ID et de la clé de votre espace de travail Azure Sentinel à utiliser dans ce tutoriel lors du [déploiement de votre connecteur de données SAP](#deploy-your-sap-data-connector). <br>Pour afficher ces détails à partir d’Azure Sentinel, accédez à **Paramètres** > **Paramètres d’espace de travail** > **Gestion des agents**. <br><br>**Possibilité de créer des ressources Azure**. Pour plus d’informations, consultez la [documentation sur Azure Resource Manager](/azure/azure-resource-manager/management/manage-resources-portal). <br><br>**Accès à la solution Azure Key Vault**. Ce tutoriel décrit les étapes recommandées pour utiliser la solution Azure Key Vault afin de stocker vos informations d’identification. Pour plus d’informations, consultez la [documentation sur Azure Key Vault](/azure/key-vault/).       |
|**Prérequis système**     |   **Logiciels**. Le script de déploiement du connecteur SAP Data installe automatiquement les logiciels requis. Pour plus d’informations, consultez [Logiciel installés automatiquement](#automatically-installed-software). <br><br> **Connectivité du système**. Assurez-vous que la machine virtuelle hôte du connecteur de données SAP a accès aux ressources suivantes : <br>- Azure Sentinel <br>- Azure Key Vault <br>- L’hôte de l’environnement SAP, via les ports TCP suivants : *32xx*, *5xx13* et *33xx*, où *xx* est le numéro de l’instance SAP. <br><br>Assurez-vous que vous disposez également d’un compte d’utilisateur SAP pour accéder à la page de téléchargement des logiciels SAP.<br><br>**Architecture du système**. La solution SAP est déployée sur une machine virtuelle en tant que conteneur Docker, et chaque client SAP requiert sa propre instance de conteneur. <br>Votre machine virtuelle et l’espace de travail Azure Sentinel peuvent se trouver dans des abonnements Azure différents, voire dans des locataires Azure AD différents.|
|**Configuration requise pour SAP**     |   **Versions de SAP prises en charge**. Nous vous recommandons d’utiliser [SAP_BASIS versions 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) ou versions ultérieures. <br>Certaines étapes de ce tutoriel fournissent des instructions distinctes si vous travaillez sur [SAP_BASIS version 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows).<br><br> **Détails du système SAP**. Prenez note des détails suivants du système SAP à utiliser dans ce tutoriel :<br>    - Adresse IP du système SAP<br>- Numéro du système SAP, par exemple `00`<br>    - ID du système SAP, provenant du système SAP NetWeaver. Par exemple : `NPL`. <br>- ID client SAP, tel que `001`.<br><br>**Accès à l’instance SAP NetWeaver**. L’accès à vos instances SAP doit utiliser l’une des options suivantes : <br>- [Utilisateur/mot de passe SAP ABAP](#configure-your-sap-system). <br>- Un utilisateur avec un certificat x509, utilisant SAP CRYPTOLIB PSE. Cette option peut nécessiter des étapes manuelles expertes.<br><br>**Support de votre équipe SAP**.  Vous aurez besoin du support de votre équipe SAP pour vous assurer que votre système SAP est [correctement configuré](#configure-your-sap-system) pour le déploiement de la solution.   |
|     |         |


### <a name="automatically-installed-software"></a>Logiciels installés automatiquement

Le [script de déploiement du connecteur de données SAP](#deploy-your-sap-data-connector) installe les logiciels suivants sur votre machine virtuelle à l’aide de privilèges SUDO (racine) :

- [Unzip](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 ou version ultérieure](https://www.python.org/downloads/)
- [Python3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>Configurer votre système SAP

Cette procédure décrit comment vérifier que votre système SAP présente la configuration requise et est configuré pour le déploiement du connecteur de données SAP Azure Sentinel.

> [!IMPORTANT]
> Suivez cette procédure avec votre équipe SAP pour vous assurer que les configurations sont correctes.
>

**Pour configurer votre système SAP pour le connecteur de données SAP** :

1. Si vous utilisez une version de SAP antérieure à la version 750, assurez-vous que les notes SAP suivantes sont déployées dans votre système :

    - **SPS12641084**. Pour les systèmes exécutant des versions de SAP antérieures à SAP BASIS 750 SPS13
    - **2502336**. Pour les systèmes exécutant des versions de SAP antérieures à SAP BASIS 750 SPS1
    - **2173545**. Pour les systèmes exécutant des versions de SAP antérieures à SAP BASIS 750

    Accédez à ces notes SAP sur le [site du Launchpad du support SAP](https://support.sap.com/en/index.html) en utilisant un compte d’utilisateur SAP.

1. Téléchargez et installez l’une des demandes de modification de SAP suivantes à partir du dépôt GitHub Azure Sentinel, à l’adresse https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR:

    - **SAP versions 750 ou ultérieures** : installez la demande de modification SAP *131 (NPLK900131)*
    - **SAP versions 740** : installez la demande de modification SAP *132 (NPLK900132)*

    Au cours de cette étape, utilisez le code de transaction SAP **STMS_IMPORT**.

    > [!NOTE]
    > Dans la zone **Options d’importation** de SAP, l’option **Ignorer la version du composant non valide** peut s’afficher. Si elle est affichée, sélectionnez-la avant de continuer.
    >

1. Créez un rôle SAP nommé **/MSFTSEN/SENTINEL_CONNECTOR** en important la demande de modification SAP *14 (NPLK900114)* . Utilisez le code de transaction SAP **STMS_IMPORT**.

    Vérifiez que le rôle est créé avec les autorisations requises, par exemple :

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Autorisations de rôle SAP requises pour le connecteur de données SAP Sentinel Azure.":::

    Pour plus d’informations, consultez les [autorisations pour utilisateur d’ABAP](sap-solution-detailed-requirements.md#required-abap-authorizations).

1. Créez un utilisateur de RFC/NetWeaver autre que par boîte de dialogue pour le connecteur de données SAP, et attachez le rôle nouvellement créé **/MSFTSEN/SENTINEL_CONNECTOR**.

    - Après avoir attaché le rôle, vérifiez que les autorisations de rôle sont distribuées à l’utilisateur.
    - Ce processus requiert l’utilisation d’un nom d’utilisateur et d’un mot de passe pour l’utilisateur d’ABAP. Une fois le nouvel utilisateur créé et doté des autorisations requises, veillez à modifier le mot de passe de l’utilisateur d’ABAP.

1. Téléchargez et placez le **Kit de développement logiciel (SDK) RFC de SAP NetWeaver 7.50 pour Linux sur x86_64 version 64 bits** sur votre machine virtuelle, car il est requis pendant le processus d’installation.

    Par exemple, recherchez le Kit de développement logiciel (SDK) sur le [site de téléchargement de logiciels SAP](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**. Veillez à télécharger l’option **LINUX ON X86_64 65BIT**. Copiez le fichier, par exemple à l’aide du protocole SCP, sur votre machine virtuelle.

    Vous aurez besoin d’un compte d’utilisateur SAP pour accéder à la page de téléchargement de logiciels SAP.

1. (Facultatif) Le fichier SAP **Auditlog** est utilisé dans l’ensemble du système et prend en charge plusieurs clients SAP. Toutefois, chaque instance de la solution SAP Azure Sentinel ne prend en charge qu’un seul client SAP.

    Par conséquent, si vous avez un système SAP à plusieurs clients, nous vous recommandons d’activer le fichier **Auditlog** uniquement pour le client sur lequel vous déployez la solution SAP afin d’éviter la duplication de données.


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>Déployer une machine virtuelle Linux pour votre connecteur de données SAP

Cette procédure décrit comment utiliser Azure CLI pour déployer une machine virtuelle Ubuntu Server 18.04 LTS et lui attribuer une [identité gérée par le système](/azure/active-directory/managed-identities-azure-resources/).

> [!TIP]
> Vous pouvez également déployer le connecteur de données sur RHEL versions 7.7 et ultérieures, ou SUSE versions 15 et ultérieures. Notez que le système d’exploitation et les correctifs doivent être complètement mis à jour.
>

**Pour déployer et préparer votre machine virtuelle Ubuntu** :

1. Utilisez, par exemple, la commande suivante en insérant les valeurs de votre groupe de ressources et de votre nom de machine virtuelle :

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username AzureUser --data-disk-sizes-gb 10 – --size Standard_DS2_– --generate-ssh-keys  --assign-identity
    ```

1. Sur votre nouvelle machine virtuelle, installez :

    - [Venv](https://docs.python.org/3.8/library/venv.html) avec Python version 3.8 ou ultérieure.
    - [Azure CLI](/cli/azure/) version 2.8.0 ou ultérieure.

> [!IMPORTANT]
> Veillez à respecter les meilleures pratiques de sécurité de votre organisation, comme vous le feriez pour n’importe quelle autre machine virtuelle.
>

Pour plus d’informations, consultez [Démarrage rapide : Créer une machine virtuelle Linux avec Azure CLI](/azure/virtual-machines/linux/quick-create-cli).

## <a name="create-key-vault-for-your-sap-credentials"></a>Créer un coffre de clés pour vos informations d’identification SAP

Ce tutoriel utilise un [Azure Key Vault](/azure/key-vault/) nouvellement créé ou dédié pour stocker les informations d’identification de votre connecteur de données SAP.

**Pour créer ou dédier un Azure Key Vault** :

1. Créez un Azure Key Vault ou choisissez-en un à dédier à votre déploiement de connecteur de données SAP.

    Par exemple, pour créer un Key Vault, exécutez les commandes suivantes en utilisant le nom de votre groupe de ressources de Key Vault et en entrant le nom de votre Key Vault :

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create Key Vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. Attribuez une stratégie d’accès, dont des autorisations GET, LIST et SET, à l’identité managée de la machine virtuelle.

    Dans Azure Key Vault, sélectionnez **Accéder aux stratégies** > **Ajouter une stratégie d’accès - Autorisations du secret : Get, List et Set** > **Sélectionner le principal**. Entrez le [nom de votre machine virtuelle](#deploy-a-linux-vm-for-your-sap-data-connector), puis sélectionnez **Ajouter** > **Enregistrer**.

    Pour plus d’informations, consultez la [documentation relative à Key Vault](/azure/key-vault/general/assign-access-policy-portal).

1. Exécutez la commande suivante pour obtenir l’[ID principal de la machine virtuelle](#deploy-a-linux-vm-for-your-sap-data-connector), en entrant le nom de votre groupe de ressources Azure :

    ```azurecli
    az vm show -g [resource group] -n [Virtual Machine] --query identity.principal– --out tsv
    ```

    Votre ID principal est affiché. Vous allez l’utiliser à l’étape suivante.

1. Exécutez la commande suivante pour attribuer les autorisations d’accès de la machine virtuelle au Key Vault, en entrant le nom de votre groupe de ressources et la valeur d’ID principal renvoyée à l’étape précédente.

    ```azurecli
    az keyvault set-policy  --name $kv  --resource-group [resource group]  --object-id [Principal ID]  --secret-permissions get set
    ```

## <a name="deploy-your-sap-data-connector"></a>Déployer votre connecteur de données SAP

Le script de déploiement du connecteur de données SAP Azure Sentinel installe les [logiciels requis](#automatically-installed-software), puis le connecteur sur votre [machine virtuelle nouvellement créée](#deploy-a-linux-vm-for-your-sap-data-connector), en stockant les informations d’identification dans votre [coffre de clés dédié](#create-key-vault-for-your-sap-credentials).

Le script de déploiement du connecteur de données SAP est stocké dans le répertoire [Dépôt GitHub Azure Sentinel > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/SAP/).

Pour exécuter le script de déploiement du connecteur de données SAP, vous avez besoin des informations suivantes :

- détails de votre espace de travail Azure Sentinel, tels qu’indiqués dans la section [Prérequis](#prerequisites) ;
- détails du système SAP indiqués dans la section [Prérequis](#prerequisites) ;
- accès à un utilisateur de machine virtuelle disposant de privilèges SUDO ;
- utilisateur SAP que vous avez créé dans la section [Configurer votre système SAP](#configure-your-sap-system), avec le rôle **/MSFTSEN/SENTINEL_CONNECTOR** appliqué ;
- aide de votre équipe SAP.


**Pour exécuter le script de déploiement de la solution SAP** :

1. Exécutez la commande suivante pour déployer la solution SAP sur votre machine virtuelle :

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. Suivez les instructions à l’écran pour entrer les détails de votre SAP et de votre Key Vault, et accomplissez le déploiement. Une fois le déploiement terminé, un message de confirmation s’affiche :

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Azure Sentinel commence à récupérer les journaux SAP pour l’intervalle de temps configuré, jusqu’à 24 heures avant l’heure d’initialisation.

1. Nous vous recommandons de consulter les journaux système pour vous assurer que le connecteur de données transmet des données. Exécutez :

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>Déployer le contenu de sécurité SAP

Déployez le [contenu de sécurité SAP](sap-solution-security-content.md) à partir des zones **Solutions** et **Watchlists** d’Azure Sentinel.

La solution **Azure Sentinel – Surveillance continue des menaces pour SAP** permet au connecteur de données SAP de s’afficher dans la zone de **Connecteurs de données** d’Azure Sentinel, et déploie le classeur des **SAP – Applications et produits système** ainsi que les règles d’analyse liées à SAP.

Ajoutez manuellement les watchlists liées à SAP à votre espace de travail Azure Sentinel.

**Pour déployer le contenu de sécurité de la solution SAP** :

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Solutions (préversion)** .

    La page **Solutions** affiche une liste de solutions filtrée pouvant faire l’objet d’une recherche.

1. Sélectionnez **Azure Sentinel – Surveillance continue des menaces pour SAP (préversion)** pour ouvrir la page de la solution SAP.

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Azure Sentinel – Surveillance continue des menaces pour SAP (préversion).":::

1. Sélectionnez **Créer** pour lancer l’Assistant Déploiement de la solution, puis entrez les détails de l’abonnement Azure, du groupe de ressources et de l’espace de travail Log Analytics dans lequel vous souhaitez déployer la solution.

1. Sélectionnez **Suivant** pour parcourir les onglets **Connecteurs de données**, **Analytique** et **Classeurs**, où vous pouvez découvrir les composants qui seront déployés avec cette solution.

    Le nom par défaut du classeur est **SAP – Applications et produits système – Préversion**. Modifiez-le sous l’onglet Classeurs si nécessaire.

    Pour plus d’informations, consultez [Solution SAP Azure Sentinel : Informations de référence sur le contenu de sécurité (préversion publique)](sap-solution-security-content.md).

1. Sous l’onglet **Vérifier + créer**, attendez que le message **Validation réussie** s’affiche, puis sélectionnez **Créer** pour déployer la solution.

    > [!TIP]
    > Vous pouvez également sélectionner **Télécharger un modèle** afin d’obtenir un lien pour déployer la solution en tant que code.

1. Une fois le déploiement terminé, un message de confirmation s’affiche en haut à droite de la page.

    Pour afficher le contenu nouvellement déployé, accédez à :

    - **Gestion des menaces** > **Classeurs** pour rechercher le classeur [SAP – Applications et produits système – Préversion](sap-solution-security-content.md#sap---system-applications-and-products-workbook).
    - **Configuration** > **Analytique** pour rechercher une série de [règles d’analyse liées à SAP](sap-solution-security-content.md#built-in-analytics-rules).

1. Ajoutez des watchlists liées à SAP à utiliser dans votre recherche, vos règles de détection, votre chasse des menaces et vos playbooks de réponse. Ces watchlists fournissent la configuration de la solution de surveillance continue des menaces SAP Azure Sentinel.

    1. Téléchargez les watchlists SAP à partir du dépôt GitHub Azure Sentinel à l’adresse https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists.

    1. Dans la zone **Watchlists** d’Azure Sentinel, ajoutez les watchlists à votre espace de travail Azure Sentinel. Utilisez les fichiers CSV téléchargés en tant que sources, puis personnalisez-les en fonction des besoins de votre environnement. 

        [ ![Watchlists liées à SAP ajoutées à Azure Sentinel.](media/sap/sap-watchlists.png)](media/sap/sap-watchlists.png#lightbox)

        Pour plus d’informations, consultez [Utiliser les Watchlists Azure Sentinel](watchlists.md) et [Watchlists SAP disponibles](sap-solution-security-content.md#available-watchlists).

1. Dans Azure Sentinel, accédez au connecteur de données **Surveillance continue des menaces Azure Sentinel pour SAP** afin de vérifier la connexion :

    [ ![Page du connecteur de données Surveillance continue des menaces Azure Sentinel pour SAP.](media/sap/sap-data-connector.png) ](media/sap/sap-data-connector.png#lightbox)

    Les journaux SAP ABAP s’affichent dans la page **Journaux** d’Azure Sentinel sous **Journaux personnalisés** :

    [ ![Journaux SAP ABAP sous Journaux personnalisés dans Azure Sentinel.](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    Pour plus d’informations, consultez [Informations de référence sur les journaux de la solution SAP Azure Sentinel (préversion publique)](sap-solution-log-reference.md).

## <a name="sap-solution-deployment-troubleshooting"></a>Résolution des problèmes de déploiement de solution SAP

Une fois le connecteur de données et le contenu de sécurité SAP déployés, il se peut que vous rencontriez les erreurs ou problèmes suivants :

|Problème  |Solution de contournement  |
|---------|---------|
|Problèmes de connectivité réseau à l’environnement SAP ou à Azure Sentinel     |  Vérifiez votre connectivité réseau si nécessaire.       |
|Informations d’identification d’utilisateur SAP ABAP incorrectes     |Vérifiez vos informations d’identification et corrigez-les en appliquant les valeurs correctes pour **ABAPUSER** et **ABAPPASS** dans Azure Key Vault.         |
|Autorisations manquantes, telles que le rôle **/MSFTSEN/SENTINEL_CONNECTOR** non attribué à l’utilisateur SAP en fonction des besoins, ou inactif     |Corrigez cette erreur en attribuant le rôle et en veillant à ce qu’il soit actif dans votre système SAP.         |
|Demande de modification SAP manquante     | Vérifiez que vous avez importé la demande de modification SAP appropriée, comme décrit dans [Configurer votre système SAP](#configure-your-sap-system).        |
|Clé ou ID d’espace de travail Azure Sentinel incorrects entrés dans le script de déploiement     |  Pour corriger cette erreur, entrez les informations d’identification correctes dans Azure Key Vault.       |
|Fichier du Kit de développement logiciel (SDK) SAP endommagé ou manquant     | Corrigez cette erreur en réinstallant le Kit de développement logiciel (SDK) SAP et en vous assurant que vous utilisez la version Linux 64 bits correcte.        |
|Données manquantes dans votre classeur ou vos alertes     |    Assurez-vous que la stratégie **Auditlog** est correctement activée côté SAP, sans erreur dans le fichier journal. Utilisez la transaction **RSAU_CONFIG_LOG** pour cette étape.     |
|     |         |

> [!TIP]
> Nous vous recommandons vivement d’examiner les journaux système après l’installation du connecteur de données. Exécutez :
>
> ```bash
> docker logs -f sapcon-[SID]
> ```
>
Pour plus d'informations, consultez les pages suivantes :

- [Afficher tous les journaux d’exécution de Docker](#view-all-docker-execution-logs)
- [Examiner et mettre à jour la configuration du connecteur de données SAP](#review-and-update-the-sap-data-connector-configuration)
- [Commandes Docker utiles](#useful-docker-commands)

### <a name="view-all-docker-execution-logs"></a>Afficher tous les journaux d’exécution de Docker

Pour afficher tous les journaux d’exécution de Docker pour votre déploiement de connecteur de données SAP Azure Sentinel, exécutez l’une des commandes suivantes :

```bash
docker exec -it sapcon-[SID] bash && cd /sapcon-app/sapcon/logs
```

ou

```bash
docker exec –it sapcon-[SID] cat /sapcon-app/sapcon/logs/[FILE_LOGNAME]
```

Une sortie similaire à celle ci-après devrait s’afficher :

```bash
Logs directory:
root@644c46cd82a9:/sapcon-app# ls sapcon/logs/ -l
total 508
-rwxr-xr-x 1 root root      0 Mar 12 09:22 ' __init__.py'
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPAppLog.log
-rw-r--r-- 1 root root   1056 Mar 12 16:01  ABAPAuditLog.log
-rw-r--r-- 1 root root    465 Mar 12 16:01  ABAPCRLog.log
-rw-r--r-- 1 root root    515 Mar 12 16:01  ABAPChangeDocsLog.log
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPJobLog.log
-rw-r--r-- 1 root root    480 Mar 12 16:01  ABAPSpoolLog.log
-rw-r--r-- 1 root root    525 Mar 12 16:01  ABAPSpoolOutputLog.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  ABAPTableDataLog.log
-rw-r--r-- 1 root root    495 Mar 12 16:01  ABAPWorkflowLog.log
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Azure Sentinel
-rw-r--r-- 1 root root      0 Mar 12 15:51  LogsDeltaManager.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  PersistenceManager.log
-rw-r--r-- 1 root root   4830 Mar 12 16:01  RFC.log
-rw-r--r-- 1 root root   5595 Mar 12 16:03  SystemAdmin.log
```

### <a name="review-and-update-the-sap-data-connector-configuration"></a>Examiner et mettre à jour la configuration du connecteur de données SAP

Si vous souhaitez vérifier le fichier de configuration du connecteur de données SAP et effectuer des mises à jour manuelles, procédez comme suit :

1. Sur votre machine virtuelle, dans le répertoire de base de l’utilisateur, ouvrez le fichier **~/sapcon/[SID]/systemconfig.ini**.
1. Mettez à jour la configuration si nécessaire, puis redémarrez le conteneur :

    ```bash
    docker restart sapcon-[SID]
    ```

### <a name="useful-docker-commands"></a>Commandes Docker utiles

Lors de la résolution des problèmes de votre connecteur de données SAP, les commandes suivantes peuvent être utiles :

|Function  |Commande  |
|---------|---------|
|**Arrêter le conteneur Docker**     |  `docker stop sapcon-[SID]`       |
|**Démarrer le conteneur Docker**     |`docker start sapcon-[SID]`         |
|**Afficher les journaux système de Docker**     |  `docker logs -f sapcon-[SID]`       |
|**Entrer le conteneur Docker**     |   `docker exec -it sapcon-[SID] bash`      |
|     |         |

Pour plus d’informations, consultez la [documentation sur l’interface de ligne de commande de Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-your-sap-data-connector"></a>Mettre à jour votre connecteur de données SAP

Si vous avez un conteneur Docker s’exécutant avec une version antérieure du connecteur de données SAP, exécutez le script de mise à jour du connecteur de données SAP pour obtenir les dernières fonctionnalités disponibles.

1. Assurez-vous que vous disposez des versions les plus récentes des scripts de déploiement appropriés du dépôt Github Azure Sentinel. Exécutez :

    ```azurecli
    - wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-update.sh
    ```

1. Exécutez la commande suivante sur votre ordinateur connecteur de données SAP :

    ```azurecli
    ./ sapcon-instance-update.sh
    ```

Le conteneur Docker du connecteur de données SAP sur votre ordinateur est mis à jour.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les solutions SAP Azure Sentinel :

- [Déployer la solution SAP Azure Sentinel en utilisant d’autres déploiements](sap-solution-deploy-alternate.md)
- [Exigences de SAP détaillées pour la solution SAP Azure Sentinel](sap-solution-detailed-requirements.md)
- [Informations de référence sur les journaux de la solution SAP Azure Sentinel](sap-solution-log-reference.md)
- [Solution SAP Azure Sentinel : contenu de sécurité intégré](sap-solution-security-content.md)

Pour plus d’informations, consultez [Solutions Azure Sentinel](sentinel-solutions.md).
