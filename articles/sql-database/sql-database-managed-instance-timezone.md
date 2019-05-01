---
title: Fuseaux horaires Azure SQL Database Managed Instance | Microsoft Docs »
description: En savoir plus sur les spécificités de fuseau horaire de Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/25/2019
ms.openlocfilehash: 6d7d065f45bca38cedd2c276bdd9b98dfd9675df
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866947"
---
# <a name="time-zones-in-azure-sql-database-managed-instance-preview"></a>Fuseaux horaires dans Azure SQL Database Managed Instance (version préliminaire)

Temps universel coordonné (UTC) est le fuseau horaire recommandé pour la couche de données de solutions de cloud. Azure SQL Database Managed Instance offre également un choix de fuseaux horaires pour répondre aux besoins des applications existantes qui stockent les valeurs de date et d’heure et appellent des fonctions de date et d’heure avec un contexte implicite d’un fuseau horaire spécifique.

Les fonctions T-SQL comme [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou code CLR observer le fuseau horaire défini sur l’instance à niveau. Travaux SQL Server Agent Suivez également les planifications selon le fuseau horaire de l’instance.

  >[!NOTE]
  > Managed Instance est l’option de déploiement uniquement de base de données SQL Azure qui prend en charge le paramètre de fuseau horaire. Autres options de déploiement suivent toujours UTC.
Utilisez [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) dans uniques et bureaux bases de données SQL si vous avez besoin d’interpréter les informations de date et d’heure dans un fuseau horaire de non-UTC.

## <a name="supported-time-zones"></a>Prise en charge des fuseaux horaires

Un ensemble de fuseaux horaires pris en charge est hérité à partir du système d’exploitation sous-jacent de l’instance gérée. Il est régulièrement mis à jour pour obtenir de nouvelles définitions de fuseau horaire et refléter les modifications apportées à des colonnes existantes. 

Une liste avec les noms des fuseaux horaires pris en charge est exposée via la [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) vue système.

## <a name="set-a-time-zone"></a>Définir un fuseau horaire

Un fuseau horaire d’une instance managée peut être défini lors de la création d’instance uniquement. Le fuseau horaire par défaut est UTC.

  >[!NOTE]
  > Le fuseau horaire d’une instance managée existante ne peut pas être modifié.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Définir le fuseau horaire via le portail Azure

Lorsque vous entrez des paramètres pour une nouvelle instance, sélectionnez un fuseau horaire dans la liste des fuseaux horaires pris en charge. 
  
![Définition d’un fuseau horaire lors de la création d’instance](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

Spécifiez la propriété timezoneId dans votre [modèle Resource Manager](https://aka.ms/sql-mi-create-arm-posh) pour définir le fuseau horaire lors de la création d’instance.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Une liste de valeurs prises en charge pour la propriété timezoneId est à la fin de cet article.

Si non spécifié, le fuseau horaire est défini sur UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Vérifier le fuseau horaire d’une instance

Le [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) fonction retourne un nom d’affichage du fuseau horaire de l’instance.

## <a name="cross-feature-considerations"></a>Considérations relatives à l’entre fonctionnalités

### <a name="restore-and-import"></a>Importation et restauration

Vous pouvez restaurer un fichier de sauvegarde ou importer des données dans une instance gérée à partir d’une instance ou un serveur avec les paramètres de fuseau horaire différent. Veillez à le faire avec précaution. Analyser le comportement des applications et les résultats des requêtes et des rapports, comme lorsque vous transférez des données entre deux instances de SQL Server avec les paramètres de fuseau horaire différent.

### <a name="point-in-time-restore"></a>Limite de restauration dans le temps

Lorsque vous effectuez une restauration de point-à-temps, le temps à restaurer est interprété en heure UTC. Ce paramètre permet d’éviter toute ambiguïté en raison de l’heure d’été et de ses modifications potentielles.

### <a name="auto-failover-groups"></a>Groupes de basculement automatique

À l’aide de la même fuseau horaire sur une instance principale et secondaire dans un groupe de basculement n’est pas appliqué, mais fortement recommandé.

  >[!WARNING]
  > Nous vous recommandons fortement d’utiliser le même fuseau horaire pour l’instance primaire et secondaire dans un groupe de basculement. En raison de certains cas rares, en conservant le même fuseau horaire pour les instances principales et secondaire n’est pas appliqué. Il est important de comprendre que, en cas de basculement manuel ou automatique, l’instance secondaire conserve son fuseau horaire d’origine.

## <a name="limitations"></a>Limites

- Le fuseau horaire de l’instance gérée existante ne peut pas être modifié.
- Les processus externes lancées depuis les travaux SQL Server Agent n’observer le fuseau horaire de l’instance.

## <a name="list-of-supported-time-zones"></a>Liste des fuseaux horaires pris en charge

| **ID de fuseau horaire** | **Nom d’affichage de fuseau horaire** |
| --- | --- |
| Changement de date | (UTC-12:00) Ligne de changement de date ouest |
| UTC-11 | (UTC-11:00) Temps universel coordonné-11 |
| Heure standard Îles Aléoutiennes | (UTC-10:00) Îles aléoutiennes |
| Hawaii | (UTC-10:00) Hawaii |
| Heure standard Marquises | (UTC-09:30) Îles Marquises |
| Alaska | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Temps universel coordonné-09 |
| Pacifique (Mexique) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Temps universel coordonné-08 |
| Pacifique | (UTC-08:00) Heure du Pacifique (États-Unis et Canada) |
| Heure d'hiver États-Unis (Montagnes Rocheuses) | (UTC-07:00) Arizona |
| Heure d'hiver Montagnes Rocheuses (Mexique) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Rocheuses | (UTC-07:00) Heure des montagnes Rocheuses (États-Unis et Canada) |
| Amérique centrale | (UTC-06:00) Amérique centrale |
| Centre | (UTC-06:00) Heure du centre (États-Unis et Canada) |
| Heure standard Île de Pâques | (UTC-06:00) Île de Pâques |
| Heure d’hiver Centre (Mexique) | (UTC-06:00) Guadalajara, Mexico City, Monterrey |
| Heure standard de la région Canada Centre | (UTC-06:00) Saskatchewan |
| Heure d'hiver Amérique du Sud Pacifique | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Heure d’hiver de l'Est (Mexique) | (UTC-05:00) Chetumal |
| Est | (UTC-05:00) Heure de l’Est (États-Unis et Canada) |
| Heure standard Haïti | (UTC-05:00) Haïti |
| Heure standard Cuba | (UTC-05:00) La Havane |
| Heure d'hiver États-Unis (Est) | (UTC-05:00) Indiana (Est) |
| Îles Turks et Caicos heure d’hiver | (UTC-05:00) Îles Turques-et-Caïques |
| Paraguay | (UTC-04:00) Asuncion |
| Atlantique | (UTC-04:00) Heure de l’Atlantique (Canada) |
| Venezuela | (UTC-04:00) Caracas |
| Brésil Centre | (UTC-04:00) Cuiaba |
| Heure d'hiver Amérique du Sud (Ouest) | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Heure d'hiver Amérique du Sud Pacifique | (UTC-04:00) Santiago |
| Heure d’hiver Terre-Neuve | (UTC-03:30) Terre-Neuve |
| Heure standard Tocantins | (UTC-03:00) Araguaina |
| E. Heure d’hiver Amérique du Sud | (UTC-03:00) Brasilia |
| Heure d'hiver Amérique du Sud (Est) | (UTC-03:00) Cayenne, Fortaleza |
| Argentine | (UTC-03:00) Buenos Aires |
| Heure standard du Groenland | (UTC-03:00) Groenland |
| Montevideo | (UTC-03:00) Montevideo |
| Heure standard de Magallanes  | (UTC-03:00) Punta Arenas |
| Heure standard Saint-Pierre | (UTC-03:00) Saint-Pierre-et-Miquelon |
| Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Temps universel coordonné-02 |
| Centre-Atlantique | (UTC-02:00) Centre-Atlantique - Ancien |
| Heure standard des Açores | (UTC-01:00) Açores |
| Cap-Vert | (UTC-01:00) Îles du Cap Vert |
| UTC | (UTC) Temps universel coordonné |
| GMT | (UTC+00:00) Dublin, Édimbourg, Lisbonne, Londres |
| Greenwich | (UTC+00:00) Monrovia, Reykjavik |
| W. Heure d’hiver Europe | (UTC+01:00) Amsterdam, Berlin, Berne, Rome, Stockholm, Vienne |
| Europe centrale | (UTC+01:00) Belgrade, Bratislava, Budapest, Ljubljana, Prague |
| Paris, Madrid | (UTC+01:00) Bruxelles, Copenhague, Madrid, Paris |
| Maroc | (UTC+01:00) Casablanca |
| Heure standard de São Tomé | (UTC+01:00) São Tomé |
| Centre de l'Europe | (UTC+01:00) Sarajevo, Skopje, Varsovie, Zagreb |
| W. Afrique centrale | (UTC+01:00) Afrique Centrale de l’Ouest |
| Jordanie | (UTC+02:00) Amman |
| GTB | (UTC+02:00) Athènes, Bucharest |
| Heure standard du Moyen-Orient | (UTC+02:00) Beyrouth |
| Égypte | (UTC+02:00) Le Caire |
| E. Heure d’hiver Europe | (UTC+02:00) Chisinau |
| Syrie | (UTC+02:00) Damas |
| Heure d’hiver Cisjordanie | (UTC+02:00) Gaza, Hébron |
| Afrique du Sud | (UTC+02:00) Harare, Pretoria |
| FLE | (UTC+02:00) Helsinki, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israël | (UTC+02:00) Jérusalem |
| Heure d’hiver Kaliningrad | (UTC+02:00) Kaliningrad |
| Heure standard du Soudan | (UTC+02:00) Khartoum |
| Libye | (UTC+02:00) Tripoli |
| Namibie | (UTC+02:00) Windhoek |
| Arabie | (UTC+03:00) Bagdad |
| Turquie | (UTC+03:00) Istanbul |
| Heure standard des pays arabes | (UTC+03:00) Koweït, Riyad |
| Heure d’hiver Belarus | (UTC+03:00) Minsk |
| Heure d’hiver Russie | (UTC+03:00) Moscou, St. Petersbourg |
| E. Heure d’hiver Afrique | (UTC+03:00) Nairobi |
| Iran | (UTC+03:30) Téhéran |
| Arabie | (UTC+04:00) Abu Dhabi, Mascate |
| Heure standard Astrakhan | (UTC+04:00) Astrakhan, Oulianovsk |
| Azerbaïdjan | (UTC+04:00) Bakou |
| Fuseau horaire de Russie 3 | (UTC+04:00) Izhevsk, Samara |
| Île Maurice | (UTC+04:00) Port Louis |
| Heure standard de Saratov | (UTC+04:00) Saratov |
| Géorgie | (UTC+04:00) Tbilissi |
| Heure Standard de Volgograd | (UTC+04:00) Volgograd |
| Caucase | (UTC+04:00) Erevan |
| Heure d’hiver Afghanistan | (UTC+04:30) Kaboul |
| Asie de l'Ouest | (UTC+05:00) Achgabat, Tachkent |
| Iekaterinbourg | (UTC+05:00) Iekaterinbourg |
| Pakistan | (UTC+05:00) Islamabad, Karachi |
| Inde | (UTC+05:30) Chennai, Kolkata, Mumbai, New Delhi |
| Sri Lanka | (UTC+05:30) Sri Jayawardenepura |
| Népal | (UTC+05:45) Katmandou |
| Asie centrale | (UTC+06:00) Astana |
| Bangladesh | (UTC+06:00) Dacca |
| Heure standard Omsk | (UTC+06:00) Omsk |
| Myanmar | (UTC+06:30) Yangon (Rangoon) |
| Heure d’hiver Asie (Sud-Est) | (UTC+07:00) Bangkok, Hanoï, Jakarta |
| Heure standard Altaï | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Heure d’hiver Mongolie | (UTC+07:00) Hovd |
| Asie du Nord | (UTC+07:00) Krasnoïarsk |
| N. Asie centrale | (UTC+07:00) Novossibirsk |
| Heure standard Tomsk | (UTC+07:00) Tomsk |
| Chine | (UTC+08:00) Beijing, Chongqing, Hong Kong, Urumqi |
| Asie du Nord est des États-Unis | (UTC+08:00) Irkoutsk |
| Heure d’hiver Singapour | (UTC+08:00) Kuala Lumpur, Singapour |
| W. Heure d’hiver Australie | (UTC+08:00) Perth |
| Heure standard de Taipei | (UTC+08:00) Taipei |
| Oulan-Bator | (UTC+08:00) Oulan-Bator |
| Heure standard Australie Centre-Ouest | (UTC+08:45) Eucla |
| Heure standard Transbaïkalie | (UTC+09:00) Chita |
| Tokyo | (UTC+09:00) Osaka, Sapporo, Tokyo |
| Heure d’hiver Corée du Nord | (UTC+09:00) Pyongyang |
| Corée | (UTC+09:00) Séoul |
| Yakoutsk | (UTC+09:00) Iakoutsk |
| CEN. Heure d’hiver Australie | (UTC+09:30) Adelaide |
| Heure d'hiver Australie centrale | (UTC+09:30) Darwin |
| E. Heure d’hiver Australie | (UTC+10:00) Brisbane |
| Heure d’hiver Australie (Est) | (UTC+10:00) Canberra, Melbourne, Sydney |
| Pacifique Ouest | (UTC+10:00) Guam, Port Moresby |
| Tasmanie | (UTC+10:00) Hobart |
| Vladivostok Standard Time | (UTC+10:00) Vladivostok |
| Heure standard Lord Howe | (UTC+10:30) Île Lord Howe |
| Heure standard Bougainville | (UTC+11:00) Île Bougainville |
| Fuseau horaire de Russie 10 | (UTC+11:00) Chokurdakh |
| Magadan | (UTC+11:00) Magadan |
| Heure standard Norfolk | (UTC+11:00) Île Norfolk |
| Heure standard Sakhaline | (UTC+11:00) Sakhaline |
| Pacifique Centre | (UTC+11:00) Îles Salomon, Nouvelle-Calédonie |
| Fuseau horaire de Russie 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nouvelle-Zélande | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Temps universel coordonné+12 |
| Heure d’hiver Fidji | (UTC+12:00) Fidji |
| Kamchatka | (UTC+12:00) Petropavlovsk-Kamchatsky - Ancien |
| Heure standard Îles Chatham | (UTC+12:45) Îles Chatham |
| UTC+13 | (UTC+13:00) Temps universel coordonné +13 |
| Tonga | (UTC+13:00) Nuku’alofa |
| Heure standard de Samoa | (UTC+13:00) Samoa |
| Heure d'hiver Îles de la Ligne | (UTC+14:00) Île de Kiritimati |

## <a name="see-also"></a>Voir aussi 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
