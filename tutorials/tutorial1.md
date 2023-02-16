---
title: TD1 &ndash; Introduction à Git et aux fonctionnalités de Github
subtitle: Versioning, Github
layout: tutorial
lang: fr
---

## Introduction

Dans ce premier TD, vous allez apprendre (ou réapprendre / découvrir) les fonctionnalités du logiciel **git**. Ce logiciel est un logiciel dit de **versioning**. Concrètement, cela signifit qu'il est possible de sauvegarder l'état d'un ensemble de fichiers à un moment donné. Cet ensemble de fichiers (par exemple, un projet) est appellé **repository**. La sauvegarde (et le versioning) de l'état dun repository s'effectue grâce à une action appellée **commit** qui crée une **référence** (unique, dans le projet). Il est alors possible de revenir à ce **commit** en utilisant la référence, ultérieurement, et surtout, consulter l'historique sur l'évolution du développement d'une application.

Le versioning est aussi essentiel car il permet, par exemple, de sauvegarder un état "stable" d'un projet et ne pas risquer de tout perdre à cause d'une fausse manipulation, d'essais infructeux, etc...

Au delà du versioning, git est aussi un outil permettant de gérer le développment d'un projet en équipe. En effet, git permet d'intégrer les modifications et ajouts de code provenant de deux commits différents (plutôt provenant de **branches**, mais nous en reparlerons). Cette activité est appellée **merging** et se fait *la plupart du temps* automatiquement. Par exemple, dans un projet, plusieurs personnes vont travailler sur différentes fonctionnalités (par exemple, une **user story** dans un sprint **SCRUM**). A un moment (à la fin du sprint) il y a besoin d'intégrer ces fonctionnalités au produit final. C'est là qu'intervient le **merging**. Dans certains cas, si deux développeurs ont modifié le même fichier et qu'il est difficile d'appliquer les règles de fusion automatique, un **conflit** survient. C'est alors au développeur de prendre la main est de décider du fichier final qui sera conservé (version du fichier 1, version fu fichier 2, ou bien, un mix des deux versions).

Afin de rendre facilement accessible le projet entre différentes machines et développeurs, il est possible de lier son **repository** à un dépôt distant. Le projet peut alors être **cloné** depuis cet espace distant. Les développeurs synchronisent alors leur repository local avec le repository distant. Il s'agit également d'une sécurité supplémentaire : en plus d'être suavegardé sur la machine, le projet (et son historique de versions) est suavegardé en ligne. Ainsi, en cas d'incident sur la machine locale ou de perte des données, le projet ne sera pas perdu! Il faut donc penser à se synchroniser aussi souvent que possible. L'action de soumettre les modifcations du'n repository local vers un repository distant est appellée **push**. L'action de récupérer des informations depuis un repository distant est appellée **pull**. Si la version locale difféère avec la version distante, une opération de **merging** est appliquée.

Différentes plateformes permettant de gérer des dépôts distant ont vu le jour. Les plus populaires sont notamment : **Github**, **Gitlab** et **Bitbucket**. En plus de gérer la "mise en cloud" du repository, ces plateformes proposent aussi des services que nous allons explorer durant les 2 TDs. Lors de la prochaine séance, nous parlerons notamment des fonctionnalités **d'intégration et de déploiement continu** qui permettent de tester, construire, publier notre programme de manière automatique et s'assurer que toutes les fonctionnalités s'intégrent bien ensemblent.

Lors des deux séances, le fil rouge sera une application **JAVA** (déjà existante mais incomplète) sur laquelle vous travaillerez. D'abord seul puis ensuite en collaboration avec d'autres membres du groupe. Le but sera alors de verisonner se projet et d'y ajouter des nouvelles fonctionnalités. Lors du second TP, nous étudierons les **workflows** de github qui permettront de tester et déployer automatiquement cette application (et d'autres!).

## Prise en main

Le but de cette première section est de prendre en main l'application qui vous est fournie, de la comprendre, et d'initaliser un repository **git** pour ce projet. Pour la plateforme en ligne, nous utiliserons **github**. Néamoins, les fonctionnalités présentées à travers **github** sont applicables à d'autres plateformes! Il suffit de s'adapter au fonctionnement et spécificités de la plateforme (comme quand vous apprenez un nouveau langage de programmation).

### Installation du projet

<div class="exercise">

1. Tout d'abord, commencez par télécharger le [code source de l'application]({{site.baseurl}}/assets/TD1/EditeurDeTexteEtudiant.zip).

2. Extrayez le projet puis ouvrez le avec **IntelliJ IDEA**. Laissez le temps au projet de se configurer.

3. Explorez le code mis à disposition et déduisez le fonctionnement de l'application. N'hésitez pas à demander des explications si il y a des éléments que vous ne comrpenez pas.

</div>

L'application fournie est un mini éditeur de texte en mode ligne de commande. A l'aide du design pattern **Command** le programme est capable de reçevoir et gérer des commandes et d'éxécuter des actions sur le "document texte" que gère l'application.

On a le fonctionnement suivant :

- La classe **Document** contient une chaîne de caractères ainsi que diverses méthodes qui permettent de manipuler cette chaîne de caractères.

- Les classes de "commandes" (comme **CommandeAjouter**) contiennent le document et intéragissent avec. Chaque commande possède également un ensemble de paramètres (sous la forme d'un tableau de chaînes de caractères). Quand une commande est éxécutée, celle-ci utilise les informations des paramètres pour appeller une action sur le document.  

- La classe **CommandFactory** est une classe qui permet d'instancier une commande à partir d'un nom (et le document ainsi que les paramètres). Cette classe permet donc de faire l'association entre le nom d'une commande tapée par l'utilisateur et la classe correspondante.

- La classe **CommandInvoker** permet simplement d'éxécuter une commande. Pour l'instant, cette classe peut vous sembler assez inutile, mais vous l'améliorerez dans le futur (si vous êtes en avance!).

- Enfin, le **main** va lire les entrées utilisateurs, extraire le nom de la commande et les paramètres. La factory est ensuite appellée pour instancier la commande correspondante et enfin, l'invoker l'éxécute. On se fixe comme règle que l'utilisateur doit séparer les différents paramètres d'une commande par des **points virgules**.

**Attention**, dans les commandes, le tableau des paramètres est passé tel quel, sans autre traitement. Donc, la première case de ce tableau (index 0) contient le **nom de la commande**. Pour obtenir les paramètres qui intéressent la commande, il faut aller chercher à partir de la seconde case (d'index 1).

Au lancement, le programme contient un document "vide". Après l'éxécution d'une commande, la chaîne de caractères est ré-affichée. Pour le moment, une seule commande est disponnible, **la commande d'ajout**. Pour l'utiliser, il faut donc écrire (dans la console, une fois le programme lancé) : 

```bash
ajouter;texte à ajouter
```

On peut donc faire, par exemple :

```bash
ajouter;hello
hello
ajouter; world
hello world
```

Vous l'aurez compris, votre objectif sera de dôter notre document de **nouvelles fonctionnalités** accessibles au travers de nouvelles commandes.

### Votre (premier...?) repository

Les commandes "de base" à connaître pour manipuler un repository **git** sont les suivantes :

**Initiatialisation d'un repository**

```bash
git init
```

Cette commande permet d'initialiser un nouveau **repository**. Cela ne signfiie pas que cela doit nécessairement être fait dans un dossier vide. En fait, cette commande va simplement créer un dossier **.git** (caché, par défaut) dans le dossier courant. A partir du moment où ce dossier est donc initalisé, l'ensemble des éléments se trouvant dans le même dossier sont prêts à être versionnés. Les informations des changements effectués entre chaque version sont alors stockées dans ce dossier **.git**.

Attention à ne pas supprimer le dossier **.git** par erreur! Vous pedriez alors tout votre historique de versions comme si votre projet n'avait jamais été versionné...!

**Identité du développeur**

```bash
git config user.name nom
git config user.email adresseMail
```

Les différents **commits** réalisés doivent porter des informations sur leur auteur. De plus, les plateformes de gestion de repositories distants ont besoin de ces informations pour identifier le profil de l'utilisateur (sur le site) ayant effectué un commit.

Ces commandes configurent l'identité du développeur **seulement pour le repostiroy courant**. Il est néamoins possible de définir une identié **globale** en ajoutant le paramètre `--global`. Ainsi, la commande n'aura pas à être rappelée lorsqu'on créé un nouveau repository. Il est néamoins toujours possible de configurer une identité globale générale et une identié spécifique pour un repository donné, si besoin.

**Ajout des fichiers à versionner**

```bash
git add element
```

Par défaut, tous les changement de fichiers ne sont pas automatiquement traqués par **git**. Avant d'effectuer un **commit**, il faut dire les fichiers qu'on ajoute. Si on précise un dossier, tous ses fichiers (et sous-dossiers) seront ajoutés, recursivement. Donc, si on souhaite ajouter tous les fichiers, il suffit simplement de faire, depuis la racine du **repository** :

```bash
git add .
```

En effet, le point "." désigne le dossier courant. On le rappelle encore, il faut systématiquement ajouter les fichiers qui ont subi des changements (si on souhaite toujours les traquer) avant de réaliser un **commit** (de toute façon, si ce n'est pas fait, **git** ne vous laissera pas faire de commit).

**Commit**

```bash
git commit -m "Message du commit"
```

Cette commande permet donc de créer un **point d'ancrage** pour versionner l'état actuel de votre code. Une **référence** unique (à votre repository) est alors créée. On l'utilise donc comme une commande de sauvegarde.

Le **message** est un élément obligatoire. Il indique ce qui a changé depuis le dernier commit, et s'affichera aussi dans l'historique.

**Déplacement vers un commit**

Si besoin, vous pouvez revenir à une version précédente de votre projet en vous déplçant vers un commit spécifique avec :

```bash
git checkout referenceCommit
```

Néamoins, on préférera se déplacer vers une **branche** ou bien un **tag** avec cette commande plutôt que sur un **commit**. Nous en reparlerons plus tard.

**Ignorer certains éléments**

Parfois, dnas l'environnement de travail, il y a des éléments (fichiers/dossiers) qu'il n'est pas souhaitable de versionner. Par exemple, les **dépendances** d'un projet (qui sont généralement lourdes et installées de manière externe) ou bien encore, les **fichiers de classes compilées** qui ne font pas partie du code source.

Pour faire en sorte que **git** ne traque pas certains éléments, il suffit de créer un fichier **.gitignore** à la racine du repository. On peut alors lui préciser des chemins de fichiers ou des chemin de dossiers à ignorer (chemin depuis la racine du repository). Les chemins des dossiers doivent temriner par le séparateur **/**  
Par exemple :

```bash
exemple/a/mon_fichier_inutile.txt
mon_fichier_inutile2.txt
exemple/a/dossier_a_ignorer/
```

<div class="exercise">

1. Ouvrez un terminal et rendez-vous dans le dossier de l'application `EditeurTexteEdutiant`.

2. Initialisez un repository.

3. Configurez vos informations. Plus tard, vous devrez utiliser **Github**. Indiquez le nom d'utilisateur et l'adresse mail que vous utilisez sur cette plateforme. Si vous n'avez pas encore de compte, choissiez déja le nom d'utilisateur et à l'adresse mail que vous utiliserez pour vous inscrire sur cette plateforme.

4. Ajoutez un fichier **.gitignore** permettant d'ignorer le dossier `targets/`. Il s'agit de fichiers générés à chaque éxécution du projet depuis l'IDE, cela ne doit pas être versionné. Faites aussi en sorte d'ignorer le dossier `.idea/` (fichiers caches relatifs à l'IDE).

5. Ajoutez tous les fichiers, afin qu'ils soient disponnibles pour le **commit**.

6. Effectuez un **commit** ayant pour message "init".

7. Exécutez la commande **git log** pour observer l'historique.

8. Effectuez un petit changement, par exemple, l'ajout d'un fichier **README.md**. Faites en sorte de faire un nouveau commit et observez de nouveau l'historique avec **git log**.

</div>

### Mise en place d'un dépôt distant avec Github

Vous disposez maintenant de quoi **versioner** votre code en local, il est donc temps d'attacher ce repository à un **repository** distant et de le synchroniser.

<div class="exercise">

1. Rendez vous sur [github](https://github.com) et connectez-vous, ou bien, créez un compte (dans ce cas, préférez utiliser votre adresse email universitaire).

2. Si vous souhaitez que votre adresse mail ne soit pas visible dans l'historique des commits mais que github puisse toujours vous identifier, rendez-vous [sur cette page](https://github.com/settings/emails) et cochez la case **"Keep my email addresses private"**. Plus haut, une adresse mail "noreply" vous est donnée dans le champ d'explication den dessous de **"Primary email address"**. Vous pouvez alors remplacer votre adresse mail dans la configuration de **git** par celle-ci.

2. Une fois connecté, rendez vous sur [la page de création d'un repository](https://github.com/new).

3. Donnez le nom de votre choix au repository et laissez la visiblité sur **public** (nécessaire dans le cadre du TP!). La visibilité d'un projet n'est pas fixe, il est possible de la changer utlérieurement. Validez et observez votre nouveau repository (vide, pour le moment).

4. Copiez l'adresse **ssh** de type `git@...` qui vous est donnée dans le cadre **Quick Setup**.

</div>

Pour pouvoir relier un repository local à un repository distant, on utilise la commande suviante :

```bash
git remote add nom_distant adresse_repository
```
Les deux paramètres dont vous devez changer la valeur sont donc :

- `nom_distant` : Un nom custom que vous donnez au repository distant. Généralement, on le nomme **origin**.  
- `adresse_repository` : L'adresse du repository (par exemple, celle que vous avez copié à la fin de l'exercice précédent).

Enfin, à tout moment, on peut **pousser** tous les **commits** effectués en local afin de les synchroniser avec le repository distant en utilisant la commande :

```bash
git push nom_distant nom_branche
```

Le paramètre `nom_branche` correspond à la **branche** dans laquelle vous vous trouvez. Par défaut, il s'agit de la branche nommée **master**. Nous reviendrons bientôt sur cette notion de branche. Pour l'instant, vous pousserez donc seulement sur la branche **master**.

De l'autre côté, si vous souhaitez récupérer les modifications apportées sur un repository distant afin de mettre à jour votre repository local (par exemple, si un commit a été poussé par un collègue sur le repository distant) il faut utiliser :

```bash
git pull nom_distant nom_branche
```

Enfin, il est possible de `cloner` un repository distant avant de lre récupérer sur sa machine (par exemple, si vous êtes sur une autre machine et que vous souhaitez reprendre le travail). On se place alors dans un repertoire qui accueillera le futur dossier du repository et on éxécute la commande :

```bash
git clone adresse_repository
```

### Sécurité et clé

Bien sûr, cela serait un désastre en termes de sécurité si n'importe qui disposant de l'adresse de votre repository pouvait **push** des commits! Heureusement, git ne laisse pas faire et utilise un système de paire de clés de chiffrement asymétriques (privée/publique) pour s'assurer que seules les personnes autorisée effectue des changements sur le repository distant.

Il faut donc créer et enregistrer une **clé privée** sur votre machine et enregistrer une **clé publique** publique sur votre profil **github**. Ensuite, on indiquera à git d'utiliser cette clé pour vous authentifier quand il pousse des modifications. Il faudra donc répéter l'opération sur toutes les machines où vous utiliserez le repository (ou alors, transférer votre clé privée d'une machine à l'autre).

Nous allons effectuer ces étapes pas à pas.

<div class="exercise">

1. Depuis un terminal, générez une paire clés asymétriques :

    ```bash
    ssh-keygen -t ed25519 -C "votre_adresse_mail_github@example.com"
    ```

    Par défaut, un chemin vous est proposé pour stocker la clé. Vous pouvez le changer ou laisser vide pour garder le chemin par défaut. Vous pouvez aussi changer le nom de la clé

    Une **passphrase** (mot de passe / phrase secrète) peut aussi être spécifiée. Si vous en utilisez une, vous devrez taper ce mot de passe lors de chaque **push**. Si vous n'en mettez pas, rien ne vous sera demandé lors d'un **push**, mais si le fichier contenant la clé est volé, quelqu'un pourrait usurper votre identité!

2. Ajoutez votre **clé privée** à **git** :

    ```bash
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_ed25519
    ```

    Bien sûr, il s'agit ici du chemin par défaut. Si vous avez nommé votre clé différemment et que vous l'avez placée à un autre endroit, il faudra adapter cette commande.

3. Copiez le contenu de votre **clé publique** dans le presse papier :

    ```bash
    clip < ~/.ssh/id_ed25519.pub
    ```

    Toujours en adaptant la commande, si vous n'avez pas gardé les valeurs par défaut.

4. Rendez vous sur [la page de gestion des clés](https://github.com/settings/keys) de votre profil github. Cliquez sur **"New SSH key"**.

5. Précisez le nom que vous souhaitez puis **collez votre clé publique** (qui doit normalement être dans votre presse-papier). Validez et constatez que votre clé a bien été ajoutée à votre profil **github**.

6. Toujours dans votre terminal, rendez-vous à la racine de votre **repository** (si vous n'y êtes pas déjà) puis éxécutez enfin la commande pour **pousser** vos commits. Rechargez la page de votre repository sur github. Vous devriez voir vos fichiers. N'hésitez pas à explorer cette page.
</div>

Vous avez testé de **pousser** des modifications sur le repository distant, vous allez maintenant tester la **synchronisation** de votre repository local avec le contenu du repository distant.

<div class="exercise">

1. Nous allons simuler un collègue effectuant des modifications sur le projet. Depuis votre terminal, déplacez vous quelque part, en dehors de votre repository.

2. **Clonez** votre repository distant avec la commande adéquate. Dans le contexte de cet exemple, il s'agirait de votre collègue, récupérant le repository.

3. Dans ce nouveau repository importé, effectuez une petite modification (par exemple, un ajout dans le readme...) puis effectuez un **commit** et un **push** (et n'oubliez pas de faire le **add** au préalable pour traquer les modifications effectuées!). Attention, si au début du TP vous aviez cofniguré votre **identité git** (nom/email) en **local** et non pas en **global**, il faudra recommencer.

4. Supprimez ce repository cloné.

5. Retournez dans le repository local original. Récupérez les modifications effectuées sur le repository distant en utilisant la commande adéquate. Vérifiez que les ajouts ont bien été synchronisés avec votre repository local.

</div>

### Ajout de fonctionnalités

Vous allez maintenant ajouter quelques nouvelles commandes à l'application d'édition de texte!  
Pour le prochain exercice (et pour pouvoir vous montrer quelque chose d'interessant dans la prochaine section), vous devez effectuer **plusieurs commits** pendant le développement de la fonctionnalité (ne pas avoir un seul commit par fonctionnalité). A vous de voir quand effectuer ces commits.

<div class="exercise">

1. On souhaite ajouter une commande pour **remplacer** une portion du texte par une autre chaîne de caractères. Tout d'abord, commencez par ajouter la méthode suivante dans la classe `Document` :

    ```java
    public void remplacer(int start, int end, String remplacement) {
        String leftPart = this.texte.substring(0, start);
        String rightPart = this.texte.substring(end);
        this.texte = leftPart + remplacement + rightPart;
    }
    ```
2. On souhaite donc créer une commande `remplacer` qui s'éxécutera ainsi :

    ```bash
    remplacer;depart;fin;chaine
    ```
   
   Cette commande remplace la portion du texte du document située entre l'index `depart` (inclus) et l'index `fin` (exclus) par la chaîne de caractères `chaine` (qui n'est pas olbigée d'avoir la même longueur que la zone remplacée).

   Voici un exemple d'éxécution utilisant la commande `ajouter` et `remplacer` :

    ```bash
    ajouter;hello world!
    hello world!
    remplacer;0;5;banane
    banane world!
    remplacer;0;3;
    ane world!
    ```

3. En vous inspirant de la classe `CommandeAjouter`, créez une classe `CommandeRemplacer`. Attention, les paramètres fournis dans la commande sont de type `String` et vous avez besoin de nombres `entier`. Vous pouvez alors utiliser la méthode de classe `Integer.parseInt(...)` pour convertir une chaîne de caractères en entier.

4. Enregistrez cette nouvelle commande dans la méthode **createCommand** de `CommandeFactory`, en vous basant sur l'exemple de la commande `ajouter`.

5. Testez que votre nouvelle commande fonctionne comme attendu.

6. Normalement, vous avez effectuez plusieurs `commits`. Poussez vos modifications sur le repository distant.
</div>

### Regroupper des commits (et éventuellement réparer ses bêtises!)

Parfois, lors du développement d'une fonctionnalité, il arrive de faire plusieurs commits (et pas un seul, quand la focntionnalité est terminée et fonctionnelle). Cela peut vite alourdir l'historique des commits et le rendre assez peu clair. Néamoins, c'est une bonne chose de versionner régulièrement son travail! Il faut juste ne pas le faire de manière excessive (pas un commit après chaque ligne de code!).

Là aussi, git propose une solution : La réécriture d'historique / le **squash de commits**. Cette fonctionnalité permet de sélectionner un ensemble de commits (par exemple, les 5 derniers commits) et de le regroupper en un seul et même commits. Ainsi, quand une fonctionnalité a fini d'être développée, on peut regroupper tous les commits qui ont été réalisés lors du développement de cette partie en un seul et même commit et ainsi, rendre l'historique plus élégant.

On utilise pour cela la commande suivante :

```bash
git rebase -i HEAD~N
```

Ici, `N` doit être remplacé par le nombre de commits à selectionner (les `N` derniers commits). Si on doit veut sélectionner jusqu'au premier commit du repository on remplace le `HEAD~N` par `--root`. Une fois la commande éxécutée, une interface s'affiche, en console.  
Par exemple :

```bash
pick 2e5d3fd debut ajout chat
pick earc042 chat progression
pick f2ep173 chat fini
pick t2du1z9 ha non en fait, fix du chat...
```

Les commits sélectionnés sont présentés du plus ancien au plus récent. Il suffit alors de remplacer le mot clé `pick` par `s` (pour squash) pour tous les commits qu'on veut **squasher**. Tous les commits labellés par **s** seront alors fusionnés dans le premier commit labellé **pick** au-dessus d'eux. On quitte ensuite cette interface en faisant `Echap` puis `:wq` (écrire et quitter).

```bash
pick 2e5d3fd debut ajout chat
s earc042 chat progression
s f2ep173 chat fini
s t2du1z9 ha non en fait, fix du chat...
```

Une autre interface s'affiche alors. Elle présente le "nouveau message" du commit qui combine les messages de tous les commits précédents. Vous pouvez alors réecrire un autre message. On quitte cette interface de la même manière que précédemment (`Echap` puis `:wq`).

```bash
#1st commit message
debut ajout chat

#2nd commit message
chat progression

#3rd commit message
chat fini

#4rd commit message
ha non en fait, fix du chat...
```

Peut devenir : 

```bash
#Commit message
Ajout du chat
```

Le **rebasing** s'effectue alors. On peut consulter l'historique pour constater le changement (`git log`).

Depuis cet état, votre repository distant va refuser d'intégrer vos changements car des commits ont disparus...il ne sait plus où vous en êtes. Vous pouvvez alors forcer le changement en utilisant l'option `--force` lors du push.   Attention à ne pas abuser de cette option, car il force le repository distant à se synchroniser sur votre version et donc, effacer les différences entre son contenu et votre repository. Il est donc conseillé du'tiliser cette option seulement dans le cas du rebase, comme nous venons de le voir.

<div class="exercise">

1. Regrouppez tous les commits ayant servi au développement de la commande `remplacer` en un seul.

3. Observez l'historique des commits.

4. Poussez sur votre repository github **en forçant**.

</div>

Maintenant, nous allons ajouter une troisième commande! Là aussi, effectuez plusieurs commits.

<div class="exercise">

1. On souhaite ajouter une commande pour mettre en **majuscules** une portion du texte. Commencez par définir et compléter le code de la méthode suivante dans `Document` :

    ```java
    public void majuscules(int start, int end) {
        //TO-DO!
    }
    ```

    **Astuces** : 

    - Pour extraire une portion d'une chaîne de caractères, servez vous de la méthode `substring`. 
    - Pour passer l'intégralité d'une chaîne de caractrèes, on utilise la méthode `toUpper`.
    - Reservez-vous de la méthode `remplacer`!

2. On souhaite appeller la commande ainsi :

    ```bash
    majuscules;depart;fin
    ```

    Définissez donc une classe adéquate pour cette `commande` et enregistrez là dans la `factory`.

3. Testez votre commande. Regrouppez vos commits puis poussez vos modifications sur votre repository **Github**.

</div>

L'intitulé de cette section contient **"réparer vos bêtises"** car ce mécanisme permet aussi d'effacer certains commits intermédiaires contenant des informations sensibles, entre autres.

Par exemple, imaginons le scénario suivant :

1. On créé un **site web PHP** avec une base de données.

2. On ajoute un fichier de configuration pour la base de données, **avec les identifiants de connexion!** (login / mot de passe)

3. On a oublié de mettre ce fichier de configuration dans le `.gitignore`! Et on a fait des commits qu'on a poussé sur le repository distant! N'importe qui peut donc accèder aux identifiants.

4. On fait un nouveau commit où le fichier est ajouté au .gitignore...on éxécute la commande `git rm -r --cached .` pour prendre en compte cet ajout tardif, comme ça, le fichier de configuration ne sera plus traqué par git. Mais c'est trop tard, on peut remonter l'historique des commits, et retourner à une verison où ce fichier est bien là.

5. Eurêka! Avec la technique de rebasing, on peut regroupper mon dernier commit "sain" avec le commit précédent. Ainsi, il en résultera un seul commit où se fichier de configuration n'a jamais été traqué...! On force le `push`, l'incident est réparé!

### Conventions pour les commits

Jusqu'ici, vous avez écrit des message de commits comme bon vous semble. En réalité, les messages des commits doivent respecter des **conventions** et un certain **format** :

```bash
git commit -m "<type>[portee (optionnel)]: <description>"
```

On y retrouve :

- Le `type` : la nature des changements apportés lors de ce commit, par exemple :

    - `feat` : un commit qui ajoute une nouvelle fonctionnalité
    - `fix` : un commit qui corrige un bug
    - `refactor` : un commit qui retravaille l'architecture du code (sans affecter les fonctionnalités)
    - `docs` : un commit qui affecte seulement la documentation
    - Et bien d'autres!

- La `portée` : paramètre **optionnel**, donne des informations supplémentaires sur le contexte (par exemple "sprint1").

- La `descrption` : Une description rapide de ce qui a changé, ce qu'apporte le commit (presque équivalent aux messages de commits que vous avez écrit jusqu'ici).

On peut également rajouter (optionnelement) tout un corps au commit, pour donner plus de détails.

On purrait avoir, par exemple, le commit suivant :

refactor:utilisation de l'injection de dépendances

Plus d'informations sur [cette ressource](https://gist.github.com/qoomon/5dfcdf8eec66a051ecd85625518cfd13).

Bien sûr, si vous faite beaucoup de commits, ce format peut vite devenir lourd! Vous pouvez donc utiliser l'approche suivante lors du développement d'une fonctionnalité :

- Faire des commits simples pendant le développement.  
- A la fin, regroupper les commits en un seul et appliquer un message respectant les conventions.

### Les branches

Vous pouvez visualiser votre **repository** git comme un **arbre**. Une branche est un endroit où on peut effectuer des commits. Au début, cet "arbre" ne possède q'une seule **branche** (la branche master). ur une branche, à partir de n'improte quel point (un commit, ou même dans un repository qui vient d'être initialisé), il est possible de démarrer une nouvelle branche à partir de ce point.  
La nouvelle **branche** créée est une copie de la branche d'où elle a été créée. Par contre, elle évolue différemment. Tous les commits effectués sur cette branche n'affectent pas la branche d'origine.  

Il est alors possible de changer la branche de travail à n'importe quel moment (tant que les derniers changements sur la branche ont été commit). On retourne alors dans la version définie par le commit le plus récent de la branche.

A un moment donné, il est alors possible de **fusionner** une branche avec une autre. La branche "receveuse" tente alors d'intégrer les commits de l'autre branche.

Si cette visualisation est trop abstraite pour vous, imaginez vous dans un univers de science fiction où vous auriez le pouvoir de créer différentes dimensions parallèle à la nôtre et d'y voyager librement! A terme, si cela est possible, vous aimeriez réeunir plusieurs éléments de chaque dimensions dans la nôtre! Mais cela peu parfois occassioner certains soucis...(c'est tout le sujet du comics DC **Crisis on Infinite Earths** où à la fin, cinq terres se retrouvent réeunies en une seule).

Dans un repository **git**, vous allez useuellement trouver des **branches permanentes** et des **branches temporaires**.

Par exemple, pour les **branches permanentes** :

- `master` : la branche principale dite de `production`. Elle contient une version stable et fonctionnelle du code. Pendant le développement, on ne `commit` jamais direcetment dessus! (oups, c'est ce qu'on a fait jusqu'ici...!)

- `development` : la branche dérivée de `master` et utilisée pour le développement. Généralement, on s'en sert pendant la phase de développement pour intégrer peu à peu les différentes branches de fonctionnalités développées pendant le cycle (un sprint, par exemple). Cette branche sera alors fusionnée, une fois stable, sur `master`.

- `docs` : une brnache contenant la documentation...

Pour les **branches temporaires**, on peut avoir :

- `feature/nom_feature` : branche dérivée de `development` et utilisée pour développer une fonctionnalité "`nom_feature`". A terme, cette branche sera fusionnée sur `development`.

- `bugfix/description_bug` : branche dérivée de `development` et utilisée pour réparer un bug (décrit succintement par `description_bug`).

- `hotfix/description_bug` : similaire à un bugfix, mais fait dans l'urgence car la situation l'exige. Le code n'est pas forcément de qualité, c'est une solution temporaire.

- `test/description_test` : branche pour essayer des choses, pour expérimenter.

Ces branches sont temporaires car elles ne sont plus utiles une fois fusionnées et intégrées, par exemple.

On peut aussi ajotuer des identifiants : `feature/1/nom_feature`, etc... 

Pour **créer une nouvelle branche** dérivée de la branche sur laquelle vous travaillez actuellement, il suffit d'utiliser la commande suivante :

```bash
git branch nom_branche
```

Ensuite, pour se **déplacer** dans la branche en question (depuis n'importe où) :

```bash
git checkout nom_branche
```

On peut combiner ces deux opérations ainsi afin de créer une branche et d'immédiatement s'y déplacer :

```bash
git checkout -b nom_branche
```

Pour connaître la branche sur laquelle on se trouve actuellement, on peut utiliser :

```bash
git rev-parse --abbrev-ref HEAD
```

**Attention**, une fois sur une nouvelle branche, quand vous voudre **push**, il faudra bien préciser la branche où vous vous trouvez (et donc pas forcément `master`!).

Pour **supprimer une branche**, on utilise la commande suivante :

```bash
git branch -D nom_branche
```

Enfin, quand on souhaite **fusionner** deux branches, **on se déplace dans la branche receveuse** (celle qui va intégrer les changements) et on exécute la commande suivante :

```bash
git merge nom_branche_a_integrer
```

Par exemple, si on se trouve sur la branche `development` et qu'on souhaite intégrer les changements de la branche `feature/chat` :

- On se déplace sur la branche `development` (si on n'y ait pas déjà) :

```bash
git checkout development
```

Et enfin :

```bash
git merge feature/chat
```

Si tout va bien et qu'il n'y a pas de **conflits**, la fusion va s'opérer. Sinon, s'il y a des conflits, il faudra les régler ça à la main. En tous cas, une fois la fusion finalisée, il ne faut pas oublier de **push** tout ça!

Une fois fusionnée, la branche `feature/chat` n'est plus utile, on peut la supprimer :

```bash
git branch -D feature/chat
```

Lors d'un merge, en cas de conflit (si le **merge** automatique échoue) il faut :

- Ouvrir les fichiers concernés par les conflits. Ils présentent des sections avec la source des conflits (les deux versions qui différent)

- Editer ces fichiers pour ne garder que les modifications qui nous intéressent et adapter si besoin.

- Ajouter les fichiers (`git add .`), faire un `commit` puis un `push`.

Il est aussi tout à fait possible (si besoin) de resynchroniser une sous-branche dérivée de `development` en faisant un `pull` de la branche `development` depuis la sous-branche. Ainsi, la sous-branche itnégrera les derniers ajouts sur development. Il faut utiliser ce mécanisme seulement si nécessaire, c'est à dire si la fonctionnalité doit avoir besoin qu'une autre fonctionnalité soit complétée afin de porusuivre le dévelopment, par exemple.

### Développement

Nous allons mettre en application ce que vous avez appris sur les **branches** et les **commits** pour le développement de l'éditeur de texte.

<div class="exercise">

1. Depuis votre branche `master`, créez et déplacez-vous dans une nouvelle branche nommée `development`.

2. Dans l'éditeur, on souhaite ajouter une commande pour `effacer` une partie du texte (entre deux positions). depuis `development`, créez une branche adéquate et développez cette fonctionnalité. Vous ferez attention aux messages de commit (en tout cas, au moins le "dernier" regroupant tous vos commits, s'il y en a plusieurs).

3. Une fois cette fonctionnaltié développée, revenez dans votre branche `development`. On souhaite alors ajouter une commande `clear` qui efface tout le texte. Réappliquez le même processus que pour la question précédente. Cepndant, vous **ajouterez un petit bug léger** (qui ne fait planter l'application). Par exemple, avoir avoir effacé le contenu, on ajoute une lettre quelconque dans le texte, ou n'importe quoi. Nous reviendrons sur ce bug plus tard.

4. Revenez dans votre branche `development` puis fusionnez d'abord la branche correspondant à la commande `effacer`. Supprimez ensuite la branche ayant servie à développer cette fonctionnalité.

5. Faites la même chose pour la commande `clear`.

6. Vérifiez que tout fonctionne bien (sans tenir compte du fait que la commande `clear` soit buggée), puis, faites en sorte d'intégrer vos fonctionnaltiés à la branche principale `master`.

</div>

## Collaborer sur un projet

Jusqu'ici, vous avez travaillé seul, mais le but de **git** est aussi de pouvoir travailer en équipe! Nous allons voir comment collaborer efficacement et aussi voir ce que github propose comme outils, dans ce sens.

### Ouverture et gestion d'une issue

Vous et les autres collègues de votre groupe avaient inséré un mini bug lors de l'exercice précédent. Nous nous plaçons alors dans le contexte où vous découvrez cette application et recontrez ce bug! (évidemment, le projet n'a pas de tests unitaires!).  

Comment faire pour reporter ce bug au développeur? Github propose simplement un onglet `Issues` dans le repository, qui va servir à l'ouverture et la fermeture de tickets. Ces tickets sont souvent des signalements de bug, mais on peut aussi avoir des suggestions de fonctionnalités, etc... Un autre utilisateur peut même réaliser du code solution et le proposer en résolution du ticket, si le propriétaire du repository l'accepte!

Pour la suite des exercices, trouvez-vous un binôme qui est au même point que vous. Si l'attente est trop longue, vous pouvez directement passer à la section 4 **"Bonus (pour les plus rapides)"**, en attendant.

<div class="exercise">

1. Clonez le repository d'un collègue qui en est au même point que vous et testez d'éxécuter son programme pour trouver le bug qu'il a placé au niveau de la commande `clear`.

2. Sur ce repository github, créez une `issue` et expliquer le bug que vous rencontrez. Une issue est en fait un fil de discussion où différentes personnes peuvent intervenir.

3. Lorsque votre collègue a fait de même avec **votre repository**, **relevez l'identifiant** (numéro) de l'issue puis retournez dans votre projet et développez un **bugfix**. Il faudra respecter les conventions citées plus tôt pour les branches. Pour le message final du commit, veillez à bien indiquer dans la description le texte suivant : `Fixes #numeroIssue` (en remplaçant numerpIssue, bien sûr).

4. Une fois le **bugfix** publié puis ultimement intégré à **master** via la fusion de la branche `development`, retournez voir l'issue sur github. Vous constaterez que votre commit est alors attaché à cette issue! Vous pouvez alors la fermer.

</div>

### Extension d'un projet existant

Sur un **repository** en ligne, il est généralement possible d'effectuer un **fork**. Cette action va copier le repository dans votre espace de travail, et vous pourrez alors travailler sur une verison dérivée de l'application sans directement affecter le repository d'origine (où de tout façon vous n'avez pas les droits, normalement). A terme, vous pouvez proposer d'intégrer vos ajouts directement au repository principal.

Ce mécanisme peut s'avérer utile si un développeur externe au projet veut proposer une amélioration ou bien simplement un bugfix. Le(s) proprietaire(s) du repository pourront intégrer automatiquement (ou refuser) les changements proposés.

Le fait de faire une demande d'intégration de son code dans le projet d'origine se nomme `pull request` : on demande à ce que le projet "récupère" (pull) notre code / branche. Il va donc en resulter une fusion (`merge`).

<div class="exercise">

1. Encore une fois, rendez vous dans le repository de votre collègue. En haut à droite, appuyez sur le bouton **fork**. Validez. Un nouveau repository "forké" à partir de cleui de votre collègue est disponnible.

2. Clonez ce repository sur votre machine : comme ce repository forké vous appartient, vous aurez droit de faire des push dessus!

3. Dans l'application récupérée, ajoutez  une commande `inserer` qui permet d'insérer une chaîne de caractères à partir d'un index donné. Par exemple :

```bash
ajouter;bonjour monde
bonjour monde
inserer;6; le 
bonjour le monde
```

Il faudra bien respecter le fait d'aller sur la branche de développement puis une branche pour la fonctionnalité, etc...!

4. Une fois la fonctionalité prête et push sur Github, rendez vous dans votre repository forké puis cliquez sur l'onglet **Pull requests**. Cliquez ensuite sur **New pull request**.

5. Dans l'interface de gauche est présenté le repository de votre collègue. Selectionnez la branche de destination (development). A droite (votre repository forké) selectionnez la branche contenant la fonctionnalité.

6. Cliquez sur **Create pull request**.

7. Une fois que votre collègue aura fait la même chose pour vous, rendez-vous dans votre repository, également dans l'onglet **Pull requests**. Acceptez la **PR** et constatez le changement, sur la branche development. N'oubliez pas de **pull** les modifications, en local!

</div>

### Travailler en équipe

Dans l'exercice précédent, vous avez collaboré avec un collègue mais celui-ci était "extérieur" au projet. Il est bien sûr tout à fait possible d'intégrer d'autres personnes à un repository pour qu'ils aient les mêmes droits que vous.

<div class="exercise">

1. Dans votre repository github (pas celui forké, celui original), rendez-vous dans l'onglet **Settings** puis **Collaborators**.

2. Cliquez sur **Add people** et ajoutez votre collègue. Il devra accepter l'invitation via un mail envoyé par github.

3. Votre collègue fera la même chose pour vous, acceptez l'invitation.

4. Dans votre repository d'origine (celui dont vous êtes auteur, depuis le début du TP), ajoutez une **nouvelle fonctionnalité** permettant d'afficher la description de chaque commande. Il faudra donc commencer par rajouter une méthode dans l'itnerface `Commande`...! Respectez bien le processus que vous avez jusq'ici : création de sous-branches, commits conventionnels, etc...Une fois terminé, ne fusionnez pas la branche de votre fonctionnalité sur `development`.

5. Quand vous avez fini, clonez le repository de votre collègue sur votre machine. Vous avez maintenant les droits d'accès et surtout d'écriture. 

6. Sur ce repository, ajoutez une nouvelle commande `minuscules` similaire à `majuscules` mais pour les minuscules (on utilise `toLowerCase`). Une fois terminé, ne fusionnez pas la branche de votre fonctionnalité sur `development`.

7. Une fois que vous êtes synchronisés (votre collègue a fini sa fonctionnalité sur votre repository, et vous avez terminé avec la description des commandes sur votre repository), sur votre repository, fusionnez les deux nouvelles branches de fonctionnalités de la fonctionnalité dans la branche `development`. Tout devrait bien se passer. Si besoin, vous pouvez récupérer les branches distantes (non sycnrhonisées avec votre repository local) avec `git fetch`.

8. Une fois le `merge` réalisé, jetez un oeil à votre code. Il ne devrait plus compiler. Pourquoi? Corrigez les erreurs et faites un nouveau commit et un push. Une fois tout rentré dans l'ordre, vous pourrez fusionner `development` dans `master`.

</div>

## Bonus (pour les plus rapides)

Si vous êtes en avance, imaginiez et codez une fonctionnalité `undo` permettant **d'annuler** la dernière commande éxécutée (et donc, revenir à l'état précédent du document). Si vous arrivez à implémenter cette fonctionnalité, vous pouvez aussi implémenter le `redo`, c'est à dire **réexécuter** une action qui a été annulée. En gros, le `CTRL+Z` et le `CTRL+Y`

## Conclusion

Lors du dernier exercice, nous avons eu un cas où la fusion a bien fonctionnée (pas de conflit dans les fichiers) mais où le code produit n'était **pas compilable**, malgré le fait que séparemment, ces fonctionnalités fonctionnaient bien! Dans le prochain TD, nous allons étudier les **workflows** qui permettront notamment à Github de détecter ce genre de cas et de nous avertir.  
Nous parlerons aussi de techniques d'automatisation, notament pour le **déploiement** et la publication des programmes, à partir du repository.

Nous avons exploré pas mal de commandes de **git** à travers ce premier TP, mais il en existe beaucoup d'autres, et certaines très utiles! Je vous conseille donc de vous informer sur ce sujet, en complément.