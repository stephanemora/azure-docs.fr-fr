---
title: Connecter des machines hybrides à Azure à grande échelle
description: Dans cet article, vous allez apprendre à connecter des machines à Azure à l’aide d’Azure Arc pour serveurs (préversion) en utilisant un principal de service.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 45a61b5bc6f1082b84bf94db7e8ad5ce49ec068f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83648058"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Connecter des machines hybrides à Azure à grande échelle

Vous pouvez activer Azure Arc pour serveurs (préversion) pour plusieurs ordinateurs Windows ou Linux dans votre environnement avec plusieurs options flexibles en fonction de vos besoins. À l’aide du modèle de script que nous fournissons, vous pouvez automatiser chaque étape de l’installation, y compris l’établissement de la connexion à Azure Arc. Toutefois, vous devez exécuter ce script de manière interactive avec un compte qui dispose d’autorisations élevées sur l’ordinateur cible et dans Azure. Pour connecter les ordinateurs à Azure Arc pour serveurs, vous pouvez utiliser un [principal de service](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory au lieu d’utiliser votre identité privilégiée pour [connecter l’ordinateur de manière interactive](onboard-portal.md). Un principal du service est une identité de gestion limitée spéciale qui ne dispose que de l’autorisation minimale nécessaire pour connecter des machines à Azure à l’aide de la commande `azcmagent`. Cela est plus sûr que d’utiliser un compte doté de privilèges plus élevés comme un Administrateur client et respecte nos meilleures pratiques en matière de sécurité du contrôle d’accès. Le principal de service est utilisé uniquement pendant l’intégration ; il n’est pas utilisé à d’autres fins.  

Les méthodes d’installation pour installer et configurer l’agent Connected Machine requièrent que la méthode automatisée que vous utilisez dispose des autorisations d’administrateur sur les ordinateurs. Vous utilisez le compte root sur Linux et un membre du groupe Administrateurs local sur Windows.

Avant de commencer, veillez à consulter les [conditions préalables](agent-overview.md#prerequisites) et vérifiez que votre abonnement et vos ressources répondent aux exigences.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

À la fin de ce processus, vous aurez correctement connecté vos machines hybrides à Azure Arc pour serveurs.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Créer un principal de service pour une intégration à grande échelle

Vous pouvez utiliser [Azure PowerShell](/powershell/azure/install-az-ps) pour créer un principal de service avec la cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal). Vous pouvez également suivre les étapes indiquées sous [Créer un principal de service à l’aide du Portail Azure](../../active-directory/develop/howto-create-service-principal-portal.md) pour effectuer cette tâche.

> [!NOTE]
> Quand vous créez un principal de service, votre compte doit être Propriétaire ou Administrateur de l’accès utilisateur sur l’abonnement à utiliser pour l’intégration. Si vous n’avez pas les autorisations appropriées pour créer les attributions de rôles, le principal de service sera créé, mais il ne pourra pas intégrer de machines.
>

Pour créer le principal de service à l’aide de PowerShell, procédez comme suit.

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

Le rôle **Intégration Azure Connected Machine** contient uniquement les autorisations requises pour intégrer un ordinateur. Vous pouvez attribuer l’autorisation du principal de service pour permettre à son étendue d’inclure un groupe de ressources ou un abonnement. Pour ajouter une attribution de rôle, consultez [Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et du Portail Azure](../../role-based-access-control/role-assignments-portal.md) ou [Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Installer l’agent et le connecter à Azure

Les étapes suivantes installent et configurent l’agent Connected Machine sur vos machines hybrides à l’aide du modèle de script, qui effectue les mêmes étapes que celles décrites dans l’article [Connecter des machines hybrides à Azure à partir du Portail Azure](onboard-portal.md). La différence réside dans la dernière étape où vous établissez la connexion à Azure Arc à l’aide de la commande `azcmagent` et du principal de service. 

Voici les paramètres que vous configurez à l’aide de la commande `azcmagent` utilisée pour le principal du service.

* `tenant-id` : Identificateur unique (GUID) qui représente votre instance dédiée d’Azure AD.
* `subscription-id` : ID d’abonnement (GUID) de votre abonnement Azure dans lequel vous souhaitez placer les machines.
* `resource-group` : Nom du groupe de ressources auquel vous souhaitez que vos ordinateurs connectés appartiennent.
* `location` : Voir les [régions Azure prises en charge](overview.md#supported-regions). Cet emplacement peut être identique ou différent de l’emplacement du groupe de ressources.
* `resource-name` : (*Facultatif*) Utilisé pour la représentation de ressource Azure de votre ordinateur local. Si vous ne spécifiez pas cette valeur, le nom d’hôte de l’ordinateur est utilisé.

Pour en savoir plus sur l’outil de ligne de commande `azcmagent`, consultez les [informations de référence relatives à Azcmagent](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Script d’installation Windows

Voici un exemple du script d’installation de l’agent Connected Machine pour Windows qui a été modifié pour utiliser le principal du service afin de prendre en charge une installation entièrement automatisée et non interactive de l’agent.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Script d’installation Linux

Voici un exemple du script d’installation de l’agent Connected Machine pour Linux qui a été modifié pour utiliser le principal du service afin de prendre en charge une installation entièrement automatisée et non interactive de l’agent.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

Une fois que vous avez installé l’agent et que vous l’avez configuré pour qu’il se connecte à Azure Arc pour serveurs (préversion), accédez au portail Azure pour vérifier que le serveur a été correctement connecté. Affichez vos machines dans le [portail Azure](https://aka.ms/hybridmachineportal).

![Connexion au serveur réussie](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à gérer votre machine à l’aide d’[Azure Policy](../../governance/policy/overview.md), par exemple pour la [configuration invité](../../governance/policy/concepts/guest-configuration.md) des machines virtuelles, pour vérifier que l’ordinateur crée des rapports sur l’espace de travail Log Analytics prévu, pour activer l’analyse d’[Azure Monitor sur des machines virtuelles](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) et bien plus encore.

- En savoir plus sur [l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez superviser de manière proactive le système d’exploitation et les charges de travail en cours d’exécution sur la machine, gérer le système d’exploitation à l’aide de runbooks Automation ou de solutions comme Update Management ou utiliser d’autres services Azure tels qu’[Azure Security Center](../../security-center/security-center-intro.md).
