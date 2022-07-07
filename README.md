# Preuve de la dénombrabilité de $~ \mathbb{Q}^{+} $, implémentation en ocaml
## Introduction
On s'intéresse à l'ensemble des rationnels $~ \mathbb{Q}^{+} $, c'est à dire exactement :  
$\mathbb{Q}^{\+} = \\{\frac{p}{q}, p \in \mathbb{N} \wedge q \in \mathbb{N}^{\*} \\}$  

On considère que les fractions "non réduites" sont identiques aux fractions "réduites" (par exemple $\frac{6}{3} $ sera strictement équivalent à $\frac{2}{1} $). Pour être précis, ces deux fractions appartiennent à la même classe d'équivalence). On travaillera par la suite sur des frcations réduites (c'est à dire dont on peut dire que le PGCD du numérateur et du dénominateur est 1) pour plus de clarté.  
Dire qu'un ensemble E est dénombrable, c'est grossièrement dire que si l'on avait une main à n doigts, on pourrait compter les éléments de E. Formellement, on dira qu'il existe une bijection entre E et $\mathbb{N}$ (ou une de ses sous parties). Quelques exemples d'ensemble dénombrables :  

+ $\\{1,2,3,4,5\\}$ est dénombrable car la fonction identité définie sur $\\{1,2,3,4,5\\} $ forme une bijection avec notre ensemble formé uniquement d'entiers naturels. De la même façon, on remarque que la fonction identité forme une bijection de toute partie de $\~ \mathbb{N} $ sur elle-même, c'est pourquoi toute partie de $\~ \mathbb{N} $ est dénombrable. C'est cohérent avec notre définition *naïve* de la dénombrabilité : avec suffisamment de doigts sur notre main, on peut compter tous les ensembles finis d'entiers naturels. De la même façon, $\~ \mathbb{N}$ est dénombrable puisqu'il est en bijection avec lui-même par la fonction identité.
+ L'ensemble des nombres pairs, $\\{2n, n \in \mathbb{N} \\} $, est dénombrable. Si on pose $\~f(n) = 2n \~ \forall \~ n \in \mathbb{N} $ on remarque que cette fonction est une bijection de $\~ \mathbb{N} $ dans $~\\{2n, n \in \mathbb{N} \\} $  
Nous allons maintenant montrer que $\mathbb{Q}$ est dénombrable, et faire l'implémentation des outils que l'on utilisera en ocaml. 

## Arbre de Calkin-Wilf

Pour montrer la dénombrabilité de $~ \mathbb{Q}^{+}$ on utilisera l'arbre de Calkin-Wilf. Un arbre binaire est une structure de données qui peut se représenter sous la forme d'une hiérarchie dont chaque élément est appelé nœud, le nœud initial étant appelé racine. Ce document ne détaillera pas tous les termes utilisés pour parler de l'arbre de Calkin-Wilf, c'est cependant une structure de données assez intuitive et simple à comprendre (en cas de question voir fr.wikipedia.org/wiki/Arbre_binaire). L'arbre de Calkin-Wilf est défini comme suit :  
+ La racine vaut 1  

+ Chaque noeud est une fraction, qu'on peut nommer $\frac{p}{q} $  

+ Le fils gauche de chaque noeud vaut $\frac{\frac{p}{q}}{1+\frac{p}{q}} $, et chaque fils droit vaut $\frac{p}{q} + 1$  

Voici par exemple l'arbre de Calkin-Wilf avec une profondeur de 3 :
```text
        1/1
       /   \
    1/2     2/1
   /   \   /   \
 1/3  3/2 2/3  3/1
```

Nous allons baser notre preuve sur cet arbre, montrant que l'ensemble des noeuds de cet arbre est en bijection avec l'ensemble des entiers naturels. Pour ça nous allons poser une suite $U_n $ définie sur $\mathbb{N} $, qui parcourt l'arbre "de gauche à droite". Par exemple $~ U_0 = 1, U_1 = \frac{1}{2}, U_2 = \frac{2}{1} $ etc...   
