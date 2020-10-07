---
title: Connecter une machine hybride avec Azure Arc enabled servers
description: Découvrez comment connecter et inscrire votre machine hybride à l’aide d’Azure Arc enabled servers.
ms.topic: quickstart
ms.date: 09/23/2020
ms.openlocfilehash: b57f30821a105a99041d8187716b75096116ea8e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327882"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Démarrage rapide : Connecter une machine hybride avec Azure Arc enabled servers

[Azure Arc enabled servers](../overview.md) vous permet de gérer et contrôler vos machines Windows et Linux hébergées dans des environnements locaux, périphériques et multiclouds. Dans ce guide de démarrage rapide, vous allez déployer et configurer l’agent Connected Machine sur votre machine Windows ou Linux hébergée en dehors d’Azure pour permettre la gestion par Arc enabled servers.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Pour déployer l’agent Hybrid Connected Machine d’Arc enabled servers, vous devez disposer d’autorisations d’administrateur sur la machine où installer et configurer l’agent. Vous utilisez le compte racine sous Linux et, sous Windows, un compte membre du groupe Administrateurs local.

* Avant de commencer, veillez à passer en revue les [conditions préalables](../agent-overview.md#prerequisites) de l’agent et à vérifier les éléments suivants :

    * Votre machine cible exécute un [système d’exploitation](../agent-overview.md#supported-operating-systems) pris en charge.

    * Votre compte est affecté aux [rôles Azure requis](../agent-overview.md#required-permissions).

    * Si la machine se connecte via un pare-feu ou un serveur proxy pour communiquer sur Internet, assurez-vous que les URL [répertoriées](../agent-overview.md#networking-configuration) ne sont pas bloquées.

    * Azure Arc enabled servers prend uniquement en charge les régions spécifiées [ici](../overview.md#supported-regions).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Inscrire des fournisseurs de ressources Azure

Pour utiliser ce service, les serveurs activés par Azure Arc dépendent des fournisseurs de ressources Azure suivants dans votre abonnement :

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Enregistrez-les à l’aide des commandes suivantes :

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Générer un script d’installation

Le script permettant d’automatiser le téléchargement et l’installation, ainsi que d’établir la connexion avec Azure Arc, est disponible sur le portail Azure. Pour effectuer le processus, procédez comme suit :

1. Lancez le service Azure Arc dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Machines - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Rechercher des serveurs avec Azure Arc dans Tous les services" border="false":::

1. Dans la page **Machines - Azure Arc**, sélectionnez **Ajouter**, en haut à gauche, ou l’option **Créer une machine - Azure Arc** en bas du volet central.

1. Dans la page **Sélectionner une méthode**, sélectionnez la vignette **Ajouter des machines à l’aide d’un script interactif**, puis sélectionnez **Générer un script**.

1. Dans la page **Générer un script**, sélectionnez l’abonnement et le groupe de ressources où vous souhaitez que la machine soit gérée dans Azure. Sélectionnez une localisation Azure destinée au stockage des métadonnées de la machine.

1. Dans la page **Générer un script**, dans la liste déroulante **Système d’exploitation**, sélectionnez le système d’exploitation sur lequel le script s’exécutera.

1. Si la machine communique via un serveur proxy pour se connecter à Internet, sélectionnez **Suivant : Server proxy**.

1. Sous l’onglet **Serveur proxy**, spécifiez l’adresse IP du serveur proxy ou le nom et le numéro de port que la machine doit utiliser pour communiquer avec le serveur proxy. Saisissez la valeur au format `http://<proxyURL>:<proxyport>`.

1. Sélectionnez **Vérifier + générer**.

1. Sous l’onglet **Vérifier + générer**, passez en revue les informations de résumé, puis sélectionnez **Télécharger**. Si vous avez encore besoin d’apporter des modifications, sélectionnez **Précédent**.

## <a name="install-the-agent-using-the-script"></a>Installer l’agent à l’aide du script

### <a name="windows-agent"></a>Agent Windows

1. Connectez-vous au serveur.

1. Ouvrez une invite de commandes PowerShell 64 bits avec élévation de privilèges.

1. Accédez au dossier ou au partage sur lequel vous avez copié le script et exécutez-le sur le serveur en exécutant le script `./OnboardingScript.ps1`.

### <a name="linux-agent"></a>Agent Linux

1. Pour installer l’agent Linux sur la machine cible pouvant directement communiquer avec Azure, exécutez les commandes suivantes :

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Si la machine cible communique par le biais d’un serveur proxy, exécutez la commande suivante :

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Vérifier la connexion avec Azure Arc

Une fois que vous avez installé l’agent et que vous l’avez configuré pour qu’il se connecte à Azure Arc enabled servers, accédez au portail Azure pour vérifier que le serveur s’est correctement connecté. Affichez vos machines dans le [portail Azure](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Rechercher des serveurs avec Azure Arc dans Tous les services" border="false":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez activé votre machine hybride Linux ou Windows et que vous vous êtes connecté au service avec succès, vous pouvez activer Azure Policy pour comprendre la conformité dans Azure.

Pour savoir comment identifier la machine activée Azure Arc enabled servers sur laquelle l’agent Log Analytics n’est pas installé, suivez le tutoriel :

> [!div class="nextstepaction"]
> [Créer une affectation de stratégie pour identifier les ressources non conformes](tutorial-assign-policy-portal.md)
