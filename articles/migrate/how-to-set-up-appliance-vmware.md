---
title: Configurer une appliance Azure Migrate pour VMware
description: Découvrez comment configurer une appliance Azure Migrate pour évaluer et migrer des serveurs dans un environnement VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 64be28838abb5d5021f0a8cefc0eed2c2516498b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865228"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>Configurer une appliance pour les serveurs dans un environnement VMware

Suivez cet article pour configurer l’appliance Azure Migrate pour l’évaluation avec l’outil [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) et pour la migration sans agent en utilisant l’outil [Azure Migrate : Migration de serveurs](migrate-services-overview.md#azure-migrate-server-migration-tool).

L’[appliance Azure Migrate](migrate-appliance.md) est une appliance légère utilisée par Azure Migrate : découverte et évaluation et la migration de servuers pour découvrir les serveurs s’exécutant dans vCenter Server, envoyer la configuration du serveur et les métadonnées de performances à Azure, ainsi que pour répliquer des serveurs à l’aide d’une migration sans agent.

Vous pouvez déployer l’appliance à l’aide de deux méthodes :

- Créer un serveur sur vCenter Server à l’aide d’un modèle OVA téléchargé. Il s’agit de la méthode décrite dans cet article.
- Configurer l’appliance sur un serveur existant à l’aide d’un script d’installation PowerShell. Utilisez [cette méthode](deploy-appliance-script.md) si vous ne pouvez pas utiliser un modèle OVA ou si vous êtes dans Azure Government.

Après avoir créé l’appliance, vérifiez qu’elle peut se connecter à Azure Migrate : découverte et évaluation, inscrivez-la auprès du projet et configurez-la pour lancer une découverte.

## <a name="deploy-with-ova"></a>Effectuer un déploiement avec OVA

Pour configurer l’appliance avec un modèle OVA, vous :
1. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
1. Téléchargez un fichier de modèle OVA, puis importez-le dans vCenter Server. Vérifiez que l’OVA est sécurisé.
1. Créez la machine virtuelle appliance à partir du modèle OVA et vérifiez qu’elle peut se connecter à Azure Migrate.
1. Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet en utilisant la clé de projet.

### <a name="1-generate-the-project-key"></a>1. Générer la clé de projet

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : découverte et évaluation**, sélectionnez **Découvrir**.
2. Dans **Découvrir les serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Oui, avec l’hyperviseur vSphere VMware**.
3. Dans **1 : Générer une clé de projet**, attribuez un nom à l’appliance Azure Migrate que vous allez configurer pour la découverte de serveurs dans votre environnement VMware. Le nom doit être alphanumérique et compter au maximum de 14 caractères.
1. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure nécessaires. Ne fermez pas la page Découvrir pendant la création des ressources.
1. Une fois les ressources Azure créées avec succès, une **clé de projet** est générée.
1. Copiez la clé car vous en aurez besoin pour terminer l'inscription de l'appliance lors de sa configuration.

### <a name="2-download-the-ova-template"></a>2. Télécharger le modèle OVA

Dans **2 : Télécharger l’appliance Azure Migrate**, sélectionnez le fichier .OVA et cliquez sur **Télécharger**.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier OVA est sécurisé avant de le déployer :

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Vérifiez les toutes dernières versions de l’appliance et les valeurs de hachage :

    - Pour le cloud public Azure :
    
        **Algorithme** | **Télécharger** | **SHA256**
        --- | --- | ---
        VMware (11,9 Go) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74


### <a name="3-create-the-appliance-server"></a>3. Créer le serveur de l’appliance

Importez le fichier téléchargé, puis créez un serveur dans l’environnement VMware.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).
2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .OVA.
3. Dans **Nom** et **Emplacement**, spécifiez un nom convivial pour le serveur. Sélectionnez l’objet d’inventaire dans lequel le serveur doit être hébergée.
5. Dans **Hôte/Cluster**, spécifiez l’hôte ou le cluster sur lequel s’exécute le serveur.
6. Dans **Stockage**, spécifiez la destination du stockage du serveur.
7. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
8. Dans **Mappage réseau**, spécifiez le réseau auquel se connectera. Le réseau nécessite une connexion à Internet pour envoyer des métadonnées à Azure Migrate.
9. Passez en revue les paramètres, confirmez-les, puis cliquez sur **Finish** (Terminer).


### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que le serveur de l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).


### <a name="4-configure-the-appliance"></a>4. Configurer l’appliance

Configurez l’appliance pour la première fois.

> [!NOTE]
> Si vous configurez l’appliance à l’aide d’un [**script PowerShell**](deploy-appliance-script.md) au lieu du modèle OVA téléchargé, les deux premières étapes de cette procédure ne s’appliquent pas.

1. Dans la console du client vSphere, cliquez avec le bouton droit sur le serveur, puis sélectionnez **Ouvrir la console**.
2. Spécifiez la langue, le fuseau horaire et le mot de passe pour l’appliance.
3. Ouvrez un navigateur sur un serveur pouvant se connecter au serveur de l’appliance, puis ouvrez l’URL du gestionnaire de configuration de l’appliance : `https://appliance name or IP address: 44368`.

   Vous pouvez également ouvrir le gestionnaire de configuration à partir du bureau du serveur de l’appliance en sélectionnant le raccourci du gestionnaire de configuration.
1. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
1. Dans le gestionnaire de configuration > **Configurer les prérequis**, procédez comme suit :
   - **Connectivité** : l’appliance vérifie que le serveur a accès à Internet. Si le serveur utilise un proxy :
     - Cliquez sur **Configurer le proxy** pour spécifier l’adresse du proxy sous la forme `http://ProxyIPAddress` ou `http://ProxyFQDN`, ainsi que le port d’écoute.
     - Spécifiez les informations d’identification si le proxy nécessite une authentification.
     - Seuls les proxys HTTP sont pris en charge.
     - Si vous avez ajouté les détails du proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
   - **Synchronisation de l’heure** : L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
   - **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées. Une fois la vérification terminée, vous pouvez cliquer sur **Afficher les services de l’appliance** pour voir l’état et les versions des services s’exécutant sur le serveur d’appliance.
   - **Installer VDDK** : L’appliance vérifie que VDDK (VMware vSphere Virtual Disk Development Kit) est installé. S’il n’est pas installé, téléchargez VDDK 6.7 à partir de VMware, puis extrayez le contenu du fichier zip téléchargé sur l’appliance à l’emplacement indiqué dans les **instructions d’installation**.

     L’outil de migration de serveur Azure Migrate utilise VDDK pour répliquer les serveurs durant la migration vers Azure. 
1. Si vous le souhaitez, vous pouvez **réexécuter les prérequis** à tout moment pendant la configuration de l’appliance pour vérifier si celle-ci remplit les remplit tous.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panneau 1 sur le gestionnaire de configuration de l’appliance":::


## <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la **clé de projet** copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Découverte et évaluation > Découvrir > Gérer des appliances existantes**, sélectionnez le nom d’appliance que vous avez indiqué au moment de générer la clé, puis copiez la clé correspondante.
1. Vous aurez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. Le fait de cliquer sur **Connexion** ouvre une boîte de dialogue modale comprenant le code de l’appareil, comme celle affichée ci-dessous.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Boîte de dialogue modale indiquant le code de l’appareil":::

1. Cliquez sur **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
1. Sous le nouvel onglet, collez le code de l’appareil, puis connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure.
   
   La connexion avec un code PIN n’est pas prise en charge.
3. Si vous avez fermé accidentellement l’onglet Connexion avant de vous être connecté, vous devrez actualiser l’onglet Appliance Configuration Manager pour réactiver le bouton de connexion.
1. Une fois connecté, revenez à l’onglet précédent, c’est-à-dire, l’onglet Appliance Configuration Manager.
1. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des autorisations adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
1. Une fois l'appliance inscrite, vous pouvez consulter les détails de l'inscription en cliquant sur **Afficher les détails**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Volet 2 sur le gestionnaire de configuration d’appliance":::

## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

### <a name="provide-vcenter-server-details"></a>Fournir les détails de vCenter Server

L’appliance doit se connecter à vCenter Server pour découvrir les données de configuration et de performances des serveurs.

1. À l’**Étape 1 : Fournir les informations d’identification de vCenter Server**, cliquez sur **Ajouter des informations d’identification** pour attribuer un nom convivial aux informations d’identification, puis ajoutez un **nom d’utilisateur** et un **mot de passe** pour le compte vCenter Server dont l’appliance se servira pour découvrir les serveurs s’exécutant sur le vCenter Server.
    - Vous devriez avoir configuré un compte avec les autorisations requises, comme décrit dans cet article ci-dessus.
    - Si vous souhaitez étendre la découverte à des objets VMware spécifiques (centres de données vCenter Server, clusters, dossier de clusters, hôtes, dossier d’hôtes ou serveurs individuels), passez en revue les instructions contenues dans [cet article](set-discovery-scope.md) pour restreindre le compte utilisé par Azure Migrate.
1. À l’**Étape 2 : Fournir les détails de vCenter Server**, cliquez sur **Ajouter une source de découverte** pour sélectionner le nom convivial des informations d’identification dans la liste déroulante, puis spécifiez l’**adresse IP/nom de domaine complet** du vCenter Server. Vous pouvez laisser le **port** par défaut (443) ou spécifier un port personnalisé sur lequel vCenter Server est à l’écoute, puis cliquez sur **Enregistrer**.
1. Dès que vous cliquez sur **Enregistrer**, l’appliance tente de valider la connexion au vCenter Server avec les informations d’identification indiquées, et affiche l’**état de validation** dans le tableau pour l’adresse IP/le nom de domaine complet de vCenter Server.
1. Vous pouvez **revalider** la connectivité à vCenter Server à tout moment avant de lancer la découverte.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panneau 3 sur le gestionnaire de configuration de l’appliance pour le détails de vCenter Server":::

### <a name="provide-server-credentials"></a>Fournir les informations d’identification du serveur

À l’**Étape 3 : Fournir les informations d’identification de serveur pour effectuer l’inventaire logiciel, l’analyse des dépendances sans agent et la découverte des instances et bases de données SQL Server**, vous pouvez choisir de fournir plusieurs informations d’identification de serveur ou, si vous ne souhaitez pas tirer parti de ces fonctionnalités, vous pouvez ignorer l’étape et procéder à la découverte du vCenter Server. Vous pouvez revenir sur votre décision à tout moment.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panneau 3 sur le gestionnaire de configuration de l’appliance pour les détails du serveur":::


Si vous souhaitez tirer parti de ces fonctionnalités, vous pouvez fournir les informations d’identification du serveur en suivant les étapes ci-dessous. L’appliance tentera de mapper automatiquement les informations d’identification aux serveurs pour exécuter les fonctionnalités de découverte.

- Vous pouvez ajouter les informations d’identification du serveur en cliquant sur le bouton **Ajouter des informations d’identification**. Cela a pour effet d’ouvrir une fenêtre modale dans laquelle vous pouvez choisir le **type d’informations d’identification** dans une liste déroulante.
- Vous pouvez fournir des informations d’identification de domaine/Windows (hors domaine)/Linux (hors domaine)/SQL Server. [Apprenez-en davantage](add-server-credentials.md) sur la manière de fournir des informations d’identification et la façon dont nous les traitons.
- Pour chaque type d’informations d’identification, vous devez spécifier un nom convivial, ajouter un **Nom d’utilisateur** et un **Mot de passe**, puis cliquer sur **Enregistrer**.
- Si vous choisissez des informations d’identification de domaine, vous devez également spécifier le nom de domaine complet (FQDN) du domaine. Celui-ci est requis pour valider l’authenticité des informations d’identification auprès de l’Active Directory de ce domaine.
- Examinez les [autorisations requises](add-server-credentials.md#required-permissions) sur le compte pour la découverte des applications installées, l’analyse des dépendances sans agent ou la découverte des instances et bases de données SQL Server.
- Si vous souhaitez ajouter plusieurs informations d’identification à la fois, cliquez sur **Ajouter** pour enregistrer et ajouter d’autres informations d'identification.
- Lorsque vous cliquez sur **Enregistrer** ou **Ajouter**, l’appliance valide les informations d’identification de domaine auprès de l’Active Directory du domaine pour vérifier leur authenticité. Cela permet d’éviter les verrouillages de compte lorsque l’appliance effectue plusieurs itérations pour mapper les informations d’identification aux serveurs concernés.
- Vous pouvez voir l’**État de validation** de toutes les informations d’identification de domaine dans la table des informations d’identification. Seules les informations d’identification de domaine seront validées.
- Si la validation échoue, vous pouvez cliquer sur état **Échec** pour voir l’erreur rencontrée, puis cliquer sur **Revalider les informations d’identification** après avoir corrigé le problème afin de valider à nouveau les informations d’identification de domaine qui ont échoué.


### <a name="start-discovery"></a>Démarrer la découverte

1. Cliquez sur **Démarrer la découverte** pour lancer la découverte du vCenter Server. Une fois la découverte lancée, vous pouvez vérifier son état par rapport à l’adresse IP ou au le nom de domaine complet de vCenter Server dans la table des sources.
1. Si vous avez fourni des informations d’identification de serveur, l’inventaire logiciel (découverte des applications installées) est automatiquement lancé une fois la découverte de vCenter Server terminée. L’inventaire logiciel est effectué une fois toutes les 12 heures.
1. L’ [inventaire logiciel](how-to-discover-applications.md) identifie les instances SQL Server s’exécutant sur les serveurs et utilisant les informations. L’appliance tente de se connecter aux instances via l’authentification Windows ou les informations d’identification d’authentification de SQL Server fournies sur l’appliance, et de collecter des données sur les bases de données SQL Server et leurs propriétés. La découverte SQL est effectuée une fois toutes les 24 heures.
1. Au cours de l’inventaire logiciel, les informations d’identification des serveurs ajoutées sont comparées aux serveurs et validées pour l’analyse des dépendances sans agent. Vous pouvez activer l’analyse des dépendances sans agent pour les serveurs à partir du portail. Seuls les serveurs dont la validation réussit peuvent être sélectionnés pour activer l’analyse des dépendances sans agent.

La découverte fonctionne comme ceci :
- Environ 15 minutes s’écoulent avant que l’inventaire des serveurs découverts apparaisse dans le portail.
- La découverte des applications installées peut prendre un certain temps. Sa durée dépend du nombre de serveurs découverts. Pour 500 serveurs, environ une heure s’écoule avant que l’inventaire découvert apparaisse dans le portail Azure Migrate.
- Une fois la découverte des serveurs terminée, vous pouvez activer l’analyse des dépendances sans agent sur les serveurs à partir du portail.
- Les données d’instances et de bases de données SQL Server commenceront à apparaître dans le portail dans les 24 heures après le lancement de la découverte.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les tutoriels pour l'[évaluation VMware](./tutorial-assess-vmware-azure-vm.md) et la [migration sans agent](tutorial-migrate-vmware.md).