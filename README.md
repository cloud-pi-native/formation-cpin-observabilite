# Observabilité sur CPiN

Vous en êtes à l'étape 6 de la formation CPiN :
1. [Gestion des projets CPiN](https://github.com/cloud-pi-native/formation-cpin-gestion-projet)
2. [Application d'exemple pour déploiement sur CPiN](https://github.com/cloud-pi-native/formation-cpin-repo-applicatif)
3. [Gestion des artefacts sur CPiN](https://github.com/cloud-pi-native/formation-cpin-harbor-trivy)
4. [Chart Helm de démonstration sur CPiN](https://github.com/cloud-pi-native/formation-cpin-deploiement)
5. [Gestion des secrets sur CPIN](https://github.com/cloud-pi-native/formation-cpin-gestion-secret)
6. ➡️ [Observabilité sur CPiN](https://github.com/cloud-pi-native/formation-cpin-observabilite)

## Grafana

> [!WARNING]
> Il est nécessaire d'avoir déployé une application sur CPiN pour poursuivre cette formation

### Accès

▶️ Depuis la console CPiN, allez sur votre projet dans l'onglet `Services Externes`. Cliquez sur la tuile *Grafana* 
correspondant à votre environnement :
 - **Grafana Production** : Accès aux logs et métriques des environnements de type production
 - **Grafana Hors production** : Accès aux logs et métriques des environnements de type différent de production (dev, 
staging, etc.)

> [!WARNING]
> Attention, le type d'environnement est déterminant dans la façon de consommer l'observabilité.

![choix grafana](./img/observabilite-choix-grafana.png)

▶️ Pour vous authentifier, sélectionnez **Sign in with grafana-projects**.

### Accès aux dashboards par défaut

▶️ Allez dans le menu **dashboard** sur la gauche puis ouvrez le folder **dso-grafana**. 

![menu dashboard](./img/observabilite-menu-dashboard.png)

Différents dashboards par défaut sont accessibles :
 - **Falcosidekick** : Métriques concernant les indicateurs Falco (détection de comportements étranges sur les *PODs*)
 - **Kubernetes / Views / Namespaces** : Métriques sur la consommation de ressources système CPU/RAM etc. 
 - **Loki Kubernetes logs** : Accès aux logs applicatifs (tout ce qui est envoyé sur stdout / stderr sur les *PODs*)
 - **PolicyReport Details** : Métriques concernant les indicateurs Kyverno (respect des contraintes CPiN sur les 
manifests, par exemple la présence de labels, etc.)
 - **Trivy Operator - Vulnerabilities** : Métriques concernant les indicateurs Trivy (présence de CVE sur les 
containers)
 - **Security overview** : Dashboard synthétique des indicateurs Kyverno, Falco et Trivy

> [!WARNING] 
> À noter que certains comportements peuvent différer entre PAX et MI, notamment certains dashboard peuvent ne pas 
> fonctionner sur PAX.

Exemple de remontée de métriques :

![menu dashboard](./img/observabilite-dashboard-metriques.png)

### Ajout de dashboard custom

CPiN intègre une fonctionnalité de *Dashboard as Code*.

Pour rappel, lors de la création d'un projet, un dépôt de code **infra-observability** est automatiquement créé dans 
gitlab.

![repo infra-observability](/img/repo-infra-observability.png)

Ce dépôt de code peut contenir 2 types de fichiers :
 - Des Dashboard grafana au format *.json* dans le répertoire `/files/dashboards/`
 - Des alertes au format *.yaml.tpl* dans le répertoire `/files/rules/`

Les fichiers présents dans ces répertoires sont automatiquement déployés dans grafana. Le déploiement peut prendre 
jusqu'à 3 minutes pour se synchroniser.

Le format *JSON* des dashboards étant complexe, il est généralement recommandé de le construire directement via 
l'interface Grafana ou d'en copier un existant. Pour créer votre dashboard, vous devrez l'exporter au format *JSON* et
copier son contenu dans un fichier, dans le répertoire `/files/dashboards/` comme mentionné plus haut.

Pour cela depuis un dashboard existant, cliquez sur *share* :

![share](./img/dashboard-share.png)

Puis *Export* et *View JSON*

![export](./img/dashboard-export-view-json.png)

Enfin cliquez sur *Copy to Clipboard*

![copy](./img/dashboard-copy-to-clipboard.png)

La synchronisation du repo de code gitlab *infra-observability* vers *Grafana* se fait via une *Application ArgoCD* 
nommée [env]-[projet]-observability :

![argo](./img/argocd-dashboard-as-code.png)

Un objet de type Kubernetes *GrafanaDashboard* est alors créé pour chaque dashboard *as code* présent dans le dépôt. En 
cas d'erreur sur le contenu d'un fichier *JSON*, les erreurs seront visibles dans les événements cet objet via ArgoCD.

> [!NOTE]
> À noter que seule la branche **main** est synchronisée.

La video suivante illustre cette fonctionnalité :

https://cpin-public-ressources.s3.fr-par.scw.cloud/documentation/cloud-pi-native/dashboard-as-code.mp4

Bravo, vous avez terminé le dernier chapitre de la formation CPiN sur l'observabilité !