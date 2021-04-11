---
title: Fournir des informations d’identification de serveur pour découvrir un inventaire logiciel, des dépendances, ainsi que les instances et bases de données SQL Server
description: Découvrez comment fournir des informations d’identification de serveur sur le gestionnaire de configuration de l’appliance
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: e72d509f71704bbf8608543df5e819a9b8783935
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562067"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-and-sql-server-instances-and-databases"></a>Fournir des informations d’identification de serveur pour découvrir un inventaire logiciel, des dépendances, ainsi que les instances et bases de données SQL Server

Cet article explique comment ajouter diverses informations d’identification de serveur sur le gestionnaire de configuration de l’appliance pour effectuer un inventaire logiciel (découvrir les applications installées) et une analyse des dépendances sans agent, ainsi que découvrir les instances et bases de données SQL Server.

> [!Note]
> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

L’[appliance Azure Migrate](migrate-appliance.md) est une appliance légère que l’outil de découverte et d’évaluation d’Azure Migrate utilise pour découvrir les serveurs locaux s’exécutant dans un environnement VMware et envoyer des métadonnées de configuration et de performances du serveur à Azure. L’appliance permet également d’effectuer un inventaire logiciel et une analyse des dépendances sans agent, ainsi que de découvrir les instances et bases de données SQL Server.

Si vous souhaitez utiliser ces fonctionnalités, vous pouvez fournir des informations d’identification de serveur en procédant comme suit. L’appliance tente de mapper automatiquement les informations d’identification aux serveurs pour exécuter les fonctionnalités de découverte.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Ajouter des informations d’identification pour les serveurs s’exécutant dans un environnement VMware

### <a name="types-of-server-credentials-supported"></a>Types d’informations d’identification de serveur pris en charge

Vous pouvez ajouter diverses informations d’identification de serveur au gestionnaire de configuration de l’appliance, qui peuvent être des informations d’identification d’authentification de domaine, hors domaine (Windows ou Linux) ou SQL Server.

Les types d’informations d’identification de serveur pris en charge sont répertoriés dans le tableau ci-dessous :

Type d'informations de connexion | Description
--- | ---
**Informations d'identification de domaine** | Vous pouvez ajouter des **informations d’identification de domaine** en sélectionnant cette option dans la liste déroulante de la fenêtre modale **Ajouter les informations d’identification**. <br/><br/> Pour fournir des informations d’identification de domaine, vous devez spécifier le **nom de domaine** au format de nom de domaine complet (par exemple, prod.corp.contoso.com). <br/><br/> Vous devez également spécifier un nom convivial pour les informations d’identification, le nom d’utilisateur et le mot de passe. <br/><br/> Les informations d’identification de domaine ajoutées seront automatiquement validées sur le plan de leur authenticité par rapport à l’Active Directory du domaine. Cela vise à éviter tout verrouillage de compte quand l’appliance tente de mapper les informations d’identification de domaine aux serveurs découverts. <br/><br/> L’appliance ne tente pas de mapper les informations d’identification de domaine dont la validation a échoué. Pour poursuivre l’inventaire logiciel, vous devez disposer d’au moins une information d’identification de domaine correctement validée ou d’au moins une information d’identification hors domaine.<br/><br/>Les informations d’identification de domaine mappées automatiquement aux serveurs Windows seront utilisées pour effectuer l’inventaire logiciel. Elle peuvent également l’être pour découvrir les instances et bases de données pour poursuivre l’inventaire logiciel _(si vous avez configuré le mode d’authentification Windows sur vos serveurs SQL)_ .<br/> [Apprenez-en davantage](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sur les types de modes d’authentification pris en charge sur les serveurs SQL.
**Informations d’identification hors domaine (Windows/Linux)** | Vous pouvez ajouter des informations d'identification **Windows (hors domaine)** ou **Linux (hors domaine)** en sélectionnant l’option requise dans la liste déroulante de la fenêtre modale **Ajouter des informations d’identification**. <br/><br/> Vous devez spécifier un nom convivial pour les informations d’identification, le nom d’utilisateur et le mot de passe.
**Informations d'identification d’authentification SQL Server** | Vous pouvez ajouter des informations d’identification d’**Authentification SQL Server** en sélectionnant cette option dans la liste déroulante de la fenêtre modale **Ajouter les informations d’identification**. <br/><br/> Vous devez spécifier un nom convivial pour les informations d’identification, le nom d’utilisateur et le mot de passe. <br/><br/> Vous pouvez ajouter ce type d’informations d’identification pour découvrir les instances et bases de données SQL Server s’exécutant dans votre environnement VMware si vous avez configuré le mode d’authentification SQL Server sur vos serveurs SQL Server.<br/> [Apprenez-en davantage](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sur les types de modes d’authentification pris en charge sur les serveurs SQL.<br/><br/> Vous devez fournir au moins une information d’identification de domaine correctement validée ou au moins une information d’identification Windows (hors domaine) afin que l’appliance puisse terminer l’inventaire logiciel pour découvrir SQL installé sur les serveurs avant d’utiliser les informations d’identification d’authentification SQL Server pour découvrir les instances et bases de données SQL Server.

Vérifiez les autorisations requises sur les informations d’identification Windows/Linux pour effectuer l’inventaire logiciel et l’analyse des dépendances sans agent, ainsi que découvrir les instances et bases de données SQL Server.

### <a name="required-permissions"></a>Autorisations requises

Le tableau ci-dessous répertorie les autorisations requises sur les informations d’identification de serveur fournies sur l’appliance pour exécuter les fonctionnalités respectives :

Fonctionnalité | Informations d'identification Windows | Informations d’identification Linux
---| ---| ---
**Inventaire logiciel** | Compte d'utilisateur invité | Compte d’utilisateur standard/normal (autorisations d’accès non-sudo)
**Découverte des instances et bases de données SQL Server** | Compte d’utilisateur membre du rôle serveur sysadmin. | _Non pris en charge actuellement_
**Analyse de dépendances sans agent** | Compte de domaine ou hors domaine (local) avec des autorisations d’administration | Compte d’utilisateur racine ou <br/> compte disposant de ces autorisations sur les fichiers /bin/netstat et /bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE.<br/><br/> Définissez ces fonctionnalités à l’aide des commandes suivantes : <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Pratiques recommandées pour fournir des informations d’identification

- Il est recommandé de créer un compte d’utilisateur de domaine dédié avec les [autorisations requises](add-server-credentials.md#required-permissions), dont l’étendue est limitée à l’inventaire logiciel et à l’analyse des dépendances sans agent, ainsi qu’à la découverte des instances et bases de données SQL Server sur les serveurs souhaités.
- Il est recommandé de fournir au moins une information d’identification de domaine validée avec succès ou au moins une information hors domaine pour lancer l’inventaire logiciel.
- Pour découvrir les instances et bases de données SQL Server, vous pouvez fournir des informations d’identification de domaine si vous avez configuré le mode d’authentification Windows sur vos serveurs SQL.
- Vous pouvez également fournir des informations d’identification d’authentification SQL Server si vous avez configuré le mode d’authentification SQL Server sur vos serveurs SQL Server, mais il est recommandé de fournir au moins une information d’identification de domaine correctement validée ou au moins une information d’identification Windows (hors domaine) afin que l’appliance puisse d’abord effectuer l’inventaire logiciel.

## <a name="credentials-handling-on-appliance"></a>Gestion des informations d’identification sur l’appliance

- Toutes les informations d’identification fournies sur le gestionnaire de configuration de l’appliance sont stockées localement sur le serveur de l’appliance et ne sont pas envoyées à Azure.
- Les informations d’identification stockées sur le serveur de l’appliance sont chiffrées à l’aide de l’API de protection des données (DPAPI).
- Une fois que vous avez ajouté les informations d’identification, l’appliance tente de mapper les automatiquement pour effectuer la découverte sur les serveurs respectifs.
- L’appliance utilise les informations d’identification mappées automatiquement sur un serveur pour tous les cycles de découverte suivants, jusqu’à ce que les informations d’identification soient en mesure d’extraire les données de découverte requises. Si les informations d’identification cessent de fonctionner, l’appliance tente à nouveau d’opérer un mappage à partir de la liste des informations d’identification ajoutées, et de poursuivre la découverte en cours sur le serveur.
- Les informations d’identification de domaine ajoutées seront automatiquement validées sur le plan de leur authenticité par rapport à l’Active Directory du domaine. Cela vise à éviter tout verrouillage de compte quand l’appliance tente de mapper les informations d’identification de domaine aux serveurs découverts. L’appliance ne tente pas de mapper les informations d’identification de domaine dont la validation a échoué.
- Si l’appliance ne peut pas mapper d’informations d’identification de domaine ou hors domaine à un serveur, vous verrez l’état « Informations d’identification non disponibles » pour le serveur dans votre projet.

## <a name="next-steps"></a>Étapes suivantes

Passer en revue les tutoriels concernant la [découverte des serveurs s’exécutant dans votre environnement VMware](tutorial-discover-vmware.md)