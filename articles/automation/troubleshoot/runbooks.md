---
title: Résoudre les erreurs avec les runbooks Azure Automation
description: Découvrez comment résoudre les problèmes avec les runbooks Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b96d723f6c7ca423343c0586f59770abb55ada9f
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929347"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Résoudre les erreurs avec les runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erreurs d’authentification avec des runbooks Azure Automation

### <a name="sign-in-failed"></a>Scénario : La connexion au compte Azure a échoué

#### <a name="issue"></a>Problème

L’erreur suivante s’affiche quand vous utilisez les applets de commande `Add-AzureAccount` ou `Connect-AzureRmAccount`.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Cause :

Cette erreur se produit si le nom de la ressource d’informations d’identification n’est pas valide, ou si le nom d’utilisateur et le mot de passe que vous avez utilisés pour configurer la ressource d’informations d’identification Automation ne sont pas valides.

#### <a name="resolution"></a>Résolution :

Pour identifier le problème, effectuez les étapes suivantes :  

1. Assurez-vous qu’il n’existe aucun caractère spécial, notamment le caractère **@** , dans le nom de la ressources d’informations d’identification Automation que vous utilisez pour vous connecter à Azure.  
2. Vérifiez que vous pouvez utiliser le nom d’utilisateur et le mot de passe stockés dans les informations d’identification Azure Automation dans votre éditeur PowerShell ISE local. Vous pouvez le faire en exécutant les applets de commande suivantes dans PowerShell ISE :  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Si l’authentification échoue localement, cela signifie que vous n’avez pas correctement configuré vos informations d’identification Azure Active Directory. Reportez-vous au billet de blog [Authenticating to Azure using Azure Active Directory (Authentification auprès d’Azure à l’aide d’Azure Active Directory)](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour configurer correctement le compte Azure Active Directory.  

### <a name="unable-to-find-subscription"></a>Scénario : Impossible de trouver l’abonnement Azure

#### <a name="issue"></a>Problème

L’erreur suivante s’affiche quand vous utilisez les applets de commande `Select-AzureSubscription` ou `Select-AzureRmSubscription` :

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

Cette erreur se produit si le nom de l’abonnement n’est pas valide ou si l’utilisateur Azure Active Directory qui essaie d’obtenir les détails de l’abonnement n’est pas configuré comme administrateur de l’abonnement.

#### <a name="resolution"></a>Résolution :

Pour déterminer si vous vous êtes correctement authentifié auprès d’Azure et si avez accès à l’abonnement que vous essayez de sélectionner, effectuez les étapes suivantes :  

1. Assurez-vous d’exécuter l’applet de commande **Add-AzureAccount** avant d’exécuter l’applet de commande **Select-AzureSubscription**.  
2. Si le message d’erreur persiste, modifiez votre code en ajoutant l’applet de commande **Get-AzureSubscription** à la suite de l’applet de commande **Add-AzureAccount**, puis exécutez le code. Maintenant, vérifiez si la sortie de Get-AzureSubscription contient les détails de votre abonnement.  

   * Si la sortie ne contient aucun des détails de l’abonnement, vous pouvez en conclure que l’abonnement n’est pas encore initialisé.  
   * Si vous ne voyez pas les détails de l’abonnement dans la sortie, vérifiez que vous utilisez le bon nom d’abonnement ou le bon identificateur avec l’applet de commande **Select-AzureSubscription** .

### <a name="auth-failed-mfa"></a>Scénario : L’authentification auprès d’Azure a échoué, car l’authentification multifacteur est activée

#### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lors de l’authentification auprès d’Azure avec votre nom d’utilisateur et votre mot de passe Azure :

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Cause :

Si l’authentification multifacteur est configurée sur votre compte Azure, vous ne pouvez pas vous authentifier auprès d’Azure avec un utilisateur Azure Active Directory. Au lieu de cela, vous devez utiliser un certificat ou un principal de service pour l’authentification dans Azure.

#### <a name="resolution"></a>Résolution :

Pour utiliser un certificat avec les applets de commande de modèle de déploiement Azure Classic, reportez-vous à [Création et ajout d’un certificat pour gérer des services Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser un principal du service avec des applets de commande Azure Resource Manager, voir la rubrique sur la [création du principal du service à l’aide du portail Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md) et l’[authentification d’un principal du service à l’aide d’Azure Resource Manager](../../azure-resource-manager/resource-group-authenticate-service-principal.md).

## <a name="common-errors-when-working-with-runbooks"></a>Erreurs courantes avec des runbooks

### <a name="job-attempted-3-times"></a>Scénario : Le travail du runbook a tenté de démarrer à trois reprises, mais n’a pas pu démarrer

#### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Cause :

Cette erreur peut être due aux raisons suivantes :

1. Limite de mémoire. Il existe des limites documentées sur la quantité de mémoire allouée à un bac à sable que le [service Automation limite](../../azure-subscription-service-limits.md#automation-limits), de façon à ce qu’un travail puisse échouer s’il utilise plus de 400 Mo de mémoire.

2. Module incompatible. Cela peut se produire si les dépendances de module ne sont pas correctes. Dans ce cas, votre runbook renvoie généralement un message « Commande introuvable » ou « Impossible de lier le paramètre ».

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Les méthodes recommandées pour travailler dans la limite de mémoire consistent à diviser la charge de travail en plusieurs runbooks, à ne pas traiter trop de données en mémoire, à ne pas écrire de sortie inutile depuis vos runbooks ou à prendre en compte le nombre de points de contrôle que vous écrivez dans vos runbooks de flux de travail PowerShell.  

* Mettez à jour vos modules Azure en suivant les étapes de l’article [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Scénario : Le runbook échoue à cause d’un objet désérialisé

#### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Cause :

Si votre runbook est un flux de travail PowerShell, il stocke des objets complexes au format désérialisé afin de conserver l’état du runbook si le flux de travail est suspendu.

#### <a name="resolution"></a>Résolution :

Chacune des trois solutions suivantes résout ce problème :

1. Si vous transférez des objets complexes d’une applet de commande vers une autre, encapsulez ces applets de commande dans un bloc InlineScript.
2. Transmettez le nom ou la valeur dont vous avez besoin depuis l’objet complexe au lieu de transmettre la totalité de l’objet.
3. Utilisez un runbook PowerShell au lieu d’un runbook Workflow PowerShell.

### <a name="quota-exceeded"></a>Scénario : Le travail du Runbook a échoué, car le quota alloué a été dépassé

#### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Cause :

Cette erreur se produit quand l’exécution du travail dépasse le quota gratuit de 500 minutes pour votre compte. Ce quota s’applique à tous les types de tâches d’exécution de travail, notamment un le test d’un travail, le démarrage d’un travail à partir du portail, l’exécution d’un travail à l’aide de webhooks et la planification d’un travail à exécuter en utilisant le portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification d’Automation, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Résolution :

Si vous voulez utiliser plus de 500 minutes de traitement par mois, vous devez changer votre abonnement et le faire passer du niveau Gratuit au niveau De base. Une mise à niveau vers le niveau de base s’effectue de la façon suivante :  

1. Connectez-vous à votre abonnement Azure :  
2. Sélectionnez le compte Automation que vous souhaitez mettre à niveau  
3. Cliquez sur **Paramètres** > **Tarifs**.
4. Cliquez sur **Activer** en bas de page pour actualiser votre compte au niveau **De base**.

### <a name="cmdlet-not-recognized"></a>Scénario : L’applet de commande n’est pas reconnue lors de l’exécution d’un runbook

#### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Cause :

Cette erreur survient quand le moteur PowerShell ne trouve pas l’applet de commande que vous utilisez dans votre runbook. Cela peut être dû au fait que le module contenant l’applet de commande n’est pas présent dans le compte, qu’il existe un conflit de nom avec un nom de runbook ou que l’applet de commande existe dans un autre module et Automation ne peut pas résoudre le nom.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :  

* Vérifiez que vous avez correctement saisi le nom de l’applet de commande.  
* Assurez-vous que l’applet de commande existe dans votre compte Automation et qu’il n’y a aucun conflit. Pour vérifier si l’applet de commande est présente, ouvrez un runbook en mode édition et recherchez l’applet de commande dans la bibliothèque ou exécutez `Get-Command <CommandName>`. Une fois que vous avez vérifié que l’applet de commande est disponible pour le compte, et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou runbooks, ajoutez-la à la zone de dessin et assurez-vous que vous utilisez un paramètre valide défini dans votre runbook.  
* Si vous rencontrez un conflit de noms et si l’applet de commande est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom qualifié complet de l’applet de commande. Vous pouvez par exemple utiliser **Nom_module\Nom_applet_de_commande**.  
* Si vous exécutez le runbook localement dans un groupe de Workers hybride, assurez-vous que le module/applet de commande est installé sur l'ordinateur qui héberge le Worker hybride.

### <a name="evicted-from-checkpoint"></a>Scénario : Un runbook de longue durée échoue systématiquement avec l’exception : « La tâche ne peut pas continuer, car elle a été exclue à plusieurs reprises du même point de contrôle »

#### <a name="issue"></a>Problème

Il s’agit du comportement par défaut dû à la surveillance de la répartition de charge équilibrée des processus au sein d’Azure Automation, qui suspend automatiquement un runbook s’il s’exécute pendant plus de 3 heures. Toutefois, le message d’erreur ne fournit pas d’options pour la suite des événements.

#### <a name="cause"></a>Cause :

Un runbook peut être interrompu pour plusieurs raisons. Les suspensions sont généralement dues à des erreurs. Par exemple, une exception non interceptée dans un runbook, une panne réseau ou une panne sur le Runbook Worker qui exécute le runbook entraîne la suspension de ce dernier et son démarrage à partir de son dernier point de contrôle au moment de la reprise.

#### <a name="resolution"></a>Résolution :

La solution documentée pour éviter ce problème consiste à utiliser des points de contrôle dans un flux de travail. Pour plus d’informations, consultez [Découverte des flux de travail PowerShell](../automation-powershell-workflow.md#checkpoints). Vous trouverez une explication plus détaillée de la distribution de charge équilibrée dans le billet de blog [Using Checkpoints in Runbooks](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/) (Utilisation de points de contrôle dans des runbooks).

### <a name="long-running-runbook"></a>Scénario : échec de l’exécution d’un long runbook

#### <a name="issue"></a>Problème

Il s’agit du comportement par défaut dans les bacs à sable Azure dû à la surveillance de la répartition de charge équilibrée des processus au sein d’Azure Automation, qui suspend automatiquement un runbook s’il s’exécute pendant plus de 3 heures.

#### <a name="cause"></a>Cause :

Le runbook s’est exécuté au-delà de la limite de 3 heures autorisée par la répartition de charge équilibrée dans un bac à sable Azure

#### <a name="resolution"></a>Résolution :

La solution recommandée consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md). Les Workers hybrides ne sont pas restreints par la limite d’exécution de runbook de 3 heures autorisée par la [répartition de charge équilibrée](../automation-runbook-execution.md#fair-share).

## <a name="common-errors-when-importing-modules"></a>Erreurs courantes survenant lors de l’importation de modules

### <a name="module-fails-to-import"></a>Scénario : Le module ne parvient pas à terminer l’importation ou il est impossible d’exécuter des applets de commande après l’importation

#### <a name="issue"></a>Problème

Un module ne parvient pas à importer ou réussit l’importation, mais aucune applet de commande n’est extraite.

#### <a name="cause"></a>Cause :

Voici quelques raisons courantes pour lesquelles l’importation d’un module dans Azure Automation peut échouer :

* La structure ne correspond pas à la structure dont Automation a besoin.
* Le module dépend d’un autre module qui n’a pas été déployé sur votre compte Automation.
* Le module n’a pas de dépendances dans le dossier.
* L’applet de commande `New-AzureRmAutomationModule` est utilisée pour charger le module, et vous n’avez pas spécifié le chemin de stockage complet ou vous n’avez pas chargé le module en utilisant une URL accessible publiquement.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Assurez-vous que le module suit le format suivant : NomModule.zip **->** NomModule ou Numéro de version **->** (ModuleName.psm1, ModuleName.psd1)
* Ouvrez le fichier .psd1 et regardez si le module possède des dépendances. Si c’est le cas, téléchargez ces modules dans le compte Automation.
* Assurez-vous que les fichiers .dll référencés sont présents dans le dossier de module.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.