## TP Gestion d'hôtel

On vous demande de concevoir un système de réservations dans un hôtel.

*Note* : le programme ne prendra aucune entrée utilisateur. Pour vous assurer de son bon fonctionnement, vous utiliserez la méthode `main` pour instancier des objets avec des valeurs « en dur » et afficher les résultats des différentes opérations, en vous assurant visuellement que tout fonctionne comme prévu.

### A. Conception de la classe `Chambre`

Créez quatre attributs pour cette classe, avec des types appropriés :

- `numéro` de chambre

- `joursRestants` : le nombre de jours restants (payés) pour l'occupant actuel

- `type` : l'une des trois valeurs suivantes : « simple », « double », « suite » (on pourra utiliser une _énumération Java_)

- `nomOccupant` : occupant actuel (prénom et nom) ; `null` si la chambre est inoccupée

Écrivez un constructeur pour la classe `Chambre`. Il doit prendre deux paramètres : le numéro de chambre et le type. Le nombre de jours restants doit être automatiquement mis à `0` et l'occupant à `null`. On ne doit pas pouvoir avoir un type de chambre non permis.

Écrivez les méthodes suivantes :

- `définirOccupant` prend deux paramètres : le nom de l'occupant et le nombre de jours pendant lesquels il souhaite rester. Elle renvoie _vrai_ si l'opération s'est bien passé et _faux_ sinon. Vous devez vérifier si la chambre est déjà louée, auquel cas la méthode renvoie _faux_. Si elle est libre, on met à jour le nom de l'occupant et le nombre de jours et on renvoie _vrai_.

- `jourSuivant` décrémente de 1 le nombre de jours restants. Si on arrive à 0, la chambre est de nouveau libre : on remettra `nomOccupant` à `null`.

- `toString` renvoie une string représentant l'état courant d'une instance. Deux exemples montrant le formatage attendu : « *Chambre 123 : double - louée à Jean Dupont - 3 jour(s) restant(s)* » ; « *Chambre 456 : suite - libre* »

NB : la méthode `toString` est une méthode spéciale en Java ; elle est automatiquement appelée lorsqu'on essaie d'afficher un objet. Par exemple, `System.out.println(chambre)` appellera automatiquement `chambre.toString()` et affichera la string renvoyée par cette méthode.

### B. Conception de la classe `Hotel`

- Trois attributs : `nom` de l'hôtel, chambres (une liste de toutes les chambres), nombre de chambres

- Constructeur : trois paramètres - nom de l'hotel, nombre de chambres, nombre d'étages. Après l'initialisation des variables d'instances correspondantes, vous devez instancier la liste de chambres. Puis vous créerez une instance de `Chambre` pour chaque élément de la liste. Voici comment créer les chambres :

  - le numéro est une valeur entre 100 et 999. Le premier chiffre correspond à l'étage, et les deux autres doivent s'incrémenter dans l'ordre (100, 101, 102...)

  - cependant, chaque étage aura le même nombre de chambres (on considérera que les nombres de chambres et d'étages donnés en paramètres permettent cette répartition)

  - chaque étage aura exactement 4 chambres simples, 1 suite et le reste des chambres à allouer seront toutes doubles (de même, on considérera qu'il y a suffisamment de chambres pour respecter cette contrainte)

  - la suite aura le numéro de chambre le plus grand sur l'étage, les chambres simples les numéros les plus petits, et les doubles les numéros intermédiaires

  - exemple : si l'hotel a 2 étages et 14 chambres, la répartition devra être exactement celle indiquée ci-dessous (7 chanbres par étage, 4 simples et 1 suite obligatoires, le reste en doubles) :

```
Répartition :
étage 1 : 100 (simple), 101 (simple), 102 (simple), 103 (simple), 104 (double), 105 (double), 106 (suite)
étage 2 : 200 (simple), 201 (simple), 202 (simple), 203 (simple), 204 (double), 205 (double), 206 (suite)
```

- Écrivez les méthodes publiques suivantes (vous ajouterez autant de méthodes privées que nécessaire pour « aider » ces méthodes) :

  - un _getter_ pour le nombre total de chambres

  - `nbChambresOccupées` doit examiner chacune des chambres et renvoyer combien d'entre elles sont actuellement occupées

  - `tauxOccupation` renvoie un double entre 0 et 100 représentant le pourcentage d'occupation actuel de l'hôtel

  - `louerChambre` prend trois arguments : le type de chambre, le nom du client et le nombre de jours du séjour

    - elle renvoie un booléen indiquant le succès ou l'échec de l'opération

    - parcourez les chambres de l'hôtel et trouvez la première chambre inoccupée qui correspond au type recherché

    - enregistrez alors les informations (occupant et temps de séjour) pour cette chambre maintenant louée et renvoyez _vrai_

    - ne renvoyez _faux_ que si aucune chambre adéquate n'est trouvée

  - `jourSuivant` passe au jour suivant ; la méthode doit répercuter l'information à tous les objets `Chambre` pour la mise à jour des nombres de jours restants dans chaque chambre

  - `toString` renvoie une string représentant l'état courant de l'hôtel. Le format attendu est le suivant :

```
Hôtel Aifone

Nombre de chambres occupées : 6 / 12 (50 % d'occupation)

Détail :

Chambre 100 : simple – louée à Jean Dupont - 3 jour(s) restant(s)
Chambre 101 : simple – libre
Chambre 102 : simple – libre
Chambre 103 : simple – louée à Alice Martin - 1 jour(s) restant(s)
Chambre 104 : double – libre
Chambre 105 : suite – louée à Robert Durant - 5 jour(s) restant(s)
Chambre 200 : simple – libre
...
Chambre 205 : suite – libre
```

### C. Implémentation de bonnes pratiques : valider les entrées par rapport aux contraintes métier

On souhaite maintenant vérifier que les entrées garantissent effectivement que :

- chaque étage pourra avoir le même nombre de chambres ;
- on aura au moins 5 chambres par étage ;
- pas plus de 100 chambres par étage (sinon pas assez de numéros).

Par exemple, un hôtel avec 3 étages doit avoir au moins 15 chambres : 3 x (4 simples + 1 suite), pas de double. De plus, 3 étages et 16 chambres n'est pas acceptable car on ne pourra pas avoir le même nombre de chambres par étage.

En cas de non-respect des contraintes, vous devez afficher un message d'erreur correspondant au problème rencontré et sortir immédiatement du programme (`System.exit(0)` arrête immédiatement la JVM et donc le programme).

Écrivez une méthode `validerEntrees` qui prend en paramètres le nombre de chambres et le nombre d'étages, et qui effectue les vérifications nécessaires. Utilisez ensuite cette méthode pour respecter les contraintes lors de la création d'une instance de `Hotel`.
