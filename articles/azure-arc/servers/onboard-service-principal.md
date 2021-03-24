---
title: Connecter des machines hybrides à Azure à grande échelle
description: Dans cet article, vous allez apprendre à connecter des machines à Azure à l’aide de serveurs activés par Azure Arc en utilisant un principal de service.
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175938"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Connecter des machines hybrides à Azure à grande échelle

Vous pouvez activer des serveurs activés par Azure Arc pour plusieurs ordinateurs Windows ou Linux dans votre environnement avec plusieurs options flexibles en fonction de vos besoins. À l’aide du modèle de script que nous fournissons, vous pouvez automatiser chaque étape de l’installation, y compris l’établissement de la connexion à Azure Arc. Toutefois, vous devez exécuter ce script de manière interactive avec un compte qui dispose d’autorisations élevées sur l’ordinateur cible et dans Azure.

Pour connecter les ordinateurs à des serveur activés par Azure Arc, vous pouvez utiliser un [principal de service](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory au lieu d’utiliser votre identité privilégiée pour [connecter l’ordinateur de manière interactive](onboard-portal.md). Un principal du service est une identité de gestion limitée spéciale qui ne dispose que de l’autorisation minimale nécessaire pour connecter des machines à Azure à l’aide de la commande `azcmagent`. Cela est plus sûr que d’utiliser un compte doté de privilèges plus élevés comme un Administrateur client et respecte nos meilleures pratiques en matière de sécurité du contrôle d’accès. Le principal de service est utilisé uniquement pendant l’intégration ; il n’est pas utilisé à d’autres fins.  

Les méthodes d’installation pour installer et configurer l’agent Connected Machine requièrent que la méthode automatisée que vous utilisez dispose des autorisations d’administrateur sur les ordinateurs. Vous utilisez le compte root sur Linux et un membre du groupe Administrateurs local sur Windows.

Avant de commencer, veillez à consulter les [conditions préalables](agent-overview.md#prerequisites) et vérifiez que votre abonnement et vos ressources répondent aux exigences. Pour plus d'informations sur les régions prises en charge et d'autres considérations connexes, consultez [Régions Azure prises en charge](overview.md#supported-regions). Consultez également notre [Guide de planification à grande échelle](plan-at-scale-deployment.md) pour comprendre les critères de conception et de déploiement, ainsi que nos recommandations en matière de gestion et de surveillance.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Créer un principal de service pour une intégration à grande échelle

Vous pouvez utiliser [Azure PowerShell](/powershell/azure/install-az-ps) pour créer un principal de service avec la cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal). Vous pouvez également suivre les étapes indiquées sous [Créer un principal de service à l’aide du Portail Azure](../../active-directory/develop/howto-create-service-principal-portal.md) pour effectuer cette tâche.

> [!NOTE]
> Quand vous créez un principal de service, votre compte doit être membre des rôles **Propriétaire** ou **Administrateur** de l’accès utilisateur sur l’abonnement à utiliser pour l’intégration. Si vous n’avez pas les autorisations appropriées pour créer les attributions de rôles, le principal de service sera peut-être créé, mais il ne pourra pas intégrer de machines.
>

Pour créer le principal de service à l’aide de PowerShell, suivez ces étapes.

1. Exécutez la commande suivante : Vous devez stocker la sortie de la cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) dans une variable, sinon vous ne pourrez pas récupérer le mot de passe nécessaire à une étape ultérieure.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Pour récupérer le mot de passe stocké dans la variable `$sp`, exécutez la commande suivante :

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Dans la sortie, recherchez la valeur du mot de passe sous le champ **password** et copiez-la. Recherchez aussi la valeur sous le champ **ApplicationId** et copiez-la également. Enregistrez-les pour une utilisation ultérieure dans un endroit sécurisé. Si vous oubliez ou perdez le mot de passe de votre principal de service, vous pouvez le réinitialiser à l’aide de la cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential).

Les valeurs des propriétés suivantes sont utilisées avec les paramètres transmis à `azcmagent` :

* La valeur de la propriété **ApplicationId** est utilisée pour la valeur du paramètre `--service-principal-id`.
* La valeur de la propriété **password** est utilisée pour le paramètre `--service-principal-secret` utilisé pour connecter l’agent.

> [!NOTE]
> Veillez à utiliser la propriété **ApplicationId** du principal de service, et non la propriété **Id**.
>

Le rôle **Intégration Azure Connected Machine** contient uniquement les autorisations requises pour intégrer un ordinateur. Vous pouvez attribuer l’autorisation du principal de service pour permettre à son étendue d’inclure un groupe de ressources ou un abonnement. Pour ajouter une attribution de rôle, consultez [Attribuer des rôles Azure à l’aide du Portail Azure](../../role-based-access-control/role-assignments-portal.md) ou [Attribuer des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Générer le script d’installation à partir du portail Azure

Le script permettant d’automatiser le téléchargement et l’installation, et d’établir la connexion avec Azure Arc, est disponible sur le portail Azure. Pour terminer le processus, procédez comme suit :

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

1. Dans la page **Serveurs - Azure Arc**, sélectionnez **Ajouter** en haut à gauche.

1. Sur la page **Sélectionner une méthode**, sélectionnez la vignette **Ajouter plusieurs serveurs**, puis sélectionnez **Générer un script**.

1. Dans la page **Générer un script**, sélectionnez l’abonnement et le groupe de ressources où vous souhaitez que la machine soit gérée dans Azure. Sélectionnez une localisation Azure destinée au stockage des métadonnées de la machine. Cet emplacement peut être identique ou différent de l’emplacement du groupe de ressources.

1. Dans la page **Prérequis**, passez en revue les informations, puis sélectionnez **Suivant : Détails des ressources**.

1. Dans la page **Détails des ressources**, spécifiez les informations suivantes :

    1. Dans la liste déroulante **Groupe de ressources**, sélectionnez le groupe de ressources à partir duquel sera gérée la machine.
    1. Dans la liste déroulante **Région**, sélectionnez la région Azure dans laquelle seront stockées les métadonnées des serveurs.
    1. Dans la liste déroulante **Système d’exploitation**, sélectionnez le système d’exploitation sur lequel le script est configuré pour s’exécuter.
    1. Si la machine passe par un serveur proxy pour se connecter à Internet, spécifiez l’adresse IP du serveur proxy ou le nom et le numéro de port que la machine doit utiliser pour communiquer avec le serveur proxy. Saisissez la valeur au format `http://<proxyURL>:<proxyport>`.
    1. Sélectionnez **Suivant : authentification**.

1. Sur la page **Authentification**, sous la liste déroulante **Principal du service**, sélectionnez **Arc-for-servers**.  Ensuite, sélectionnez **Suivant : Balises**.

1. Dans la page **Balises**, passez en revue les suggestions de **balises d’emplacement physique** par défaut et entrez une valeur, ou spécifiez une ou plusieurs **Balises personnalisées** en fonction de vos standards.

1. Sélectionnez **Suivant : Télécharger et exécuter le script**.

1. Dans la page **Télécharger et exécuter le script**, passez en revue les informations de résumé, puis sélectionnez **Télécharger**. Si vous avez encore besoin d’apporter des modifications, sélectionnez **Précédent**.

Pour Windows, vous êtes invité à enregistrer `OnboardingScript.ps1` et pour Linux `OnboardingScript.sh` sur votre ordinateur.

## <a name="install-the-agent-and-connect-to-azure"></a>Installer l’agent et le connecter à Azure

Si vous avez créé le modèle de script précédemment, vous pouvez installer et configurer l’agent de l’ordinateur connecté sur plusieurs machines Linux et Windows hybrides à l’aide de l’outil d’automatisation préféré de votre organisation. Ce script effectue des étapes similaires à celles décrites dans l’article [Connecter des machines hybrides à Azure à partir du Portail Azure](onboard-portal.md). La différence réside dans la dernière étape où vous établissez la connexion à Azure Arc à l’aide de la commande `azcmagent` et du principal de service.

Voici les paramètres que vous configurez à l’aide de la commande `azcmagent` utilisée pour le principal du service.

* `service-principal-id` : identificateur unique (GUID) qui représente l’ID d’application du principal de service.
* `service-principal-secret` : mot de passe du principal de service.
* `tenant-id` : Identificateur unique (GUID) qui représente votre instance dédiée d’Azure AD.
* `subscription-id` : ID d’abonnement (GUID) de votre abonnement Azure dans lequel vous souhaitez placer les machines.
* `resource-group` : Nom du groupe de ressources auquel vous souhaitez que vos ordinateurs connectés appartiennent.
* `location` : Voir les [régions Azure prises en charge](overview.md#supported-regions). Cet emplacement peut être identique ou différent de l’emplacement du groupe de ressources.
* `resource-name` : (*Facultatif*) Utilisé pour la représentation de ressource Azure de votre ordinateur local. Si vous ne spécifiez pas cette valeur, le nom d’hôte de l’ordinateur est utilisé.

Pour en savoir plus sur l’outil de ligne de commande `azcmagent`, consultez les [informations de référence relatives à Azcmagent](./manage-agent.md).

>[!NOTE]
>Le script Windows PowerShell ne prend en charge que l’exécution à partir d’une version 64 bits de Windows PowerShell.
>

Une fois que vous avez installé l’agent et que vous l’avez configuré pour qu’il se connecte à Azure Arc enabled servers, accédez au portail Azure pour vérifier que le serveur s’est correctement connecté. Affichez vos machines dans le [portail Azure](https://aka.ms/hybridmachineportal).

![Connexion au serveur réussie](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la résolution des problèmes, consultez le [guide Résoudre les problèmes de l’agent Connected Machine](troubleshoot-agent-onboard.md).

- Apprenez à gérer votre machine à l’aide d’[Azure Policy](../../governance/policy/overview.md), par exemple pour la [configuration invité](../../governance/policy/concepts/guest-configuration.md) des machines virtuelles, pour vérifier que l’ordinateur crée des rapports sur l’espace de travail Log Analytics prévu, pour activer l’analyse d’[Azure Monitor pour machines virtuelles](../../azure-monitor/vm/vminsights-enable-policy.md) et bien plus encore.

- En savoir plus sur [l’agent Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez collecter des données de supervision du système d’exploitation et de la charge de travail avec Azure Monitor pour machines virtuelles, gérer les ressources à l’aide de runbooks Automation ou de fonctionnalités comme Update Management, ou utiliser d’autres services Azure comme [Azure Security Center](../../security-center/security-center-introduction.md).
