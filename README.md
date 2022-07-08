# Preuve de la dénombrabilité de $~ \mathbb{Q}^{+} $, implémentation en ocaml
## Introduction
On s'intéresse à l'ensemble des rationnels $~ \mathbb{Q}^{+} $, c'est à dire exactement :  
$\mathbb{Q}^{\+} = \\{\frac{p}{q}, p \in \mathbb{N} \wedge q \in \mathbb{N}^{\*} \\}$  

On considère que les fractions "non réduites" sont identiques aux fractions "réduites" (par exemple $\frac{6}{3} $ sera strictement équivalent à $\frac{2}{1} $). Pour être précis, ces deux fractions appartiennent à la même classe d'équivalence). On travaillera par la suite sur des frcations réduites (c'est à dire dont on peut dire que le PGCD du numérateur et du dénominateur est 1) pour plus de clarté.  
Dire qu'un ensemble E est dénombrable, c'est grossièrement dire que si l'on avait une main à n doigts, on pourrait compter les éléments de E. Formellement, on dira qu'il existe une bijection entre E et $\mathbb{N}$ (ou une de ses sous parties). Quelques exemples d'ensemble dénombrables :  

+ $\\{1,2,3,4,5\\}$ est dénombrable car la fonction identité définie sur $\\{1,2,3,4,5\\} $ forme une bijection avec notre ensemble formé uniquement d'entiers naturels. De la même façon, on remarque que la fonction identité forme une bijection de toute partie de $\~ \mathbb{N} $ sur elle-même, c'est pourquoi toute partie de $\~ \mathbb{N} $ est dénombrable. C'est cohérent avec notre définition *naïve* de la dénombrabilité : avec suffisamment de doigts sur notre main, on peut compter tous les ensembles finis d'entiers naturels. De la même façon, $\~ \mathbb{N}$ est dénombrable puisqu'il est en bijection avec lui-même par la fonction identité.
+ L'ensemble des nombres pairs, $\\{2n, n \in \mathbb{N} \\} $, est dénombrable. Si on pose $\~f(n) = 2n \~ \forall \~ n \in \mathbb{N} $ on remarque que cette fonction est une bijection de $\~ \mathbb{N} $ dans $~\\{2n, n \in \mathbb{N} \\} $  
Nous allons maintenant montrer que $\mathbb{Q}^{+}$ est dénombrable, et faire l'implémentation des outils que l'on utilisera en ocaml. 

## Arbre de Calkin-Wilf

Pour montrer la dénombrabilité de $~ \mathbb{Q}^{+}$ on utilisera l'arbre de Calkin-Wilf. Un arbre binaire est une structure de données qui peut se représenter sous la forme d'une hiérarchie dont chaque élément est appelé nœud, le nœud initial étant appelé racine. Ce document ne détaillera pas tous les termes utilisés pour parler de l'arbre de Calkin-Wilf, c'est cependant une structure de données assez intuitive et simple à comprendre (en cas de question voir fr.wikipedia.org/wiki/Arbre_binaire). L'arbre de Calkin-Wilf est défini comme suit :  
+ La racine vaut 1  

+ Chaque noeud est une fraction, qu'on peut nommer $\frac{p}{q} $  

+ Le fils gauche de chaque noeud vaut $\frac{\frac{p}{q}}{1+\frac{p}{q}} $, et chaque fils droit vaut $\frac{p}{q} + 1 $. On peut aussi dire qu'un noeud $\frac{p}{q} $ a pour fils gauche $\frac{p}{q+p} $ et pour fils droit $\~ \frac{p+q}{q} $

Voici par exemple l'arbre de Calkin-Wilf avec une profondeur de 3 :
```text
        1/1
       /   \
    1/2     2/1
   /   \   /   \
 1/3  3/2 2/3  3/1
```

On base notre preuve sur cet arbre, montrant que l'ensemble des noeuds de cet arbre est en bijection avec l'ensemble des entiers naturels. Pour ça nous allons poser une suite $\~U_n $ définie sur $\~\mathbb{N}^{\*} $, qui parcourt l'arbre "de gauche à droite". Par exemple $\~ U_1 = 1, U_2 = \frac{1}{2}, U_3 = \frac{2}{1} $ etc... On pose $\~U_0 = 0 $, ce qui permet de "compléter" la bijectivité de $~U_n $ dans $\~ \mathbb{Q}^{+} $.
Autrement dit, nous allons montrer que pour chaque fraction F qui existe il existe un unique n tel que $U_n = F$, ou que F est représenté exactement une fois dans l'arbre.  

### Une implémentation ocaml de l'arbre de Calkin-Wilf
Pour implémenter notre outil, nous allons utiliser ocaml. On définit alors d'abord notre type frac comme suit :
```ocaml
type frac = {p : int; q : int} 
```
Ne pas immédiatement utiliser une fraction comme 2. /.3. est essentiel, puisque si l'on décide de représenter les données comme des float alors on perd en précision. Pour rappel en ocaml :
```ocaml
─( 00:00:00 )─< command 1 >─────────────────────────────────────────────{ counter: 0 }─
utop # 1. /. 3. ;; 
- : float = 0.333333333333333315
```
Donc notre représentation est "mathématiquement fausse". De plus, si l'on décide par exemple d'afficher une partie de l'arbre en console il est préférable de voir $\frac{1}{3} $ plutôt que 0.333333333333333315. On définit ensuite récursivement notre structure de noeud : 
```ocaml
type noeud = F of frac | N of (noeud * frac * noeud)
```
Techniquement notre arbre est infini, donc chaque noeud est censé contenir son fils gauche et son fils droit, mais on ne peut pas stocker une infinité de données. C'est pourquoi par exemple pour représenter notre arbre de profondeur 3 on écrira :
```ocaml
─( 20:37:06 )─< command 8 >─────────────────────────────────────────────{ counter: 0 }─
utop # let prof3 = ((({p=1;q=3}),{p=1;q=2},({p=3;q=2})), {p=1;q=1}, ({p=2;q=3},{p=2;q=1},{p=3;q=1})) ;; 
val prof3 : (frac * frac * frac) * frac * (frac * frac * frac) =
  (({p = 1; q = 3}, {p = 1; q = 2}, {p = 3; q = 2}), {p = 1; q = 1},
   ({p = 2; q = 3}, {p = 2; q = 1}, {p = 3; q = 1}))
```

## Les fractions de l'arbre sont irréductibles

Un premier élément de notre preuve est que toute fraction de l'arbre est irréductible. On raisonne par récurrence :  
Propriété :  
**Pour tout noeud à une profondeur n, ce noeud est une fraction irréductible.** 
Initialisation :  
Le noeud à une profondeur 0 est $\~ \frac{1}{1} $, irréductible.  
Hérédité : 
Supposons que tous les noeuds à une profondeur n soient irréductibles pour n fixé dans $\~ \mathbb{N} $, montrons que tous les noeuds à une profondeur n+1 sont irréductibles.  
Les noeuds à une profondeur n+1 sont tous les fils directs des noeuds $\~ N_k $ à une profondeur n. Ils s'écrivent donc tous $\~ N_k + 1 $ si ils sont fils droits directs ou $\frac{N_k}{N_k +1} $ si ils sont fils gauches directs.   
Cas du fils droit :  
Par hypothèse de récurrence $N_k $ est une fraction irréductible, donc $N_k + 1 $ est irréductible (on peut le prouver facilement par l'absurde en divisant tout par le dénominateur de $N_k $, ou par un théorème plus fort comme la contraposée théorème de bézout). Or $N_k + 1 = N_{k+1} $ donc on a bien $N_{k+1} $ irréductible.  
Cas de fils gauche :  
Par hypothèse de récurrence $N_k $ est une fraction irréductible donc $\frac{N_k}{N_k + 1} $ est irréductible (On peut le prouver facilement par l'absurde en divisant tout par le $N_k $, ou par un théorème plus fort comme la contraposée théorème de bézout). Or $\frac{N_k}{N_k + 1} = N_{k+1} $ donc on a bien $N_{k+1} $ irréductible.

### Une implémentation ocaml pour réduire les fractions
Comme on *n'évaluera* pas d'expression contenant la fraction et qu'on s'est promis un peu plus tôt de ne travailler qu'avec des fractions réduites pour plus de clarté, on se doit d'établir un algotithme qui réduit nos fractions. L'idée pour réduire une fraction c'est de diviser son dénominateur et son numérateur par leur PGCD. Il nous faut donc trouver le PGCD de nos deux nombres. Voici une première fonction qui trouve le PGCD de deux nombres en utilisant l'algorithme d'Euclide. 
```ocaml
type frac = {p : int; q : int} ;; (* pour rappel voici comment on avait défini nos fractions *)

let PGCD (p : int) (q : int) : int =	
	if (p mod q) = 0 then q else (PGCD q (p mod q))
```
Il est également possible (avec beaucoup plus de difficulté, beaucoup moins de lisibilité et d'optimisation) de programmer cette fonction "naïvement", c'est à dire sans l'algorithme d'Euclide. L'idée est alors de lister les diviseurs de chaque nombre puis d'évaluer le plus grand élément commun aux deux listes de diviseurs. Vous pouvez retrouver cette implémentation ici #INSÉRER LE TRUC QUE J'AI FAIT EN OCAML en ocaml. En attendant, il ne nous reste plus qu'à diviser notre dénominateur et notre numérateur par leur PGCD :  
```ocaml
type frac = {p : int; q : int} ;; (* pour rappel voici comment on avait défini nos fractions *)

let PGCD (p : int) (q : int) : int =  
  if (p mod q) = 0 then q else (PGCD q (p mod q)) ;;

let simplifier (f : frac) : frac =
  let pgcd = PGCD f.p f.q in {p = f.p/pgcd ; q = f.q/pgcd} ;;
```

## Chaque fraction est représentée dans l'arbre maximum une fois

Maintenant, on se propose de montrer que chaque fraction est représentée dans l'arbre au moins une fois. Autrement dit, on montre que $U_n $ est une injection de $\~\mathbb{N} $ dans $\~\mathbb{Q}^{+} $. Supposons $\~\frac{p}{q} $ la fraction qui au moins deux fois dans l'arbre  dont le dénominateur est le plus petit  et parmi celles-ci (pour différencier celles dont le dénominateur est le miminum de l'arbre), celle dont le numérateur est le plus petit. Si p < q alors $\~ \frac{p}{q} $ est le fils gauche direct de  $\~ \frac{p}{q - p} $, et donc $\~\frac{p}{q - p} $ apparait deux fois. Or son dénominateur est plus petit que celui de $\~ \frac{p}{q} $, ce qui est absurde. Si q < p alors $\~ \frac{p}{q} $ est le fils droit direct de $\~ \frac{p - q}{q} $  et donc $\~ \frac{p - q}{q} $ apparait deux fois. Or son numérateur est plus petit que celui de $\frac{p}{q} $, c'est donc absurde. Si p = q alors la fraction vaut 1. Or elle est le fils droit d'un noeud N, tel que $N +  1 = 1 \Rightarrow N = 0 $. Or aucun noeud n'est nul (se prouve trivialement par récurrence), c'est donc absurde.   

## Chaque fraction est représentée maximum une fois dans l'arbre

Maintenant, on se propose de montrer que chaque fraction est représentée dans l'arbre maximum une fois. Autrement dit, on montre que $U_n $ est une sujection de $\~ \mathbb{N} $ dans $\~ \mathbb{Q}^{+} $. Supposons $\~ \frac{p}{q} $ la fraction qui n'est pas dans l'arbre  dont le dénominateur est le plus petit  et parmi celles-ci (pour différencier celles dont le dénominateur est le miminum de l'arbre), celle dont le numérateur est le plus petit. Si p > q alors $\~ \frac{(p-q)}{q} $ n'est pas dans l'arbre, sinon ce serait le parent direct de $\~ \frac{p}{q} $ (qui serait alors fils droit immédiat). Or le numérateur de $\~ \frac{(p-q)}{q} $ est plus petit que le numérateur de $\~ \frac{p}{q} $, ce qui est absurde. Si q > p alors $\~ \frac{p}{(q-p)} $ n'est pas dans l'arbre, puisque ce serait le parent direct de $\~ \frac{p}{q} $, or le dénominateur de $\~ \frac{p}{(q-p)} $ est plus petit que $\~ \frac{p}{q} $ donc c'est absurde. Si p = q alors le noeud vaut exactement 1/1, et c'est la racine de l'arbre donc il en fait partie. 


## Conclusion 

On a montré que $U_n $ était injective et surjective, çàd qu'elle formait une bijection de $\~ \mathbb{N} $ dans $\~ \mathbb{{Q}^{+}}^{*} $, c'est à dire que $\mathbb{Q}^{+} $ est dénombrable.
