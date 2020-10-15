---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400958"
---
|Nom du paramètre| Type | Description| Valeurs possibles|
|-|-|-|-|
| /ServerMode|Obligatoire|Spécifie si les serveurs de configuration et de processus doivent être installés ou si seul le serveur de processus doit être installé|CS<br>PS|
|/InstallLocation|Obligatoire|Dossier d’installation des composants| N’importe quel dossier sur l’ordinateur|
|/MySQLCredsFilePath|Obligatoire|Chemin d’accès du fichier où sont stockées les informations d’identification du serveur MySQL|Le fichier doit être au format spécifié ci-dessous|
|/VaultCredsFilePath|Obligatoire|Chemin d’accès du fichier d’informations d’identification du coffre|Chemin d’accès valide du fichier|
|/EnvType|Obligatoire|Type d’environnement à protéger |VMware<br>NonVMware|
|/PSIP|Obligatoire|Adresse IP de la carte réseau à utiliser pour le transfert de données de réplication| Une adresse IP valide|
|/CSIP|Obligatoire|Adresse IP de la carte réseau sur laquelle le serveur de configuration écoute| Une adresse IP valide|
|/PassphraseFilePath|Obligatoire|Chemin d’accès complet vers l’emplacement du fichier de phrase secrète|Chemin d’accès valide du fichier|
|/BypassProxy|Facultatif|Indique que le serveur de configuration se connecte à Azure sans proxy||
|/ProxySettingsFilePath|Facultatif|Paramètres de proxy (Le proxy par défaut nécessite une authentification, sinon il faut un proxy personnalisé)|Le fichier doit être au format spécifié ci-dessous|
|DataTransferSecurePort|Facultatif|Numéro de port sur le PSIP à utiliser pour les données de réplication| Numéro de port valide (valeur par défaut : 9433)|
|/SkipSpaceCheck|Facultatif|Ignorer la vérification des espaces pour le disque du cache| |
|/AcceptThirdpartyEULA|Obligatoire|Cet indicateur implique l’acceptation du CLUF tiers| |
|/ShowThirdpartyEULA|Facultatif|Affiche le CLUF tiers. S’il est fourni en entrée, tous les autres paramètres sont ignorés| |
