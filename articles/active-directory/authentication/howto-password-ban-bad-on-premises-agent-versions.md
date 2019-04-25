---
title: Le mot de passe Azure AD Protection agent historique des versions - Azure Active Directory local
description: Historique de publication des versions de documents et des changements de comportement
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60ce66b079942944176540826c7f3e7a91b070d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358206"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historique des versions de l’agent de protection par mot de passe Azure AD

## <a name="121250"></a>1.2.125.0

Date de publication : 3/22/2019

* Corriger les erreurs de faute de frappe mineures dans les messages du journal des événements
* CLUF de la mise à jour vers la dernière version en disponibilité générale

> [!NOTE]
> Build 1.2.125.0 est la génération de la disponibilité générale. Nous vous remercions à nouveau pour tout le monde a fourni des commentaires sur le produit !

## <a name="121160"></a>1.2.116.0

Date de publication : 3/13/2019

* Les cmdlets Get-AzureADPasswordProtectionProxy et Get-AzureADPasswordProtectionDCAgent maintenant la version du logiciel et Azure actif du client avec les limitations suivantes :
  * Version du logiciel et les données de locataire Azure sont uniquement disponibles pour les agents de contrôleur de domaine et des serveurs proxy en cours d’exécution version 1.2.116.0 ou version ultérieure.
  * Données de locataire Azure peuvent ne pas être rapportées jusqu'à une réinscription (ou le renouvellement) du proxy ou forêt s’est produite.
* Le service de Proxy requiert désormais que .NET 4.7 est installé.
  * .NET 4.7 doit déjà être installé sur un serveur Windows entièrement mis à jour. Si ce n’est pas le cas, téléchargez et exécutez le programme d’installation, consultez [le .NET Framework 4.7 un programme d’installation hors connexion pour Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Sur les systèmes de Server Core, il peut être nécessaire de passer l’indicateur /q le programme d’installation de .NET 4.7 pour qu’il réussisse.
* Le service de Proxy prend désormais en charge la mise à niveau automatique. Mise à niveau automatique utilise le service Microsoft Azure AD se connecter l’Agent mise à jour qui est installée côte à côte avec le service de Proxy. Mise à niveau automatique est activée par défaut.
* Mise à niveau automatique peut être activée ou désactivée à l’aide de l’applet de commande Set-AzureADPasswordProtectionProxyConfiguration. Le paramètre actuel peut être interrogé à l’aide de l’applet de commande Get-AzureADPasswordProtectionProxyConfiguration.
* Le service binaire pour le service de l’agent du contrôleur de domaine a été renommé en AzureADPasswordProtectionDCAgent.exe.
* Le service binaire pour le service de Proxy a été renommé en AzureADPasswordProtectionProxy.exe. Règles de pare-feu devrez peut-être être modifiées en conséquence, si un pare-feu tiers est en cours d’utilisation.
  * Remarque : si un fichier de configuration du proxy http a été utilisé dans le Proxy d’un précédent installation, il doit être renommé (à partir de *proxyservice.exe.config* à *AzureADPasswordProtectionProxy.exe.config*) après cela mise à niveau.
* Toutes les vérifications de durée limitée des fonctionnalités ont été supprimées de l’agent du contrôleur de domaine.
* Correctifs de bogues mineurs et améliorations de la journalisation.

## <a name="12650"></a>1.2.65.0

Date de publication : 1/2/2019

Modifications :

* L’agent du contrôleur de domaine et le service proxy sont désormais pris en charge sur noyau du serveur. La configuration requise du système d’exploitation est identique à ce qu’elle était auparavant : Windows Server 2012 pour les agents du contrôleur de domaine et Windows Server 2012 R2 pour les proxys.
* Les cmdlets Register-AzureADPasswordProtectionProxy et Register-AzureADPasswordProtectionForest prennent désormais en charge les modes d’authentification Azure basés sur le code d’appareil.
* La cmdlet Get-AzureADPasswordProtectionDCAgent ignore les points de connexion de service altérés ou non valides. Cela résout le bogue qui avait pour effet que les contrôleurs de domaine apparaissaient parfois plusieurs fois dans la sortie.
* La cmdlet Get-AzureADPasswordProtectionSummaryReport ignore les points de connexion de service altérés ou non valides. Cela résout le bogue qui avait pour effet que les contrôleurs de domaine apparaissaient parfois plusieurs fois dans la sortie.
* Le module Powershell du proxy est désormais inscrit à partir de %ProgramFiles%\WindowsPowerShell\Modules. La variable d’environnement PSModulePath de la machine n’est plus modifiée.
* Une nouvelle cmdlet Get-AzureADPasswordProtectionProxy a été ajoutée pour faciliter la découverte de proxys inscrits dans une forêt ou un domaine.
* L’agent du contrôleur de domaine utilise un nouveau dossier dans le partage sysvol pour la réplication des stratégies de mot de passe et d’autres fichiers.

   Ancien emplacement du dossier :

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nouvel emplacement du dossier :

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Cette modification a été apportée pour éviter les avertissements « GPO orphelin » faux positifs.)

   > [!NOTE]
   > Aucun partage de données ou migration n’est effectué entre l’ancien dossier et le nouveau. Les versions antérieures de l’agent du contrôleur de domaine continueront à utiliser l’ancien emplacement jusqu’à leur mise à niveau vers cette version ou une version ultérieure. Une fois que tous les agents du contrôleur de domaine exécutent la version 1.2.65.0 ou version ultérieure, l’ancien dossier sysvol peut être supprimé manuellement.

* Désormais, le service proxy et l’agent du contrôleur de domaine détectent et suppriment les copies altérées de leurs points de connexion de service respectifs.
* Chaque agent du contrôleur de domaine supprime régulièrement les points de connexion de service altérés et périmés dans son domaine, pour les points de connexion tant de l’agent du contrôleur de domaine que du service proxy. Les points de connexion tant de l’agent du contrôleur de domaine que du service proxy sont considérés comme périmés si leur indicateur de pulsations date de plus de sept jours.
* L’agent du contrôleur de domaine renouvelle désormais le certificat de la forêt en fonction des besoins.
* Le service proxy renouvelle désormais le certificat du proxy en fonction des besoins.
* Mises à jour de l’algorithme de validation de mot de passe : la liste globale de mots de passe interdits et la liste spécifique du client de mots de passe interdits (si configurée) sont combinées avant les validations de mot de passe. Un mot de passe donné peut désormais être rejeté (échec ou audit uniquement) s’il contient des jetons provenant tant de la liste globale que de la liste spécifique du client. La documentation sur le journal des événements a été mise à jour pour refléter cela. À cet égard, veuillez consulter [Surveiller la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Correctifs de performances et de robustesse
* Amélioration de la journalisation

> [!WARNING]
> Fonctionnalité limitée dans le temps : le service de l’agent du contrôleur de domaine dans cette version (1.2.65.0) cessera de traiter les demandes de validation de mot de passe le 1e septembre 2019.  Les services de l’agent du contrôleur de domaine dans les versions antérieures (voir la liste ci-dessous) cesseront d’opérer le 1e juillet 2019. Le service de l’agent du contrôleur de domaine dans toutes les versions consignera des événements 10021 dans le journal des événements d’administration dans les deux mois précédant ces délais. Toutes les restrictions de limite de temps seront supprimées dans la version en disponibilité générale à venir. Le service de l’agent proxy n’est limité dans le temps dans aucune version, mais il doit toujours être mis à niveau vers la dernière version afin de tirer parti de tous les corrections de bogues et d’autres améliorations subséquentes.

## <a name="12250"></a>1.2.25.0

Date de publication : 01/11/2018

Correctifs :

* L'agent DC et le service proxy ne devraient plus échouer pour cause d'échecs des certificats de confiance.
* L'agent DC et le service proxy disposent de correctifs supplémentaires pour les ordinateurs compatibles avec les normes FIPS.
* Le service proxy fonctionnera désormais correctement dans un environnement réseau utilisant uniquement le protocole TLS 1.2.
* Correctifs mineurs de performances et de robustesse
* Amélioration de la journalisation

Modifications :

* Windows Server 2012 R2 est désormais le système d'exploitation minimum requis pour le service proxy. Windows Server 2012 reste le système d'exploitation minimum requis pour le service de l'agent DC.
* Le service proxy requiert désormais .NET version 4.6.2.
* L’algorithme de validation de mot de passe utilise une table de normalisation des caractères étendus. Cela peut entraîner le rejet de mots de passe qui étaient acceptés dans les versions précédentes.

## <a name="12100"></a>1.2.10.0

Date de publication : 17/08/2018

Correctifs :

* Register-AzureADPasswordProtectionProxy et Register-AzureADPasswordProtectionForest prennent désormais en charge l’authentification multifacteur
* Register-AzureADPasswordProtectionProxy nécessite un contrôleur de domaine WS2012 ou ultérieur dans le domaine afin d’éviter les erreurs de chiffrement.
* Le service de l’agent de contrôleur de domaine est plus fiable en ce qui concerne les demandes de nouvelle stratégie de mot de passe à Azure au démarrage.
* Le service de l’agent de contrôleur de domaine demande une nouvelle stratégie de mot de passe à Azure toutes les heures si nécessaire, mais il le fait désormais à compter d’une heure de début sélectionnée de façon aléatoire.
* Le service de l’agent de contrôleur de domaine ne provoque plus un délai illimité lors d’une nouvelle publication de contrôleur de domaine en cas d’installation sur un serveur avant sa promotion en tant que réplica.
* Le service de l’agent de contrôleur de domaine respecte désormais le paramètre de configuration « Activer la protection par mot de passe sur Windows Server Active Directory ».
* Les programmes d’installation du proxy et de l’agent de contrôleur de domaine prennent désormais en charge la mise à niveau sur place en cas de mise à niveau vers des versions ultérieures.

> [!WARNING]
> La mise à niveau sur place à partir de la version 1.1.10.3 n’est pas prise en charge et provoque une erreur d’installation. Pour mettre à niveau vers la version 1.2.10 ou ultérieure, vous devez d’abord désinstaller complètement le logiciel du service de proxy et d’agent de contrôleur de domaine, puis installer la nouvelle version à partir de zéro. La réinscription du service de proxy de protection par mot de passe Azure AD est nécessaire.  La réinscription de la forêt n’est pas nécessaire.

> [!NOTE]
> Les mises à niveau sur place du logiciel d’agent de contrôleur de domaine nécessitent un redémarrage.

* Le service de proxy et l’agent de contrôleur de domaine prennent désormais en charge l’exécution sur un serveur configuré pour utiliser uniquement des algorithmes compatibles FIPS.
* Correctifs mineurs de performances et de robustesse
* Amélioration de la journalisation

## <a name="11103"></a>1.1.10.3

Date de publication : 15/06/2018

Préversion publique initiale

## <a name="next-steps"></a>Étapes suivantes

[Déployer la protection par mot de passe Azure AD](howto-password-ban-bad-on-premises-deploy.md)
