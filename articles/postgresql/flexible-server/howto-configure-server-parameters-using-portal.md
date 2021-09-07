---
title: Configurer les paramètres de serveur - Portail Azure - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article décrit comment configurer les paramètres Postgres dans Azure Database pour PostgreSQL - Serveur flexible via le portail Azure.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 1ca1501f790f914412fa61fce5ceed5ba45130b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562681"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Configurer les paramètres de serveur dans Azure Database pour PostgreSQL - Serveur flexible par le biais du portail Azure 
Vous pouvez lister, afficher et mettre à jour les paramètres de configuration d’un serveur Azure Database pour PostgreSQL en utilisant le portail Azure.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Azure Database pour PostgreSQL - Serveur flexible](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Affichage et modification des paramètres
1. Ouvrez le [portail Azure](https://portal.azure.com).

2. Sélectionnez votre serveur Azure Database pour PostgreSQL.

3. Dans la section **Paramètres**, sélectionnez **Paramètres du serveur**. La page affiche une liste de paramètres avec leurs valeurs et leurs descriptions.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Page de vue d’ensemble des paramètres":::

4. Sélectionnez le bouton **déroulant** pour afficher les valeurs possibles des paramètres de type énuméré comme client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Bouton déroulant Énumérer":::

5. Sélectionnez ou placez le curseur sur le bouton **i** (informations) pour afficher la plage des valeurs possibles des paramètres numériques comme cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="Bouton Informations":::

6. Si nécessaire, utilisez la **zone de recherche** pour limiter la recherche à un paramètre spécifique. La recherche s’effectue sur le nom et la description des paramètres.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Résultats de la recherche":::

7. Modifiez les valeurs de paramètres souhaitées. Toutes les modifications que vous apportez dans une session sont surlignées en violet. Une fois que vous avez modifié les valeurs, vous pouvez sélectionner **Enregistrer**. Vous pouvez également **Abandonner** vos modifications.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Enregistrer ou annuler les modifications":::

8. Si vous avez enregistré de nouvelles valeurs pour les paramètres, vous pouvez toujours rétablir toutes les valeurs par défaut en sélectionnant **Rétablir toutes les valeurs par défaut**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Rétablir toutes les valeurs par défaut":::

## <a name="working-with-time-zone-parameters"></a>Utilisation du paramètre de fuseau horaire
Si vous prévoyez de travailler avec des données de date et d’heure dans PostgreSQL, vous devez veiller à définir le bon fuseau horaire pour votre localisation. Toutes les dates et heures prenant en charge les fuseaux horaires sont stockées en interne dans Postgres au format UTC. Elles sont converties en heure locale dans la zone spécifiée par le paramètre de serveur **TimeZone** avant d’être affichées sur le client.  Ce paramètre peut être modifié dans la page **Paramètres du serveur** comme expliqué ci-dessus. PostgreSQL vous permet de spécifier des fuseaux horaires sous trois formes différentes :
1. Un nom de fuseau horaire complet, par exemple America/New_York. Les noms de fuseau horaire reconnus sont listés dans la vue [**pg_timezone_names**](https://www.postgresql.org/docs/9.2/view-pg-timezone-names.html).  
   Exemple pour interroger cette vue en psql et obtenir la liste des noms de fuseau horaire :
   <pre>select name FROM pg_timezone_names LIMIT 20;</pre>

   Vous devez voir un ensemble de résultats comme celui-ci :

   <pre>
            name
        -----------------------
        GMT0
        Iceland
        Factory
        NZ-CHAT
        America/Panama
        America/Fort_Nelson
        America/Pangnirtung
        America/Belem
        America/Coral_Harbour
        America/Guayaquil
        America/Marigot
        America/Barbados
        America/Porto_Velho
        America/Bogota
        America/Menominee
        America/Martinique
        America/Asuncion
        America/Toronto
        America/Tortola
        America/Managua
        (20 rows)
    </pre>
   
2. Une abréviation de fuseau horaire, par exemple PST. Une telle spécification définit simplement un décalage particulier par rapport à l’heure UTC, contrairement aux noms de fuseaux horaires complets qui peuvent également impliquer un ensemble de règles de passage à l’heure d’été. Les abréviations reconnues sont listées dans la [**vue pg_timezone_abbrevs**](https://www.postgresql.org/docs/9.4/view-pg-timezone-abbrevs.html). Pour interroger cette vue en psql et obtenir la liste des abréviations de fuseau horaire :

   <pre> select abbrev from pg_timezone_abbrevs limit 20;</pre>

    Vous devez voir un ensemble de résultats comme celui-ci :

     <pre>
        abbrev|
        ------+
        ACDT  |
        ACSST |
        ACST  |
        ACT   |
        ACWST |
        ADT   |
        AEDT  |
        AESST |
        AEST  |
        AFT   |
        AKDT  |
        AKST  |
        ALMST |
        ALMT  |
        AMST  |
        AMT   |
        ANAST |
        ANAT  |
        ARST  |
        ART   |
    </pre>

3. En plus des abréviations et des noms de fuseau horaire, PostgreSQL accepte les spécifications de fuseau horaire de type POSIX de la forme STDdécalage ou STDdécalageDST, où « STD » est une abréviation de zone, « offset » est un décalage numérique exprimé en heures à l’ouest de l’heure UTC, et « DST » est une abréviation de zone d’heure d’été facultative, supposée représenter une heure en avance pour le décalage donné. 
   

## <a name="next-steps"></a>Étapes suivantes
Vous en saurez plus sur :
- [Vue d’ensemble des paramètres de serveur dans Azure Database pour PostgreSQL](concepts-server-parameters.md)
- [Configurer les paramètres d’Azure Database pour PostgreSQL - Serveur flexible via l’interface CLI](howto-configure-server-parameters-using-cli.md)
  