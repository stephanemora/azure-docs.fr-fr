---
title: Exporter un connecteur Microsoft Identity Manager pour une utilisation avec l’Hôte de connecteur ECMA Azure AD
description: Décrit les procédures de création et d’exportation d’un connecteur à partir de la synchronisation MIM à utiliser avec l’Hôte de connecteur ECMA Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 229da477e358a0efd85ea555762443de9859b253
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111569997"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-azure-ad-ecma-connector-host"></a>Exporter un connecteur Microsoft Identity Manager pour une utilisation avec l’Hôte de connecteur ECMA Azure AD

>[!IMPORTANT]
> Actuellement, la préversion de l’approvisionnement local n’est disponible que sur invitation. Vous pouvez demander l’accès à cette fonctionnalité [ici](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Vous pouvez importer dans l’Hôte de connecteur ECMA Azure AD une configuration pour un connecteur spécifique à partir d’une installation de synchronisation FIM ou MIM.  Notez que l’installation de la synchronisation MIM n’est utilisée que pour la configuration et non pour la synchronisation en cours à partir d’Azure AD.

>[!IMPORTANT]
>Actuellement, seul le connecteur SQL générique (GSQL) est pris en charge pour une utilisation avec l’Hôte de connecteur ECMA Azure AD.


## <a name="creating-and-exporting-a-connector-configuration-in-mim-sync"></a>Création et exportation d’une configuration de connecteur dans la synchronisation MIM
Si vous avez déjà configuré la synchronisation MIM avec votre connecteur ECMA, passez à l’étape 10.

 1. Préparez un serveur Windows Server 2016, qui est différent du serveur qui sera utilisé pour l’exécution de l’Hôte de connecteur ECMA Azure AD.  Le serveur hôte doit avoir une base de données SQL Server 2016 colocalisée ou disposer d’une connexion réseau à une base de données SQL Server 2016.  Une façon de configurer ce serveur consiste à déployer une machine virtuelle Azure avec l’image **SQL Server 2016 SP1 Standard sur Windows Server 2016**.  Notez que ce serveur n’a pas besoin d’une connectivité Internet, si ce n’est un accès au Bureau à distance à des fins d’installation.
 2. Créez un compte à utiliser lors de l’installation de la synchronisation MIM.  Il peut s’agir d’un compte local sur ce serveur Windows Server.  Pour créer un compte local, lancez le panneau de configuration, ouvrez des comptes d’utilisateur, puis ajoutez un compte d’utilisateur **mimsync**.
 3. Ajoutez le compte créé à l’étape précédente au groupe Administrateurs local.
 4. Donnez au compte créé précédemment la possibilité d’exécuter un service.  Lancez la stratégie de sécurité locale, cliquez sur Stratégies locales, Attribution des droits utilisateur et **Ouvrir une session en tant que service**.  Ajoutez le compte mentionné précédemment.
 5. Installez la synchronisation MIM sur cet hôte. Si vous n’avez pas de fichiers binaires de synchronisation MIM, vous pouvez installer une évaluation en téléchargeant le fichier ZIP à partir de [https://www.microsoft.com/en-us/download/details.aspx?id=48244](https://www.microsoft.com/en-us/download/details.aspx?id=48244), en montant l’image ISO et en copiant le dossier **Service de synchronisation** sur l’hôte Windows Server.  Exécutez ensuite le programme d’installation contenu dans ce dossier.   Notez que le logiciel d’évaluation est limité dans le temps et qu’il expire, et qu’il n’est pas destiné à une utilisation en production.
 6. Une fois l’installation de la synchronisation MIM terminée, déconnectez-vous et reconnectez-vous.
 7. Installez votre connecteur sur le même serveur que la synchronisation MIM. (À des fins d’illustration, ce guide de laboratoire de test illustre l’utilisation de l’un des connecteurs fournis par Microsoft pour le téléchargement à partir de [https://www.microsoft.com/en-us/download/details.aspx?id=51495](https://www.microsoft.com/en-us/download/details.aspx?id=51495)).
 8. Lancez l’interface utilisateur du service de synchronisation.  Cliquez sur **Agents de gestion**.  Cliquez sur **Créer**, puis spécifiez l’agent de gestion de connecteur.  Veillez à sélectionner un agent de gestion de connecteur basé sur ECMA.
 9. Donnez un nom au connecteur et configurez les paramètres nécessaires à l’importation et à l’exportation des données dans le connecteur.  Veillez à spécifier que le connecteur peut importer et exporter des attributs de chaîne à valeur unique d’un type d’objet utilisateur ou personne.
 10. Sur l’ordinateur serveur de synchronisation MIM, lancez l’interface utilisateur du service de synchronisation si elle n’est pas encore en cours d’exécution.  Cliquez sur **Agents de gestion**.
 11. Sélectionnez le connecteur, puis cliquez sur **Exporter l’agent de gestion**.  Enregistrez le fichier XML, ainsi que la DLL et le logiciel associé à votre connecteur, sur le serveur Windows Server qui contiendra l’Hôte de connecteur ECMA.

À ce stade, le serveur de synchronisation MIM n’est plus nécessaire.

 1. Connectez-vous à Windows Server avec le compte que l’Hôte de connecteur ECMA Azure AD va utiliser pour s’exécuter.
 2. Accédez au répertoire c:\program files\Microsoft ECMA2host\Service\ECMA et assurez-vous qu’au moins une DLL est déjà présente dans ce répertoire.  (Ces DLL correspondent aux connecteurs fournis par Microsoft).
 3. Copiez la DLL MA de votre connecteur, ainsi que l’une de ses DLL prérequises, dans le même sous-répertoire ECMA du répertoire du service.
 4. Accédez au répertoire C:\program files\Microsoft ECMA2Host\Wizard et exécutez le programme Microsoft.ECMA2Host.ConfigWizard.exe pour définir la configuration de l’Hôte de connecteur ECMA.
 5. Une nouvelle fenêtre s’affiche avec la liste des connecteurs. Par défaut, aucun connecteur n’est présent.  Cliquez sur **Nouveau connecteur**.
 6. Spécifiez le fichier XML de l’agent de gestion qui a été exporté précédemment à partir de MIM.  Poursuivez avec les instructions de configuration et de mise en correspondance du schéma figurant dans la section Configuration d’un connecteur ci-dessus.



## <a name="next-steps"></a>Étapes suivantes


- [Approvisionnement d’applications](user-provisioning.md)
- [Prérequis de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-prerequisites.md)
- [Installation de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-install.md)
- [Configuration de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-configure.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)