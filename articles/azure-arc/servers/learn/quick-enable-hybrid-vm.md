---
title: Connecter une machine hybride à l’aide d’un serveur avec Azure Arc (préversion)
description: Découvrez comment vous connecter et inscrire votre machine hybride à l’aide des serveurs avec Azure Arc (préversion).
ms.topic: quickstart
ms.date: 08/12/2020
ms.openlocfilehash: eacf75871b1f7cc7fc3b703d8859338578e43456
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213616"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers-preview"></a>Démarrage rapide : Connecter une machine hybride à l’aide d’un serveur avec Azure Arc (préversion)

[Les serveurs avec Azure Arc](../overview.md) (préversion) vous permettent de gérer et de régir vos machines Windows et Linux hébergées dans des environnements locaux, de périphérie et multiclouds. Dans ce guide de démarrage rapide, vous allez déployer et configurer l’agent Machine connectée sur votre machine Windows ou Linux hébergée en dehors d’Azure pour qu’il soit géré par les serveurs avec Arc (préversion).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Pour déployer l’agent de Machine connectée hybride (préversion) des serveurs avec Arc, vous devez disposer d’autorisations d’administrateur sur la machine pour l’installation et la configuration de l’agent. Vous utilisez le compte racine sous Linux et, sous Windows, un compte membre du groupe Administrateurs local.

* Avant de commencer, veillez à passer en revue les [conditions préalables](../agent-overview.md#prerequisites) de l’agent et à vérifier les éléments suivants :

    * Votre machine cible exécute un [système d’exploitation](../agent-overview.md#supported-operating-systems) pris en charge.

    * Votre compte est affecté aux [rôles Azure requis](../agent-overview.md#required-permissions).

    * Si la machine se connecte via un pare-feu ou un serveur proxy pour communiquer sur Internet, assurez-vous que les URL [répertoriées](../agent-overview.md#networking-configuration) ne sont pas bloquées.

    * Les serveurs avec Azure Arc (préversion) prennent uniquement en charge les régions spécifiées [ici](../overview.md#supported-regions).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Inscrire des fournisseurs de ressources Azure

Les serveurs avec Azure Arc (préversion) dépendent des fournisseurs de ressources Azure suivants dans votre abonnement pour pouvoir utiliser ce service :

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

Une fois que vous avez installé l’agent et que vous l’avez configuré pour qu’il se connecte à un serveur avec Azure Arc (préversion), accédez au portail Azure pour vérifier que le serveur s’est correctement connecté. Affichez vos machines dans le [portail Azure](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Une connexion de machine réussie" border="false":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez activé votre machine hybride Linux ou Windows et que vous vous êtes connecté au service avec succès, vous pouvez activer Azure Policy pour comprendre la conformité dans Azure.

Pour savoir comment identifier les serveurs avec Azure Arc (préversion) sur lesquels l’agent de l’Analytique des journaux d'activité n’est pas installé, passez au didacticiel :

> [!div class="nextstepaction"]
> [Créer une affectation de stratégie pour identifier les ressources non conformes](tutorial-assign-policy-portal.md)
