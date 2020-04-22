---
title: Création d’un compte Azure Automation autonome
description: Cet article vous guide tout au long des procédures de création, de test et d’utilisation d’un exemple d’authentification de principal de sécurité dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 72a40363edf0e83eea26ee697ce992226da0db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392287"
---
# <a name="create-a-standalone-azure-automation-account"></a>Création d’un compte Azure Automation autonome

Cet article vous montre comment créer un compte Azure Automation dans le portail Azure. Vous pouvez utiliser le compte Automation du portail pour évaluer Automation et vous familiariser avec la solution sans recourir à des solutions de gestion supplémentaires ou à une intégration aux journaux Azure Monitor. Ajoutez ces solutions de gestion ou intégrez-les aux journaux Azure Monitor pour prendre en charge une supervision avancée des tâches de runbook ultérieurement.

Avec un compte Automation, vous pouvez authentifier des runbooks en gérant des ressources dans Azure Resource Manager ou dans le modèle de déploiement classique. Un compte Automation peut gérer les ressources sur toutes les régions et abonnements pour un locataire donné.

Lorsque vous créez un compte Automation sur le portail Azure, le **compte d'identification** est automatiquement créé. Ce compte effectue les tâches suivantes :

* Création d’un principal du service dans Azure Active Directory (Azure AD).
* Création d’un certificat.
* Affectation du rôle RBAC (contrôle d’accès en fonction du rôle) de contributeur qui gère les ressources Azure Resource Manager à l’aide de runbooks.

Grâce à ce compte créé pour vous, vous pouvez rapidement commencer à créer et à déployer des runbooks pour répondre à vos besoins d'automatisation.

## <a name="permissions-required-to-create-an-automation-account"></a>Autorisations requises pour créer un compte Automation

Pour créer ou mettre à jour un compte Automation et effectuer les tâches décrites dans cet article, vous devez disposer des privilèges et autorisations suivants :

* Pour créer un compte Automation, votre compte d'utilisateur Azure AD doit être ajouté à un rôle disposant d'autorisations équivalentes à celles du rôle Propriétaire des ressources `Microsoft.Automation`. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
* Dans le portail Azure, sous **Azure Active Directory** > **GÉRER** > **Paramètres utilisateur**, si **Inscriptions des applications** a la valeur **Oui**, les utilisateurs non-administrateurs dans votre locataire Azure AD peuvent [inscrire les applications Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Si **Inscriptions des applications** est défini sur **Non**, l’utilisateur qui effectue cette action doit être administrateur général dans Azure AD.

Si vous n'êtes pas membre de l'instance Active Directory de l'abonnement avant de recevoir le rôle d'Administrateur général/Coadministrateur de l'abonnement, vous êtes ajouté à Active Directory en tant qu'invité. Dans ce scénario, le volet Ajouter un compte Automation affiche ce message : `You do not have permissions to create.`

Si un utilisateur reçoit d'abord le rôle d'Administrateur général/Coadministrateur, vous pouvez le supprimer de l'instance Active Directory de l'abonnement. Vous pouvez réattribuer le rôle d'Utilisateur à l'utilisateur dans Active Directory.

Pour vérifier les rôles d’utilisateur :

1. Sur le portail Azure, accédez au volet Azure Active Directory.
1. Sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Tous les utilisateurs**.
1. Après avoir sélectionné un utilisateur spécifique, sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Créer un compte Automation dans le portail Azure

Pour créer un compte Azure Automation dans le portail Azure, suivez les étapes suivantes :

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
1. Sélectionnez **+ Créer une ressource**.
1. Recherchez **Automation**. Dans les résultats de recherche, sélectionnez **Automation**.

   ![Rechercher et sélectionner Automation & Control dans la Place de marché Azure](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Sur l'écran suivant, sélectionnez **Créer nouveau**.

   ![Ajouter un compte Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Si le message suivant s'affiche dans le volet Ajouter un compte Automation, votre compte ne dispose pas du rôle d'Administrateur ou de Coadministrateur de l'abonnement.
   >
   > ![Avertissement Ajouter un compte Automation](media/automation-create-standalone-account/create-account-without-perms.png)

1. Dans le volet Ajouter un compte Automation, entrez un nom pour votre nouveau compte Automation dans le champ **Nom**. Une fois choisi, ce nom n'est pas modifiable. 

    > [!NOTE]
    > Les noms de compte Automation sont spécifiques à une région et à un groupe de ressources. Les noms des comptes Automation supprimés peuvent ne pas être immédiatement disponibles.

1. Si vous disposez de plusieurs abonnements, utilisez le champ **Abonnement** afin de spécifier l'abonnement à utiliser pour le nouveau compte.
1. Pour **Groupe de ressources**, saisissez ou sélectionnez un groupe de ressources nouveau ou existant.
1. Pour **Emplacement**, sélectionnez un emplacement de centre de données Azure.
1. Pour l'option **Créer un compte d'identification Azure**, assurez-vous que l'option **Oui** est sélectionnée, puis cliquez sur **Créer**.

   > [!NOTE]
   > Si vous choisissez de ne pas créer le compte d'identification en sélectionnant **Non** pour l'option **Créer un compte d'identification Azure**, un message s'affiche dans le volet Ajouter un compte Automation. Bien que le compte soit créé dans le portail Azure, il ne possède pas d’identité d’authentification correspondante au sein de votre abonnement du modèle de déploiement classique ou dans le service d’annuaire de l’abonnement Azure Resource Manager. Par conséquent, le compte Automation n’a pas accès aux ressources de votre abonnement. Cela empêche tous les Runbooks faisant référence à ce compte de pouvoir s’authentifier et d’effectuer des tâches sur les ressources de ces modèles de déploiement.
   >
   > ![Avertissement Ajouter un compte Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Le rôle Contributeur n’est pas attribué en cas de non-création du principal du service.
   >

1. Pour suivre l'avancement de la création du compte Automation, sélectionnez **Notifications** dans le menu.

### <a name="resources-included"></a>Ressources incluses

Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées. Suite à cette création, ces runbooks peuvent être supprimés en toute sécurité si vous ne souhaitez pas les conserver. Les comptes d’identification peuvent être utilisés pour authentifier votre compte dans un runbook et doivent être conservés sauf si vous en créez un autre ou si vous n’en avez pas besoin. Le tableau ci-dessous récapitule les ressources du compte d’identification.

| Ressource | Description |
| --- | --- |
| Runbook AzureAutomationTutorial |Exemple de runbook graphique qui illustre l’authentification à l’aide du compte d’identification. Le runbook récupère toutes les ressources Resource Manager. |
| Runbook AzureAutomationTutorialScript |Exemple de runbook PowerShell qui illustre comment s’authentifier à l’aide du compte d’identification. Le runbook récupère toutes les ressources Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Exemple de runbook Python qui illustre comment s’authentifier à l’aide du compte d’identification. Le runbook répertorie l’ensemble des groupes de ressources présents dans l’abonnement. |
| AzureRunAsCertificate |Ressource de certificat automatiquement créée lors de la création du compte Automation, ou à l’aide d’un script PowerShell pour un compte existant. Le certificat permet de vous authentifier auprès d’Azure afin de pouvoir gérer les ressources Azure Resource Manager des runbooks. Ce certificat a une durée de vie d’un an. |
| AzureRunAsConnection |Ressource de connexion automatiquement créée lors de la création du compte Automation, ou à l’aide d’un script PowerShell pour un compte existant. |

## <a name="create-a-classic-run-as-account"></a>Créer un compte d'identification Classic

Les comptes d'identification Classic ne sont plus créés par défaut lorsque vous créez un compte Azure Automation. Si vous avez toujours besoin d'un compte d'identification Classic :

1. Depuis votre compte Automation, sélectionnez **Comptes d'identification** sous **Paramètres du compte**.
2. Sélectionnez **Compte d’identification Azure Classic**.
3. Cliquez sur **Créer** pour confirmer la création du compte d’identification Classic.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour obtenir des informations sur la prise en main des Runbooks de workflow PowerShell, voir [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md).
* Pour vous familiariser avec les runbooks Python 2, consultez [Mon premier runbook Python 2](automation-first-runbook-textual-python2.md).
* Pour obtenir des informations de référence sur les cmdlets PowerShell, consultez [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
