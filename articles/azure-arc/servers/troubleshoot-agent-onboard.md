---
title: Résoudre les problèmes de connexion liés à l’agent Serveurs activés par Azure Arc
description: Cet article explique comment résoudre les problèmes liés à l’agent Connected Machine qui surviennent avec des Serveurs activés par Azure Arc (préversion) lors de la tentative de connexion au service.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497997"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Résoudre les problèmes de connexion liés à l’agent Serveurs activés par Azure Arc

Cet article fournit des informations sur la résolution des problèmes qui peuvent survenir lors de la tentative de configuration de l’agent Connected Machine de Serveurs activés par Azure Arc pour Windows ou Linux. Les méthodes d'installation interactive et d'installation à grande échelle utilisables lors de la configuration de la connexion au service sont toutes deux incluses. Pour obtenir des informations d’ordre général, consultez [Présentation de Serveurs avec Arc](./overview.md).

## <a name="agent-error-codes"></a>Codes d’erreur de l’agent

Si vous recevez une erreur lors de la configuration de l’agent des serveurs avec Azure Arc, le tableau suivant peut vous aider à identifier la cause probable et les étapes suggérées pour résoudre votre problème. Vous aurez besoin du code d’erreur `AZCM0000` (« 0000 » peut être n’importe quel nombre à 4 chiffres) imprimé sur la console ou de la sortie du script pour continuer.

| Code d'erreur | Cause probable | Correction suggérée |
|------------|----------------|-----------------------|
| AZCM0000 | L’action a réussi | N/A |
| AZCM0001 | Une erreur inconnue s’est produite. | Contactez le support technique Microsoft pour obtenir une assistance supplémentaire |
| AZCM0011 | L’utilisateur a annulé l’action (CTRL+C) | Réessayez la commande précédente |
| AZCM0012 | Le jeton d’accès fourni n’est pas valide | Obtenez un nouveau jeton d’accès et réessayez |
| AZCM0013 | Les étiquettes fournies ne sont pas valides | Vérifiez que les étiquettes sont placées entre guillemets doubles, séparées par des virgules, et que les noms ou les valeurs avec des espaces sont placés entre guillemets simples : `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | Le cloud n’est pas valide | Spécifiez un cloud pris en charge : `AzureCloud` ou `AzureUSGovernment` |
| AZCM0015 | L’ID de corrélation spécifié n’est pas un GUID valide | Fournissez un GUID valide pour `--correlation-id` |
| AZCM0016 | Paramètre obligatoire manquant | Examinez la sortie pour identifier les paramètres manquants |
| AZCM0017 | Le nom de ressource n’est pas valide | Spécifiez un nom qui utilise uniquement des caractères alphanumériques, des traits d’union et/ou des traits de soulignement. Le nom ne peut pas se terminer par un trait d’union ni un trait de soulignement. |
| AZCM0018 | La commande a été exécutée sans privilèges administratifs | Réessayez la commande avec des privilèges d’administrateur ou racine dans une invite de commandes ou une session de console avec élévation de privilèges. |
| AZCM0041 | Les informations d’identification fournies ne sont pas valides | Pour les connexions d’appareils, vérifiez que le compte d’utilisateur spécifié a accès au locataire et à l’abonnement où la ressource de serveur sera créée. Pour les connexions du principal de service, vérifiez que l’ID client et le secret sont corrects, la date d’expiration du secret et que le principal de service provient du même locataire que celui où la ressource de serveur sera créée. |
| AZCM0042 | Échec de la création de la ressource de serveur avec Azure Arc | Vérifiez que l’utilisateur/le principal de service indiqué a accès pour créer des ressources de serveur avec Azure Arc dans le groupe de ressources spécifié. |
| AZCM0043 | Échec de la suppression de la ressource de serveur avec Azure Arc | Vérifiez que l’utilisateur/le principal de service indiqué a accès pour supprimer des ressources de serveur avec Azure Arc dans le groupe de ressources spécifié. Si la ressource n’existe plus dans Azure, utilisez l’indicateur `--force-local-only` pour continuer. |
| AZCM0044 | Une ressource du même nom existe déjà | Spécifiez un autre nom pour le paramètre `--resource-name` ou supprimez le serveur avec Azure Arc existant dans Azure, puis réessayez. |
| AZCM0061 | Impossible d’atteindre le service de l’agent | Vérifiez que vous exécutez la commande dans un contexte utilisateur avec élévation de privilèges (administrateur/racine) et que le service HIMDS est en cours d’exécution sur votre serveur. |
| AZCM0062 | Une erreur s’est produite lors de la connexion du serveur | Passez en revue les autres codes d’erreur de la sortie pour obtenir des informations plus spécifiques. Si l’erreur s’est produite après la création de la ressource Azure, vous devez supprimer le serveur Arc de votre groupe de ressources avant d’effectuer une nouvelle tentative. |
| AZCM0063 | Une erreur s’est produite lors de la déconnexion du serveur | Passez en revue les autres codes d’erreur de la sortie pour obtenir des informations plus spécifiques. Si vous continuez à rencontrer cette erreur, vous pouvez supprimer la ressource dans Azure, puis exécuter `azcmagent disconnect --force-local-only` sur le serveur pour déconnecter l’agent. |
| AZCM0064 | Le service de l’agent ne répond pas | Vérifiez l’état du service `himds` pour vous assurer qu’il est en cours d’exécution. Démarrez le service s’il n’est pas en cours d’exécution. S’il est en cours d’exécution, patientez une minute, puis réessayez. |
| AZCM0065 | Une erreur de communication interne de l’agent s’est produite | Contactez le support technique Microsoft pour obtenir une assistance |
| AZCM0066 | Le service web de l’agent ne répond pas ou n’est pas disponible | Contactez le support technique Microsoft pour obtenir une assistance |
| AZCM0067 | L’agent est déjà connecté à Azure | Suivez d’abord les étapes décrites dans [Déconnecter l’agent](manage-agent.md#unregister-machine), puis réessayez. |
| AZCM0068 | Une erreur interne s’est produite lors de la déconnexion du serveur d’Azure | Contactez le support technique Microsoft pour obtenir une assistance |
| AZCM0081 | Une erreur s’est produite lors du téléchargement du certificat d’identité managée Azure Active Directory | Si ce message s’affiche lors d’une tentative de connexion du serveur à Azure, l’agent ne peut pas communiquer avec le service Azure Arc. Supprimez la ressource dans Azure et réessayez de vous connecter. |
| AZCM0101 | La commande n’a pas été analysée correctement | Exécutez `azcmagent <command> --help` pour vérifier la syntaxe de commande correcte |
| AZCM0102 | Impossible de récupérer le nom d’hôte de l’ordinateur | Exécutez `hostname` pour rechercher les messages d’erreur au niveau du système, puis contactez le support technique Microsoft. |
| AZCM0103 | Une erreur s’est produite lors de la génération des clés RSA | Contactez le support technique Microsoft pour obtenir une assistance |
| AZCM0104 | Échec de la lecture des informations système | Vérifiez que l’identité utilisée pour exécuter `azcmagent` possède des privilèges d’administrateur/racine sur le système, puis réessayez. |

## <a name="agent-verbose-log"></a>Journal d'activité détaillé de l'agent

Avant de suivre les étapes de résolution des problèmes décrites plus loin dans cet article, vous devez au minimum disposer du journal d'activité détaillé. Celui-ci contient la sortie des commandes de l'outil **azcmagent**, lorsque l'argument détaillé (-v) est utilisé. Les fichiers journaux sont enregistrés sous `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` pour Windows, et sous `/var/opt/azcmagent/log/azcmagent.log` pour Linux.

### <a name="windows"></a>Windows

L'exemple de commande suivant permet d'activer la journalisation détaillée avec l'agent Connected Machine pour Windows lors d'une installation interactive.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

L'exemple de commande suivant permet d'activer la journalisation détaillée avec l'agent Connected Machine pour Windows lors d'une installation à grande échelle à l'aide d'un principal de service.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

L'exemple de commande suivant permet d'activer la journalisation détaillée avec l'agent Connected Machine pour Linux lors d'une installation interactive.

>[!NOTE]
>Vous devez disposer des autorisations d’accès *racine* sur les ordinateurs Linux pour exécuter **azcmagent**.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

L'exemple de commande suivant permet d'activer la journalisation détaillée avec l'agent Connected Machine pour Linux lors d'une installation à grande échelle à l'aide d'un principal de service.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problèmes rencontrés par l'agent pour se connecter au service

Le tableau suivant répertorie certaines erreurs connues ainsi que des suggestions de résolution de celles-ci.

|Message |Error |Cause probable |Solution |
|--------|------|---------------|---------|
|Échec de l'acquisition du flux de périphérique du jeton d'autorisation |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Impossible de joindre le point de terminaison `login.windows.net` | Vérifiez la connectivité au point de terminaison. |
|Échec de l'acquisition du flux de périphérique du jeton d'autorisation |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Le proxy ou le pare-feu bloque l'accès au point de terminaison `login.windows.net`. | Vérifiez que la connectivité au point de terminaison n'est pas bloquée par un pare-feu ou par un serveur proxy. |
|Échec de l'acquisition du flux de périphérique du jeton d'autorisation  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | L’objet de stratégie de groupe (GPO) *Configuration ordinateur\Modèles d’administration\Système\Profils utilisateur\Supprimer les profils utilisateur datant de plus d’un certain nombre de jours lors du redémarrage du système* est activé. | Vérifiez que le GPO est activé et cible la machine concernée. Pour plus d’informations, consultez la note de bas de page <sup>[1](#footnote1)</sup>. |
|Échec de l'acquisition du jeton d'autorisation à partir du nom du principal de service |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Le proxy ou le pare-feu bloque l'accès au point de terminaison `login.windows.net`. |Vérifiez que la connectivité au point de terminaison n'est pas bloquée par un pare-feu ou par un serveur proxy. |
|Échec de l'acquisition du jeton d'autorisation à partir du nom du principal de service |`Invalid client secret is provided` |Secret du principal de service incorrect ou non valide. |Vérifiez le secret du principal de service. |
| Échec de l'acquisition du jeton d'autorisation à partir du nom du principal de service |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Principal du service et/ou ID de locataire incorrects. |Vérifiez le principal de service et/ou l'ID du locataire.|
|Obtenir une réponse des ressources ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Informations d'identification et/ou autorisations incorrectes |Vérifiez que vous ou le principal de service êtes membre du rôle **Intégration d'Azure Connected Machine**. |
|Échec de la ressource ARM AzcmagentConnect |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Les fournisseurs de ressources Azure ne sont pas inscrits. |Inscrivez les [fournisseurs de ressources](./agent-overview.md#register-azure-resource-providers). |
|Échec de la ressource ARM AzcmagentConnect |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Le serveur proxy ou le pare-feu bloque l'accès au point de terminaison `management.azure.com`. |Vérifiez que la connectivité au point de terminaison n'est pas bloquée par un pare-feu ou par un serveur proxy. |

<a name="footnote1"></a><sup>1</sup> Si ce GPO est activé et s’applique aux machines dotées de l’agent Connected Machine, il supprime le profil utilisateur associé au compte intégré spécifié pour le service *himds*. Par conséquent, il supprime également le certificat d’authentification utilisé pour communiquer avec le service mis en cache dans le magasin de certificats local pendant 30 jours. Avant la délai de 30 jours, une tentative de renouvellement du certificat est effectuée. Pour résoudre ce problème, suivez les étapes visant à [désinscrire la machine](manage-agent.md#unregister-machine), puis inscrivez-la de nouveau auprès du service qui exécute `azcmagent connect`.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas ici ou que vous ne pouvez pas le résoudre, utilisez un des canaux suivants pour obtenir une aide supplémentaire :

* Obtenez des réponses d'experts Azure par le biais de [Microsoft Q&A](/answers/topics/azure-arc.html).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Le Support Azure fournit à la communauté Azure des réponses, un support technique et des experts.

* Signaler un incident au support Azure Accédez au [site du support Azure](https://azure.microsoft.com/support/options/), puis sélectionnez **Obtenir de l’aide**.
