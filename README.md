# Preuve de la dénombrabilité de Q+, implémentation en ocaml
## Introduction
On s'intéresse à l'ensemble des rationnels Q+, c'est à dire exactement :  
$\mathbb{Q}^{\+} = \\{\frac{p}{q}, p \in \mathbb{N} \wedge q \in \mathbb{N}^{\*} \\}$  

On considère que les fractions "non réduites" sont identiques aux fractions "réduites" (par exemple $\frac{6}{3} $ sera strictement équivalent à $\frac{2}{1} $). Pour être précis, ces deux fractions appartiennent à la même classe d'équivalence). On travaillera par la suite sur des frcations réduites (c'est à dire dont on peut dire que le PGCD du numérateur et du dénominateur est 1) pour plus de clarté.  
Dire qu'un ensemble E est dénombrable, c'est grossièrement dire que si l'on avait une main à n doigts, où n est un entier naturel, on pourrait compter les éléments de E. Formellement, on dira qu'il existe une bijection entre E et $\mathbb{N}$ (ou une de ses sous parties). Quelques exemples d'ensemble dénombrables :  

+ $\\{1,2,3,4,5\\}$ est dénombrable car la fonction identité définie sur $\\{1,2,3,4,5\\} $ forme une bijection avec notre ensemble formé uniquement d'entiers naturels. De la même façon, on remarque que la fonction identité forme une bijection de toute partie de $\~ \mathbb{N} $ sur elle-même, c'est pourquoi toute partie de $\~ \mathbb{N} $ est dénombrable. C'est cohérent avec notre définition *naïve* de la dénombrabilité : avec suffisamment de doigts sur notre main, on peut compter tous les ensembles finis d'entiers naturels. De la même façon, $\~ \mathbb{N}$ est dénombrable puisqu'il est en bijection avec lui-même par la fonction identité.
+ L'ensemble des nombres pairs, $\\{2n, n \in \mathbb{N} \\} $, est dénombrable. Si on pose $~f(n) = 2n ~ \forall ~ n \in \mathbb{N} $ on remarque que cette fonction est une bijection de $\~ \mathbb{N} $ dans $\\{2n, n \in \mathbb{N} \\} $  
Nous allons maintenant montrer que $\mathbb{Q}$ est dénombrable, et faire l'implémentation des outils que l'on utilisera en ocaml. 

