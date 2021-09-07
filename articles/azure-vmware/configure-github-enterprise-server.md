---
title: Configurer GitHub Enterprise Server sur Azure VMware Solution
description: Découvrez comment configurer GitHub Enterprise Server sur votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: 7b3289742c765e6b809cd96db3bfa51d8407a282
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534943"
---
# <a name="configure-github-enterprise-server-on-azure-vmware-solution"></a>Configurer GitHub Enterprise Server sur Azure VMware Solution

Cet article vous explique comment configurer GitHub Enterprise Server, la version « locale » de [GitHub.com](https://github.com/), sur votre cloud privé Azure VMware Solution. Ce scénario concerne une instance GitHub Enterprise Server pouvant servir jusqu’à 3 000 développeurs exécutant jusqu’à 25 travaux par minute sur GitHub Actions. Il comprend l’installation (au moment de la rédaction de cet article) des *fonctionnalités d’évaluation*, telles que GitHub Actions. Pour personnaliser la configuration en fonction de vos besoins spécifiques, consultez la configuration requise indiquée dans [Installing GitHub Enterprise Server on VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations) (Installation de GitHub Enterprise Server sur VMware).

## <a name="before-you-begin"></a>Avant de commencer

GitHub Enterprise Server nécessite une clé de licence valide. Vous pouvez vous inscrire pour obtenir une [licence d’essai](https://enterprise.github.com/trial). Si vous souhaitez étendre les capacités de GitHub Enterprise Server par le biais d’une intégration, vous pouvez bénéficier d’une licence de développement gratuite pour cinq postes. Appliquez cette licence par le biais du [programme partenaire de GitHub](https://partner.github.com/).

## <a name="install-github-enterprise-server-on-vmware"></a>Installer GitHub Enterprise Server sur VMware

1. Téléchargez [la version actuelle de GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) pour VMware ESXi/vSphere (OVA) et [déployez le modèle OVA](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) que vous avez téléchargé.

   :::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Capture d’écran montrant les options d’installation de GitHub Enterprise Server sur VMware."::: 

   :::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Capture d’écran montrant l’option de menu Deploy the OVA Template.":::    

1. Donnez un nom reconnaissable à votre nouvelle machine virtuelle, par exemple GitHubEnterpriseServer. Vous n’avez pas besoin d’inclure les détails de la version dans le nom de la machine virtuelle, car ces détails deviennent obsolètes lorsque l’instance est mise à niveau. 

1. Sélectionnez toutes les valeurs par défaut pour l’instant (nous modifierons ces détails prochainement) et attendez que l’OVA soit importé.

1. Une fois l’importation terminée, [ajustez la configuration matérielle](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) en fonction de vos besoins. Dans notre exemple de scénario, nous avons besoin de la configuration suivante.

   | Ressource | Installation standard | Installation standard + « fonctionnalités bêta » (Actions) |
   | --- | --- | --- |
   | Processeurs virtuels | 4 | 8 |
   | Mémoire | 32 Go | 61 Go |
   | Stockage associé | 250 Go | 300 Go |
   | Stockage racine | 200 Go | 200 Go |

   Vos besoins peuvent varier. Reportez-vous aux conseils relatifs aux considérations matérielles [Installing GitHub Enterprise Server on VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations) (Installation de GitHub Enterprise Server sur VMware). Consultez également [Adding CPU or memory resources for VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) (Ajout de ressources de processeur ou mémoire pour VMware) pour personnaliser la configuration matérielle en fonction de votre situation.

## <a name="configure-the-github-enterprise-server-instance"></a>Configurer l'instance de GitHub Enterprise Server

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Capture d’écran de la fenêtre Install GitHub Enterprise."::: 

Une fois que la machine virtuelle nouvellement provisionnée a été mise sous tension, [configurez-la à l’aide de votre navigateur](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Il vous sera demandé de charger votre fichier de licence et de définir un mot de passe pour la console de gestion. Veillez à noter ce mot de passe dans un endroit sûr.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Capture de l’écran SSH Access de GitHub Enterprise pour ajouter une nouvelle clé SSH.":::    

Nous vous recommandons d’effectuer au moins les étapes suivantes :

1. Téléchargez une clé SSH publique dans la console de gestion pour pouvoir [accéder à l’interpréteur de commandes d’administration par le biais de SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Configurez le protocole TLS sur votre instance](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) pour pouvoir utiliser un certificat signé par une autorité de certification approuvée. Appliquez vos paramètres.

   :::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Capture d’écran montrant les paramètres appliqués à votre instance.":::

1. Pendant le redémarrage de l’instance, configurez le stockage Blob pour GitHub Actions.

   >[!NOTE]
   >GitHub Actions est [actuellement disponible en tant que version bêta limitée sur GitHub Enterprise Server version 2.22](https://docs.github.com/en/enterprise/admin/github-actions).
    
   Le stockage blob externe est nécessaire pour activer GitHub Actions sur GitHub Enterprise Server (actuellement disponible en tant que fonctionnalité « bêta »). Actions utilise ce stockage Blob externe pour stocker des artefacts et des journaux. Actions sur GitHub Enterprise Server [prend en charge Stockage Blob Azure en tant que fournisseur de stockage](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (et d’autres). Nous allons donc provisionner un nouveau compte de stockage Azure avec le [type de compte de stockage](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage.
    
   :::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Capture d’écran montrant les informations de l’instance à entrer pour le provisionnement d’un compte de stockage Blob Azure.":::
    
1. Après le déploiement de la nouvelle ressource BlobStorage, enregistrez la chaîne de connexion (disponible sous Access keys). Vous aurez besoin de cette chaîne dans un instant.

1. Une fois l’instance redémarrée, créez un compte administrateur sur l’instance. Veillez à noter également le mot de passe de cet utilisateur.

   :::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Capture d’écran montrant la fenêtre Create admin account pour GitHub Enterprise.":::

### <a name="other-configuration-steps"></a>Autres étapes de configuration

Pour renforcer votre instance à des fins de production, il est recommandé d’effectuer les étapes de configuration facultatives suivantes :

1. Configurez [la haute disponibilité](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) pour la protection contre les incidents suivants :

    - Pannes logicielles (au niveau du système d’exploitation ou de l’application)
    - Défaillances matérielles (stockage, UC, RAM, etc.)
    - Défaillances du système hôte de virtualisation
    - Réseau rompu logiquement ou physiquement

2. [Configurez](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [des utilitaires de sauvegarde](https://github.com/github/backup-utils) en fournissant des instantanés de version pour la récupération d’urgence hébergés dans une disponibilité distincte de l’instance principale.
3. [Configurez l’isolement de sous-domaine](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation) en utilisant un certificat TLS valide pour limiter le scripting inter-site et autres vulnérabilités associées.


## <a name="set-up-the-github-actions-runner"></a>Configurer l'exécuteur GitHub Actions

> [!NOTE]
> GitHub Actions est [actuellement disponible en tant que version bêta limitée sur GitHub Enterprise Server version 2.22](https://docs.github.com/en/enterprise/admin/github-actions).

À ce stade, vous devez avoir une instance GitHub Enterprise Server en état de fonctionnement, avec un compte administrateur créé. Vous devez également disposer d’un stockage Blob externe utilisé par GitHub Actions pour la persistance.

Créez un emplacement où exécuter GitHub Actions. Là encore, nous allons utiliser Azure VMware Solution.

1. Provisionnez une nouvelle machine virtuelle sur le cluster et basez-la sur [une version récente d’Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

   :::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Capture d’écran montrant le nom et l’emplacement de la machine virtuelle pour provisionner une nouvelle machine virtuelle.":::

1. Poursuivez la configuration en sélectionnant la ressource de calcul, le stockage et la compatibilité.

1. Sélectionnez le système d’exploitation invité que vous voulez installer sur la machine virtuelle.

   :::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Capture d’écran montrant la famille de systèmes d’exploitation invités et la version de système d’exploitation invité à installer sur la machine virtuelle.":::

1. Une fois la machine virtuelle créée, mettez-la sous tension et connectez-vous via SSH.

1. Installez l’application de [l’exécuteur Actions](https://github.com/actions/runner), qui exécute un travail à partir d’un workflow GitHub Actions. Identifiez et téléchargez la version Linux x64 la plus récente de l’exécuteur Actions, soit à partir de [la page des versions](https://github.com/actions/runner/releases), soit en exécutant le script rapide suivant. Ce script nécessite la présence de curl et [jq](https://stedolan.github.io/jq/) sur votre machine virtuelle.

   ```bash
   LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \
    
   jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )
    
   DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \
    
   jq -r '.browser\_download\_url' )
    
   curl -OL $DOWNLOAD\_URL
   ```
    
   Vous devez maintenant disposer d’un fichier local sur votre machine virtuelle, actions-runner-linux-arm64-\*.tar.gz. Extrayez ce tarball localement :
    
   ```bash
   tar xzf actions-runner-linux-arm64-\*.tar.gz
   ```
    
   Cette extraction décompresse quelques fichiers localement, notamment les scripts `config.sh` et `run.sh`.

## <a name="enable-github-actions"></a>Activer GitHub Actions

>[!NOTE]
>GitHub Actions est [actuellement disponible en tant que version bêta limitée sur GitHub Enterprise Server version 2.22](https://docs.github.com/en/enterprise/admin/github-actions).

Configurez et activez GitHub Actions sur l’instance GitHub Enterprise Server. 

1. [Accédez à l’interpréteur de commandes d’administration de l’instance GitHub Enterprise Server via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh), puis exécuter les commandes suivantes :

1. Définissez une variable d’environnement contenant votre chaîne de connexion de stockage Blob.

   ```bash
   export CONNECTION\_STRING="<your connection string from the blob storage step>"
   ```    

1. Configurez le stockage des actions.
    
   ```bash
   ghe-config secrets.actions.storage.blob-provider azure
  
   ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING`      
   ```

1. Appliquez les paramètres.

   ```bash
   ghe-config-apply
   ```    

1. Exécutez une prévérification pour installer les logiciels supplémentaires requis par Actions sur GitHub Enterprise Server.
    
   ```bash
   ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"
   ```

1. Activez les actions, puis réappliquez la configuration.
 
   ```bash
   ghe-config app.actions.enabled true
    
   ghe-config-apply      
   ```

1. Vérifiez l’intégrité de votre stockage Blob.

   ```bash
   ghe-actions-check -s blob
   ```

   Cette sortie doit s’afficher : _Stockage Blob est sain_.

1. La fonctionnalité **GitHub Actions** est configurée. À présent, activez-la pour vos utilisateurs. Connectez-vous à votre instance GitHub Enterprise Server en tant qu’administrateur, puis sélectionnez l’icône :::image type="icon" source="media/github-enterprise-server/rocket-icon.png"::: dans l’angle supérieur droit d’une page. 

1. Dans la barre latérale gauche, sélectionnez **Vue d’ensemble de l’entreprise**, puis **Stratégies**, **Actions**, et sélectionnez l’option permettant d’**activer Actions pour toutes les organisations**.

1. Configurez votre exécuteur à partir de l’onglet **Self-hosted runners** (Exécuteurs auto-hébergés). Sélectionnez **Add new**, puis **New runner** (Nouvel exécuteur) dans le menu déroulant. Un ensemble de commandes à exécuter s’affiche.

1. Copiez la commande pour **configurer** l’exécuteur, par exemple :

   ```bash
   ./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA
   ```

1. Copiez la commande `config.sh` et collez-la dans une session sur votre exécuteur Actions (créé précédemment).

   :::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Capture d’écran montrant l’inscription et les paramètres de l’exécuteur GitHub Actions.":::

1. Utilisez la commande `./run.sh` pour *exécuter* l’exécuteur :

   >[!TIP]
   >Pour mettre cet exécuteur à la disposition des organisations de votre entreprise, modifiez son accès aux organisations. Vous pouvez limiter l’accès à un sous-ensemble d’organisations, voire à des dépôts spécifiques.
   >
   >:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Capture d’écran montrant comment modifier l’accès pour les exécuteurs auto-hébergés.":::


## <a name="optional-configure-github-connect"></a>(Facultatif) Configurer GitHub Connect

Bien que cette étape soit facultative, nous vous recommandons de l’utiliser si vous envisagez de consommer des actions open source disponibles sur GitHub.com. Cela vous permet de vous appuyer sur le travail des autres en référençant ces actions réutilisables dans vos workflows.

Pour activer GitHub Connect, suivez les étapes décrites dans [Enabling automatic access to GitHub.com actions using GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect) (Activation de l’accès automatique aux actions de GitHub.com en utilisant GitHub Connect).

Une fois que GitHub Connect est activé, sélectionnez l’option **Serveur permettant d’utiliser des actions de GitHub.com dans des exécutions de workflow**.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Capture d’écran montrant l’option Server can use actions from GitHub.com in workflow runs définie sur Enabled.":::

## <a name="set-up-and-run-your-first-workflow"></a>Configurer et exécuter votre premier workflow

Maintenant que les fonctionnalités GitHub Actions et GitHub Connect sont configurées, mettons tout ce travail à profit. Voici un exemple de workflow qui fait référence à l’excellent [octokit/request-action](https://github.com/octokit/request-action), qui nous permet de « scripter » GitHub par le biais d’interactions à l’aide de l’API GitHub, alimentée par GitHub Actions.

Dans ce workflow de base, nous allons utiliser `octokit/request-action` pour soumettre un problème sur GitHub à l’aide de l’API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Capture d’écran d’un exemple de workflow.":::

>[!NOTE]
>GitHub.com héberge l’action, mais lorsque celle-ci s’exécute sur GitHub Enterprise Server, elle utilise *automatiquement* l’API de GitHub Enterprise Server.

Si vous choisissez de ne pas activer GitHub Connect, vous pouvez utiliser l’autre workflow suivant.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Capture d’écran d’un autre exemple de workflow.":::

1. Accédez à un référentiel sur votre instance et ajoutez le workflow ci-dessus en tant que : `.github/workflows/hello-world.yml`.

   :::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Capture d’écran d’un autre exemple de workflow.":::

1. Dans l’onglet **Actions** pour votre référentiel, attendez que le workflow s’exécute.

   :::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Capture d’écran d’un exemple de workflow exécuté.":::

   Vous pouvez observer son traitement par l’exécuteur.

   :::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Capture d’écran du workflow traité par l’exécuteur.":::

Si tout s’est bien passé, vous devriez voir un nouveau problème dans votre référentiel, intitulé « Hello world ».

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Capture d’écran du problème Hello world dans GitHub créé par github-actions.":::

Félicitations ! Vous venez de terminer votre premier workflow Actions sur GitHub Enterprise Server, qui s’exécute sur votre cloud privé Azure VMware Solution.

Dans cet article, nous avons configuré une nouvelle instance GitHub Enterprise Server, l’équivalent auto-hébergé de GitHub.com, sur votre cloud privé Azure VMware Solution. L’instance comprend la prise en charge de GitHub Actions et utilise Stockage Blob Azure pour la persistance des journaux et des artefacts. Mais nous n’avons fait qu’effleurer la surface de ce que vous pouvez faire avec GitHub Actions. Consultez la liste des actions sur la [place de marché de GitHub](https://github.com/marketplace) ou [créez vos propres actions](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que nous avons vu comment configurer GitHub Enterprise Server sur votre cloud privé Azure VMware Solution, vous souhaiterez peut-être en savoir plus sur : 

- [la prise en main de GitHub Actions](https://docs.github.com/en/actions) ;
- [la participation au programme bêta](https://resources.github.com/beta-signup/) ;
- [l’administration de GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started).
