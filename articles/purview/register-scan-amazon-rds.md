---
title: Connecteur d’analyse multicloud Amazon RDS pour Azure Purview
description: Ce guide pratique décrit en détail comment analyser des bases de données Amazon RDS incluant des données Microsoft SQL et PostgreSQL.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: eb8140fa1d4c9328bf69fb84b867255876a235c6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096262"
---
# <a name="amazon-rds-multi-cloud-scanning-connector-for-azure-purview-public-preview"></a>Connecteur d’analyse multicloud Amazon RDS pour Azure Purview (préversion publique)

Le connecteur d’analyse multicloud pour Azure Purview vous permet d’explorer les données de votre organisation hébergées par plusieurs fournisseurs de cloud, dont Amazon Web Services, en plus des services de stockage Azure.

Cet article décrit comment utiliser Azure Purview pour analyser vos données non structurées actuellement stockées dans Amazon RDS, incluant des bases de données Microsoft SQL et PostgreSQL, et découvrir les types d’informations sensibles existant dans vos données. Il explique également comment identifier les bases de données Amazon RDS dans lesquelles les données sont actuellement stockées, afin de faciliter la protection des informations et la conformité des données.

Pour ce service, Purview vous permet de fournir un compte Microsoft avec un accès sécurisé à AWS, dans lequel s’exécuteront les connecteurs d’analyse multicloud pour Azure Purview. Les connecteurs d’analyse multicloud pour Azure Purview utilisent cet accès à vos bases de données Amazon RDS pour lire vos données, puis rapporte à Azure les résultats de l’analyse, qui comprennent uniquement les métadonnées et la classification. Utilisez les rapports de classification et d’étiquetage Purview pour analyser et examiner vos résultats d’analyse de données.

> [!IMPORTANT]
> Les connecteurs d’analyse multicloud pour Azure Purview sont des modules complémentaires distincts d’Azure Purview. Les conditions générales d’utilisation des connecteurs d’analyse multicloud pour Azure Purview sont contenues dans le contrat en vertu duquel vous avez obtenu les services Microsoft Azure. Pour plus d’informations, consultez la page Informations Juridiques Microsoft Azure à l’adresse https://azure.microsoft.com/support/legal/.
>

> [!IMPORTANT]
> La prise en charge de Purview pour Amazon RDS est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="purview-scope-for-amazon-rds"></a>Étendue de Purview pour Amazon RDS

- **Moteurs de base de données pris en charge** : le stockage de données structurées Amazon RDS prend en charge plusieurs moteurs de base de données. Azure Purview prend en charge Amazon RDS avec ou basé sur Microsoft SQL et PostgreSQL.

- **Nombre maximal de colonnes prises en charge** : l’analyse de tables RDS contenant plus de 300 colonnes n’est pas prise en charge.

- **Prise en charge de l’accès public** : Purview ne prend en charge l’analyse qu’avec Azure Private Link dans AWS, et n’inclut pas d’analyse d’accès public.

- **Régions prises en charge** : Purview ne prend en charge que les bases de données Amazon RDS situées dans les régions AWS suivantes :

    - USA Est (Ohio)
    - USA Est (Virginie Nord)
    - USA Ouest (Californie Nord)
    - USA Ouest (Oregon)
    - Europe (Francfort)
    - Asie-Pacifique (Tokyo)
    - Asie-Pacifique (Singapour)
    - Asie-Pacifique (Sydney)
    - Europe (Irlande)
    - Europe (Londres)
    - Europe (Paris)

- **Exigences relatives à l’adresse IP** : votre base de données RDS doit avoir une adresse IP statique. L’adresse IP statique est utilisée pour configurer AWS PrivateLink de la manière décrite dans cet article.

- **Problèmes connus** : les fonctionnalités suivantes ne sont pas prises en charge actuellement :

    - Bouton **Tester la connexion**. Les messages d’état d’analyse indiquent les erreurs liées à la configuration de la connexion.
    - Sélection de tables spécifiques dans votre base de données à analyser.
    - [Traçabilité des données](concept-data-lineage.md).

Pour plus d'informations, consultez les pages suivantes :

- [Gestion et augmentation des quotas de ressources avec Azure Purview](how-to-manage-quotas.md)
- [Sources de données et types de fichiers pris en charge dans Azure Purview](sources-and-scans.md)
- [Utiliser des points de terminaison privés pour votre compte Purview](catalog-private-link.md)

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous remplissez les prérequis suivants avant d’ajouter votre base de données Amazon RDS comme source de données Purview et d’analyser vos données S3.

> [!div class="checklist"]
> * Vous devez être administrateur de la source de données Azure Purview.
> * Vous avez besoin d’un compte Purview. [Créez un compte Azure Purview](create-catalog-portal.md) si vous n’en avez pas.
> * Vous avez besoin d’une base de données Amazon RDS PostgreSQL ou Microsoft SQL contenant des données.


## <a name="configure-aws-to-allow-purview-to-connect-to-your-rds-vpc"></a>Configurer AWS pour permettre à Purview de se connecter à votre cloud privé virtuel RDS

Azure Purview prend en charge l’analyse uniquement lorsque votre base de données est hébergée dans un cloud privé virtuel (VPC), où votre base de données RDS n’est accessible qu’à partir dudit VPC.

Les connecteurs d’analyse multicloud Azure pour le service Azure Purview s’exécutent dans un compte Microsoft distinct dans AWS. Pour analyser vos bases de données RDS, le compte Microsoft AWS doit être en mesure d’accéder à vos bases de données RDS sur votre VPC. Pour autoriser cet accès, vous devez configurer[AWS PrivateLink](https://aws.amazon.com/privatelink/) entre le VPC RDS (dans le compte client) et le VPC dans lequel s’exécutent les connecteurs d’analyse multicloud pour Azure Purview (dans le compte Microsoft).

Le diagramme suivant montre les composants de votre compte client et le compte Microsoft. Les composants mis en évidence en jaune sont ceux que vous devez créer pour activer la connectivité du VPC RDS dans votre compte au VPC dans lequel les connecteurs d’analyse multicloud pour Azure Purview s’exécutent dans le compte Microsoft.

:::image type="content" source="media/register-scan-amazon-rds/vpc-architecture.png" alt-text="Diagramme des connecteurs d’analyse multicloud pour le service Azure Purview dans une architecture de VPC." border="false":::


> [!IMPORTANT]
> Toutes les ressources AWS créées pour le réseau privé d’un client entraînent des frais supplémentaires sur la facture AWS du client.
>

### <a name="configure-aws-privatelink-using-a-cloudformation-template"></a>Configurer AWS PrivateLink à l’aide d’un modèle CloudFormation

La procédure suivante décrit comment utiliser un modèle AWS CloudFormation pour configurer AWS PrivateLink afin de permettre à Purview de se connecter à votre VPC RDS. Cette procédure effectuée dans AWS est destinée à un administrateur AWS.

Ce modèle CloudFormation disponible en téléchargement à partir du [dépôt GitHub Azure](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS) vous aide à créer un groupe cible, un équilibreur de charge et un service de point de terminaison.

- **Si vous avez plusieurs serveurs RDS sur le même VPC**, suivez cette procédure une seule fois, en [spécifiant l’ensemble des adresses IP et ports du serveur RDS](#parameters). Dans ce cas, la sortie CloudFormation inclut des ports différents pour chaque serveur RDS.

    Lors de l’[inscription de ces serveurs RDS en tant que sources de données dans Purview](#register-an-amazon-rds-data-source), utilisez les ports inclus dans la sortie au lieu des véritables ports du serveur RDS.

- **Si vous avez des serveurs RDS dans plusieurs VPC**, procédez des la sorte pour chacun d’eux.


> [!TIP]
> Vous pouvez également effectuer cette procédure manuellement. Pour plus d’informations, consultez [Configurer AWS PrivateLink manuellement (avancé)](#configure-aws-privatelink-manually-advanced).
>

**Pour préparer votre base de données RDS avec un modèle CloudFormation** :

1. Téléchargez le modèle CloudFormation [RDSPrivateLink_CloudFormation.yaml](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS) requis pour cette procédure à partir du dépôt GitHub Azure :

    1. À droite de la [page GitHub liée](https://github.com/Azure/Azure-Purview-Starter-Kit/blob/main/Amazon/AWS/RDS/Amazon_AWS_RDS_PrivateLink_CloudFormation.zip), sélectionnez **Télécharger** pour télécharger le fichier zip.

    1. Extrayez le fichier .zip dans un emplacement local afin de pouvoir accéder au fichier **RDSPrivateLink_CloudFormation.yaml**.

1. Dans le portail AWS, accédez au service **CloudFormation**. En haut à droite de la page, sélectionnez **Créer une pile** > **Avec de nouvelles ressources (standard)** .

1. Sur la page **Prérequis - Préparer le modèle**, sélectionnez **Le modèle est prêt**.

1. Dans la section **Spécifier le modèle**, sélectionnez **Charger un fichier de modèle**. Sélectionnez **Choisir un fichier**, accédez au fichier **RDSPrivateLink_CloudFormation.yaml** que vous avez téléchargé précédemment, puis sélectionnez **Suivant** pour continuer.

1. Dans la section **Nom de la pile**, entrez un nom pour votre pile. Ce nom sera utilisé, associé à un suffixe ajouté automatiquement, pour les noms de ressources créés plus tard dans le processus. Par conséquent :

    - Veillez à utiliser un nom explicite pour votre pile.
    - Assurez-vous que le nom de la pile ne compte pas plus de 19 caractères.

1. <a name="parameters"></a>Dans la zone **Paramètres**, entrez les valeurs suivantes en utilisant les données disponibles de la page de votre base de données RDS dans AWS :

    |Nom  |Description  |
    |---------|---------|
    |**Port du point de terminaison**    | Entrez l’adresse IP résolue de l’URL et du port du point de terminaison RDS. Par exemple : `192.168.1.1:5432` <br><br>- **Si un proxy RDS est configuré**, utilisez l’adresse IP du point de terminaison de lecture/écriture du proxy pour la base de données concernée. Nous vous recommandons d’utiliser un proxy RDS lors de l’utilisation de Purview, car l’adresse IP est statique.<br><br>- **Si vous avez plusieurs points de terminaison derrière le même VPC**, entrez jusqu’à 10 points de terminaison séparés par des virgules.  Dans ce cas, un seul équilibreur de charge est créé sur le VPC, autorisant une connexion à partir du connecteur d’analyse multicloud Amazon RDS pour Azure Purview dans AWS à tous les points de terminaison RDS dans le VPC.       |
    |**Mise en réseau**     | Entrez l’ID de votre VPC.        |
    |**CIDR IPv4 du VPC**     | Entrez la valeur du CIDR de votre VPC. Vous pouvez trouver cette valeur en sélectionnant le lien VPC sur la page de votre base de données RDS. Par exemple : `192.168.0.0/16`        |
    |**Sous-réseaux**     |Sélectionnez tous les sous-réseaux associés à votre VPC.         |
    |**Sécurité**     | Sélectionnez le groupe de sécurité VPC associé à la base de données RDS.         |
    |     |         |

    Quand vous avez terminé, sélectionnez **Suivant** pour continuer.

1. Les paramètres sous **Configurer les options de la pile** sont facultatifs pour cette procédure.

    Définissez vos paramètres en fonction des besoins de votre environnement. Pour plus d’informations, cliquez sur le lien **En savoir plus** pour accéder à la documentation AWS. Quand vous avez terminé, sélectionnez **Suivant** pour continuer.

1. Dans la page **Vérifier** , vérifiez que les valeurs que vous avez sélectionnées sont correctes pour votre environnement. Apportez les modifications nécessaires, puis sélectionnez **Créer une pile** lorsque vous avez terminé.

1. Regardez les ressources à créer. Lorsque vous avez terminé, les données pertinentes pour cette procédure s’affichent sous les onglets suivants :

    - **Événements** : affiche les événements et activités effectués par le modèle CloudFormation.
    - **Ressources** : affiche le groupe cible, l’équilibrage de charge et le service de point de terminaison nouvellement créés.
    - **Sorties** : affiche la valeur **ServiceName**, ainsi que l’adresse IP et le port des serveurs RDS.

        Si vous avez plusieurs serveurs RDS configurés, un autre port s’affiche. Dans ce cas, utilisez le port indiqué ici au lieu du port du serveur RDS réel lors de l’[inscription de votre base de données RDS](#register-an-amazon-rds-data-source) en tant que source de données Purview.

1. Sous l’onglet **Sorties**, copiez la valeur de la clé **ServiceName** dans le presse-papiers.

    Vous allez utiliser la valeur de la clé **ServiceName** dans le portail Azure Purview lors de l’[inscription de votre base de données RDS](#register-an-amazon-rds-data-source) en tant que source de données Purview. Entrez la clé **ServiceName** dans le champ **Se connecter au réseau privé via le service de point de terminaison**.

## <a name="register-an-amazon-rds-data-source"></a>Inscrire une source de données Amazon RDS

**Pour ajouter votre serveur Amazon RDS en tant que source de données Azure Purview** :

1. Dans Azure Purview, accédez à la page **Data Map**, puis sélectionnez **Inscrire** ![icône Inscrire](./media/register-scan-amazon-s3/register-button.png).

1.  Dans la page **Sources** , sélectionnez **inscrire**. Dans la page **Inscrire les sources** qui s’affiche sur la droite, sélectionnez l’onglet **Base de données**, puis **Amazon RDS (PostgreSQL)** ou **Amazon RDS (SQL)** .

    :::image type="content" source="media/register-scan-amazon-rds/register-amazon-rds.png" alt-text="Capture d’écran de la page Inscrire des sources pour sélectionner Amazon RDS (PostgreSQL).":::

1. Entrez les détails de votre source :

    |Champ  |Description  |
    |---------|---------|
    |**Nom**     |  Entrez un nom explicite pour votre source, par exemple, `AmazonPostgreSql-Ups`.       |
    |**Nom du serveur**     | Entrez le nom de votre base de données RDS dans la syntaxe suivante : `<instance identifier>.<xxxxxxxxxxxx>.<region>.rds.amazonaws.com`.  <br><br>Nous vous recommandons de copier cette URL à partir du portail Amazon RDS, et de vous assurer que l’URL comprend la région AWS.      |
    |**Port**     |  Entrez le port utilisé pour la connexion à la base de données RDS :<br><br>        - PostgreSQL : `5432`<br> - Microsoft SQL : `1433`<br><br>        Si vous avez [configuré AWS PrivateLink à l’aide d’un modèle CloudFormation](#configure-aws-privatelink-using-a-cloudformation-template) et disposez de plusieurs serveurs RDS dans le même VPC, utilisez les ports répertoriés sous l’onglet **Sorties** de CloudFormation à la place des ports de lecture du serveur RDS.       |
    |**Se connecter à un réseau privé via un service de point de terminaison**     |  Entrez la valeur de clé **ServiceName** obtenue à la fin de la [procédure précédente](#configure-aws-privatelink-using-a-cloudformation-template). <br><br>Si vous avez préparé manuellement votre base de données RDS, utilisez la valeur **Nom du service** obtenue à la fin de l’[Étape 5 : Créer un service de point de terminaison](#step-5-create-an-endpoint-service).       |
    |**Collection** (facultatif)     |  Sélectionnez une collection à laquelle ajouter votre source de données. Pour plus d’informations, consultez [Gérer des sources de données dans Azure Purview (préversion)](manage-data-sources.md).       |
    |     |         |

1. Quand vous êtes prêt à continuer, sélectionnez **Registre**.

Votre source de données RDS apparaît dans la liste ou le mappage de source. Par exemple :

:::image type="content" source="media/register-scan-amazon-rds/amazon-rds-in-sources.png" alt-text="Capture d’écran d’une source de données Amazon RDS sur la page Sources.":::

## <a name="create-purview-credentials-for-your-rds-scan"></a>Créer des informations d’identification Purview pour votre analyse RDS

Les informations d’identification prises en charge pour les sources de données Amazon RDS incluent uniquement l’authentification par nom d’utilisateur/mot de passe, avec un mot de passe stocké dans un secret Azure Key Vault.

### <a name="create-a-secret-for-your-rds-credentials-to-use-in-purview"></a>Créer un secret pour vos informations d’identification RDS à utiliser dans Purview

1.  Ajoutez votre mot de passe à un Azure Key Vault en tant que secret. Pour plus d’informations, consultez [Définir et récupérer un secret depuis Key Vault à l’aide du portail Azure](/azure/key-vault/secrets/quick-create-portal).

1.  Ajoutez une stratégie d’accès à votre coffre de clés avec les autorisations **Get** et **List**. Par exemple :

    :::image type="content" source="media/register-scan-amazon-rds/keyvault-for-rds.png" alt-text="Capture d’écran d’une stratégie d’accès pour RDS dans Purview.":::

    Lors de la définition du principal pour la stratégie, sélectionnez votre compte Purview. Par exemple :

    :::image type="content" source="media/register-scan-amazon-rds/select-purview-as-principal.png" alt-text="Capture d’écran de la sélection de votre compte Purview en tant que Principal.":::

    Sélectionnez **Enregistrer** pour enregistrer la mise à jour de votre stratégie d’accès. Pour plus d’informations, consultez [Attribuer une stratégie d’accès Azure Key Vault](/azure/key-vault/general/assign-access-policy-portal).

1. Dans Azure Purview, ajoutez une connexion Key Vault pour connecter le coffre de clés avec votre secret RDS à Purview. Pour plus d’informations, consultez [Informations d’identification pour l’authentification des sources dans Azure Purview](manage-credentials.md).

### <a name="create-your-purview-credential-object-for-rds"></a>Créer votre objet d’informations d’identification Purview pour RDS

Dans Azure Purview, créez un objet d’informations d’identification à utiliser lors de l’analyse de votre compte Amazon RDS.

1. Dans la zone de **Gestion** de Purview, sélectionnez **Sécurité et accès** > **Informations d’identification** > **Nouveau**.

1. Sélectionnez **Authentification SQL** comme méthode d’authentification. Ensuite, entrez les détails du coffre de clés dans lequel vos informations d’identification RDS sont stockées, y compris les noms de votre coffre de clés et votre secret.

    Par exemple :

    :::image type="content" source="media/register-scan-amazon-rds/new-credential-for-rds.png" alt-text="Capture d’écran de nouvelles informations d’identification pour RDS.":::

Pour plus d’informations, consultez [Informations d’identification pour l’authentification des sources dans Azure Purview](manage-credentials.md).

## <a name="scan-an-amazon-rds-database"></a>Analyser une base de données Amazon RDS

Pour configurer une analyse Azure Purview pour votre base de données RDS :

1.  Dans la page **Sources** de Purview, sélectionnez la source de données Amazon RDS à analyser.

1.  Sélectionnez :::image type="icon" source="media/register-scan-amazon-s3/new-scan-button.png" border="false"::: **Nouvelle analyse** pour commencer à définir votre analyse. Dans le volet qui s’ouvre à droite, entrez les informations suivantes, puis sélectionnez **Continuer**.

    - **Nom** : entrez un nom explicite pour votre analyse.
    - **Nom de la base de données** : entrez le nom de la base de données que vous souhaitez analyser. Vous devez rechercher les noms disponibles à partir de l’extérieur de Purview et créer une analyse distincte pour chaque base de données hébergée sur le serveur RDS inscrit.
    - **Informations d’identification**: sélectionnez les informations d’identification que vous avez créées précédemment afin de permettre aux connecteurs d’analyse multicloud pour Azure Purview d’accéder à la base de données RDS.

1.  Dans le volet **Sélectionner un ensemble de règles d’analyse** , sélectionnez l’ensemble de règles d’analyse que vous souhaitez utiliser, ou créez-en un. Pour plus d’informations, consultez [Création d’un ensemble de règles d’analyse](create-a-scan-rule-set.md).

1.  Dans le volet **Définir un déclencheur d’analyse**, indiquez si vous souhaitez exécuter l’analyse une seule fois, ou à une heure récurrente, puis sélectionnez **Continuer**.

1.  Dans le volet **Examiner votre analyse**, passez en revue les détails, puis sélectionnez **Enregistrer et exécuter** ou **Enregistrer** pour exécuter l’analyse ultérieurement.

Pendant l’exécution de l’analyse, sélectionnez **Actualiser** pour surveiller sa progression.

> [!NOTE]
> Lorsque vous utilisez des bases de données Amazon RDS PostgreSQL, seules les analyses complètes sont prises en charge. Les analyses incrémentielles ne sont pas prises en charge, car PostgreSQL n’a pas de valeur d’**heure de dernière modification**. 
>

## <a name="explore-scanning-results"></a>Explorer les résultats de l’analyse

Une fois l’analyse Purview terminée sur vos bases de données Amazon RDS, explorez au niveau du détail la zone **Data Map** de Purview pour voir l’historique d’analyse. Sélectionnez une source de données pour voir ses détails, puis sélectionnez l’onglet **Analyses** pour voir les analyses en cours d’exécution ou terminées.

Utilisez les autres zones de Purview pour en savoir plus sur le contenu de vos données, dont vos bases de données Amazon RDS :

- **Explorez les données RDS dans le catalogue**. Le catalogue Purview affiche une vue unifiée de tous les types de sources, et les résultats de l’analyse RDS s’affichent de la même façon pour Azure SQL. Vous pouvez parcourir le catalogue à l’aide de filtres, ou parcourir les ressources et la hiérarchie. Pour plus d'informations, consultez les pages suivantes :

    - [Tutoriel : Parcourir les ressources dans Azure Purview (préversion) et afficher leur traçabilité](tutorial-browse-and-view-lineage.md)
    - [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
    - [Inscrire et analyser une base de données Azure SQL](register-scan-azure-sql-database.md)

- **Consultez les rapports d’insights** pour voir des statistiques sur la classification, les étiquettes de confidentialité, les types de fichier et d’autres détails sur votre contenu.

    Tous les rapports d’insights Purview comprennent les résultats d’analyse d’Amazon RDS, ainsi que les autres résultats de vos sources de données Azure. Le cas échéant, un type de ressource **Amazon RDS** supplémentaire est ajouté aux options de filtrage de rapport.

    Pour plus d’informations, consultez [Présentation des insights dans Azure Purview](concept-insights.md).

- **Affichez les données RDS dans d’autres fonctionnalités de Purview**, telles que les zones **Analyses** et **Glossaire**. Pour plus d'informations, consultez les pages suivantes :

    - [Créer un ensemble de règles d’analyse](create-a-scan-rule-set.md)
    - [Tutoriel : Créer et importer des termes de glossaire dans Azure Purview (préversion)](tutorial-import-create-glossary-terms.md)


## <a name="configure-aws-privatelink-manually-advanced"></a>Configurer AWS PrivateLink manuellement (avancé)

Cette procédure décrit les étapes manuelles nécessaires à la préparation de votre base de données RDS dans un VPC pour la connexion à Azure Purview.

Par défaut, nous vous recommandons d’utiliser un modèle CloudFormation à la place, comme décrit précédemment dans cet article. Pour plus d’informations, consultez [Configurer AWS PrivateLink à l’aide d’un modèle CloudFormation](#configure-aws-privatelink-using-a-cloudformation-template).

### <a name="step-1-retrieve-your-amazon-rds-endpoint-ip-address"></a>Étape 1 : Récupérer votre adresse IP de point de terminaison Amazon RDS

Localisez l’adresse IP de votre point de terminaison Amazon RDS, hébergée à l’intérieur d’un VPC Amazon. Vous allez utiliser cette adresse IP plus tard dans le processus, lors de la création de votre groupe cible.

**Pour récupérer l’adresse IP de votre point de terminaison RDS** :

1.  Dans Amazon RDS, accédez à votre base de données RDS et identifiez l’URL de votre point de terminaison. Celle-ci se trouve sous **Connectivité et sécurité** en tant que valeur de votre **Point de terminaison**.

    > [!TIP]
    > Utilisez la commande suivante pour obtenir la liste des bases de données figurant dans votre point de terminaison : `aws rds describe-db-instances`
    >

1.  Utilisez l’URL du point de terminaison pour trouver l’adresse IP de votre base de données Amazon RDS. Par exemple, utilisez l’une des méthodes suivantes :

    - **Ping** : `ping <DB-Endpoint>`

    - **nslookup** : `nslookup <Db-Endpoint>`

    - **Nslookup en ligne**. Entrez la valeur de **Point de terminaison** de votre base de données dans la zone de recherche, puis sélectionnez **Rechercher les enregistrements DNS**. **Nslookup.IO** affiche votre adresse IP sur l’écran suivant.

### <a name="step-2-enable-your-rds-connection-from-a-load-balancer"></a>Étape 2 : Activer votre connexion RDS à partir d’un équilibreur de charge

Pour vous assurer que votre connexion RDS sera autorisée à partir de l’équilibreur de charge que vous allez créer plus tard dans le processus :

1.  **Recherchez la plage d’adresses IP VPC**.

    Dans Amazon RDS, accédez à votre base de données RDS. Dans la zone **Connectivité et sécurité**, sélectionnez le lien **VPC** pour rechercher sa plage d’adresses IP (CIDR IPv4).

    Dans la zone **Vos VPC**, votre plage d’adresses IP figure dans la colonne **CIDR IPv4**.

    > [!TIP]
    > Pour accomplir cette étape à l’aide de l’interface de ligne de commande, utilisez la commande suivante : `aws ec2 describe-vpcs`
    >
    > Pour plus d’informations, consultez [ec2 — AWS CLI 1.19.105 Command Reference (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/).
    >

1.  <a name="security-group"></a>**Créez un groupe de sécurité pour cette plage d’adresses IP**.

    1. Ouvrez la console Amazon EC2 à l’adresse https://console.aws.amazon.com/ec2/, puis accédez à **Groupes de sécurité**.

    1. Sélectionnez **Créer un groupe de sécurité**, puis créez votre groupe de sécurité en veillant à inclure les détails suivants :

        - **Nom du groupe de sécurité** : entrez un nom explicite.
        - **Description** : entrez une description pour ce groupe de sécurité.
        - **VPC** : sélectionnez le VPC de votre base de données RDS.

    1.  Sous **Règles de trafic entrant**, sélectionnez **Ajouter une règle**, puis entrez les informations suivantes :

        - **Type** : sélectionnez **TCP personnalisé**.
        - **Plage de ports** : entrez le port de votre base de données RDS.
        - **Source** : sélectionnez **Personnalisé**, puis entrez la plage d’adresses IP VPC de l’étape précédente.

    1.  Faites défiler vers le bas de la page, puis sélectionnez **Créer un groupe de sécurité**.

1.  **Associez le nouveau groupe de sécurité à RDS**.

    1.  Dans Amazon RDS, accédez à votre base de données RDS, puis sélectionnez **Modifier**.

    1.  Faites défiler jusqu’à la section **Connectivité**, puis, dans le champ **Groupe de sécurité**, ajoutez le nouveau groupe de sécurité que vous avez créé à l’[étape précédente](#security-group). Faites défiler la page jusqu’en bas, puis sélectionnez **Continuer**.

    1.  Dans la section **Planification des modifications**, sélectionnez **Appliquer immédiatement** pour mettre immédiatement à jour le groupe de sécurité.

    1.  Sélectionnez **Modifier l’instance de BD**.

> [!TIP]
> Pour effectuer cette étape à l’aide de l’interface de ligne de commande, utilisez les commandes suivantes :
>
> - `aws  ec2 create-security-group--description <value>--group-name <value>[--vpc-id <value>]`
>
>     Pour plus d’informations, consultez [create-security-group — AWS CLI 1.19.105 Command Reference (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html).
>
> - `aws rds --db-instance-identifier <value> --vpc-security-group-ids <value>`
>
>     Pour plus d’informations, consultez [modify-db-instance — AWS CLI 1.19.105 Command Reference (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html).
>

### <a name="step-3-create-a-target-group"></a>Étape 3 : Créer un groupe cible

**Pour créer votre groupe cible dans AWS** :

1.  Ouvrez la console Amazon EC2 à l’adresse https://console.aws.amazon.com/ec2/, puis accédez à **Équilibrage de charge** > **Groupes cibles**.

1.  Sélectionnez **Créer un groupe cible**, puis créez votre groupe cible en veillant à inclure les détails suivants :

    - **Type de cible** : sélectionnez **Adresses IP** (facultatif).
    - **Protocole** : sélectionnez **TCP**.
    - **Port** : entrez le port de votre base de données RDS.
    - **VPC** : entrez le VPC de votre base de données RDS.

    > [!NOTE]
    > Vous pouvez trouver le port de base de données RDS et les valeurs VPC sur la page de votre base de données RDS, sous **Connectivité et sécurité**

    Quand vous avez terminé, sélectionnez **Suivant** pour continuer.

1.  Dans la page **Inscrire les cibles**, entrez l’adresse IP de votre base de données RDS, puis sélectionnez **Inclure comme en attente ci-dessous**.

1.  Une fois que la nouvelle cible apparaît dans le tableau **Cibles**, sélectionnez **Créer un groupe cible** en bas de la page.

> [!TIP]
> Pour accomplir cette étape à l’aide de l’interface de ligne de commande, utilisez la commande suivante :
>
> - `aws elbv2 create-target-group --name <tg-name> --protocol <db-protocol> --port <db-port> --target-type ip --vpc-id <db-vpc-id>`
>
>     Pour plus d’informations, consultez [create-target-group — AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-target-group.html).
>
> - `aws elbv2 register-targets --target-group-arn <tg-arn> --targets Id=<db-ip>,Port=<db-port>`
>
>     Pour plus d’informations, consultez [register-targets — AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/register-targets.html).
>

### <a name="step-4-create-a-load-balancer"></a>Étape 4 : Créer un équilibrage de charge

Vous pouvez [Créer un équilibreur de charge réseau](#create-load-balancer) pour transférer le trafic vers l’adresse IP RDS ou [Ajouter un nouvel écouteur](#listener) à un équilibreur de charge existant.

<a name="create-load-balancer"></a>**Pour créer un équilibreur de charge réseau afin de transférer le trafic vers l’adresse IP de RDS** :

1.  Ouvrez la console Amazon EC2 à l’adresse https://console.aws.amazon.com/ec2/, puis accédez à **Équilibrage de charge** > **Équilibreurs de charge**.

1.  Sélectionnez **Créer un équilibreur de charge** > **Équilibreur de charge réseau**, puis sélectionnez ou entrez les valeurs suivantes :

    - **Schéma** : sélectionnez **Interne**.

    - **VPC** : sélectionnez le VPC de votre base de données RDS.

    - **Mappage** : assurez-vous que le RDS est défini pour toutes les régions AWS, puis veillez à sélectionner toutes ces régions. Vous pouvez trouver ces informations dans la valeur **Zone de disponibilité** de la page [Base de données RDS](#step-1-retrieve-your-amazon-rds-endpoint-ip-address), sous l’onglet **Connectivité et sécurité**.

    - **Écouteurs et routage** :

        - *Protocole* : sélectionnez **TCP**.
        - *Port* : sélectionnez **Port de base de données RDS**.
        - *Action par défaut* : sélectionnez le groupe cible créé à l’[étape précédente](#step-3-create-a-target-group).

1.  En bas de la page, sélectionnez **Créer un équilibreur de charge** > **Afficher les équilibreurs de charge**.

1.  Attendez quelques minutes, puis actualisez l’écran jusqu’à ce que la colonne **État** du nouvel équilibreur de charge indique **Actif**.


> [!TIP]
> Pour effectuer cette étape à l’aide de l’interface de ligne de commande, utilisez les commandes suivantes :
> - `aws elbv2 create-load-balancer --name <lb-name> --type network --scheme internal --subnet-mappings SubnetId=<value>`
>
>    Pour plus d’informations, consultez [create-load-balancer — AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-load-balancer.html).
>
> - `aws elbv2 create-listener --load-balancer-arn <lb-arn> --protocol TCP --port 80 --default-actions Type=forward,TargetGroupArn=<tg-arn>`
>
>    Pour plus d’informations, consultez [create-listener — AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-listener.html).
>

<a name="listener"></a>**Pour ajouter un écouteur à un équilibreur de charge existant** :

1. Ouvrez la console Amazon EC2 à l’adresse https://console.aws.amazon.com/ec2/, puis accédez à **Équilibrage de charge** > **Équilibreurs de charge**.

1. Sélectionnez votre équilibreur de charge > **Écouteurs** > **Ajouter un écouteur**.

1. Sous l’onglet **Écouteurs**, dans la zone **Protocole : port**, sélectionnez **TCP**, puis entrez un nouveau port pour votre écouteur.


> [!TIP]
> Pour accomplir cette étape à l’aide de l’interface de ligne de commande, utilisez la commande suivante : `aws elbv2  create-listener --load-balancer-arn <value> --protocol <value>  --port <value> --default-actions Type=forward,TargetGroupArn=<target_group_arn>`
>
> Pour plus d’informations, consultez la [documentation AWS](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-listener.html).
>

### <a name="step-5-create-an-endpoint-service"></a>Étape 5 : Créer un service de point de terminaison

Une fois que l’[équilibreur de charge créé](#step-4-create-a-load-balancer) et dans l’état **Actif**, vous pouvez créer le service de point de terminaison.

**Pour créer le service de point de terminaison** :

1.  Ouvrez la console Amazon VPC à l’adresse https://console.aws.amazon.com/vpc/, puis accédez à **Cloud privé virtuel > Services de point de terminaison**.

1.  Sélectionnez **Créer un service de point de terminaison**, puis, dans la liste déroulante **Équilibreurs de charge disponibles**, sélectionnez le nouvel équilibreur de charge créé à l’[étape précédente](#step-4-create-a-load-balancer), ou l’équilibreur de charge dans lequel vous avez ajouté un nouvel écouteur.

1.  Dans la page **Créer un service de point de terminaison**, désactivez l’option **Exiger une acceptation pour le point de terminaison**.

1.  En bas de la page, sélectionnez **Créer un service** > **Fermer**.

1.  De retour dans la page **Services de point de terminaison** :

    1. Sélectionnez le nouveau service de point de terminaison que vous avez créé.
    1. Sous l’onglet **Autoriser les principaux**, sélectionnez **Ajouter des principaux**.
    1. Dans le champ **Principaux à ajouter > ARN**, entrez `arn:aws:iam::181328463391:root`.
    1. Sélectionnez **Ajouter des principaux**.

    > [!NOTE]
    > Quand vous ajoutez une identité, utilisez un astérisque (*****) pour ajouter des autorisations pour tous les principaux. Cela permet à tous les principaux dans tous les comptes AWS de créer un point de terminaison pour votre service de point de terminaison. Pour plus d’informations, consultez la [documentation AWS](https://docs.aws.amazon.com/vpc/latest/privatelink/add-endpoint-service-permissions.html).

> [!TIP]
> Pour effectuer cette étape à l’aide de l’interface de ligne de commande, utilisez les commandes suivantes :
>
> - `aws ec2 create-vpc-endpoint-service-configuration --network-load-balancer-arns  <lb-arn>  --no-acceptance-required`
>
>    Pour plus d’informations, consultez [create-vpc-endpoint-service-configuration — AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-vpc-endpoint-service-configuration.html).
>
> - `aws ec2 modify-vpc-endpoint-service-permissions --service-id <endpoint-service-id> --add-allowed-principals <purview-scanner-arn>`
>
>    Pour plus d’informations, consultez [modify-vpc-endpoint-service-permissions — AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html).
>

<a name="service-name"></a>**Pour copier le nom du service à utiliser dans Azure Purview** :

Après avoir créé votre service de point de terminaison, vous pouvez copier la valeur **Nom du service** dans le portail Azure Purview lors de l’[inscription de votre base de données RDS](#register-an-amazon-rds-data-source) en tant que source de données Purview.

Recherchez le **Nom du service** sous l’onglet **Détails** pour votre service de point de terminaison sélectionné.

> [!TIP]
> Pour accomplir cette étape à l’aide de l’interface de ligne de commande, utilisez la commande suivante : `Aws ec2 describe-vpc-endpoint-services`
>
> Pour plus d’informations, consultez [describe-vpc-endpoint-services — AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-vpc-endpoint-services.html).
>

## <a name="troubleshoot-your-vpc-connection"></a>Résoudre les problèmes de votre connexion VPC

Cette section décrit les erreurs courantes qui peuvent se produire lors de la configuration de votre connexion VPC avec Azure Purview, ainsi que la manière de les résoudre.

### <a name="invalid-vpc-service-name"></a>Nom de service VPC non valide

Si une erreur `Invalid VPC service name` ou `Invalid endpoint service` s’affiche dans Azure Purview, procédez comme suit pour résoudre les problèmes :

1. Assurez-vous que le nom de votre service VPC est correct. Par exemple :

    :::image type="content" source="media/register-scan-amazon-rds/locate-service-name.png" alt-text="Capture d’écran du nom du service VPC dans AWS.":::

1. Assurez-vous que Microsoft ARN est répertorié dans les principaux autorisés : `arn:aws:iam::181328463391:root`

    Pour plus d’informations, consultez [Étape 5 : Créer un service de point de terminaison](#step-5-create-an-endpoint-service).

1. Assurez-vous que votre base de données RDS est répertoriée dans l’une des régions prises en charge. Pour plus d’informations, consultez [Étendue de Purview pour Amazon RDS](#purview-scope-for-amazon-rds).

### <a name="invalid-availability-zone"></a>Zone de disponibilité non valide

Si une erreur de `Invalid Availability Zone` s’affiche dans Azure Purview, assurez-vous que votre RDS est défini pour au moins l’une des trois régions suivantes :

- **us-east-1a**
- **us-east-1b**
- **us-east-1c**

Pour plus d’informations, consultez la [documentation AWS](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-getting-started.html).

### <a name="rds-errors"></a>Erreurs RDS

Les erreurs suivantes peuvent apparaître dans Azure Purview :

- `Unknown database`. Dans ce cas, la base de données définie n’existe pas. Vérifiez que le nom de base de données configuré est correct

- `Failed to login to the Sql data source. The given auth credential does not have permission on the target database.` Dans ce cas, votre nom d’utilisateur et votre mot de passe sont incorrects. Vérifiez vos informations d’identification et mettez-les à jour si nécessaire.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les rapports d’insights Azure Purview :

> [!div class="nextstepaction"]
> [Présentation des insights dans Azure Purview](concept-insights.md)
