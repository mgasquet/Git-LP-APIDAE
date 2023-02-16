---
title: TD2 &ndash; Tests, Intégration et déploiement à l'aide des workflows
subtitle: Pages, Documentation, Tests automatisés, Intégration et déploiement continu
layout: tutorial
lang: fr
---

Dans ce second (et dernier) TD, nous allons étudier diverses fonctinnalités proposées par **Github** notamment des outils de **CI/CD** (continuous integration/continuous delivery). Globalement, il s'agit d'automatiser différentes tâches qui seraient fastidieuses (et chronophages) de faire "à la main" comme le fait de tester l'intégration de différents modules de code (le problème que vous avez dû rencontrer lors du dernir TD), la construction et la mise à disposition de l'éxécutable de l'application, sur plusieurs environements. Si nécessaire, la mise en ligne de la documentation...

## Les pages de Github

Vous l'ignorez peut-être, mais github met à disposition un outil appellé **Github Pages** qui permet d'heberger un **site web statique** (côté client donc, pas ee PHP ou autre technologies serveur...). Des pages HTML/CSS simples, une application JS, un site construit avec une technologie réactive comme Angular, React, etc peuvent donc être hébergés à travers un repository et accessibles au monde entier via une URL!  
D'ailleurs, le site de TD utilise la technologie **Jekyll** et est hebergé sur github! Vous pouvez même cliquer sur **Source** en haut de la page pour accèder au repository!

Pour que cela fonctionne, il faut que votre repository soit **public**.

### Hébergement de pages statiques simples

Nous allons faire un premier essai d'hebergement d'un site statique simple. 

<div class="exercise">

1. Créez un site web simple avec une **page HTML** possédant le contenu de votre choix (et éventuellement du CSS/JS). Vous pouvez peut-être reprendre un projet développé cette année?

2. Dans le repertoire contenant votre page HTML, **initialisez** un repository.

3. Sur **Github**, créez un repository, reliez-le à votre repository en local, faites un commit et un push de votre site.

4. Toujours sur **Github**, dans votre repository, rendez vous dans l'onglet **Settings** puis **Pages** (sur le menu latéral gauche).

5. Dans la section **branch**, sélectionnez **master**. On vous propose deux dossiers : **root** (la racine du repository) ou un éventuel dossier **docs**. Laissez donc le paramètrage à **root**.

6. Cliquez sur **Save** et patientez un petit peu. Votre site web sera bientôt accessible  à l'adresse : [https://votre_pseudo_git.github.io/nom_repository/](https://votre_pseudo_git.github.io/nom_repository/). Si votre page ne s'appelle pas `index.html`, il faudra rajouter le nom de la page dans l'URL.

</div>

### Un site pour la javadoc

Dans vos cours de **programmation en Java** vous avez appris à écrire de la **documentation** assez rigoureusement! Une fois la documentation écrite, il est alors possible de générer un **site web** permettant d'explorer votre documentation!  
Nous allons combiner cela avec les **pages** de github afin que le repository héberge notre javadoc et qu'elle soit accessible, en ligne.

<div class="exercise">

1. Dans le projet d'éditeur de texte, ajoutez de la **documentation** à vos classes, à vos méthodes, etc... (peut-être pas *tout* le projet, si c'est trop long, mais suffisament!)

2. Nous allons ajouter une **tâche** à éxécuter afin que le gestionnaire de dépendances et de projet (ici **Maven**) génère le site de la javadoc. Sur IntelliJ, rendez-vous dans **Run** puis **Edit Configurations**.  Appuyez sur `+` et sélectionnez **Maven**. Donnez le nom **"Javadoc"** à votre nouvelle tâche et spécifiez `javadoc::javadoc` dans le champ `Run`. Validez.

3. En haut à droite, sélectionnez votre tâche `Javadoc` et éxécutez-là. Rendez-vous ensuite dans le dossier `target/site/apidocs` et explorez le site web créé, en local.

4. Déplacez le dossier `apidocs` à la racine du repository et renommez-le `docs`.

5. Enfin, faites un commit et poussez le sur votre repository distant. Sur **Github**, configurez votre **page** sur la branche **master** et le sous-dossier **docs**. Validez et patientez un peu, votre documentation est alors accessible en ligne!
</div>

Il peut être intéressant de mettre votre documentation en ligne de cette manière, notamment si votre projet à vocation à être utilisé par d'autres développeurs (si vous faites une **API** ou bien une **bibliothèsque**, etc...). Néanmoins, il est fastidieux de répéter toutes ces opérations manuellement à chaque changement du code! Nous allons donc voir comment automatiser tout ce processus grâce aux **workflows**.

## Decouverte des workflows

**Github** propose un outil appellé **Github actions**. Ce système permet de détecter quand un événement survient sur un repository (par exemple, un **push** sur une branche spécifique...) et de déclencher un **script** appellé **workflow** en conséquence. C'est un peu similaire aux **triggers** en base de données.

Le **workflow** va vous permettre de charger votre projet sur le (ou les) systèmes de votre choix et de réaliser des actions dessus (compilation, tests, publication d'un éxécutable, et bien d'autres)...On peut même intéragir directement avec notre repository, les branches, les issues, etc...Les sytèmes disponnibles sont **ubuntu**, **windows** et **macOs**.

Quand l'élément ciblé par le fichier de **workflow** survient, le script est éxécuté. On peut suivre la progression des tâches en direct et, en cas d'échec (par exemple, un test ne passe pas, le programme ne compile pas...) **Github** prévient en ligne qu'il y a eu des erreurs, avec les détails, et un mail est aussi envoyé au collaborateurs du repository. Avec ce système, **Github** pourrait détecter automatiquement s'il y a un soucis aquand, par exemple, deux branches sont **fusionnées** avec succès mais que le code ne compile plus, ou ne passe pas les tests.

Au-delà de ça, le workflow peut aussi servir à automatiser certaines tâches comme la mise à jour du site de la documentation, par exemple.

Les événements qui peuvent déclencher les workflows sont nombreux, on peut en retrouver la liste complète [ici](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows).

### Le fichier de workflow

Un fichier de **workflow** est un fichier `.yml` (format `YAML`) qui se place dans un sous-dossier `.github/workflows` à partir de la racine du repository.

Voici l'allure générale d'un tel fichier :

```yml
name : nom_custom_workflow

on: evenement #Par exemple, "push"

jobs: #Un job est un processus qui contient lui-même des étapes

  nom-job1: #Nom du job 1
    runs-on: systeme #Sélection du système d'exploitation où s'éxécute le job
    steps: #Etapes à réalisé sur le système
      - name: nom #Nom de l'action 1
        run: commande #Commande à éxécuter (par exemple, javac, gcc, etc...)
      - name: nom2 #Nom de l'action 2
        uses : exemple/action #Utilisation d'une action externe
        with : #Paramètres de l'action externe (si besoin)
            param1: ...
            param2: ...
        ...    

    nom-job2: #Nom du job2
        ...

permissions:
  ....      
```

Comme vous pouvez le constater, un **workflow** est constitué de différents processus appellés `jobs` qui s'éxécutent en **parallèle**. Chaque processus s'éxécute sur un environnent cible (par exemple, `ubuntu-latest`).

Chaque `job` contient une liste de tâches nommées qui s'éxécutent dans l'ordre. Il y a deux types de tâches :

- Les tâches `run` qui exécutent une commande. Si cette commande échoue (par exemple, échec de compilation) le workflow échoue.

- Les tâches `uses` qui permettent d'appeller une action de workflow **externe**, pour réaliser une action précise. Par exemple, `actions/checkout@v3` est fréquemment utilisé car il permet de vous placer dans une copie locale de votre repository (dans l'énvironnement d'éxécution) et donc, d'accèder aux fichiers. Certains actions ont besoin de paramètres qu'on peut préciser avec le bloc `with`. Beaucoup d'actions sont proposées par l'équipe de github, mais d'autres actions très utiles sont aussi proposées par la communauté!

On peut donner des `permissions` au robot éxécutant le script. Par exemple, si on souhaite que notre `workflow` puisse créer des nouvelles branches ou bien publier des `releases`, il faut lui donner des droits d'écriture en précisant la permission `contents: write`.

Pour un événement, il est aussi possible de préciser des conditions supplémentaires, par exemple, "seulement quand on push sur la branche development" :

```yml
#Se déclenche quand on push sur n'importe quelle branche
name : nom_custom_workflow

on: push

```

```yml
#Se déclenche seulement quand on push sur la branche "development"
name : nom_custom_workflow

on:
  push:
    branches:
      - development

```

A noter q'un `merge` d'une branche résultera en un `push` à un moment donné, dans la branche qui intègre les changements de la branche fusionnée.

### Automatisation des tests sur github

Pour mettre en pratique, nous allons coder un **workflow** très simple permettant de lancer les **tests unitaires** sur votre application. Mais tout d'abord...il vous faut des tests unitaires! (comment, vous n'avez pas fait de **TDD** depuis le début?!)

<div class="exercise">

1. Placez-vous dans votre branche `development`.

2. Dans le dossier `test/java` créez un **package** `fr.iutmontpellier.tests`.

3. Créez une classe `DocumentTest` et faites lui étendre la classe `TestCase`.

4. Ecrire quelques méthodes pour tester les méthodes de la classe `Document`.

5. Pour exécuter les tests, **IntelliJ** vous permet de le faire simplement, en cliquant sur le bouton de lancement, à côté de la déclaration de la classe.

</div>

Maintenant que vous avez des tests unitaires prêts et fonctionnels, nous allons faire en sorte que **Github** les exécutent après un **push** sur n'importe quelle branche.

Dans le workflow, vous aurez besoin d'un **job** pour :

1. Se déplacer dans votre repository. Vous pouvez utiliser l'action `actions/checkout@v3` pour cela.

2. Installer et mettre en place `Java` (version 17). Vous pouvez utiliser l'action `actions/setup-java@v1` paramétré (au niveau du `with`) avec `java-version: 17`.

3. Exécuter la commande `mvn test` (éxécution de tests avec maven).

<div class="exercise">

1. Créez un fichier `tests.yml` dans un nouveau dossier `.github/workflows` (placé à la racine de votre repository).

2. Grâce à ce **workflow**, faites en sorte qu'un **push** (sur n'improte quelle branche) éxécute les tests unitaires. Dans le `job` que vous allez mettre en place, utilisez le système `ubuntu-latest`. N'oubliez pas de nommer chaque `step` dans votre `job`.

3. Faites un `commit` et un `push` sur votre branche `development`. Sur `Github`, rendez-vous sur votre repository et sélectionnez l'onglet `Actions`. Vous devriez voir votre message de commit et un processus en cours d'éxécution. Cliquez dessus puis sur le nom du workflow en train de s'éxécuter, pour obtenir plus de détail et suivre le déroulement de chaque étape.

4. Maintenant, modifiez légérement la classe `Document` pour qu'un ou plusieurs tests ne passent plus. faites un commit et un push et vérifiez que le `workflow` échoue bien. Enfin, remettez tout en ordre.

</div>

### Gestion automatique de la javadoc

Maintenant que vous maîtrisez les **workflows**, vous allez pouvoir en créer un qui va automatiquement générer le site de la javadoc et le mettre en ligne à l'aide de **Github pages**!

Pour cela, vous aurez besoin de créer un `job` qui doit :

1. Se déplacer dans votre repository puis installer et mettre en place `Java` (version 17). Comme dans le workflow précédent.

2. Générer le site de la `javadoc` avec la commande `mvn javadoc::javadoc`.

3. Publier le contenu du site sur une branche dédiée. Pour cela, vous pouvez utiliser l'action `peaceiris/actions-gh-pages@v3`. Cette action nécessite deux paramètres :

    - `github_token` : permet de vous identifier pour autoriser le robot eéxcutant le script à pousser sur votre branche. Ce "token" est déjà disponnible, il suffit de le préciser avec `${{ secrets.GITHUB_TOKEN }}`.

    - `publish_dir` : le chemin du dossier contenant les fichiers à publier. En fait, cette action va créer une branche `gh-pages` contenant les fichiers du dossier précisé par le chemin `publish_dir`. Dans notre cas le site de la `javadoc` est généré dans le dossier `./target/site/apidocs/`.

Comme le robot va **créer une branche**, il faut lui donner les droits d'écriture :

```yml
permissions :
    contents: write
```

<div class="exercise">

1. Créez un fichier `documentation.yml` dans le dossier `.github/workflows`.

2. Grâce à ce **workflow**, faites en sorte qu'un **push** **sur la branche master** génère la documentation et la publie sur la branche `gh-pages`. Dans le `job` que vous allez mettre en place, utilisez le système `ubuntu-latest`. N'oubliez pas de nommer chaque `step` dans votre `job`.

3. Faites un `commit` et un `push` sur votre branche `development`. Sur `Github`, rendez-vous sur votre repository et sélectionnez l'onglet `Actions`. Vérifiez que rien ne se passe (car on n'a pas push sur master!).

4. Déplacez-vous dans votre branche `master` et réalisez un `merge` de la branche `development`. Faites un `push` et observez le déroulement de votre workflow dans l'onglet `Actions`. A l'issu, vous devriez voir qu'une branche `gh-pages` est créée!

5. Dans la configuration de `Github pages`, précisez la branche `gh-pages` plutôt que `master` et sélectionenz `root`.

</div>

Et voilà! Maintenant, maitnenant, dès qu'un push sera effectué sur la branche de production `master`, le site de la `javadoc` de votre application sera automatiquement mis à jour!

## Deploiement

**Github** permet de publier des **releases** de notre application, c'est à dire une version fonctionnelle du logiciel, avec les éxécutables et les ressources nécessaire. Avec les `workflows`, nous pouvons automatiser ce processus. Il s'agit de la partie `CD` (continous delivery) où l'application est déployée / délivrée de manière continue.

Sur **git** il est possible d'associer un **commit** à un **tag**. Un **tag** est une étiquette nommée. Généralement, on associe une `release` à un **tag**. Il est aussi possible de faire un `checkout` directement sur un tag!

Par convention, les tags s'utilisent donc pour indiquer la version d'une programme sous la forme `vX.Y.Z`. Donc, par exemple `v0.0.1`, `v1.0.0`, etc... 

Pour créer un **tag** on utiliser simplement la commande :

```bash
git tag vX.Y.Z
```

Cela aura pour effet d'associer le dernier commit à ce **tag**. Pour indiquer au repository distant que le tag a été créé, il faut le **pousser**. Par exemple :

```bash
git push origin v0.0.1
```

Pour **supprimer** un tag, on utilise l'option `-d` avec la commande `tag` :

```bash
git tag -d vX.Y.Z
```

### Déploiement de l'application d'édition de texte

Nous allons faire en sorte que, dès qu'un **tag** est **push** sur le repository distant, le code de l'application soit compilé et qu'une **release** contenant notre exécutable (ici le fichier `.jar`) soit publié. Ainsi, dès qu'une novuelle version est prête, il suffit de créer un **tag** et **Github** se charge alors du reste.

Dans une **release**, il est possible d'uploader différents types de fichiers. Par défaut, le code source de l'application est mis à disposition. Dans notre cas, nous allons seulement ajouter l'éxécutable `.jar` de l'application, mais dans l'absolu, il est tout à fait possible d'inclure d'autres fichiers.

Pour réaliser ce processus, le `job` du workflow aura besoin de :

1. Se déplacer dans votre repository puis installer et mettre en place `Java` (version 17). Comme dans pour les workflows précédents.

2. Générer l'éxécutable `.jar` de votre application avec la commande `mvn package`. L'éxécutable est placé dans le dossier `target` sous le nom `EditeurDeTexte-0.0.1.jar`.

3. Créer une `release` contenant le fichier `EditeurDeTexte-0.0.1.jar`. Pour cela, vous pouvez utiliser l'action `softprops/action-gh-release@v1`. Cette action nécessite un paramètre :

    - `files` : le chemin des fichiers à inclure dans la `release` (ici `target/EditeurDeTexte-0.0.1.jar`).

    Si on doit préciser **plusieurs fichiers**, on utilise ce format :

    ```yml
        files: |
            chemin1
            chemin2
            ...
    ```

Pour préciser qu'on souhaite éxécuter ce **workflow** seulement si un **tag** est poussé, on écrit :

```yml
on:
  push:
    tags:
    - '*'
```

Là aussi, il faut donner la permission 

<div class="exercise">

1. Créez un fichier `release.yml` dans le dossier `.github/workflows`.

2. Grâce à ce **workflow**, faites en sorte qu'un **push** d'un **tag** génère une **release** contenant le fichier **.jar**.

3. Faites un `commit` et un `push` sur votre branche `master`.

4. Créez un **tag** nommé `v0.0.1` puis, poussez-le sur le repository distant.

5. Sur `Github`, rendez-vous sur votre repository et sélectionnez l'onglet `Actions`. Suivez l'avancement de la création de votre `release`.

6. Une fois le `workflow` achevé, rendez-vous sur la page principale de votre `repository` puis cliquez sur `Releases` dans le menu latéral droit. Vous derviez voir votre `release` et le fichier `.jar` qu'elle contient!

</div>

Très bien, votre `workflow` fonctionne, mais on aimerait que la version affichée sur le fichier `.jar` s'adapte au nom du `tag`. Pour cela, il est possible de préciser à `maven` de changer la `version` du projet avant de générer le `.jar`. Sans cela, il faudrait modifier le fichier `workflow` à chaque publication d'une nouvelle version (et aussi modifier `pom.xml`).

Dans votre `workflow`, vous povuez donc intégrer l'éxécution de la commande suivante :

```bash
mvn versions:set -DnewVersion=nouvelle_version -DgenerateBackupPoms=false
```

A la place de `nouvelle_version` vous pouvez utiliser `${{ github.ref_name }}` qui permet d'obtenir, via le `workflow` le nom de l'élément en train d'être poussé sur le repository. Pour un commit, il s'agirait de son identifiant, et pour un tag, il s'agit simplement de son nom. Ainsi, lors de la construction du `.jar`, si le **tag** se nomme `v0.0.2`, le fichier généré sera `EditeurDeTexte-v0.0.2.jar`.

On doit utiliser une nouvelle fois `${{ github.ref_name }}` pour le chemin du fichier à inclure dans le paramètre `files` de l'action `softprops/action-gh-release@v1`.

<div class="exercise">

1. Apportez les modifications nécessaires à votre `workflow` pour que le nom de l'éxécutable s'adapte au nom du **tag**.

2. Faites un commit et poussez-le sur le repository distant puis créez un tag `v0.0.2` et poussez-le aussi.

3. Vérifiez que le `workflow` s'éxécute bien puis vérifiez que le fichier placé dans la rrelease `v0.0.2` porte le bon nom.

</div>

### Déploiement d'un programme multi-plateformes

Pour un programme développé avec un langage interprété (comme `python`) ou pour un langage utilisant une machine virutelle (comme `Java`), le fichier du programme ou l'éxécutable généré est compatible avec n'importe quel système d'exploitation. En effet, si vous compilez un programme java sur `windows`, il pourra être directement éxécuté sur un styème tournant sur `macOS`, par exemple.

Cela ne s'applique pas pour les programmes `compilés` comme le `C` où l'éxécutable dépend du système d'exploitation. Ainsi, un programme `C` compilé sous `windows` donnera un eéxécutable en `.exe` qu'on ne peut pas lancer tel quel sur `ubuntu`, par exemple. C'est pour cela que, quand on livre un tel programme, on propose généralement 3 types de téléchargements, selon le système d'exploitation de l'utilisateur.

Comme nous l'avons vu, les `workflows` permettent aussi d'utiliser `windows` et `macOS` dans les `jobs`! Nous pouvons donc lancer 3 `jobs` pour compiler un programme `C` sur les différents systèmes et ensuite inclure les 3 fichiers à une release.

Nous allons tester ce système sur un petit programme très simple, codé en `C`

<div class="exercise">

1. Téléchargez le fichier [puissance.c]({{site.baseurl}}/assets/TD2/puissance.c). Ce programme permet de calculer le résultat de `x` élevé à la puissance `n`. Il s'utilise en passant `x` et `n` en argument.

2. Compilez le programme, testez-le rapidement.

3. Créez un repository contenant seulement le fichier `puissance.c`.

4. Sur `Github`, créez un nouveau repository puis reliez-le à votre repository local. Faites ensuite votre premier `commit` ainsi que votre premier `push`.

</div>

De la même manière que pour l'application d'édition de texte, nous allons créer un `workflow` permettant de publier une `release` quand un `tag` est poussé.

Dans chaque système utilisable dans les `jobs`, le programme `gcc` (pour compiler) est déjà installé.  
Voici donc un `job` que l'on pourrait utiliser pour compiler le programme pour un environnement `windows` :

```yml
  build-windows:
    runs-on: windows-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Build
        run: gcc puissance.c -o puissance-windows.exe
```

<div class="exercise">

1. Créez un fichier `release.yml` dans un nouveau dossier `.github/workflows` placé dans votre repository.

2. Faites en sorte que ce workflow s'éxécute seulement quand un `tag` est `push`.

3. Ajoutez **trois jobs** pour compiler le programme dans chaque système. Les systèmes disponibles sont : `ubuntu-latest`, `windows-latest` et `macos-latest`. Pour `ubuntu`, l'éxécutable devra se nommer `puissance-linux.bin`, pour `macos` : `puissance-macos` (pas d'extension) et pour `windows` on aura donc `puissance-windows.exe`.

</div>

Vous avez maintenant trois jobs qui permettent de compiler votre programme de puissance dans chaque système. Cependant, pour créer la `release`, nous avons besoin de récupérer ces trois éxécutables! Et cela pose problème, car on ne peut pas y accèder direcetement (car chaque `job` s'éxécute sur un environnement différent).

Pour résoudre ce problème, nous allons utiliser le système `artifact`. Il s'agit d'une aciton qui permet d'uploader un fichier depuis un `job` (avec un nom associé) vers un espace spécial sur `Github` puis de le télécharger dans un autre `job`.

Les artifacts s'utilisent au travers de **deux actions** :

- `actions/upload-artifact@master` : permet d'uploader un artifact pour le rendre disponible aux autres `jobs`. Il faut préciser un paramètre `name` correspondant au nom de l'artifact et un autre paramètre `path` correspondant au chemin du fichier ciblé (qu'on souhaite uploader). Le fichier sera mis à disposition sous le nom précisé par `name` et les autres `jobs` pourront alors le télécharger.

- `actions/download-artifact@master` : permet de télécharger un artifact. Il faut préciser un paramètre `name` correspondant au nom de l'artifact ciblé. Le fichier est alors téléchargé et disponible dans l'environement du `job`.

Le but est donc que chaque `job` fasse un `upload` de l'éxécutable compiler. Ensuite, un autre `job` doit télécharger ces fichiers puis créer la `release` en ajoutant les trois éxécutables.  
Attention, de base, tous les `jobs` sont concurents (s'éxécutent en parallèle) mais celui-ci a besoin d'attendre que les trois autres soient terminés. Pour cela, on peut utiliser le paramètre `needs` au niveau du job pour préciser que ce job doit attendre que les `jobs` précisés doivent s'être achevés avant de débuter.

Par exemple :

```yml
name : exemple

on: ...

jobs:

    j1:
        runs-on: ...
        steps:
            ...
    
    j2:
        runs-on: ...
        steps:
            ...

    j3:
        needs: [j1, j2]
        runs-on: ...
        steps:
            ...

```

<div class="exercise">

1. Modifiez votre `workflow` afin que les trois `jobs` fasse un `upload` de leur éxécutable.

2. Ajoutez un `job` qui doit **attendre** que les trois autres `jobs` soient terminés avant de s'éxécuter. Ce job s'éxécutera sur `ubuntu-latest` et devra télécharger les trois éxécutables compilés puis créer une `release` contenant ces trois fichiers.

3. Pour que votre `workflow` soit autorisé à créer une `release`, donnez-lui la permission `contents: write`.

4. Faites un `commit`, un `push` puis créez un `tag` nommé `v1.0.0` que vous pousserez également sur votre repository distant.

5. Observez l'éxécution du `workflow` dans l'onglet `Actions` puis vérifiez que la release créée contient bien les trois éxécutables. 

</div>

Bravo! Vous pouvez maintenant déployer votre programme aisément, sur toutes les 3 plateformes. Cependant, pour les langages compilés et notamment pour le `C`, certaines librairies ne sont pas forcément les mêmes selon le système (par exemple, certaines librairies sur les `sockets`). C'est toute la problématique de la **portablilité**. Dans ce cas, il faut donc, par exemple, avoir un code source (ou une partie) pour `ubuntu`, un pour `windows` et faire l'adaptation. Les `workflows` de **Github** peuvent toujours être utilisés en compilant les fichiers spécifiques, selon le système, mais également pour tester qu'un programme fonctionne comme attendu dans un un des systèmes cibles (il serait fastidieux de tester les trois sytèmes à la main après chaque mise à jour!)

Vous aurez peut-être noté que certaines actions que nous réalisons dans les trois `jobs` pour compiler le programme sont assez redondantes. Nous pourrions optimiser cela avec des `matrices` qui permettraient d'éxécuter le contenu d'un `job` sur les trois sytèmes, en faisant de légères adaptations (pour les noms des exécutables produits).  
Si le temps le permet, allez consulter la [documentation](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs) à ce propos et essayez donc de rendre votre `workflow` plus léger.

## Conclusion

A travers ce dernier TP, vous avez donc appris à vous servir de certaines fonctionnalités plus poussées de la plateforme **Github**. Comme annoncé dans le `TD1`, ces outils ne sont pas nécessairement exclusifs à **Github**. Vous pouvez les retrouver sous d'autres formes sur **Gitlab** ou **Bitbucket**. 

Comme vous l'avez constaté, les techniques de `CI/CD` présentent beaucoup d'avantages dans le cadre du développement d'un projet. Dans ce TD, nous avons étudié seulement quelques exemples, mais il est possible de faire bien plus! Les `actions` mises à disposition par la communauté ainsi que les événements permettant de déclencher les `workflows` sont très nombreux.