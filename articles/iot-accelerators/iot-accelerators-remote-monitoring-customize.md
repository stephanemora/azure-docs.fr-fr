---
title: Personnaliser l’interface utilisateur de la solution de surveillance à distance - Azure | Microsoft Docs
description: Cet article fournit des informations sur la façon dont vous pouvez accéder au code source pour l’interface utilisateur de l’accélérateur de solution de surveillance à distance et effectuer quelques personnalisations.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 94bd864215ad80b299db09b6237f2cebe63feb88
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personnaliser l’accélérateur de solution de surveillance à distance

Cet article fournit des informations sur la façon dont vous pouvez accéder au code source et personnaliser l’interface utilisateur de l’accélérateur de solution de surveillance à distance. Cet article aborde les points suivants :

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Préparer un environnement de développement local pour l’interface utilisateur

Le code de l’interface utilisateur de l’accélérateur de solution de surveillance à distance est implémenté à l’aide du framework React.js. Vous pouvez trouver le code source dans le dépôt GitHub [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui).

Pour apporter des modifications à l’interface utilisateur, vous pouvez en exécuter une copie localement. La copie locale se connecte à une instance déployée de la solution pour effectuer des actions telles que la récupération des données de télémétrie.

Les étapes suivantes décrivent le processus de configuration d’un environnement local pour le développement de l’interface utilisateur :

1. Déployez une instance de **base** de l’accélérateur de solution à l’aide de l’interface CLI **pcs**. Notez le nom de votre déploiement et les informations d’identification que vous avez fournies pour la machine virtuelle. Pour plus d’informations, consultez [Déployer à l’aide de l’interface CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Utilisez le portail Azure ou [l’interface CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour activer l’accès SSH à la machine virtuelle qui héberge les microservices dans votre solution. Par exemple : 

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Utilisez le portail Azure ou [l’interface CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour rechercher le nom et l’adresse IP publique de votre machine virtuelle. Par exemple : 

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilisez SSH pour vous connecter à la machine virtuelle à l’aide de l’adresse IP de l’étape précédente et des informations d’identification que vous avez fournies lors de l’exécution de **pcs** pour déployer la solution.

1. Pour permettre la connexion de l’expérience utilisateur locale, exécutez les commandes suivantes au niveau du shell bash dans la machine virtuelle :

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Quand l’exécution de la commande est terminée et que le site web démarre, vous pouvez vous déconnecter de la machine virtuelle.

1. Dans votre copie locale du dépôt [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui), modifiez le fichier **.env** pour ajouter l’URL de votre solution déployée :

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. À une invite de commandes dans votre copie locale du dossier `azure-iot-pcs-remote-monitoring-webui`, exécutez les commandes suivantes pour installer les bibliothèques requises et exécuter l’interface utilisateur localement :

    ```cmd/sh
    npm install
    npm start
    ```

1. La commande précédente exécute l’interface utilisateur localement à l’adresse http://localhost:3000/dashboard. Vous pouvez modifier le code pendant que le site est en cours d’exécution et voir sa mise à jour dynamique.

## <a name="customize-the-layout"></a>Personnaliser la disposition

Chaque page de la solution de surveillance à distance est composée d’un ensemble de contrôles, appelés *panneaux* dans le code source. Par exemple, la page **Tableau de bord** se compose de cinq panneaux : Général, Carte, Alarmes, Télémétrie et Indicateurs de performance clés. Vous trouverez le code source qui définit chaque page et ses panneaux dans le dépôt GitHub [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui). Par exemple, le code qui définit la page **Tableau de bord**, sa disposition et ses panneaux se trouve dans le dossier [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Étant donné que les panneaux gèrent leurs propres disposition et dimensionnement, vous pouvez facilement modifier la disposition d’une page. Par exemple, les modifications suivantes apportées à l’élément **PageContent** dans le fichier `src/components/pages/dashboard/dashboard.js` permutent les positions des panneaux Carte et Télémétrie, et modifient les largeurs relatives des panneaux Carte et Indicateurs de performance clés :

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Modifier la disposition des panneaux](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> La carte n’est pas configurée dans le déploiement local.

Vous pouvez également ajouter plusieurs instances du même panneau, ou plusieurs versions si vous [dupliquez et personnalisez un panneau](#duplicate-and-customize-an-existing-control). L’exemple suivant montre comment ajouter deux instances du panneau Télémétrie en modifiant le fichier `src/components/pages/dashboard/dashboard.js` :

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Vous pouvez ensuite afficher différentes données de télémétrie dans chaque panneau :

![Plusieurs panneaux Télémétrie](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> La carte n’est pas configurée dans le déploiement local.

## <a name="duplicate-and-customize-an-existing-control"></a>Dupliquer et personnaliser un contrôle existant

Les étapes suivantes expliquent comment utiliser le panneau **Alarmes** comme exemple illustrant la façon de dupliquer un panneau existant, de le modifier et d’utiliser la version modifiée :

1. Dans votre copie locale du dépôt, effectuez une copie du dossier **Alarmes** dans le dossier `src/components/pages/dashboard/panels`. Nommez la nouvelle copie **cust_alarms**.

1. Dans le fichier **alarmsPanel.js** dans le dossier **cust_alarms**, remplacez le nom de la classe par **CustAlarmsPanel** :

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Ajoutez la ligne suivante au fichier `src/components/pages/dashboard/panels/index.js` :

    ```nodejs
    export * from './cust_alarms';
    ```

1. Remplacez `AlarmsPanel` par `CustAlarmsPanel` dans le fichier `src/components/pages/dashboard/dashboard.js` :

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Vous avez remplacé le panneau **Alarmes** d’origine par une copie appelée **CustAlarms**. Cette copie est identique à celle d’origine. Vous pouvez maintenant modifier la copie. Par exemple, pour modifier l’ordre des colonnes dans le panneau **Alarmes** :

1. Ouvrez le fichier `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` .

1. Modifiez les définitions de colonnes comme illustré dans l’extrait de code suivant :

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

La capture d’écran suivante montre la nouvelle version du panneau **Alarmes** :

![Panneau Alarmes mis à jour](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personnaliser le graphique de télémétrie

Le graphique de télémétrie dans la page **Tableau de bord** est défini par les fichiers dans le dossier `src/components/pages/dashboard/panels/telemtry`. L’interface utilisateur récupère les données de télémétrie du backend de solution dans le fichier `src/services/telemetryService.js`. Les étapes suivantes vous montrent comment remplacer la période de 15 minutes affichée dans le graphique de télémétrie par 5 minutes :

1. Dans le fichier `src/services/telemetryService.js`, recherchez la fonction appelée **getTelemetryByDeviceIdP15M**. Effectuez une copie de cette fonction et modifiez la copie comme suit :

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Pour utiliser cette nouvelle fonction afin de remplir le graphique de télémétrie, ouvrez le fichier `src/components/pages/dashboard/dashboard.js`. Recherchez la ligne qui initialise le flux de données de télémétrie et modifiez-la comme suit :

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Le graphique de télémétrie affiche maintenant la période de cinq minutes de données de télémétrie :

![Graphique de télémétrie illustrant une journée](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Ajouter un nouvel indicateur de performance clé

La page **Tableau de bord** affiche les indicateurs de performance clés dans le panneau **Indicateurs de performance clés** système. Ces indicateurs de performance clés sont calculés dans le fichier `src/components/pages/dashboard/dashboard.js`. Les indicateurs de performance clés sont affichés par le fichier `src/components/pages/dashboard/panels/kpis/kpisPanel.js`. Les étapes suivantes décrivent comment calculer et afficher une nouvelle valeur d’indicateur de performance clé dans la page **Tableau de bord**. L’exemple fourni consiste à ajouter un nouveau changement de pourcentage dans l’indicateur de performance clé des alarmes d’avertissement :

1. Ouvrez le fichier `src/components/pages/dashboard/dashboard.js` . Modifiez l’objet **initialState** afin d’inclure une propriété **warningAlarmsChange** comme suit :

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Modifiez l’objet **currentAlarmsStats** afin d’inclure **totalWarningCount** comme propriété :

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Calculez le nouvel indicateur de performance clé. Recherchez le calcul pour le nombre d’alarmes critiques. Dupliquez le code et modifiez la copie comme suit :

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Ajoutez le nouvel indicateur de performance clé **warningAlarmsChange** au flux des indicateurs :

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Mettez à jour les données passées au panneau Indicateurs de performance clés :

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Vous avez terminé les modifications dans le fichier `src/components/pages/dashboard/dashboard.js`. Les étapes suivantes décrivent les changements à effectuer dans le fichier `src/components/pages/dashboard/panels/kpis/kpisPanel.js` pour afficher le nouvel indicateur de performance clé :

1. Modifiez la ligne de code suivante pour récupérer la nouvelle valeur d’indicateur de performance clé comme suit :

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Modifiez le balisage pour afficher la nouvelle valeur d’indicateur de performance clé comme suit :

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

La page **Tableau de bord** affiche maintenant la nouvelle valeur d’indicateur de performance clé :

![Indicateur de performance clé d’avertissement](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personnaliser la carte

Pour obtenir des informations détaillées sur des composants de la carte de la solution, consultez la page GitHub [Customize map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Personnaliser la carte).

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Autres options de personnalisation

Pour affiner la couche de présentation et de visualisations dans la solution de surveillance à distance, vous pouvez modifier le code. Les dépôts GitHub pertinents sont les suivants :

* [Microservice de configuration pour solutions Azure IoT (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Microservice de configuration pour solutions Azure IoT (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Interface utilisateur web de surveillance à distance de solutions préconfigurées Azure IoT](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour personnaliser l’interface utilisateur web dans l’accélérateur de solution de surveillance à distance.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de surveillance à distance, consultez [Architecture de la surveillance à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Pour plus d’informations sur la personnalisation de la solution de surveillance à distance, consultez [Personnaliser et redéployer un microservice](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->